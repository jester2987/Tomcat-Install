# Apache Tomcat9 Install
By Command Linux Ubuntu 20.04 LTS

## Start

ขั้นตอนการลง Java-JDK

1.พิมพ์คำสั่งเพื่อ ทำการติดตั้ง Java (Apache tomcat จำเป็นต้องใช้ Java ในการทำงานที่สูงกว่าเวอร์ชัน 8 ขึ้นไป)

หมายเหตุ:ผู้ทำคู่มือใช้ เวอร์ชัน 11
~~~
$ sudo apt install openjdk-11-jdk
~~~
1.1 เมื่อติดตั้งเสร็จ พิมพ์คำสั่งเพื่อเช็คเวอร์ชันของ Java 
~~~
$ java –version
~~~
จะแสดงข้อมูลเวอร์ชันของ Java 
~~~
openjdk version "11.0.9.1" 2020-11-04
OpenJDK Runtime Environment (build 11.0.9.1+1-Ubuntu-0ubuntu1.20.04)
OpenJDK 64-Bit Server VM (build 11.0.9.1+1-Ubuntu-0ubuntu1.20.04, mixed mode, sh                                                                             
~~~

ขั้นตอนการดาวน์โหลดและติดตั้งและ Tomcat

พิมพ์คำสั่งเพื่อดาวน์โหลดไฟล์ Tomcat
~~~
$ wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.40/bin/apache-tomcat-9.0.40.tar.gz
~~~
หลังจากดาวน์โหลดไฟล์ tomcat พิมพ์คำสั่งเพื่อสร้างโฟลเดอร์ tomcat เพื่อใช้ย้ายไฟล์
~~~
$ sudo mkdir /opt/tomcat
~~~
พิมพ์คำสั่งเพื่อแตกไฟล์ และ ย้ายไฟล์ไปยัง Path ที่สร้างโฟลเดอร์ไว้
~~~
$ tar xzf apache-tomcat-9.0.40.tar.gz
$ sudo mv apache-tomcat-9.0.40/* /opt/tomcat/
~~~
ทำการ Create user tomcat โดยพิมพ์คำสั่ง
~~~
sudo useradd -m -d /opt/tomcat -U -s /bin/false tomcat
~~~
หมายเหตุ:หากแสดงข้อความด้านล่าง usrt ได้ถูกสร้างไว้แล้วหรือมีอยู่แล้ว
~~~
useradd: warning: the home directory /opt/tomcat already exists.
useradd: Not copying any file from skel directory into it.
~~~

การตั้งค่าไฟล์ XML ต่างๆ

สร้าง user ในการเข้าใช้ tomcat (ใช้ในการ Login เข้าใช้หน้าเว็ปไซต์ของ tomcat)
~~~
sudo vi /opt/tomcat/conf/tomcat-users.xml
~~~
แก้ไขไฟล์โดยเพิ่ม
~~~xml
    <role rolename="manager-gui" />
    <user username="ชื่อuser" password="รหัสผ่านที่ต้องการ" roles="manager-gui" />
    <role rolename="admin-gui" />
    <user username="ชื่อuser" password="รหัสผ่านที่ต้องการ" roles="manager-gui,admin-gui" />
~~~

การตั้งค่าไฟล์ manager และ host-manager

ไปที่ Path ไฟล์
~~~
sudo vi /opt/tomcat/webapps/host-manager/META-INF/context.xml
~~~
แก้ไขไฟล์โดยเพิ่ม
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
ไปที่ Path ไฟล์
~~~
sudo vi /opt/tomcat/webapps/manager/META-INF/context.xml
~~~
แก้ไขไฟล์โดยเพิ่ม
~~~xml
    <Context privileged="true" antiResourceLocking="false"
    docBase="${catalina.home}/webapps/host-manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
    </Context>
~~~
หมายเหตุ:
- หากไม่ได้แก้ไขไฟล์ context.xml ที่อยู่ใน host-manager กับ manager
จะไม่สามารถเข้าเมนู Manager App และ Host Manager ได้จากภายนอก
- การแก้ไขไฟล์ .xml ต่างๆ หรือ การเข้า Path ไฟล์ต่างๆ อาจมีการติดสิทธิ์การเข้าถึง
การใช้งาน หากต้องการแก้ไขไฟล์ต้องเปลี่ยนสิทธิ์เป็น root ก่อน

การสร้าง Service tomcat

1.พิมพ์คำสั่งเพื่อสร้างไฟล์ tomcat.service เพื่อเรียกใช้งาน tomcat
~~~
$ sudo vi /etc/systemd/system/tomcat.service
~~~
1.1 แก้ไขไฟล์เพื่อตั้งค่าการใช้งาน service ของ tomcat
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
1.2 พิมพ์คำสั่งที่ใช้ในการยืนยันการปรับเปลี่ยนการตั้งค่าและรีโหลดการตั้งค่า service ของระบบ
~~~
$ sudo systemctl daemon-reload
~~~
1.3 พิมพ์คำสั่งเพื่อย้ายสิทธิ์ การเรียกใช้ service และ การเรียกใช้ไฟล์ต่างๆของ tomcat
~~~
$ sudo chown -R tomcat:tomcat /opt/tomcat/
~~~
1.4 จากนั้นพิมพ์คำสั่งที่ใช้ในการเปิดใช้งาน Tomcat
~~~
$ sudo systemctl enable tomcat
~~~
จะแสดง
~~~
Created symlink /etc/systemd/system/multi-user.target.wants/tomcat.service → /etc/systemd/system/tomcat.service.
~~~
หมายเหตุ:เมื่อใช้คำสั่ง $ sudo systemctl enable tomcat

พิมพ์คำสั่งเพื่อเปิดการใชงาน tomcat
~~~
$ sudo systemctl start tomcat
~~~
## หมายเหตุ

คำสั่ง systemctl enable เป็นคำสั่งให้ service ทำงานทุกครั้งหลังจาก reboot เครื่อง

คำสั่ง systemctl start ใช้ในการ เปิดใช้งาน tomcat

คำสั่งใช้งาน Service เพิ่มเติมเติม

คำสั่งใช้ restart tomcat หลังจากมีการตั้งค่าไฟล์.XML
~~~
$ sudo systemctl restart tomcat
~~~
คำสั่งปิดการใช้งาน tomcat
~~~
$ sudo systemctl stop tomcat
~~~
