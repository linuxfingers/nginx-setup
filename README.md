# nginx setup documentation
*Anything in [brackets] replace with your own info*
    
# init

1. Build CentOS 8 on server
2. Perform updates
    - **dnf update -y && dnf upgrade -y**
3. Set up altuser & add to wheel
    - **adduser [altuser]**
    - **passwd [altuser]**
    - **useradd -G wheel [altuser]**
    *check that user was added successful with **id [altuser]** command
4. Disable root ssh:
    - **vim /etc/ssh/sshd_config**
    - under "Authentication" change PermitRootLogin from yes to no
    - **service sshd restart**
5. logout root and ssh via [altuser]

# nginx

6. install nginx
    http://nginx.org/en/linux_packages.html#RHEL-CentOS
    
    - **sudo dnf install nginx**
7. enable and start nginx at boot
    - **sudo systemctl enable nginx**
    - **sudo systemctl start nginx**
8. permanently enable http on port 80 & 443
    - **sudo firewall-cmd --permanent --zone=public --add-service=http --add-service=https**
    - verify http is in *services* with **sudo firewall-cmd --list-services --zone=public**
9. apply changes
    - **sudo firewall-cmd --reload**
10. go to public IP to test if NGINX was successful

# server blocks (for multiple domains on one server)

11. make directory where websites will be hosted
    - **sudo mkdir -p /var/www/[domain]/**
12. assign ownership to current user
    - **sudo chown -R $USER:$USER /var/www/[domain]/**
13. make test html page - copy from index.html in repo
    - **vi /var/www/[domain]/index.html**

14. make new server block for your domain
    - **sudo vim /etc/nginx/conf.d/[domain].conf**

<code>
    
server {
        listen 80;
        server_name domain.org www.domain.org;

        location / {
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header Host $http_host;
                proxy_pass http://localhost:8080; //unique port number per node
        }
}

</code>

15. test for syntax errors
    - **sudo nginx -t**
        *nginx: the configuration file /etc/nginx/nginx.conf syntax is ok*
        *nginx: configuration file /etc/nginx/nginx.conf test is successful*
16. restart nginx
    - **sudo systemctl restart nginx**
17. run getenforce to see if selinux is enforcing
    - **getenforce**
18. if the result is Enforcing, run the following command to make selinux allow outgoing http connections
    - **setsebool -P httpd_can_network_connect true**



