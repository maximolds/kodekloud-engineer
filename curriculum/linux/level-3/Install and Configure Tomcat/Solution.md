
### Problem

The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:

a. Install tomcat server on App Server 1.

b. Configure it to run on port 6400.

c. There is a ROOT.war file on Jump host at location /tmp.


Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl http://stapp01:6400


### Solution

```shell

sudo yum update

sudo yum install tomcat

# Configure port 6400
sudo sed -i 's/port="8080"/port="6400"/' /etc/tomcat/server.xml

# Copy to /tmp first
scp /tmp/ROOT.war tony@stapp01.stratos.xfusioncorp.com:/tmp/

# Deploy ROOT.war (copy from Jump Host first)
sudo rm -rf /var/lib/tomcat/webapps/ROOT*
sudo cp /tmp/ROOT.war /var/lib/tomcat/webapps/
sudo chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war

# Start Tomcat
sudo systemctl enable --now tomcat

# Test
curl http://stapp01:6400

```