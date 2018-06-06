# 内存泄露问题测定
命令查询采集是否有单独的容器运行
```Bash
docker ps | grep tomcatcollect
```
- [ ] 表示存在内存溢出的风险
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/nonTomcatCollect.png)
- [x] 表示已经经过排查
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/ExistTomcatCollect.png)
---
# 解决办法
(**脚本中ip地址请替换成项目上的应用服务器ip**)
1. 新建tomcatcollect镜像
```Bash
docker run -d -m 8G -p 8098:8080 --log-driver=none -v /var/log/dripping/tomcatcollect:/usr/local/tomcat/logs -e config_url=192.168.40.126:2222 -e profile=dev -l company=ewell -l product=dripping --name tomcatcollect tomcat:ewell.20171026
```
![image](https://raw.githubusercontent.com/Stromy-worker/EwellDrpDoc/master/Resource/pic/collectContainer.png)
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
