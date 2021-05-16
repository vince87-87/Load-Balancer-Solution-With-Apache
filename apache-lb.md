# Load Balancer Solution With Apache
# Configure Apache as load balancer
# Provision ec2 instance for apache using ubuntu ami
Configure security to allow port 80 on apache security group

![image](https://user-images.githubusercontent.com/49937302/118396485-58fe5a00-b682-11eb-9a87-078f0c41c583.png)

![image](https://user-images.githubusercontent.com/49937302/118396522-7af7dc80-b682-11eb-83cc-a86b09307772.png)
 
# Update repo & install Apache load balancer

#Install apache2

sudo apt update -y

sudo apt install apache2 -y

sudo apt-get install libxml2-dev -y

#Enable following modules:

sudo a2enmod rewrite

sudo a2enmod proxy

sudo a2enmod proxy_balancer

sudo a2enmod proxy_http

sudo a2enmod headers

sudo a2enmod lbmethod_bytraffic

#Restart apache2 service

sudo systemctl restart apache2

sudo systemctl status apache2

![image](https://user-images.githubusercontent.com/49937302/118395999-002dc200-b680-11eb-8b2e-95fd8cc10777.png)

# Configure Load Balancer

sudo vi /etc/apache2/sites-available/000-default.conf

add the below configuration

<Proxy "balancer://mycluster">

               BalancerMember http://172.31.26.234:80 loadfactor=5 timeout=1
               
               BalancerMember http://172.31.17.49:80 loadfactor=5 timeout=1
               
               ProxySet lbmethod=bytraffic
               
               # ProxySet lbmethod=byrequests
               
        </Proxy>

        ProxyPreserveHost On
        
        ProxyPass / balancer://mycluster/
        
        ProxyPassReverse / balancer://mycluster/

sudo systemctl restart apache2

sudo systemctl status apache2

![image](https://user-images.githubusercontent.com/49937302/118396015-13d92880-b680-11eb-92d4-d65139929f05.png)

# verify load balancing is working

Access the browser via http://lb-ip-address

![image](https://user-images.githubusercontent.com/49937302/118396312-81398900-b681-11eb-81d7-282d47091154.png)
 
Verify on /var/log/httpd/access_log that apache load balancer are hitting both web server

Sudo tail /var/log/httpd/access_log

Web server 1

![image](https://user-images.githubusercontent.com/49937302/118396043-30756080-b680-11eb-98be-b78e6fa3a70c.png)

 
Web Server 2

![image](https://user-images.githubusercontent.com/49937302/118396050-353a1480-b680-11eb-8349-139e404097c3.png)
 
# update hosts and load balancer config to resolve to hostname

Sudo vi /etc/hosts

Add the following records:

172.31.26.234   web-server1

172.31.17.49    web-server2

sudo vi /etc/apache2/sites-available/000-default.conf # update ip to host

sudo systemctl restart apache2

sudo systemctl status apache2

![image](https://user-images.githubusercontent.com/49937302/118396073-4f73f280-b680-11eb-9723-051c47db5cdd.png)

![image](https://user-images.githubusercontent.com/49937302/118396084-57339700-b680-11eb-8625-4c23ed2a45ee.png)

# verify apache load balancer able to resolve the name & are hitting web servers

Curl http://web-server1

Curl http://web-server2
 
![image](https://user-images.githubusercontent.com/49937302/118396414-0624a280-b682-11eb-9bdd-b34b8687cdcd.png)

![image](https://user-images.githubusercontent.com/49937302/118396092-66b2e000-b680-11eb-968a-d115c2dba7f5.png)

![image](https://user-images.githubusercontent.com/49937302/118396097-6a466700-b680-11eb-9e54-c97f3d5ce0d0.png)


 

