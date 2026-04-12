---
title: Install and Use Docker on ( DigitalOcean) VPS
date: 2019-03-06 10:17:09
tags: DevOps
categories: 
- DevOps
---

### Why use Docker

Docker can efficiently deploy a service environment, such as completing a web site in a few minutes, including a web server, front end, and database for hosting a web site. Meanwhile, avoid conflicting dependencies by using separate containers for each component of an application. Docker makes it easy to maintain and transfer, especially when deployed in the cloud.

In this case I built a simple webserver in Docker with Python, and Running it in the DigitalOcean VPS.

### Step 1 - Create VPS Project

1. Choose an data center

   Use the [speed test site](http://speedtest-sfo1.digitalocean.com) to test the speed of different data centers, because my customers are in China, I chose the fastest SFO1 data center. Because my customers are in China, I chose the fastest SFO1 data center. Use the services provided by DO to easily migrate to other data center, if needed later.

   [![1.png](https://i.postimg.cc/zGPwGhZ9/1.png)](https://postimg.cc/bZkS6svT)

2. Choose an image

   In order to use more services flexibly on the VPS, I started with the basic linux distribution. DO offers different Linux distributions, for example Ubuntu, FreeBSD, Fedora, Debian, and CentOS. I personally would normally recommend the newest versions of CentOS like 7.5 x64.

   [![2.png](https://i.postimg.cc/TPLVLPTh/2.png)](https://postimg.cc/Y47GTMGw)

### Step 2 - Initial  CentOS Server Setup

   [Refer to this tutorial from the DO Community](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7)

1. Root Login

   ```bash
   local$ssh root@SERVER_IP_ADDRESS
   ```

2. Create a New User

   ```bash
   $adduser sysadmin
   $passwd sysadmin
   $gpasswd -a sysadmin wheel
   ```

3. Add Public Key Authentication

   ```bash
   local$ssh-keygen
   local$ssh-copy-id sysadmin@SERVER_IP_ADDRESS
   ```

4. Configure SSH Daemon

   ```bash
   $vi /etc/ssh/sshd_config
     '...'
     '#PermitRootLogin yes -> PermitRootLogin no'
     '...'
   $systemctl reload sshd
   or 
   $sudo systemctl restart sshd.service
   ```

5. Login with new user

   ```bash
   local$ssh sysadmin@SERVER_IP_ADDRESS
   $sudo command_to_run
   $exit
   ```

### Step 3 - Install Docker and Deploy Servers

1. Prepare System

   ```bash
   $sudo yum check-update
   $sudo yum -y update
   $sudo yum -y install mc
   ```

   

2. Deploy Google BBR

   Linux Kernel 4.9 comes with improved BBR congestion control algorithms. BBR has significantly increased throughput and reduced latency for connections, that means that your shared network speed is going up. 

   - Upgrade Linux Kernel to the latest version

     ```bash
     $sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
     $sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
     $sudo yum --enablerepo=elrepo-kernel install kernel-ml -y
     ```

   - Confirm the result

     ```bash
     $rpm -qa | grep kernel
     ```

     ```bash
     kernel-ml-4.16.0-1.el7.elrepo.x86_64
     kernel-3.10.0-514.el7.x86_64
     kernel-tools-libs-3.10.0-514.2.2.el7.x86_64
     kernel-tools-3.10.0-514.2.2.el7.x86_64
     kernel-3.10.0-514.2.2.el7.x86_64
     ```

     ```bash
     $sudo awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
     ```
     or:
     ```bash
     $sudo egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
     ```

     ```bash
     0 : CentOS Linux (4.16.0-1.el7.elrepo.x86_64) 7 (Core)
     1 : CentOS Linux (3.10.0-514.2.2.el7.x86_64) 7 (Core)
     2 : CentOS Linux (3.10.0-327.36.3.el7.x86_64) 7 (Core)
     3 : CentOS Linux, with Linux 3.10.0-123.el7.x86_64
     ```

   - Enable the 4.16.0 kernel by setting up the default grub2 boot entry

     ```bash
     $sudo grub2-set-default 0
     ```

   - Reboot and Check

     ```bash
     $sudo reboot
     $sudo uname -r
     4.16.3-1.el7.elrepo.x86_64
     ```

   - Enable BBR

     ```bash
     $echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
     $echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
     sudo sysctl -p
     ```

   - Confirm that BBR is enabled

     ```bash
     $sudo sysctl net.ipv4.tcp_available_congestion_control
     net.ipv4.tcp_available_congestion_control = bbr cubic reno
     $sudo sysctl -n net.ipv4.tcp_congestion_control
     bbr
     $lsmod | grep bbr
     tcp_bbr
     ```

   - Test the network performance enhancement (optional)

     ```bash
     $sudo yum install httpd -y
     $sudo systemctl start httpd.service
     $sudo firewall-cmd --zone=public --permanent --add-service=http
     $sudo firewall-cmd --reload
     $cd /var/www/html
     $sudo dd if=/dev/zero of=500mb.zip bs=1024k count=500
     ```

     Finally, visit the URL `http://[your-server-IP]/500mb.zip` from a web browser on desktop computer.

3. Install Docker & Docker-Compose

   [Refer to this tutorial from the DO Community](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-centos-7)

   ```bash
   $sudo curl -fsSL https://get.docker.com/ | sh
   $sudo systemctl start docker
   $sudo systemctl status docker
   $sudo systemctl enable docker
   $sudo usermod -aG docker $(whoami)
   $docker
   $sudo -i
   $curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
   $chmod +x /usr/local/bin/docker-compose
   ```

4. Create & Deployment a Server with Docker Compose

   In this case, I built a Web Server with Django/PostgreSQL/Caddy with [Cookiecutter Django](https://cookiecutter-django.readthedocs.io/en/latest/index.html) templates, following the chapters [Getting Up and Running Locally With Docker](https://cookiecutter-django.readthedocs.io/en/latest/developing-locally-docker.html#) and [
   Deployment with Docker](https://cookiecutter-django.readthedocs.io/en/latest/deployment-with-docker.html#) to made this small project easy to run . If necessary, I will collect and share more about how to use docker. 

### Conclusion

Docker is useful in numerous ways, and setting up Docker on VPS is fairly easy. I like to set up the docker development environment on my mac os, which makes it easy to isolate different projects and facilitate deployment to the test and production environment on vps.