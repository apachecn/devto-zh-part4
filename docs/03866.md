# 在 AWS 上自动设置 cloudwatch 警报

> 原文：<https://dev.to/kas84/automatically-setup-cloudwatch-alarms-on-aws-211n>

在 TramitApp Control Horario ，由于我们的稳定增长，我们不得不将我们的平台从混合云迁移到 AWS，因为它具有可扩展性优势。

例如，您可以设置警报，以便当一个 ec2 实例在 Y 分钟内平均使用 X 个 CPU 时，您可以启动另一个 ec2 实例来帮助处理负载。

# 设置

创建一个将安装 CloudWatch 监控工具的脚本，并设置一个每 5 分钟发布一次指标的 cronjob，在我们的示例中是两个卷中使用的*内存*、*内存利用率*和*磁盘空间利用率*、/和/data

```
#!/bin/bash
sudo yum install -y perl-Switch perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https perl-Digest-SHA.x86_64
cd $HOME
wget http://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.1.zip
unzip CloudWatchMonitoringScripts-1.2.1.zip
rm CloudWatchMonitoringScripts-1.2.1.zip

(crontab -l 2>/dev/null; echo "*/5 * * * * ~/aws-scripts-mon/mon-put-instance-data.pl --mem-used --mem-util --disk-space-util --disk-path=/ --disk-path=/data --from-cron") | crontab - 
```

# AWS 配置

创建一个执行默认 aws-configure 的脚本，为我们的警报
配置合适的区域

```
#!/bin/sh
REGION=$(ec2-metadata -z | grep -Po "(us|sa|eu|ap)-(north|south|central)?(east|west)?-[0-9]+")

if [ ! -d /home/ec2-user/.aws/ ]; then
  mkdir -p /home/ec2-user/.aws/
fi

if [ ! -d /root/.aws/ ]; then
   mkdir -p /root/.aws/
fi

echo "[default]"> /home/ec2-user/.aws/config
echo "region = $REGION" >>/home/ec2-user/.aws/config

echo "[default]"> /root/.aws/config
echo "region = $REGION" >>/root/.aws/config 
```

# 创建报警脚本

在我的例子中，我使用 Amazon Linux，所以我们有 ec2-metadata 命令，但是你总是可以从 ec2-instance 中找到 http://169 . 254 . 169 . 254/latest/dynamic/instance-identity/document 并获得与使用 ec2-metadata 相同的信息，如果你使用其他发行版。

在本例中

```
#!/bin/sh
REGION=$(ec2-metadata -z | grep -Po "(us|sa|eu|ap)-(north|south|central)?(east|west)?-[0-9]+")

if [ "$REGION" = "eu-west-1" ]; then
  SNS_TOPIC="WHATEVER_ARN_ID_YOU_HAVE_IN_THIS_REGION"
fi

if [ "$REGION" = "eu-west-2" ]; then
  SNS_TOPIC="WHATEVER_ARN_ID_YOU_HAVE_IN_THIS_REGION"
fi

if [ "$REGION" = "eu-west-3" ]; then
  SNS_TOPIC="WHATEVER_ARN_ID_YOU_HAVE_IN_THIS_REGION"
fi

INSTANCE_ID=$(ec2-metadata --instance-id | cut -d " " -f 2)
INSTANCE_PRIVATE_IP=$(ec2-metadata -o | cut -d " " -f 2)
PRIMARY_PUBLIC_IP_ADDRESS=$(ec2-metadata -v | cut -d " " -f 2)
ROOT_DISK_THRESHOLD=75
DATA_DISK_THRESHOLD=80
MEMORY_THRESHOLD=75
CPU_THRESHOLD=75
FIVE_MINUTES_PERIOD=300
FIFTEEN_MINUTES_PERIOD=900
ROOT_DEVICE=/dev/nvme0n1p1
DATA_DEVICE=/dev/nvme1n1
ROOT_PATH=/
DATA_PATH=/data

echo "Setting up ${INSTANCE_PRIVATE_IP}-cpu-utilization"
aws cloudwatch put-metric-alarm \
--alarm-name "${INSTANCE_PRIVATE_IP}-cpu-utilization" \
--alarm-description "Alarm when CPU exceeds $CPU_THRESHOLD percent" \
--metric-name CPUUtilization \
--namespace AWS/EC2 \
--statistic Average \
--period ${FIFTEEN_MINUTES_PERIOD} \
--threshold ${CPU_THRESHOLD} \
--treat-missing-data breaching \
--comparison-operator GreaterThanThreshold \
--dimensions  Name=InstanceId,Value=${INSTANCE_ID} \
--evaluation-periods 1 \
--alarm-actions $SNS_TOPIC \
--ok-actions $SNS_TOPIC \
--unit Percent 

echo "Setting up $INSTANCE_PRIVATE_IP-root-disk-space-utilization"
aws cloudwatch put-metric-alarm \
--alarm-name $INSTANCE_PRIVATE_IP-root-disk-space-utilization \
--alarm-description "Alarm when root disk space exceeds $ROOT_DISK_THRESHOLD percent" \
--metric-name DiskSpaceUtilization \
--namespace System/Linux \
--statistic Average \
--period $FIVE_MINUTES_PERIOD \
--threshold $ROOT_DISK_THRESHOLD \
--treat-missing-data breaching \
--comparison-operator GreaterThanThreshold \
--dimensions Name=Filesystem,Value=$ROOT_DEVICE Name=InstanceId,Value=$INSTANCE_ID Name=MountPath,Value=$ROOT_PATH \
--evaluation-periods 1 \
--alarm-actions $SNS_TOPIC \
--ok-actions $SNS_TOPIC \
--unit Percent 

echo "Setting up $INSTANCE_PRIVATE_IP-data-disk-space-utilization"
aws cloudwatch put-metric-alarm \
--alarm-name $INSTANCE_PRIVATE_IP-data-disk-space-utilization \
--alarm-description "Alarm when data disk space exceeds $DATA_DISK_THRESHOLD percent" \
--metric-name DiskSpaceUtilization \
--namespace System/Linux \
--statistic Average \
--period $FIVE_MINUTES_PERIOD \
--threshold $DATA_DISK_THRESHOLD \
--treat-missing-data breaching \
--comparison-operator GreaterThanThreshold \
--dimensions Name=Filesystem,Value=$DATA_DEVICE Name=InstanceId,Value=$INSTANCE_ID Name=MountPath,Value=$DATA_PATH \
--evaluation-periods 1 \
--alarm-actions $SNS_TOPIC \
--ok-actions $SNS_TOPIC \
--unit Percent 

echo "Setting up $INSTANCE_PRIVATE_IP-memory-usage-utilization"
aws cloudwatch put-metric-alarm \
--alarm-name $INSTANCE_PRIVATE_IP-memory-usage-utilization \
--alarm-description "Alarm when memory exceeds $DATA_DISK_THRESHOLD percent" \
--metric-name MemoryUtilization \
--namespace System/Linux \
--statistic Average \
--period $FIFTEEN_MINUTES_PERIOD \
--threshold $MEMORY_THRESHOLD \
--treat-missing-data breaching \
--comparison-operator GreaterThanThreshold \
--dimensions Name=InstanceId,Value=$INSTANCE_ID \
--evaluation-periods 1 \
--alarm-actions $SNS_TOPIC \
--ok-actions $SNS_TOPIC \
--unit Percent 
```

# 亲提示

如果您从这个实例创建一个 AMI，并设置一个运行这 3 个脚本的引导服务(只需确保第一个脚本只运行一次)，您将拥有警报，而不必手动设置它们。