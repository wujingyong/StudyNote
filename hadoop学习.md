# hadoop学习（尚硅谷）

## hadoop命令

> 1. `创建目录` hadoop fs mkdir -p /user/wjy
> 2. `查看文件列表` hadoop fs -ls -R /
> 3. `上传文件` hadoop fs -put /原文件 /目标路径
> 4. `查看文件内容` hadoop fs -cat /xxx
> 5. `删除文件/文件夹` hadoop fs -rm(-rm -r) /xxx

## 需要用到的端口

```text
hadoop web 50070
yarn web 8088
yarn 日志查看 8042
SecondaryNameNode  50090
# 启动docker
docker run -d -it -p 50070:50070 -p 8088:8088 -p 8042:8042 --privileged=true --name hadoop01 -h hadoop011 [imageid] /usr/sbin/init
```



## 用docker创建hadoop镜像（centos基础镜像）

> 1. 配置jdk环境
>
> 2. 配置hadoop环境 `bin + sbin`
>
> 3. 指定hadoop下 `etc/hadoop/hadoop-env.sh`文件的`JAVA_HOME（也可采用默认配置，系统配置的JAVA_HOME）`
>
> 4. 测试wordCount 
>
>    > 1. 新建一个文件存放单词
>    >
>    > 2. 运行自带测试 
>    >
>    >    ```text
>    >    hadoop jar /opt/module/hadoop-2.7.2/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount ./wordcount wcoutput
>    >    ```
>    >
>    > 3. 在wcoutput文件夹下查看结果

## 搭建hadoop伪分布式

### 配置hdfs

> 1. 更改`hadoop/etc/hadoop/`下的配置文件
>
>    > 1. core-site.xml  添加配置
>    >
>    > ```xml
>    > <!-- 指定HDFS中NameNode地址 -->
>    > <property>
>    >     <name>fs.defaultFS</name>
>    >     <value>hdfs://127.0.0.1:8020</value>
>    > </property>
>    > <!-- 指定hadoop运行时产生文件的存储地址 -->
>    > <property>
>    >     <name>hadoop.tmp.dir</name>
>    >     <value>/opt/module/hadoop-2.7.2/data/tmp</value>
>    > </property>
>    > ```
>    >
>    > 2. hdfs-site.xml 添加配置
>    >
>    > ```xml
>    > <!-- 指定HDFS副本数量 -->
>    > <property>
>    >     <name>dfs.replication</name>
>    >     <value>1</value>
>    > </property>
>    > ```
>
> 2. 启动
>
>    > 1. 格式化nameNode（第一次启动才需格式化）
>    >
>    >    ```text
>    >    hdfs namenode -format
>    >    ```
>    >
>    > 2. 启动nameNode
>    >
>    >    ```text
>    >    hadoop-daemon.sh start namenode
>    >    ```
>    >
>    > 3. 启动dataNode
>    >
>    >    ```text
>    >    hadoop-daemon.sh start datanode
>    >    ```
>    >
>    > 4. 查看web界面
>    >
>    >    ```text
>    >    ip:50070
>    >    ```
>    >
>    > 5. wordcount测试`同hadoop镜像的wordcount测试`
>    >
>

### 配置yarn

> 1. 更改`hadoop/etc/hadoop/`下的配置文件
>
>    > 1. yarn-env.sh  配置JAVA_HOME
>    >
>    > 2. mapred-env.sh  配置JAVA_HOME
>    >
>    > 3. 配置yarn-site.xml
>    >
>    >    ```xml
>    >    <!-- reducer获取数据的方式 -->
>    >    <property>
>    >        <name>yarn.nodemanager.aux-services</name>
>    >        <value>mapreduce_shuffle</value>
>    >    </property>
>    >    <!-- 指定yarn的ResourceManager地址 -->
>    >    <property>
>    >        <name>yarn.resourcemanager.hostname</name>
>    >        <!-- 本机ip地址（不能为127.0.0.1，否则打不开web页面） -->
>    >        <value>xxx</value>
>    >    </property>
>    >    ```
>    >
>    > 4. 配置 `mapred-site.xml ` 复制一份 `mapred-site.xml.template`  为 ` mapred-site.xml`
>    >
>    >    ```xml
>    >    <!-- 指定mr运行在yarn上 -->
>    >    <property>
>    >        <name>mapreduce.framework.name</name>
>    >        <value>yarn</value>
>    >    </property>
>    >    ```
>
>    2. 启动
>
>    > 1. 启动`namenode、datanode` 然后启动yarn
>    >
>    >    ```text
>    >    hdfs namenode -format （初始化时执行、第二次执行要先删除datanode的数据）
>    >    hadoop-daemon.sh start namenode
>    >    hadoop-daemon.sh start namenode
>    >    yarn-daemon.sh start resourcemanager
>    >    yarn-daemon.sh start nodemanager
>    >    ```
>    >
>    > 2. 查看web界面
>    >
>    >    ```text
>    >    ip:50070
>    >    ip:8088
>    >    ```
>    >
>    > 3. wordcount测试`同hadoop镜像的wordcount测试`

## 搭建完全分布式

### 集群分配

```text
hadoop01		hadoop02			hadoop03
HDFS
namenode							SecondaryNameNode
datanode		datanode			datanode
YARN
nodemanager		resourcemanager		nodemanager
```

### 配置ssh免密

> #### docker中的额外步骤
>
> 1. `启动docker` 
>
>    ```text
>    docker run -d -it -p 50070:50070 -p 8088:8088 -p 8042:8042 --privileged=true --name hadoop01 [imageid] /usr/sbin/init
>    # docker run -d -it -p 50071:50070 -p 8089:8088 -p 8043:8042 --privileged=true --name hadoop02 [imageid] /usr/sbin/init
>    # docker run -d -it -p 50072:50070 -p 8090:8088 -p 8044:8042 --privileged=true --name hadoop03 [imageid] /usr/sbin/init
>    ```
>
> 2. `配置hosts`
>
>    ```text
>    source /etc/profile
>    echo "172.17.0.2      hadoop01
>    172.17.0.3      hadoop02
>    172.17.0.4      hadoop03" >> /etc/hosts
>    
>    ```
>
> 3. `给docker容器添加root密码 ` sudo passwd root 
>
> 4. `安装sshd并设置开机自启动` 
>
>    ```text
>    yum install openssh-server -y
>    yum install openssh-clients -y
>    systemctl start sshd
>    systemctl enable sshd
>    ```
>
> #### 直接linux服务器
>
> 1. `生成免密登录密钥` ssh-keygen -t rsa
> 2. `将公钥放入免密的机器上` ssh-copy-id hadoop01

### 配置hadoop集群（根据集群分配进行搭建）

> ### 1. 配置hdfs
>
> > 1. `hadoop-env.sh` 配置jdk
> >
> > 2. `core-site.xml`  添加配置
> >
> >    ```xml
> >    <!-- 指定HDFS中NameNode地址 -->
> >    <property>
> >        <name>fs.defaultFS</name>
> >        <value>hdfs://hadoop01:8020</value>
> >    </property>
> >    <!-- 指定hadoop运行时产生文件的存储地址 -->
> >    <property>
> >        <name>hadoop.tmp.dir</name>
> >        <value>/opt/module/hadoop-2.7.2/data/tmp</value>
> >    </property>
> >    ```
> >
> > 3. `hdfs-site.xml` 添加配置
> >
> >    ```xml
> >    <!-- 指定HDFS副本数量 默认3可以不配 -->
> >    <property>
> >        <name>dfs.replication</name>
> >        <value>3</value>
> >    </property>
> >    <!-- secondaryNameNode所在节点 -->
> >    <property>
> >        <name>dfs.namenode.secondary.http-address</name>
> >        <value>hadoop03:50090</value>
> >    </property>
> >    ```
> >
> > 4. `配置slaves(3个datanode)`
> >
> >    ```text
> >    hadoop01
> >    hadoop02
> >    hadoop03
> >    ```
>
> ### 2. 配置yarn
>
> >1. `yarn-env.sh` 配置jdk 
> >
> >2. `yarn-site.xml` 
> >
> >   ```xml
> >   <!-- reducer获取数据的方式 -->
> >   <property>
> >       <name>yarn.nodemanager.aux-services</name>
> >       <value>mapreduce_shuffle</value>
> >   </property>
> >   <!-- 指定yarn的ResourceManager地址 -->
> >   <property>
> >       <name>yarn.resourcemanager.hostname</name>
> >       <value>hadoop02</value>
> >   </property>
> >   ```
>
> ### 3. 配置mapreduce
>
> > 1. `mapred-env.sh` 配置jdk
> >
> > 2. 配置 `mapred-site.xml ` 复制一份 `mapred-site.xml.template`  为 ` mapred-site.xml`
> >
> >    ```xml
> >    <!-- 指定mr运行在yarn上 -->
> >    <property>
> >        <name>mapreduce.framework.name</name>
> >        <value>yarn</value>
> >    </property>
> >    ```
>
> ### 4. 启动
>
> > 1. 启动
> >
> >    ```text
> >    hdfs namenode -format （初始化时执行、第二次执行要先删除datanode的数据）
> >    start-dfs.sh  在hadoop01中启动dfs（coresite.xml中配置的地址）
> >    start-yarn.sh  在hadoop02中启动yarn（yarnsite.xml中配置的地址）
> >    ```
> >
> > 2. 用jps验证各节点启动情况