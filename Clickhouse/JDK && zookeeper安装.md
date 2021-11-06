#### 第一部分：安装JDK环境

- sudo apt-get update

- sudo apt-get install openjdk-8-jdk

- 编辑etc/profile 文件，配置JAVA_HOME， 执行 source /etc/profile

```bash
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "`id -u`" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/sbin:/usr/sbin"
fi
export PATH

if [ "$PS1" ]; then
  if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi

# 增加下面两行
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
tty | egrep -q tty[1-6] && export LC_ALL=C

```

#### 第二部分：安装 zookeeper

- wget https://dlcdn.apache.org/zookeeper/zookeeper-3.5.9/apache-zookeeper-3.5.9-bin.tar.gz

- tar -xzvf apache-zookeeper-3.5.9-bin.tar.gz

- mv apache-zookeeper-3.5.9-bin /usr/local/zookeeper

- cp conf/zoo_sample.cfg conf/zoo.cfg

```json
tickTime=2000
dataDir=/var/zookeeper/data
dataLogDir=/var/zookeeper/log
clientPort=2181
initLimit=5
syncLimit=2
maxClientCnxns=60
```


##### 启动 zookeeper 服务 

- cd /usr/local/zookeeper/bin

- ./zkServer.sh start


##### 测试ZooKeeper服务

- ./zkCli.sh