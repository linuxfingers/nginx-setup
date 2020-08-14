# centos8-nginx-setup
Notes to remember setting up a Linode with CentOS 8 using NGINX

*Anything in <brackets> replace with your own info, sans brackets.*
    
# init

1. Build CentOS 8 on Linode
2. Perform updates
    - **dnf update -y && dnf upgrade -y**
3. Set up altuser & add to wheel
    - **adduser <altuser>**
    - **passwd <altuser>**
    - **usermad -aG wheel <altuser>**
    *check that user was added successful with **id <altuser>** command
4. Disable root ssh:
    - **vim /etc/ssh/sshd_config**
    - under "Authentication" change PermitRootLogin from yes to no
    - **service sshd restart**
5. logout root and ssh via <altuser>

# nginx

6. install nginx
    http://nginx.org/en/linux_packages.html#RHEL-CentOS
    
    - **sudo dnf install nginx**
7. enable and start nginx at boot
    - **sudo systemctl enable nginx**
    - **sudo systemctl start nginx**
8. permanently enable hhtp on port 80 & 443
    - **sudo firewall-cmd --permanent --zone=public --add-service=http --add-service=https**
    - verify http is in *services* with **sudo firewall-cmd --list-services --zone=public**
9. apply chages
    - **sudo firewall-cmd --reload**
10. go to public IP to test if NGINX was successful

# server blocks (for multiple domains on one server)

11. make directory where websites will be hosted
    - **sudo mkdir -p /var/www/<domain>/**
12. assign ownership to current user
    - **sudo chown -R $USER:$USER /var/www/<domain>/**
13. make test html page
    - **vi/var/www/<domain>index.html**
    
    <html>
        <head>
            <title>NGINX Test Page</title>
        </head>
        <body>
            <h1>NGINX Setup Successful</h1>
                <p>Nice job, buddy.</p>
        </body>
    </html>
14. make new server block for your domain
    - **sudo vim /etc/nginx/conf.d/<domain>.conf**
    
    server {
        listen 80;
        listen [::]:80;

        root /var/www/<domain>/html;
        index index.html index.htm index.nginx-debian.html;

        server_name <domain> <www.domain>;

        location / {
                try_files $uri $uri/ =404;
        }
    }
15. test for syntax errors
    - **sudo nginx -t**

        nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
        nginx: configuration file /etc/nginx/nginx.conf test is successful

16. restart nginx
    - **sudo systemctl restart nginx**
17. update SELinux security context to serve files through new directory
    - **chcon -vR system_u:object_r:httpd_sys_content_t:s0 /var/www/<domain>/**



