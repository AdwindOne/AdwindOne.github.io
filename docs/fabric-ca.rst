
Fabric-ca 部署测试
=================


一、环境准备
----------

- go 版本 1.9以上（本机 go version go1.10.3darwin/amd64） 
- 依赖库安装 
::
brew install libtool (mac) 
::
sudo apt install libtool libltdl-dev (ubuntu)

二、安装 Fabric-ca
----------------

-  

   1. 安装fabric-ca-server and fabric-ca-client 二进制文件 到
      $GOPATH/bin > go get -u github.com/hyperledger/fabric-ca/cmd/…

-  2.注意切换到主分支上,否则如下

::

   ☁  fabric-ca [74f8f4d] git pull --ff-only
   remote: Counting objects: 45, done.
   remote: Compressing objects: 100% (38/38), done.
   remote: Total 45 (delta 7), reused 45 (delta 7), pack-reused 0
   Unpacking objects: 100% (45/45), done.
   From https://github.com/hyperledger/fabric-ca
      3a0a86a..4b1f26c  master     -> origin/master
   You are not currently on a branch.
   Please specify which branch you want to merge with.
   See git-pull(1) for details.

       git pull <remote> <branch>

-  3.解决过程，如下

::

   ☁  fabric-ca [74f8f4d] git branch
   * (HEAD detached at v1.0.0)
     release-1.2
   ☁  fabric-ca [74f8f4d] git checkout release-1.2
   Previous HEAD position was 74f8f4d... FAB-5067 Hyperledger Fabric CA v1.0.0 release
   Switched to branch 'release-1.2'
   Your branch is up to date with 'origin/release-1.2'.
   ☁  fabric-ca [release-1.2] git pull --ff-only
   Already up to date.

-  

   4. 启动客户端，报错如下

::

   ☁  fabric-ca [release-1.2] fabric-ca-server start -b admin:adminpw
   zsh: command not found: fabric-ca-server

-  5.报错原因，环境变量配置问题（此处我mac用的 zsh），解决如下：

::

   ☁  fabric-ca [release-1.2] sudo find / -name fabric-ca-server
   /Users/yunlong/go/bin/fabric-ca-server

-  6.添加环境变量

1. 编辑文件

::

   ☁  fabric-ca  vim ~/.zshrc

2. 添加如下 第五步找到的路径

::

   export PATH="/Users/yunlong/go/bin:$PATH"

3. 加载配置

::

   ☁  fabric-ca  exec zsh

-  7.重新启动

::

   ☁  fabric-ca  fabric-ca-server start -b admin:adminpw
   2018/08/08 15:28:11 [INFO] Created default configuration file at /Users/yunlong/Applications/fabric-ca/fabric-ca-server-config.yaml
   2018/08/08 15:28:11 [INFO] Starting server in home directory: /Users/yunlong/Applications/fabric-ca
   2018/08/08 15:28:11 [INFO] Server Version: 1.2.0
   2018/08/08 15:28:11 [INFO] Server Levels: &{Identity:1 Affiliation:1 Certificate:1 Credential:1 RAInfo:1 Nonce:1}
   2018/08/08 15:28:11 [WARNING] &{69 The specified CA certificate file /Users/yunlong/Applications/fabric-ca/ca-cert.pem does not exist}
   2018/08/08 15:28:11 [INFO] generating key: &{A:ecdsa S:256}
   2018/08/08 15:28:11 [INFO] encoded CSR
   2018/08/08 15:28:11 [INFO] signed certificate with serial number 74053294106764471670431733063714078401174691198
   2018/08/08 15:28:11 [INFO] The CA key and certificate were generated for CA
   2018/08/08 15:28:11 [INFO] The key was stored by BCCSP provider 'SW'
   2018/08/08 15:28:11 [INFO] The certificate is at: /Users/yunlong/Applications/fabric-ca/ca-cert.pem
   2018/08/08 15:28:11 [INFO] Initialized sqlite3 database at /Users/yunlong/Applications/fabric-ca/fabric-ca-server.db
   2018/08/08 15:28:11 [INFO] The issuer key was successfully stored. The public key is at: /Users/yunlong/Applications/fabric-ca/IssuerPublicKey, secret key is at: /Users/yunlong/Applications/fabric-ca/msp/keystore/IssuerSecretKey
   2018/08/08 15:28:12 [INFO] The revocation key was successfully stored. The public key is at: /Users/yunlong/Applications/fabric-ca/IssuerRevocationPublicKey, private key is at: /Users/yunlong/Applications/fabric-ca/msp/keystore/IssuerRevocationPrivateKey
   2018/08/08 15:28:12 [INFO] Home directory for default CA: /Users/yunlong/Applications/fabric-ca
   2018/08/08 15:28:12 [INFO] Listening on http://0.0.0.0:7054

三、简单使用，测试 
---------------

- 1.重开一个终端,查看当前目录下文件

::

   ☁  fabric-ca  tree
   .
   ├── IssuerPublicKey
   ├── IssuerRevocationPublicKey
   ├── ca-cert.pem
   ├── fabric-ca-server-config.yaml
   ├── fabric-ca-server.db
   └── msp
       └── keystore
           ├── IssuerRevocationPrivateKey
           ├── IssuerSecretKey
           └── c73b84199e4d095def6bc40d514ded38be950970592848fb8230e13f00848de6_sk

   2 directories, 8 files

-  2.通过openssl命令查看证书内容(如下可以看到，数字证书包含了签名算法信息、申请者基本信息、申请者公钥信息等)

::

   ☁  fabric-ca  openssl x509 -in ca-cert.pem -inform pem -noout -text
   Certificate:
       Data:
           Version: 3 (0x2)
           Serial Number:
               0c:f8:aa:20:34:a9:40:7f:89:3c:ee:4c:7d:84:27:37:64:87:dd:7e
       Signature Algorithm: ecdsa-with-SHA256
           Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server
           Validity
               Not Before: Aug  8 07:23:00 2018 GMT
               Not After : Aug  4 07:23:00 2033 GMT
           Subject: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server
           Subject Public Key Info:
               Public Key Algorithm: id-ecPublicKey
                   Public-Key: (256 bit)
                   pub:
                       04:c7:ca:6e:a3:d7:09:f7:5f:d8:37:5b:e5:50:1e:
                       b2:72:ec:98:95:92:ef:36:83:3a:83:10:43:5d:5c:
                       c8:44:fa:55:4d:1d:40:b0:8f:a6:2c:52:2d:0f:10:
                       fd:6b:09:75:28:b2:b8:32:ee:f5:9b:f2:88:5d:83:
                       57:17:f3:b4:2f
                   ASN1 OID: prime256v1
                   NIST CURVE: P-256
           X509v3 extensions:
               X509v3 Key Usage: critical
                   Certificate Sign, CRL Sign
               X509v3 Basic Constraints: critical
                   CA:TRUE, pathlen:1
               X509v3 Subject Key Identifier:
                   8F:49:3A:41:46:C4:95:BA:0A:D7:5C:9F:18:CF:FB:B9:EF:32:88:AF
       Signature Algorithm: ecdsa-with-SHA256
            30:44:02:20:62:ad:56:8f:42:49:54:c4:f1:9c:e6:c6:e7:fb:
            03:19:83:81:1a:14:33:86:50:b8:b8:47:46:6f:81:3d:a6:90:
            02:20:32:8a:98:b7:1b:c8:4c:52:9a:36:ce:d1:b7:65:8b:36:
            8c:00:2f:f3:16:73:4e:3b:f2:52:98:43:d0:2d:f9:1b

-  3.打开存储发放证书信息的sqlite3 数据库

::

   ☁  fabric-ca  sqlite3 fabric-ca-server.db
   SQLite version 3.19.3 2017-06-27 16:48:08
   Enter ".help" for usage hints.
   sqlite> .tables
   affiliations               properties
   certificates               revocation_authority_info
   credentials                users
   nonces
   sqlite> select *from users;
   admin|$2a$10$1YDii60staOkDsblKUxwXurcnpvFirMUkR2oo09suiR9y5stsp/wm|client||[{"name":"hf.Registrar.DelegateRoles","value":"*"},{"name":"hf.Revoker","value":"1"},{"name":"hf.IntermediateCA","value":"1"},{"name":"hf.GenCRL","value":"1"},{"name":"hf.Registrar.Attributes","value":"*"},{"name":"hf.AffiliationMgr","value":"1"},{"name":"hf.Registrar.Roles","value":"*"}]|0|-1|1
   sqlite> select *from certificates;
   sqlite>

``其中users表中目前只有启动时设定的引导节点，还没有注册其他节点。certificates表中目前没有任何发放证书记录``

-  4.使用fabric-ca先登记引导节点，命令如下：
   这里admin和adminpw即为fabric-ca-server启动时设置的引导节点登记id和密码
   fabric-ca-client 执行结果：

::

   ☁  fabric-ca  fabric-ca-client enroll -u http://admin:adminpw@localhost:7054
   2018/08/08 16:45:53 [INFO] Created a default configuration file at /Users/yunlong/.fabric-ca-client/fabric-ca-client-config.yaml
   2018/08/08 16:45:53 [INFO] generating key: &{A:ecdsa S:256}
   2018/08/08 16:45:53 [INFO] encoded CSR
   2018/08/08 16:45:53 [INFO] Stored client certificate at /Users/yunlong/.fabric-ca-client/msp/signcerts/cert.pem
   2018/08/08 16:45:53 [INFO] Stored root CA certificate at /Users/yunlong/.fabric-ca-client/msp/cacerts/localhost-7054.pem

-  5.server 接收到登记请求：

::

   2018/08/08 16:45:53 [INFO] signed certificate with serial number 260014369527395255236344828754181219899579142314
   2018/08/08 16:45:53 [INFO] 127.0.0.1:61210 POST /enroll 201 0 "OK"

-  6.在ca-client目录下生成的目录结构如下：

::

   ☁  fabric-ca  tree /Users/yunlong/.fabric-ca-client
   /Users/yunlong/.fabric-ca-client
   ├── fabric-ca-client-config.yaml
   └── msp
       ├── cacerts
       │   └── localhost-7054.pem
       ├── keystore
       │   └── 7b4b9b03c83c11081774ad8385765dfca195f0dc04e3a2efae419d9eff570418_sk
       ├── signcerts
       │   └── cert.pem
       └── user

``其中msp/signcerts目录下存储的为client的数字证书，cacerts目录下存储的为证书链信息。``

-  7.打开sqlite3数据库，可以看到刚刚颁发的数字证书已经记录在数据库里了

::

   sqlite> select *from certificates;
   sqlite> select *from certificates;
   admin|2d8b729447b9da894a82943c2c692052e6ad90aa|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08 08:46:00+00:00|0001-01-01 00:00:00+00:00|-----BEGIN CERTIFICATE-----
   MIICODCCAd+gAwIBAgIULYtylEe52olKgpQ8LGkgUuatkKowCgYIKoZIzj0EAwIw
   aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
   EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
   Y2Etc2VydmVyMB4XDTE4MDgwODA4NDEwMFoXDTE5MDgwODA4NDYwMFowXTELMAkG
   A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
   cmxlZGdlcjEPMA0GA1UECxMGY2xpZW50MQ4wDAYDVQQDEwVhZG1pbjBZMBMGByqG
   SM49AgEGCCqGSM49AwEHA0IABAplU7X/AVqTf4+D26GONNPm07Ra5KLTkANuIzIt
   BLjT3sqkQXVGufL7/LdnCfIUVfIx1XP/pKeaG4hz5+QvfoCjcjBwMA4GA1UdDwEB
   /wQEAwIHgDAMBgNVHRMBAf8EAjAAMB0GA1UdDgQWBBT14uqOio2f9ioMw380s7DG
   P1agFTAfBgNVHSMEGDAWgBSPSTpBRsSVugrXXJ8Yz/u57zKIrzAQBgNVHREECTAH
   ggVib2dvbjAKBggqhkjOPQQDAgNHADBEAiAGHoFsb3GRZZyXa8PGofiimQDEIxeU
   jzz9LkoH90gQIAIgEpel1MGL1LE8f3rzX4n8YKAGOlD0NaLv9ArIOiOv/pA=
   -----END CERTIFICATE-----

-  8.接下来使用fabric-ca-client注册一个新的节点，这一步就是为fabric中的peer、user角色进行注册并登记的过程了，注册的命令为：
   ``fabric-ca-client register --id.name admin2 --id.type user --id.affiliation org1.department1 --id.attrs 'hf.Revoker=true,foo=bar'``
   这里有两点需要注意： 
   1.–id.type参数必须是是server端配置文件里hf.Registrar.Roles属性值里的一个
   打开fabric-ca-server的配置文件

::

   125   identities:
   126      - name: admin
   127        pass: adminpw
   128        type: client
   129        affiliation: ""
   130        attrs:
   131           hf.Registrar.Roles: "*"
   132           hf.Registrar.DelegateRoles: "*"
   133           hf.Revoker: true
   134           hf.IntermediateCA: true
   135           hf.GenCRL: true
   136           hf.Registrar.Attributes: "*"
   137           hf.AffiliationMgr: true

``其中通过hf.Registrar.Roles的属性值可以看出，客户端可以注册的类型是"*"代表所有包括( client,user,peer,validator和auditor)``

   2. –id.affiliation这个属性表示：被注册对象的从属关系参数一定要归属于预先配置的组织属性。换句话说，被注册对象的affiliation参数一定要是预先配置的affiliation参数的一个前缀。例如，假设预先配置的组织关系为：“a.b.c”，那么新注册的对象的affiliation属性可以是“a.b.c”，也可以是“a.b”，但“a.c”就不能通过注册。
      执行节点注册命令后，注册了一个user结点：

client端执行结果：

::

   ☁  fabric-ca  fabric-ca-client register --id.name admin2 --id.type user --id.affiliation org1.department1 --id.attrs 'hf.Revoker=true,foo=bar'
   2018/08/08 17:53:35 [INFO] Configuration file location: /Users/yunlong/.fabric-ca-client/fabric-ca-client-config.yaml
   Password: BDQDZMAmkwHE

``这里分配的password需要记住，后面在登记该节点时会用到。``
server端接收到请求:

::

   2018/08/08 17:53:35 [INFO] 127.0.0.1:63853 POST /register 201 0 "OK"

-  9.登记刚刚注册过的admin2节点，登记命令为： sudo fabric-ca-client
   enroll -u http://admin2:BDQDZMAmkwHE@localhost:7054 -M
   $FABRIC_CA_CLIENT_HOME/msp
   这里登记时使用的erollId和密码就是上边注册过程中的节点名称和返回的密码信息。这册过程中也是可以指定密码的。
   执行登记命令，结果如下：

::

   ☁  fabric-ca  sudo fabric-ca-client enroll -u http://admin2:BDQDZMAmkwHE@localhost:7054 -M $FABRIC_CA_CLIENT_HOME/msp
   Password:
   2018/08/08 18:01:47 [INFO] generating key: &{A:ecdsa S:256}
   2018/08/08 18:01:47 [INFO] encoded CSR
   2018/08/08 18:01:47 [INFO] Stored client certificate at /msp/signcerts/cert.pem
   2018/08/08 18:01:47 [INFO] Stored root CA certificate at /msp/cacerts/localhost-7054.pem

``此处sudo 是因为我没有设置环境变量配置目录要创建目录权限``

-  10.打开fabric-ca-server的sqlite数据库

::

   sqlite> select *from users;
   admin|$2a$10$1YDii60staOkDsblKUxwXurcnpvFirMUkR2oo09suiR9y5stsp/wm|client||[{"name":"hf.Registrar.DelegateRoles","value":"*"},{"name":"hf.Revoker","value":"1"},{"name":"hf.IntermediateCA","value":"1"},{"name":"hf.GenCRL","value":"1"},{"name":"hf.Registrar.Attributes","value":"*"},{"name":"hf.AffiliationMgr","value":"1"},{"name":"hf.Registrar.Roles","value":"*"}]|1|-1|1
   admin2|$2a$10$ij5tKwGKPwNh7Pf3FP.WNeht62MVxo7eFHKmxeFb5NxjsjYf0pwjK|user|org1.department1|[{"name":"hf.Revoker","value":"true"},{"name":"foo","value":"bar"},{"name":"hf.EnrollmentID","value":"admin2","ecert":true},{"name":"hf.Type","value":"user","ecert":true},{"name":"hf.Affiliation","value":"org1.department1","ecert":true}]|1|-1|1
   sqlite>

-  11.查看用户信息，其中有一个client节点admin和一个user节点admin2。其中admin是一个intermediateCA。
   打开数据库表certificates，这是可以看到给admin2所颁发的证书已经记录了

::

   sqlite> select *from certificates;
   admin|2d8b729447b9da894a82943c2c692052e6ad90aa|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08 08:46:00+00:00|0001-01-01 00:00:00+00:00|-----BEGIN CERTIFICATE-----
   MIICODCCAd+gAwIBAgIULYtylEe52olKgpQ8LGkgUuatkKowCgYIKoZIzj0EAwIw
   aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
   EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
   Y2Etc2VydmVyMB4XDTE4MDgwODA4NDEwMFoXDTE5MDgwODA4NDYwMFowXTELMAkG
   A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
   cmxlZGdlcjEPMA0GA1UECxMGY2xpZW50MQ4wDAYDVQQDEwVhZG1pbjBZMBMGByqG
   SM49AgEGCCqGSM49AwEHA0IABAplU7X/AVqTf4+D26GONNPm07Ra5KLTkANuIzIt
   BLjT3sqkQXVGufL7/LdnCfIUVfIx1XP/pKeaG4hz5+QvfoCjcjBwMA4GA1UdDwEB
   /wQEAwIHgDAMBgNVHRMBAf8EAjAAMB0GA1UdDgQWBBT14uqOio2f9ioMw380s7DG
   P1agFTAfBgNVHSMEGDAWgBSPSTpBRsSVugrXXJ8Yz/u57zKIrzAQBgNVHREECTAH
   ggVib2dvbjAKBggqhkjOPQQDAgNHADBEAiAGHoFsb3GRZZyXa8PGofiimQDEIxeU
   jzz9LkoH90gQIAIgEpel1MGL1LE8f3rzX4n8YKAGOlD0NaLv9ArIOiOv/pA=
   -----END CERTIFICATE-----
   |1
   admin2|6c8efe0b08916a953fa8a0054ad879c4c48bff63|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08 10:02:00+00:00|0001-01-01 00:00:00+00:00|-----BEGIN CERTIFICATE-----
   MIICwzCCAmqgAwIBAgIUbI7+CwiRapU/qKAFSth5xMSL/2MwCgYIKoZIzj0EAwIw
   aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
   EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
   Y2Etc2VydmVyMB4XDTE4MDgwODA5NTcwMFoXDTE5MDgwODEwMDIwMFowfTELMAkG
   A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
   cmxlZGdlcjEuMAsGA1UECxMEdXNlcjALBgNVBAsTBG9yZzEwEgYDVQQLEwtkZXBh
   cnRtZW50MTEPMA0GA1UEAxMGYWRtaW4yMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcD
   QgAEgTh1/es2IndpNkeqB1rsyGTqIIyyb0s/DxkhaXUWeapU//bagVYCsHAiXSNX
   SXZX/gJNDsHKwWZHxkTuAlqlpqOB3DCB2TAOBgNVHQ8BAf8EBAMCB4AwDAYDVR0T
   AQH/BAIwADAdBgNVHQ4EFgQU2cR+jSPNFV+IwsQKnGnWwdO3GAQwHwYDVR0jBBgw
   FoAUj0k6QUbElboK11yfGM/7ue8yiK8wEAYDVR0RBAkwB4IFYm9nb24wZwYIKgME
   BQYHCAEEW3siYXR0cnMiOnsiaGYuQWZmaWxpYXRpb24iOiJvcmcxLmRlcGFydG1l
   bnQxIiwiaGYuRW5yb2xsbWVudElEIjoiYWRtaW4yIiwiaGYuVHlwZSI6InVzZXIi
   fX0wCgYIKoZIzj0EAwIDRwAwRAIgFhnmmq4qA11tjYDTnRPawjsSBXLFCNGk8OjM
   Hepn66wCIEjE5L/D2MPx/rZGNewlQM6sjiWsVTYsn2+6nsmQ/Cw9
   -----END CERTIFICATE-----
   |1
   sqlite>

-  12重新登记节点，在节点授权即将到期是需要重新登记节点信息。重新登记的命令为：
   ``fabric-ca-client reenroll``

::

   ☁  fabric-ca  fabric-ca-client reenroll
   2018/08/08 18:55:55 [INFO] Configuration file location: /Users/yunlong/.fabric-ca-client/fabric-ca-client-config.yaml
   2018/08/08 18:55:55 [INFO] generating key: &{A:ecdsa S:256}
   2018/08/08 18:55:55 [INFO] encoded CSR
   2018/08/08 18:55:55 [INFO] Stored client certificate at /Users/yunlong/.fabric-ca-client/msp/signcerts/cert.pem
   2018/08/08 18:55:55 [INFO] Stored root CA certificate at /Users/yunlong/.fabric-ca-client/msp/cacerts/localhost-7054.pem

-  13.重新登记过程重新发送签名证书请求，获取新的数字证书。
   打开server端数据库，可以看到两条颁发给节点admin的证书记录，两个证书颁发的时间不一样。

::

   sqlite> select \*from certificates;
   admin|2d8b729447b9da894a82943c2c692052e6ad90aa|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08
   08:46:00+00:00|0001-01-01 00:00:00+00:00|—–BEGIN CERTIFICATE—–
   MIICODCCAd+gAwIBAgIULYtylEe52olKgpQ8LGkgUuatkKowCgYIKoZIzj0EAwIw
   aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
   EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
   Y2Etc2VydmVyMB4XDTE4MDgwODA4NDEwMFoXDTE5MDgwODA4NDYwMFowXTELMAkG
   A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
   cmxlZGdlcjEPMA0GA1UECxMGY2xpZW50MQ4wDAYDVQQDEwVhZG1pbjBZMBMGByqG
   SM49AgEGCCqGSM49AwEHA0IABAplU7X/AVqTf4+D26GONNPm07Ra5KLTkANuIzIt
   BLjT3sqkQXVGufL7/LdnCfIUVfIx1XP/pKeaG4hz5+QvfoCjcjBwMA4GA1UdDwEB
   /wQEAwIHgDAMBgNVHRMBAf8EAjAAMB0GA1UdDgQWBBT14uqOio2f9ioMw380s7DG
   P1agFTAfBgNVHSMEGDAWgBSPSTpBRsSVugrXXJ8Yz/u57zKIrzAQBgNVHREECTAH
   ggVib2dvbjAKBggqhkjOPQQDAgNHADBEAiAGHoFsb3GRZZyXa8PGofiimQDEIxeU
   jzz9LkoH90gQIAIgEpel1MGL1LE8f3rzX4n8YKAGOlD0NaLv9ArIOiOv/pA= —–END
   CERTIFICATE—– \|1
   admin2|6c8efe0b08916a953fa8a0054ad879c4c48bff63|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08
   10:02:00+00:00|0001-01-01 00:00:00+00:00|—–BEGIN CERTIFICATE—–
   MIICwzCCAmqgAwIBAgIUbI7+CwiRapU/qKAFSth5xMSL/2MwCgYIKoZIzj0EAwIw
   aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
   EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
   Y2Etc2VydmVyMB4XDTE4MDgwODA5NTcwMFoXDTE5MDgwODEwMDIwMFowfTELMAkG
   A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
   cmxlZGdlcjEuMAsGA1UECxMEdXNlcjALBgNVBAsTBG9yZzEwEgYDVQQLEwtkZXBh
   cnRtZW50MTEPMA0GA1UEAxMGYWRtaW4yMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcD
   QgAEgTh1/es2IndpNkeqB1rsyGTqIIyyb0s/DxkhaXUWeapU//bagVYCsHAiXSNX
   SXZX/gJNDsHKwWZHxkTuAlqlpqOB3DCB2TAOBgNVHQ8BAf8EBAMCB4AwDAYDVR0T
   AQH/BAIwADAdBgNVHQ4EFgQU2cR+jSPNFV+IwsQKnGnWwdO3GAQwHwYDVR0jBBgw
   FoAUj0k6QUbElboK11yfGM/7ue8yiK8wEAYDVR0RBAkwB4IFYm9nb24wZwYIKgME
   BQYHCAEEW3siYXR0cnMiOnsiaGYuQWZmaWxpYXRpb24iOiJvcmcxLmRlcGFydG1l
   bnQxIiwiaGYuRW5yb2xsbWVudElEIjoiYWRtaW4yIiwiaGYuVHlwZSI6InVzZXIi
   fX0wCgYIKoZIzj0EAwIDRwAwRAIgFhnmmq4qA11tjYDTnRPawjsSBXLFCNGk8OjM
   Hepn66wCIEjE5L/D2MPx/rZGNewlQM6sjiWsVTYsn2+6nsmQ/Cw9 —–END
   CERTIFICATE—– \|1
   admin|740964b0033cfcf83b3df9c8501bbf2e2dc9e553|8f493a4146c495ba0ad75c9f18cffbb9ef3288af||good|0|2019-08-08
10:56:00+00:00|0001-01-01 00:00:00+00:00|—–BEGIN CERTIFICATE—–
MIICODCCAd+gAwIBAgIUdAlksAM8/Pg7PfnIUBu/Li3J5VMwCgYIKoZIzj0EAwIw
aDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQK
EwtIeXBlcmxlZGdlcjEPMA0GA1UECxMGRmFicmljMRkwFwYDVQQDExBmYWJyaWMt
Y2Etc2VydmVyMB4XDTE4MDgwODEwNTEwMFoXDTE5MDgwODEwNTYwMFowXTELMAkG
A1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMRQwEgYDVQQKEwtIeXBl
cmxlZGdlcjEPMA0GA1UECxMGY2xpZW50MQ4wDAYDVQQDEwVhZG1pbjBZMBMGByqG
SM49AgEGCCqGSM49AwEHA0IABKmzuxWJF7ktC+rJe88IotUSzcJ4M2S3aDfW95cB
AjskynJyI5wL7aRnKbbabqGw6oecTtE80o3mDrwuCkZbhrejcjBwMA4GA1UdDwEB
/wQEAwIHgDAMBgNVHRMBAf8EAjAAMB0GA1UdDgQWBBSx1Pn7YNSyFUwMtKgi4YL7
6b4epDAfBgNVHSMEGDAWgBSPSTpBRsSVugrXXJ8Yz/u57zKIrzAQBgNVHREECTAH
ggVib2dvbjAKBggqhkjOPQQDAgNHADBEAiA/un/UESFnG5JsDxguPG3sqTwMGUDb
hVmv95HYkEgkJAIgBrWmP3W3x9VU2cqgp+859ZaqpVb5FJFeRhNl/WTpjJw= —–END
CERTIFICATE—– \|1 sqlite> 