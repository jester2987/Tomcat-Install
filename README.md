# Apache Tomcat9 Install
By Command Linux Ubuntu 20.04 LTS

## Start
1.You must have Java installed on your system to run tomcat server. Tomcat 9 required to have Java 8 or higher version installed on your system.
~~~
$ sudo apt install openjdk-11-jdk
~~~
(1.2)Check version Java Commmand
~~~
$ java –version
~~~
2.Download Tomcat Archive
~~~
$ wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.40/bin/apache-tomcat-9.0.40.tar.gz
~~~
3.extracted downloaded archive and copy all content to tomcat home directory.
~~~
$ tar xzf apache-tomcat-9.0.40.tar.gz
$ sudo mv apache-tomcat-9.0.40/* /opt/tomcat/
~~~
4.Create manager xml file
~~~
$ sudo vi /opt/tomcat/conf/Catalina/localhost/manager.xml
~~~
(4.1)Add the following content
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="{catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
5.Then create host-manager xml file
~~~
$ sudo vi /opt/tomcat/conf/Catalina/localhost/host-manager.xml
~~~
(5.1)Add the following content
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
6.Create A Tomcat Startup Script
~~~
$ sudo vi /etc/systemd/system/tomcat.service
~~~
(6.1)Add the following content
~~~
[Unit]
Description=Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
~~~
7.Change ownership of all files in tomcat directory for used tomcat.service
~~~
$ sudo chown -R tomcat:tomcat /opt/tomcat/
~~~
8.Reload the systemd daemon service to apply changes
~~~
$ sudo systemctl daemon-reload
~~~
9.Enable and start Tomcat service on your system
~~~
$ sudo systemctl enable tomcat
$ sudo systemctl start tomcat
~~~
