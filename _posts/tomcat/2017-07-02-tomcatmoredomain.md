---
layout: blog
category: tomcat
title: linux环境tomcat多域名指向多项目server xml配置
background-image: http://s.qdcdn.com/cl/13085757,800,450.jpg
date: 2017-07-02 23:04:19
tags:
- tomcat
- tomcat多域名
- serverxml
- EC2
- linux
- java
---

```
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>
      <Host name="www.edujoy.com.cn"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="edujoy_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
	<Context docBase="//root/tomcat/webapps/ROOT/support" path="/support" reloadable="true" />
      </Host>
      <Host name="kefuzhongxin" appBase="/data/support" unpackWARs="true"
	    autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="kefuzhongxin_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
      <!-- 域名 tangtest.edujoy.com.cn -->
            <Host name="tangtest.edujoy.com.cn"  appBase="tangtest"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="tangtest_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
	<Context docBase="//root/tomcat/tangtest/ROOT/" path="" reloadable="true" />
      </Host>
        <!-- 域名  learntest.edujoy.com.cn/-->
              <Host name="learntest.edujoy.com.cn"  appBase="learn"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="learntest_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
	<Context docBase="//root/tomcat/learn/" path="" reloadable="true" />
    </Engine>
  </Service>
</Server>

```