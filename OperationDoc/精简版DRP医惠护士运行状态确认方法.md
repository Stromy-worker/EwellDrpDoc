确认方法适用于<DRP精简版本从0开始v1.1.docx>部署的应用服务器

# 检查应用服务器运行状态
1. 应用服务器是否在同局域网

        ping [应用服务器ip]
    ![image](./../Resource/pic/pingServer.png)

# 检查应用服务器医惠平台运行状态

1. 访问tomcat管理网址确定平台服务运行状态
    > tomcat管理网址:** _http://[应用服务器ip]:8080/manager/html_ **

    登录管理网址
    ![image](./../Resource/pic/tomcatLogin.png)
    查看服务运行状态，**红框**
    标记出服务的运行状态
    ![image](./../Resource/pic/tomcatPsSimple.png)
1. 处理异常状态

    异常时，请在command区域尝试重新启动
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatCommand.png)
    重新启动尝试失败，请参照下表，从服务器获取日志

    |服务名称|日志位置|日志包含关键字|
    |---|:---|---|
    |auth|[tomcat安装目录]\bin\logs|authority|
    |baseinfo2.0|[tomcat安装目录]\bin\logs|baseinfo|
    |eureka|[tomcat安装目录]\bin\logs|discovery|
    |sys|[tomcat安装目录]\bin\logs|sys|
    |configCollect|[tomcat安装目录]\bin\logs|configCollect|

    ![image](./../Resource/pic/tomcatLogDirSimple.png)

# 检查应用服务器采集服务运行状态

1. 访问tomcat管理网址确定平台服务运行状态
    > tomcat管理网址:** _http://[应用服务器ip]:8080/manager/html_ **

    *确认方法和上面tomcat检查方法一致*

1. 启动异常如何获取日志

    *获取日志位置和上面医惠平台运行状态一致*

    |服务名称|日志位置|日志包含关键字|
    |---|:---|---|
    |configCollect|[tomcat安装目录]\bin\logs|configCollect|

---

---

# 登录问题分析方法
# 登录问题案例
