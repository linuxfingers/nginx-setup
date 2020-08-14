# centos8-nginx-setup
Notes to remember setting up a Linode with CentOS 8 using NGINX

# init

1. Build CentOS 8 on Linode
2. Perform updates
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
    - **sudo dnf install nginx**
7. enable and start nginx at boot
    - **sudo systemctl enable nginx**
    - **sudo systemctl start nginx**
8. permanently enable hhtp on port 80
    - **sudo firewall-cmd --permanent --add-service=http**
    - verify http is in *services* with **sudo firewall-cmd --permanent --list-all**
9. apply chages
    - **sudo firewall-cmd --reload**
10. go to public IP to test if NGINX was successful
    




