# rabbitMq

## 安装

1. yum -y install make gcc gcc-c++ kernel-devel m4 ncurses-devel openssl-devel unixODBC-devel
2. wget http://www.rabbitmq.com/releases/erlang/erlang-19.0.4-1.el7.centos.x86_64.rpm
3. yum install socat
4. 下载rabbitMq rpm (wget wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.5/rabbitmq-server-3.6.5-1.noarch.rpm)
5. vim /usr/lib/rabbitmq/lib/rabbitmq_server-3.7.9/ebin/rabbit.app   设置用户释放guest数组
6. rabbitmq-server start &  启动rabbitmq
7. rabbitmq-server stop 停止服务
8. rabbitmq-plugins enable rabbitmq_management   启动管理台插件
9. http://192.168.16.128:15672   访问web页面

