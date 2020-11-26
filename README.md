# Apache Tomcat9 Install
By Command Linux Ubuntu 20.04 LTS

## Start
1.พิมพ์คำสั่งเพื่อ ทำการติดตั้งJava (Apache tomcat จำเป็นต้องใช้ Java ในการทำงานที่ไม่ต่ำกว่าเวอชั่น 8)
~~~
$ sudo apt install openjdk-11-jdk
~~~
(1.2)เมื่อติดตั้งเสร็จ พิมพ์คำสั่งเพื่อเช็คเวอชั่นของ Java 
~~~
$ java –version
~~~
2.ทำการ Create user tomcat โดยพิมพ์คำสั่ง
~~~
sudo useradd -m -d /opt/tomcat -U -s /bin/false tomcat
~~~
3.พิมพ์คำสั่งเพื่อดาวโหลดไฟล์Tomcat
~~~
$ wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.40/bin/apache-tomcat-9.0.40.tar.gz
~~~
3.พิมพ์คำสั่งเพื่อแตกไฟล์ และ ย้ายไฟล์ไปยังParth ที่กำหนด
~~~
$ tar xzf apache-tomcat-9.0.40.tar.gz
$ sudo mv apache-tomcat-9.0.40/* /opt/tomcat/
~~~
4.พิมพ์คำสั่งเพื่อสร้างไฟล์ manager.xml
~~~
$ sudo vi /opt/tomcat/conf/Catalina/localhost/manager.xml
~~~
(4.1)เพิ่มคำสั่ง
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="{catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
5.พิมพ์คำสั่งเพื่อสร้างไฟล์ host-manager.xml
~~~
$ sudo vi /opt/tomcat/conf/Catalina/localhost/host-manager.xml
~~~
(5.1)เพิ่มคำสั่ง
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
6.พิมพ์คำสั่งเพื่อสร้างไฟล์ tomcat service เพื่อเรียกใช้งาน tomcat
~~~
$ sudo vi /etc/systemd/system/tomcat.service
~~~
(6.1)เพิ่มคำสั่งเพื่อตั้งค่าการใช้งานserviceของtomcat
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
8.พิมพ์คำสั่งที่ใช้ในการยืนยันการเปลี่ยนค่าและรีโหลดการตั้งค่าserviceของtomcat
~~~
$ sudo systemctl daemon-reload
~~~
7.พิมพ์คำสั่งเพื่อย้ายสิทธิ์เพื่อเรียกใช้ service ของ tomcat
~~~
$ sudo chown -R tomcat:tomcat /opt/tomcat/
~~~
8.จากนั้นพิมพ์คำสั่งที่ใช้ในการเปิดใช้งาน Tomcat
~~~
$ sudo systemctl enable tomcat
$ sudo systemctl start tomcat
~~~
คำสั่งต่างที่ใช้กับ tomcat
คำสั่งเปิดใช้งาน
$ sudo systemctl start tomcat
