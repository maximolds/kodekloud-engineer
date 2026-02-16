### Problem

Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 8084 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.


Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command curl http://stapp01:8084 command from jump host.

Note: Please do not try to alter the existing index.html code, as it will lead to task failure.


### Solution

```shell

#### Jumphost

iptables -L -n

#### stapp01

ssh tony@stapp01

curl http://localhost:8084

systemctl status httpd

grep -iR "^Listen" /etc/httpd/

vi /etc/httpd/conf/httpd.conf

netstat -tlnp | grep -i 8084

systemctl stop email
systemctl disbale email

systemctl start httpd
systemctl enable httpd

curl http://localhost:8084

iptables -L -n 

iptables -I INPUT 5 -p tcp --dport 8084 -j ACCEPT
service iptables save 

exit

#### Jumphost

curl http://localhost:8084
 ```