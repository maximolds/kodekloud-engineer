## Apache Redirects

### Problem

The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:

httpd is already installed on app server 1. Configure Apache to listen on port 8089.

Configure Apache to add some redirects as mentioned below:

a.) Redirect http://stapp01.stratos.xfusioncorp.com:<Port>/ to http://www.stapp01.stratos.xfusioncorp.com:<Port>/ i.e non www to www. This must be a permanent redirect i.e 301

b.) Redirect http://www.stapp01.stratos.xfusioncorp.com:<Port>/blog/ to http://www.stapp01.stratos.xfusioncorp.com:<Port>/news/. This must be a temporary redirect i.e 302.

### Debugging

### Solution

```shell

sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@172.16.238.10

cd /etc/httpd/conf

cat /etc/httpd/conf/httpd.conf | grep Listen
sudo vi httpd.conf
change Listen to 8083
# Add ServerName to main config
echo "ServerName stapp01.stratos.xfusioncorp.com" | sudo tee -a /etc/httpd/conf/httpd.conf

cd /etc/httpd/conf.d

vi xfusion.conf

"
 *:8083

<VirtualHost *:8083>
    ServerName stapp01.stratos.xfusioncorp.com
    Redirect 301 / http://www.stapp01.stratos.xfusioncorp.com:8083/
</VirtualHost>

<VirtualHost *:8083>
    ServerName www.stapp01.stratos.xfusioncorp.com
    Redirect 302 /blog/ http://www.stapp01.stratos.xfusioncorp.com:8083/news/
</VirtualHost>
"

sudo systemctl restart httpd

sudo mkdir -p /var/www/html/news

# If you have content in /blog/, you might want to copy it:
sudo cp -r /var/www/html/blog/* /var/www/html/news/ 2>/dev/null || echo "index" | sudo tee /var/www/html/news/index.html

# Set proper permissions
sudo chown -R apache:apache /var/www/html/news
sudo chmod -R 755 /var/www/html/news

# Test configuration
sudo httpd -t

# Restart Apache
sudo systemctl restart httpd

# How to check
curl -vL http://stapp01.stratos.xfusioncorp.com:8083

curl -vL http://stapp01.stratos.xfusioncorp.com:8083/blog

curl -vL http://stapp01.stratos.xfusioncorp.com:8083/news

```

- See: [Ansible solution](solution.yaml)
