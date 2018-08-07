###Hyperledger Fabric 1.2 部署 测试 #####一.环境准备

   docker -v

::

   Docker version 18.06.0-ce, build 0ffa825

..

   git version

::

   git version 2.7.4

..

   curl –version

::

   curl 7.47.0 (x86_64-pc-linux-gnu)

..

   node -v

::

   v8.9.4

..

   npm -v

::

   5.6.0

..

   docker-compose -v

::

   docker-compose version 1.22.0, build f46880fe

..

   go version

::

   go version go1.9.2 linux/amd64

..

   sudo lsb_release -a

::

   LSB Version:    core-9.20160110ubuntu0.2-amd64:core-9.20160110ubuntu0.2-noarch:security-9.20160110ubuntu0.2-amd64:security-9.20160110ubuntu0.2-noarch
   Distributor ID: Ubuntu
   Description:    Ubuntu 16.04.4 LTS
   Release:    16.04
   Codename:   xenial

..

   wget –version

::

   GNU Wget 1.17.1 built on linux-gnu.

###二、go环境部署 >1.安装包下载

::

   wget https://dl.google.com/go/go1.9.2.linux-amd64.tar.gz

..

   2.解压

::

   tar zxvf go1.9.2.linux-amd64.tar.gz

..

   3.移动

::

   mv go /usr/local/

..

   4.编辑文件

::

      vim /etc/profile.d/go.sh  

..

   5.环境变量配置

::

   export GOROOT=/usr/local/go  
   export GOBIN=$GOROOT/bin  
   export GOPKG=$GOROOT/pkg/tool/linux_amd64  
   export GOARCH=amd64  
   export GOOS=linux  
   export GOPATH=/home/gopath    #我的开发目录  
   export PATH=.:$PATH:$GOBIN:$GOPKG  

..

   6.加载配置文件

::

   source /etc/profile

..

   7.go version(测试版本，看到版本号则成功)

::

   go version go1.9.2 linux/amd64

###三、Docker安装 >1.安装依赖

::

   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

..

   2.添加镜像

::

   curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add 

..

   3.添加仓库

::

   sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

..

   4.更新源

::

   sudo apt-get update

..

   5.安装docker-ce

::

   默认：sudo apt-get install docker-ce
   (指定版本：sudo apt-get install docker-ce=<VERSION>)

..

   6.启动docker

::

   sudo systemctl start docker

..

   7.查看docker版本

::

   sudo systemctl start docker

..

   8.设置加速器（可添加阿里云）

::

   curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://8ad7943c.m.daocloud.io

..

   9.重启docker

::

   sudo systemctl restart docker

..

   10.测试速度

::

   docker run sl (显示小火车突突而过)

###四.docker-compose 安装 >1.安装

::

   curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

..

   2.给可执行权限

::

   chmod +x /usr/local/bin/docker-compose

..

   3.查看安装是否成功

::

   docker-compose -v (出现类似docker-compose version 1.22.0, build f46880fe  则成功)

###五.安装node >1.nodejs和npm

::

   sudo apt-get install nodejs-legacy
   sudo apt-get install npm

..

   2.升级npm为最新版本

::

   sudo npm install npm@latest -g

..

   3.安装用于安装nodejs的模块n

::

   sudo npm install -g n

..

   4.通过n模块安装指定的nodejs

::

   sudo n v8.9.4

..

   5.安装npm版本v5.6.0 sudo npm install -g npm@v5.6.0

###六.源码下载 >1.创建文件目录,进入目录下

::

   sudo mkdir -p $GOPATH/src/github.com/hyperledger/ && cd $GOPATH/src/github.com/hyperledger

..

   2.克隆Fabric源码

::

   git clone https://github.com/hyperledger/fabric.git

..

   3.进入到fabric目录查看fabric的git版本

\``\` cd fabric/ git branch -a