# Load Balancer Solution with Apache

## CONFIGURE APACHE AS A LOAD BALANCER

### 1. Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this:

![server list](./images/server-list.png)

### 2. Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

![inbound rule](./images/port-80.png)

### 3. Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

```
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

#Restart apache2 service
sudo systemctl restart apache2
```

![install & Config](./images/sudo-apt.png)
![install & Config](./images/install-1.png)
![install & Config](./images/install-2.png)
![install & Config](./images/install-3.png)

### Make sure apache2 is up and running:

`sudo systemctl status apache2`

![Apache Status check](./images/apache-status.png)

## Configure load balancing:

`sudo vi /etc/apache2/sites-available/000-default.conf`

### 4. Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:

[Load Balancer Public IPv4 Address](http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php)

![LoadBalancer Public IP](./images/load-balancer.png)

![Tooling website from Load Balancer](./images/toolingwebsitefromLB.png)

### Open two ssh/Putty consoles for both Web Servers and run following command:

`sudo tail -f /var/log/httpd/access_log`

![Logs from Webserver](./images/logs.png)

## Optional Step – Configure Local DNS Names Resolution

### Sometimes it is tedious to remember and switch between IP addresses, especially if we have a lot of servers under our management.
### What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So let us configure IP address to domain name mapping for our LB.

### Open this file on your LB server

`sudo vi /etc/hosts`

### Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers

```
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```

### Now we can update your LB config file with those names instead of IP addresses

```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```

### We can try to curl the Web Servers from LB locally curl http://Web1 or curl http://Web2 – it shall work.

![curl from LB](./images/curl-1.png)
![curl from LB](./images/curl-2.png)
![curl from LB](./images/curl-3.png)

### Remember, this is only internal configuration and it is also local to our LB server, these names will neither be ‘resolvable’ from other servers internally nor from the Internet.

### I have just implemented a Load balancing Web Solution for my DevOps team.


