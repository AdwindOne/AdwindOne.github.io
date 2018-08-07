Fabric 1.2 BlockChain Explorer
==============================
 部署环境：

::

   fabric1.1
   macOS 10.13.4
   git 2.15.2
   node v8.11.3
   npm v6.1

1.配置fabric 基础环境（略）
^^^^^^^^^^^^^^^^^^^^^^^

2.启动网络
^^^^^^^^^^

::

   cd /Users/yunlong/go/src/github.com/hyperledger/fabric-samples/first-network
   ./byfn.sh -m up

..

   启动成功得样子

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-4e6f596dc6d8822d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 1.png

   1.png

3.找到fabric 配置文件路径（复制）
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-a2ed8d6f6b1f2e32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 2.png

   2.png

::

   本机:/Users/yunlong/go/src/github.com/hyperledger/fabric-samples/first-network/crypto-config

4.克隆源码，进入源码目录下

::

   https://github.com/hyperledger/blockchain-explorer.git
   cd blockchain-explorer

5.安装postgreSQL 数据库

::

   brew install PostgreSQL
   配置环境变量

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-81b2f0b254c1e7e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 3.png

   3.png

6.链接数据库

::

   psql postgres

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-d2f9159cdc1c2f87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 4.png

   4.png

7.执行创建数据库

::

   \i app/persistance/postgreSQL/db/explorerpg.sql
   \i app/persistance/postgreSQL/db/updatepg.sql

8.查看创建数据库

::

   \l view created fabricexplorer database
   \d view created tables

9.配置数据库

 文件路径如图所示（app/persistance/postgreSQL/db/pgconfig.json）

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-1459356a7e89e40d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 5.png

   5.png

(注意：可先命令行测试链接数据库是否成功，psql
postgres://hppoc:password@127.0.0.1:5432/fabricexplorer)

10.将图中标出路径本文件全部替换为 第3步 的路径
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-f80690e37361391f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 6.png

   6.png

11.另起来一个终端 build hyperledger
explorer（部分不成功，可全局代理）

::

     1. cd blockchain-explorer
     2. npm install
     3. cd blockchain-explorer/app/test
     4. npm install
     5. npm run test
     6. cd client/
     7. npm install
     8. npm test -- -u --coverage
     9. npm run build

12.启动区块浏览器

::

   cd  blockchain-explorer
   node main.js

.. figure:: https://upload-images.jianshu.io/upload_images/10417784-5e97531a8aa0a64f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650
   :alt: 7.png

   7.png

13.打开chrome 访问：http://localhost:8080/ |8.png|

.. |8.png| image:: https://upload-images.jianshu.io/upload_images/10417784-acfe1d36d9b34010.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/650