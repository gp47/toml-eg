# toml-eg

## Install on UBuntu 22.04LTS

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.13/bin/apache-tomcat-10.1.13.tar.gz
tar xzvf apache-tomcat-*.tar.gz
mv apache-tomcat-10.1.13 tomcat
sudo mv tomcat /opt/
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh' 
```

### Create Tomcat Application Accounts

Add the following values to `/opt/tomcat/conf/tomcat-users.xml `. Make sure to change the password for admin and manager access.
```bash
<!-- user manager can access only the manager section -->
<role rolename="manager-gui" />
<user username="manager" password="_SECRET_PASSWORD_" roles="manager-gui" />
 
<!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" />
<user username="admin" password="_SECRET_PASSWORD_" roles="manager-gui,admin-gui" />
```

### Allow Remote Hosts to Access Tomcat

```bash
sudo vi /opt/tomcat/webapps/manager/META-INF/context.xml
```
then:
```bash
<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
  ...
</Context>
```

Similarly edit context.xml for host manager application in text editor:

```bash
sudo vi /opt/tomcat/webapps/host-manager/META-INF/context.xml

<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
  ...
</Context>
```

### Systemd Service file

Copy `tomcat.service` to `/etc/systemd/system/`

Start and Enable Tomcat:
```bash
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat
```

### Allow port 8080 on firewall

```bash
sudo ufw allow 8080
```