确认方法适用于<4.4移动护理统合医惠护士平台合一部署文档.docx>部署的应用服务器

其他部署版本可以参考这个文档做确认

# 检查应用服务器运行状态
1. 应用服务器是否在同局域网

        ping [应用服务器ip]
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/pingServer.png)
1. 应用服务器远程服务ssh是否有开启
<br>**(需要操作的机器开启telnet功能)**

        telnet [应用服务器ip] 22
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/telnetBefore.png)

    输入命令回车，界面返回
    <br>**(使用Ctrl + ]退出到telnet，再输入quit退出telnet)**

    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/telnetAfter.png)
1. 应用服务器可以通过远程管理工具连通

    打开putty,输入登录信息尝试登录

    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/puttyInfo.png)

    输入用户名和密码
    <br>** *(密码输入是不显示在界面上的, 输好再按回车)* **

    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/puttyLogin.png)

    登录成功提示

    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/puttyOk.png)

# 检查应用服务器服务运行状态
1. 命令查询服务状态
    ```Bash
    docker ps -a | grep 'redis\|registry\|httpd\|activemq\|config\|platformWEB\|tomcat\|uploader\|tomcatcollect\|mqttmsg'
    ```
    **红框**部分标记出服务的运行状态
    <br>**紫框**部分标记出服务名字和服务id
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerPsDrp.png)

1. 处理服务运行异常

    确定**服务名字或服务id**,使用命令重启服务
    ```Bash
    docker restart [服务id或服务名]
    ```
    服务id输出表示命令已经执行
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerRestart.png)

# 检查应用服务器医惠平台运行状态
1. 访问tomcat管理网址确定平台服务运行状态
    > tomcat管理网址:** _http://[应用服务器ip]:8080/manager/html_ **

    登录管理网址
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatLogin.png)
    查看服务运行状态，**红框**
    标记出服务的运行状态
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatPs.png)
1. 处理异常状态

    异常时，请在command区域尝试重新启动
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatCommand.png)
    重新启动尝试失败，请参照下表，从服务器获取日志

    |服务名称|日志位置|日志包含关键字|
    |---|:---|---|
    |auth|/var/log/dripping/tomcat|authority|
    |baseinfo2.0|/var/log/dripping/tomcat||
    |console|/var/log/dripping/tomcat|console|
    |device|/var/log/dripping/tomcat|device|
    |eureka|/var/log/dripping/tomcat|discovery|
    |order|/var/log/dripping/tomcat|order|
    |right|/var/log/dripping/tomcat|right|
    |sys|/var/log/dripping/tomcat|sys|
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatLogDir.png)

# 检查应用服务器采集服务运行状态
**[内存泄露排查方法](https://github.com/Stromy-worker/EwellDrpDoc/blob/master/OperationDoc/DRP%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5%E6%96%B9%E6%B3%95.md)执行过的项目,可以做这个检查**
1. 访问采集的tomcat管理网址确定服务运行状态
    > tomcat管理网址:** _http://[应用服务器ip]:8098/manager/html_ **

    *确认方法和上面tomcat检查方法一致*
1. 启动异常如何获取日志

    尝试重新启动,启动失效请在下面路径中获取日志
        /var/log/dripping/tomcatcollect  #目录下文件名中带collect关键字的文件
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatcollectLogDir.png)

# 检查微应用运行状态
1. 命令查询微应用服务运行状态

    ```Bash
    docker service ls
    ```
    每行表示为一个运行服务的任务，注意**红框**标注列REPlICAS，/分隔的两个数字，前面一个表示运行的实例个数，后面一个表示任务实例的目标数量
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerServiceLs.png)

1. 如何确定微应用服务异常运行状态
    > REPlICAS列，两个数字不相等表示运行服务的任务运行异常

    从查询中获取到任务的id(紫色框选部分)，使用命令查询任务的发布状态

    ```Bash
    docker service ps [运行服务任务的id]
    ```
    **红框** 框异常原因
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerServiceException.png)
1. 微应用服务日志获取

    通过运行服务任务获取到镜像名称
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerServiceImage.png)
    通过镜像名称查询到运行服务的容器信息
    ```Bash
    docker ps | grep 'CONTAINER ID';docker ps -a | grep [运行服务任务中查询到的镜像名称]
    ```
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerPsImage.png)
    从容器信息中找到id信息，并使用命令查看/导出日志
    * 查看日志

    ```Bash
    docker logs -f [查询到的容器信息中的id]
    ```
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerLog.png)
    * 导出日志

    ```Bash
    docker logs [查询到的容器信息中的id] > [服务器上文件的绝对路径]
    ```
    ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/dockerLogFile.png)

---

---

# 登录问题分析方法
分析思路:   确认医惠平台服务运行正常 》 确认通过接口登录正常
1. 通过 [检查应用服务器医惠平台运行状态](#检查应用服务器医惠平台运行状态) 检查

1. 确认organcode设置
    1. 确认数据库的organcode

        **oauth数据库T_SYS_USER表的organcode<br>
        userdb数据库T_SYS_USER表的organcode**

        确认红框部分的*organCode*是否正确，第一个框是**organcode-loginname**的组全，第二个框是**organcode**

        ![image](./../Resource/pic/databaseOrgancode.png)

    1. 打开服务配置文件，确认配置文件中的organcode

        找到环境变量EWELL_ENV设置的路径,打开dripping文件夹，查看配置文件

        ![image](./../Resource/pic/ewellEnv.png)

        >auth-service-dev.properties

        检索定位到关键字organCode位置，确认organcode

        ![image](./../Resource/pic/authConfOrgancode.png)

        >sys-service-dev.properties

        检索定位到关键字organCode位置，确认organcode

        ![image](./../Resource/pic/sysConfOrgancode.png)

1. 访问登录验证接口，验证登录接口
    1. 访问Auth服务api swagger地址，按框选打开接口页面
        > http://[应用服务器]:8080/auth/swagger-ui.html

        ![image](./../Resource/pic/authLoginUi.png)
    1. 尝试进行登录(**注意organCode和密码的填写，密码填写需要写加密的密文**)
        > |明文|加密方式|密文|
        > |---|:---|---|
        > |ewell|md5|390b18c9fc21ffbf|
        > |123456|md5|5b4ae7428c86cc1c|

        使用admin用户验证医惠护士账户<br>
        使用dba用户验证移动护理账户

        ![image](./../Resource/pic/authLoginTest.png)

    **结果不符合预期，按[检查应用服务器医惠平台运行状态](#检查应用服务器医惠平台运行状态)提供auth的日志**

# 登录问题案例
### 关键字  用户名或密码错误-IncorrectResultSizeDataAccessException

![image](./../Resource/pic/authIncorrectResultSize.png)

使用登录问题分析方法没有找到原因，联系登录开发人员确定问题原因
OAUTH_ACCESS_TOKEN中同一个用户存在两条记录，删除这两条记录，重新登录尝试，问题修复成功

![image](./../Resource/pic/databaseOauthAccessToken.png)

---
### 关键字 internalAutheticationServiceException  dripping.userInfoUrl

![image](./../Resource/pic/internalAutheticationServiceException.png)

说明和解决方法看下图

![image](./../Resource/pic/authConfFirstLine.png)
