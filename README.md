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
4.สร้าง user ในการเข้าใช้ tomcat(ใช้ในการ Login เข้าใช้หน้าเว็ปไซต์ของ tomcat)
~~~
sudo vi /opt/tomcat/conf/tomcat-users.xml
~~~
เพิ่มคำสั่ง
~~~
<role rolename="manager-gui" />
<user username="ชื่อuser" password="รหัสผ่านที่ต้องการ" roles="manager-gui" />
<role rolename="admin-gui" />
<user username="ชื่อuser" password="รหัสผ่านที่ต้องการ" roles="manager-gui,admin-gui" />
~~~
5.การตั้งค่าไฟล์ manager และ host-manager

(5.1)ไปที่ Path ไฟล์(การที่จะเข้าPathไฟล์ดังกล่าวได้จะต้องให้สิทธิ์การเข้าถึงก่อน)
~~~
cd /opt/tomcat/webapps/host-manager/META-INF
~~~
(5.2)แก้ไขไฟล์โดยพิมพ์คำสั่ง
~~~
sudo vi context.xml
~~~
(5.3)ใส่คำสั่ง
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
(5.4)ไปที่ Path ไฟล์
~~~
cd /opt/tomcat/webapps/manager/META-INF/
~~~
(5.5)แก้ไขไฟล์โดยพิมพ์คำสั่ง
~~~
sudo vi context.xml
~~~
(5.6)ใส่คำสั่ง
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~

หมายเหตุ: คำสั่ง allow="^.*$" จากคำสั่งด้านบน จะทำไห้สามารถเข้าไปใช้งานหน้าManagerและHost-Managerได้จากทุก IP Addresses 

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
6.พิมพ์คำสั่งที่ใช้ในการยืนยันการเปลี่ยนค่าและรีโหลดการตั้งค่าserviceของtomcat
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
หมายเหตุ
หากไม่ได้แก้ไขไฟล์ context.xml ที่อยู่ใน 
path: /opt/tomcat/webapps/host-manager/META-INF/context.xml
กับ
path: /opt/tomcat/webapps/manager/META-INF/context.xml
จะไม่สามารถเข้าเมนู Manager App และ Host Manager ได้
