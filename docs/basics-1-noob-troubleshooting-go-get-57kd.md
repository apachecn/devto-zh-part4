# 基础知识#1 Noob 故障排除“go get”

> 原文：<https://dev.to/drpeck/basics-1-noob-troubleshooting-go-get-57kd>

问题

*   我如何告诉 npm 使用本地安装而不是 nvm 全局安装来使用下面的 go 命令？

信息

```
$ npm list -depth=0
gopak@1.0.0 /home/user/go
├── git@0.1.5
├── go@3.0.1
└── npm@6.10.1

$ go get github.com/ethereum/go-ethereum
Error: command is not recognized
at /home/user/.nvm/versions/node/v12.6.0/lib/node_modules/go/node_modules/go-cli/lib/invoke.js:24:13
at processTicksAndRejections (internal/process/task_queues.js:85:5)
$ env |grep go
OLDPWD=/home/delta/go/src/github.com/ethereum
PWD=/home/user/go
GOPATH=/home/user/go 
```

我删除了全局 go:

```
$ npm uninstall -g go 
```

已验证 npm global 以确保删除:

```
$ npm list -g -depth=0
/home/user/.nvm/versions/node/v12.6.0/lib
├── browserify@16.3.0
├── ethers@4.0.33
├── ethers-cli@4.0.0
├── ganache-cli@6.4.5
├── jquery@3.4.1
├── lite-server@2.5.3
├── npm@6.9.0
├── npx@10.2.0
└── truffle@5.0.27 
```

尝试再次连接，但它会检查 nvm 路径，因此当然不会找到任何 go:

```
$ go get github.com/ethereum/go-ethereum
bash: /home/user/.nvm/versions/node/v12.6.0/bin/go: No such file or directory 
```

与 nvm 相关的问题...

再次检查环境:

```
$ export GOPATH="$HOME/go"
$ env |grep go
OLDPWD=/home/user/go/src/github.com/ethereum/go-ethereum
PWD=/home/user/go 
```

我昨天(2019 年 6 月 19 日)尝试在 stackoverflow > javascript 聊天室上查看，但没有得到跟踪。

修正:
记住，网上 tuts 不“dockerize”信息，一些必须聚集在周围。
安装 go，指令【https://golang.org/doc/install? T2】download = go 1 . 12 . 7 . Linux-amd64 . tar . gz