# DevOps-Project008


****Project Implementation****

The project tasks were to configure a Load balancer and a  local DNS name resolution to access the web servers from the load balancer server. 
  
****Key Takeaways****
-  Learned what is a Load Balancer(distributes clients’ requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.) and its concepts. 
-  Horizontal and Vertical scaling on architecture configuration.

****STEPS****

**Step 1: Configuring Apache as a Load Balancer**

- Launched an Ubuntu EC2 instance name project8-lb so the complete set up looked like
 ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/1.PNG)
- Opened port 80 on the server.
- Installed Apache load balancer.
```
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev
 ```
- Enabled the following modules.
``` 
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

``` 
 ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/2.PNG)

- Restarted apache `sudo systemctl restart apache` and checked to see if the service is running `sudo systemctl status apache`
- Configured load balancing by editing the following file `sudo vi /etc/apache2/sites-available/000-default.conf` by adding the following configuration details under the http section.
``` 
<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
        
``` 
 ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/3.PNG)
 
- Restarted apache.

 ****NB: bytraffic balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.****
 
- Verified the configuation works by accessing it via the loadbalancer ip address. 
 ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/4.PNG)
 
- Unmounted /var/log/httpd which i had previously mounted on the NFS server  on both the servers and created own log directorys on the webserver. 
 ` sudo umount -l /var/log/httpd` 
-  ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/5.PNG)
 
-   ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/6.PNG)
 
- Ran the following command on both servers ` sudo tail -f /var/log/httpd/access_log` and refreshed my browser and my log file had new records with approximate requests.
![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/7.PNG)

- Configured local DNS Name resolution by edithin the following file `sudo vi /etc/hosts` and added the following 
``` 
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2

``` 
![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/9.PNG)

- Then update the Load balancer config file with the names instead of ip addresses.
  ``` 
  BalancerMember http://Web1:80 loadfactor=5 timeout=1
  BalancerMember http://Web2:80 loadfactor=5 timeout=1
  
  ``` 
  ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/10.PNG)
  
 - Tried to access the web servers locally from the load balancer server using `curl http://Web1 or curl http://Web2`
  ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/11.PNG)
  
   ![alt text](https://github.com/Ellawangari/DevOps-Project008/blob/main/Images/12.PNG)
   
   
   **I enjoyed doing the project as always no matter the errors we figure them out and succeced in the end.**
   
