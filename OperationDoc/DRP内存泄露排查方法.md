# 内存泄露问题测定

`采集有给院方开放接口的，请忽略该排查方法`

`目前和对接人员张玉刚沟通只有武汉协和医院存在这种情况`

命令查询采集是否有单独的容器运行
```Bash
docker ps | grep tomcatcollect
```
- [ ] 表示存在内存溢出的风险，需要执行解决办法
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/nonTomcatCollect.png)
- [x] 表示已经通过排查
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/ExistTomcatCollect.png)

---

# 解决办法

(**脚本中ip地址请替换成项目上的应用服务器ip**)

1. 新建tomcatcollect镜像
```Bash
docker run -d -m 8G -p 8098:8080 --log-driver=none -v /var/log/dripping/tomcatcollect:/usr/local/tomcat/logs -e config_url=192.168.40.126:2222 -e profile=dev -l company=ewell -l product=dripping --name tomcatcollect tomcat:ewell.20171026
```
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectContainer.png)

> **小伙伴请注意，ip一定要记得改，注意下面红色框选区域**

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/ipError.png)

2. 从原tomcat镜像中复制collect程序包
```Bash
mkdir collect-change;for var in $(docker exec tomcat find /usr/local/tomcat/webapps -name '*collect.war');do docker cp tomcat:$var collect-change;done;ls collect-change
```
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/findCollectWar.png)

3. 将collect程序包复制到tomcatcollect镜像中
```Bash
for var in $(find collect-change/ -type f);do docker cp $var tomcatcollect:/usr/local/tomcat/webapps;sleep 10;done
```

4. 访问tomcatcollect的包管理网址,确认collect程序包正常运行(识别红框部分均是true)

        http://[应用服务器ip]:8098/manager/html
        登录信息从部署文档中获取
  ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectStart.png)
5. 访问tomcat的包管理网址，删除采集的运行包

        http://[应用服务器ip]:8080/manager/html
        登录信息从部署文档中获取
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/undeploy.png)
---

# 处理后的服务提升
（实例来自山西儿童)

处理前状况

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectBeforeCpu.png)

处理后状况

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectAfterCpu.png)

处理思路分析

  采集是时时进行业务同步的服务，对cpu资源的使用有要求，有许多循环确认字段和数据的。采集和其他服务放置在一个tomcat里面，资源的竞争更加激烈，cpu使用优先者不断变更。更多更频繁的资源申请和顺序等待，导致cpu飙高。

  ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/tomcatlog.png)
  ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectLog.png)

  日志上看，单日输出日志多的记录来自采集和baseinfo，说明业务十分依赖时时同步。从日志内容中可看出，单位时间内业务循环可达1毫秒3次之多。采集业务对资源的要求，无法通过业务调整去平衡，将其单独放置在一个环境中，不仅可以优化采集的资源使用，也可以避免因采集对资源占用，导致正常平台业务响应不了的风险



---
# 问题反馈和处理方法
+ 没有原始镜像

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/missImage.png)

**处理方法**

  项目是早期上线的，基础镜像有更新

  新的基础镜像网盘地址

`链接：https://pan.baidu.com/s/1tKYMUgYOsM5701PHK-rhYw 密码：7ul1`

  获取到包的处理方法
  1. 上传包到服务器上

  可以使用winscp工具,使用方法可参照部署文档中的描述或网络百度

  2. 使用命令载入包

  命令定位到包所在的目录，执行命令
  ```Bash
  docker load -i tomcat-ewell.20171026.tar
  ```
![images](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/loadTomcat.png)
+ docker命令操作权限不足

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/missSudo.png)

**处理方法**

 1. 使用下面的命令提升当前用户权限
  ```Bash
  sudo usermod -aG docker $(whoami)
  ```
  ![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/grantDocker.png)

 2. 退出当前登录
 3. 登录后，重新按文档执行

+ docker命令执行警告

![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/warnDeny.png)

**处理方法**

  从执行结果上看是成功了的，因此遇到这个问题按文档继续执行就好了
