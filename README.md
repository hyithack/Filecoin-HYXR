# Filecoin-HYXR
Filecoin加入测试网
### Lotus测试网部署详情文档

[TOC]

#### 修改hosts

```bash
vim /etc/hosts
140.82.113.3 github.com
185.199.108.154 github.githubassets.com
185.199.109.154 github.githubassets.com
185.199.110.154 github.githubassets.com
185.199.111.154 github.githubassets.com
185.199.108.154 github.githubassets.com
185.199.109.154 github.githubassets.com
185.199.110.154 github.githubassets.com
185.199.111.154 github.githubassets.com
199.232.68.133 camo.githubusercontent.com
199.232.68.133 camo.githubusercontent.com
199.232.68.133 github.map.fastly.net
199.232.68.133 github.map.fastly.net
199.232.69.194 github.global.ssl.fastly.net
199.232.69.194 github.global.ssl.fastly.net
140.82.113.5 api.github.com
140.82.113.5 api.github.com
199.232.68.133 raw.githubusercontent.com
199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 favicons.githubusercontent.com
199.232.68.133 favicons.githubusercontent.com
```

#### 环境变量

```bash
#环境变量目录
vim ~/.bashrc 或者 vim /etc/profile
#生效
source ~/.bashrc 或者 source /etc/profile
#go
. "$HOME/.cargo/env"
#IPFS 网关地址: IPFS_GATEWAY,用于下载复制证明参数加速
export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/
#Linux调优
ulimit -c unlimited
#go环境变量
export GOROOT=/usr/local/go
#go环境变量
export GOPATH=$HOME/go
#go环境变量
export PATH=$GOROOT/bin:$PATH
#go环境变量
export GOPROXY=https://goproxy.cn/,https://goproxy.io,direct
#go环境变量
export PATH=$HOME/.cargo/bin:$PATH
#rust编译需要环境配置
export RUSTFLAGS="-C target-cpu=native -g"
#lotus从源码编译
export FFI_BUILD_FROM_SOURCE=1
#lotus daemon 路径
export LOTUS_PATH=/opt/lotus-data/lotus
#lotus miner 路径
export LOTUS_MINER_PATH=/opt/lotus-data/lotusminer
#Lotus worker 路径
export LOTUS_WORKER_PATH=/opt/lotus-data/lotusworker
#临时文件夹路径,用于存放显卡锁定文件.
export TMPDIR=/opt/lotus-data/lotus-data-cache/tmp
#lotus环境变量
export FIL_PROOFS_PARENT_CACHE=/opt/lotus-data/lotus-data-cache/parent_cache
#proof 证明参数路径,默认在/var/tmp/filecoin-proof-parameters下.
export FIL_PROOFS_PARAMETER_CACHE=/opt/lotus-data/lotus-data-cache/filecoin-proof-parameters
#最大化内存参数
export FIL_PROOFS_MAXIMIZE_CACHING=1
#配置Rust日志级别.
export RUST_LOG=debug
#API[tokn+lotus-ip地址]
FULLNODE_API_INFO=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBbGxvdyI6WyJyZWFkIiwid3JpdGUiLCJzaWduIiwiYWRtaW4iXX0.pIaaG4agaUIfC2QrQjJlLFlcOx2vjcNYt88pUE0g3pI:/ip4/127.0.0.1/tcp/1234/http
```

#### 1.下载Filecoin源代码

```bash
git clone https://github.com/filecoin-project/lotus.git
```

#### 2.切换到对应的tag

```bash
git checkout -b v1.11.1
```

#### 3.配置Go与Rust环境变量

##### 3.1 Go下载与环境变量

```bash
#下载
wget https://dl.google.com/go/go1.16.7.linux-amd64.tar.gz
sudo tar -zxvf go1.16.7.linux-amd64.tar.gz -C /usr/loca

#设置环境变量
sudo vim /etc/profile
export GOROOT=/usr/local/go
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
export GO111MODULE=on
export GOPROXY=https://goproxy.cn,direct

#生效
source /etc/profile
```

##### 3.2 Rust下载与环境变量

```bash
#下载[#按提示进行安装{可能需要梯子}]
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh 

#设置Rust下载源,创建 $HOME/.cargo/config文件,写入下面内容：
vim $HOME/.cargo/config
source.crates-io]
replace-with = 'sjtu'0
[source.sjtu]
registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index"

#生效
source $HOME/.cargo/env
```

#### 4.编译

```bash
#测试网
FFI_BUILD_FROM_SOURCE=1 RUSTFLAGS='-C target-cpu=native -g' make clean calibnet

#正式网
FFI_BUILD_FROM_SOURCE=1 RUSTFLAGS='-C target-cpu=native -g' make clean deps all

#基准测试
FFI_BUILD_FROM_SOURCE=1 RUSTFLAGS='-C target-cpu=native -g' make lotus-bench calibnet
```

#### 5.配置启动环境变量

##### 5.1 配置lotus环境变量

```bash
export LOTUS_PATH=$HOME/lotus-data/lotus 		// daemon启动后一些配置文件什么的在这里
export LOTUS_MINER_PATH=$HOME/lotus-data/lotusminer         /
export LOTUS_WORKER_PATH=$HOME/lotus-data/lotusworker
export BELLMAN_CPU_UTILIZATION=0.875 # 最佳值取决于您的确切硬件。
export FIL_PROOFS_MAXIMIZE_CACHING=1
export FIL_PROOFS_USE_GPU_COLUMN_BUILDER=1 # 当有 GPU 时。
export FIL_PROOFS_USE_GPU_TREE_BUILDER=1   # 当有 GPU 时。
export FIL_PROOFS_USE_MULTICORE_SDR=1
export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/
export IPFS_GATEWAY=https://filestar-proofs.s3.cn-east-1.jdcloud-oss.com/ipfs/
```

##### 5.2 配置Rust环境

```bash
#
export PATH=$HOME/.cargo/bin:$PATH
#
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
#
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
#
export RUSTFLAGS="-C target-cpu=native -g"
#
export FFI_BUILD_FROM_SOURCE=1
#
export RUST_BACKTRACE=full
#
export RUST_LOG=debug
```

##### 5.3 配置API_INFO

```bash
#
export MINER_API_INFO=<miner_token>:/ip4/<ip>/tcp/2345/http
#
export FULLNODE_API_INFO=<fullNode_token>:/ip4/<ip>/1234/http
#
export BELLMAN_CUSTOM_GPU="GeForce RTX 2080 Ti:4352"
```

#### 6.启动daemon

```bash
#启动daemon
lotus daemon

#使用nohup命令放后台运行并且设置日志文件
nohup lotus daemon >> /opt/lotus-log/daemon.log 2>&1 &

#实时监控日志文件
tail -f /opt/lotus-log/daemon.log

#等待数据同步完成就可以进行下一步了
```

#### 7.创建矿工地址=钱包地址

```bash
#创建钱包地址,钱包类型为bls
lotus wallet new bls 

#会创建两个地址,一个Owner,一个worker.
```

#### 8.测试网领取测试币

```bash
https://faucet.calibration.fildev.network
```

#### 9.拉取证明需要的参数

##### 9.1 先设置环境变量,设置证明参数下载到哪一个文件[最好是在SSD硬盘中]

```bash
#大约存放100GiB
export FIL_PROOFS_PARAMETER_CACHE=$HOME/lotus-cache/fil_proofs_params
#大约存放50GiB
export FIL_PROOFS_PARENT_CACHE=$HOME/lotus-cache/fil_parent
#密封的时候使用该文件
export TMPDIR=$HOME/lotus-cache/tmp 
```

##### 9.2 下载证明参数[根据自己的需求下载]

```bash
#32GiB证明参数
lotus-miner fetch-params 32GiB

#64GiB证明参数
lotus-miner fetch-params 64GiB
```

#### 10.矿工初始化

```bash
#参数
lotus-miner init --owner=<address>  --worker=<address> --no-local-storage
#如果在环境变量中设置了存放路径直接运行下面命令
lotus-miner init --sector-size=32GiB --no-local-storage
#放入后台一键化脚本
nohup lotus-miner init --sector-size=32GiB --no-local-storage >> /opt/lotus-log/lotus-miner-init.log 2>&1 &
```

#### 11.运行矿工程序

```bash
#启动矿工程序
lotus-miner run

#使用nohup命令放后台运行并且设置日志文件
nohup lotus-miner run >> /opt/lotus-log/lotus-miner.log 2>&1 &

#实时监控日志文件
tail -f /opt/lotus-log/lotus-miner.log

#分离出来的
command=lotus-miner run --miner-type=power --sc-type=get --sc-listen=192.168.1.24:1357
```

##### 11.1 增加存储路径store/seal目录

```bash
#设置数据存储路径，该路径用来存储最终密封好的数据
#执行该命令可能需要一点时间等待
lotus-miner storage attach --init --store <挂载的磁盘>
lotus-miner storage attach --init --store /opt/lotus-data/store

#设置密封扇区的存储路径，密封完成之后该路径下的数据会被自动清空，相当于临时目录
#执行该命令可能需要一点时间等待
lotus-miner storage attach --init --seal <挂载的磁盘>
lotus-miner storage attach --init --seal /opt/lotus-data/seal
```

==以上两个命令都是在启动了 miner 之后才可以执行,是一种动态添加存储路径的方式,非常灵活.==

![image-20210901114754187](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210901114754187.png)

#### 12.质押和查看矿工信息

```bash
#执行一次等于质押一个扇区
lotus-miner sectors pledge
#查看矿工信息
lotus-miner info
# 查看扇区 ID，获取扇区列表
lotus-miner sectors list
```
