# 使用 PowerShell 重命名、移动或重新连接 Azure 虚拟机

> 原文：<https://dev.to/omiossec/using-powershell-to-rename-move-or-reconnect-an-azure-vm-i00>

有时你需要改变虚拟机上的一些东西。出于合规性原因，您可能想要重命名它、更改网络、更改磁盘名称等

一旦你在 Azure 中创建了一个 VM，就没有办法重命名它或者它的一个组件。此外，如果您想将虚拟机重新连接到不同的虚拟网络，您也不能这样做。你可以在门户里面找找，可以搜索 Azure PowerShell Cmdlet 列表或者用 AZ CLI 试试，不行。

有些人会说，您可以使用备份存储来重新创建虚拟机，但这需要时间，而且不可扩展。你不能像这样管理多个虚拟机。

我们可以用 PowerShell 和 Azure PowerShell 模块来自动化这个过程。

想象一下，如果您需要在同一个资源组和同一个 Azure 区域中重命名一个 VM 及其磁盘

第一步是用当前虚拟机配置创建一个对象

```
$SourceVmObject  =  get-azvm  -Name  dsc-dns01  -ResourceGroupName  MyRG 
```

Enter fullscreen mode Exit fullscreen mode

如果虚拟机使用 Azure Backup Vault，在继续之前，我们需要删除虚拟机的恢复点。

当您创建备份库时，Azure 将添加一个名为 AzureBackupRF_RegionName_1 的额外资源组，其中没有任何可见内容(如果您使用门户)，但这是您可以找到虚拟机恢复点的地方。

有以虚拟机名称 AzureBackup_vmname_XXXXXXX 命名的，需要在虚拟机上执行任何复制操作之前将其删除。

问题是，它可能会失败，你可能需要再次这样做。

```
$LoopCondition  =  $false  $Count  =  0  do  {  try  {  Remove-AzResource  -ResourceId  $SourceVmRecoveryPoint.ResourceId  -Force  $LoopCondition  =  $true  }  catch  {  write-debug  "Fail to delete recovery point $($SourceVmRecoveryPoint.Name) after $($Count) attentds"  }  $count  +=1  if  ($count  -gt  5)  {  $LoopCondition  =  $true  }  }  until  ($LoopCondition) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要确保虚拟机被释放。

```
$SourceVmPowerStatus  =  (get-azvm  -Name  $SourceVmObject.Name  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -Status).Statuses  |  where-object  code  -like  "PowerState*"  if  ($SourceVmPowerStatus  -ne  "VM deallocated")  {  stop-azVm  -Name  $SourceVmObject.Name  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -Force  Start-Sleep  -Seconds  30  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们定义虚拟机的新名称

```
$vmNewName  =  "dsc-lab-dns01" 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢 Azure PowerShell 模块的一点是，你可以创建一个 VM 对象，然后用这个对象创建一个 VM。

```
$NewVmObject  =  New-AzVMConfig  -VMName  $vmNewName  -VMSize  $SourceVmObject.HardwareProfile.VmSize 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以创建虚拟机组件了。

我们需要为虚拟机创建一个新的网络接口。我们可以将其连接到原始网络或任何其他可用的网络。

```
$subnetID  =  (Get-AzNetworkInterface  -ResourceId  $SourceVmObject.NetworkProfile.NetworkInterfaces[0].id).IpConfigurations.Subnet.id  $nic  =  New-AzNetworkInterface  -Name  "$($vmNewName.ToLower())-0-nic"  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -Location  $SourceVmObject.Location  -SubnetId  $SubnetId  Add-AzVMNetworkInterface  -VM  $NewVmObject  -Id  $nic.Id 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须处理磁盘。从虚拟机对象的角度来看，有两种类型的磁盘，操作系统和数据磁盘。有关磁盘的信息位于 StorageProfile 属性中。

要复制虚拟机，我们需要为每个磁盘创建一个快照，并使用快照创建新磁盘。

先说 OS 盘。

```
$SourceOsDiskSku  =  (get-azdisk  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -DiskName  $SourceVmObject.StorageProfile.OsDisk.name).Sku.Name  $SourceOsDiskSnapConfig  =  New-AzSnapshotConfig  -SourceUri  $SourceVmObject.StorageProfile.OsDisk.ManagedDisk.Id  -Location  $SourceVmObject.Location  -CreateOption  copy  $SourceOsDiskSnap  =  New-AzSnapshot  -Snapshot  $SourceOsDiskSnapConfig  -SnapshotName  "$($SourceVmObject.Name)-os-snap"  -ResourceGroupName  $SourceVmObject.ResourceGroupName  $TargetOsDiskConfig  =  New-AzDiskConfig  -AccountType  $SourceOsDiskSku  -Location  $SourceVmObject.Location  -CreateOption  Copy  -SourceResourceId  $SourceOsDiskSnap.Id  $TargetOsDisk  =  New-AzDisk  -Disk  $TargetOsDiskConfig  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -DiskName  "$($vmNewName.ToLower())-os-vhd"  Set-AzVMOSDisk  -VM  $NewVmObject  -ManagedDiskId  $TargetOsDisk.Id  -CreateOption  Attach  -Windows 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以复制数据磁盘。一个 Azure 虚拟机可以有 0 到多个数据磁盘(这取决于虚拟机的大小)。

为此，我们需要解析 DataDisks 列表。我们需要注意 Lun 编号，这样我们就不会在重启后遇到问题

```
Foreach  ($SourceDataDisk  in  $SourceVmObject.StorageProfile.DataDisks)  {  $SourceDataDiskSku  =  (get-azdisk  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -DiskName  $SourceDataDisk.name).Sku.Name  $SourceDataDiskSnapConfig  =  New-AzSnapshotConfig  -SourceUri  $SourceDataDisk.ManagedDisk.Id  -Location  $SourceVmObject.Location  -CreateOption  copy  $SourceDataDiskSnap  =  New-AzSnapshot  -Snapshot  $SourceDataDiskSnapConfig  -SnapshotName  "$($SourceVmObject.Name)-$($SourceDataDisk.name)-snap"  -ResourceGroupName  $SourceVmObject.ResourceGroupName  $TargetDataDiskConfig  =  New-AzDiskConfig  -AccountType  $SourceDataDiskSku  -Location  $SourceVmObject.Location  -CreateOption  Copy  -SourceResourceId  $SourceDataDiskSnap.Id  $TargetDataDisk  =  New-AzDisk  -Disk  $TargetDataDiskConfig  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -DiskName  "$($vmNewName.ToLower())-$($SourceDataDisk.lun)-vhd"  Add-AzVMDataDisk  -VM  $NewVmObject  -Name  "$($vmNewName.ToLower())-$($SourceDataDisk.lun)-vhd"  -ManagedDiskId  $TargetDataDisk.Id  -Lun  $SourceDataDisk.lun  -CreateOption  "Attach"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经完成了虚拟机配置，我们可以创建虚拟机了。

```
 New-AzVM  -VM  $NewVmObject  -ResourceGroupName  $SourceVmObject.ResourceGroupName  -Location  $SourceVmObject.Location 
```

Enter fullscreen mode Exit fullscreen mode

这是重命名虚拟机的方法之一。您还可以使用它将虚拟机从一个虚拟网络重新连接到另一个虚拟网络。