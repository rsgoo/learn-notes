1：从Oracle官网中获取jdk安装包。

官网地址：https://www.oracle.com/cn/java/technologies/downloads/

2：选择版本并下载

- 这里选择 https://download.oracle.com/java/23/latest/jdk-23_linux-x64_bin.tar.gz

3：解压jdk.*.tar.gz

> tar zxvf jdk-23_linux-x64_bin.tar.gz

4：添加到环境变量(系统级和用户级可选其一)

- 备注：/home/user/jdk-23 为解压tar.gz 后的目录

4-1：系统级 **vim /etc/profile**

```
export JAVA_HOME=/home/user/jdk-23
export PATH=$JAVA_HOME/bin:$PATH
```
执行 `source /etc/profile`

4-2：用户级 **vim ~/.bashrc**

```
export JAVA_HOME=/home/user/jdk-23
export PATH=$JAVA_HOME/bin:$PATH
```
执行 `source ~/.bashrc`

5：验证安装

```
> java -version
java version "23" 2024-09-17
Java(TM) SE Runtime Environment (build 23+37-2369)
Java HotSpot(TM) 64-Bit Server VM (build 23+37-2369, mixed mode, sharing)
```
