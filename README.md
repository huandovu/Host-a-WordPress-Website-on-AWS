
# WordPress on AWS with DevOps

Host_a_WordPress_Website_on_AWS.png

This project demonstrates the deployment of a WordPress website on AWS, utilizing various AWS services to ensure high availability, scalability, and security. The deployment is done using an EC2 instance, with resources configured as detailed below.

## Architecture Overview

- Configured a Virtual Private Cloud (VPC) with both public and private subnets across two different availability zones.
- Deployed an Internet Gateway to facilitate connectivity between VPC instances and the wider Internet.
- Established Security Groups as a network firewall mechanism.
- Leveraged two Availability Zones to enhance system reliability and fault tolerance.
- Utilized Public Subnets for infrastructure components like the NAT Gateway and Application Load Balancer.
- Implemented EC2 Instance Connect Endpoint for secure connections to assets within both public and private subnets.
- Positioned web servers (EC2 instances) within Private Subnets for enhanced security.
- Enabled instances in both the private Application and Data subnets to access the Internet via the NAT Gateway.
- Hosted the website on EC2 Instances.
- Employed an Application Load Balancer and a target group for evenly distributing web traffic to an Auto Scaling Group of EC2 instances across multiple Availability Zones.
- Utilized an Auto Scaling Group to automatically manage EC2 instances, ensuring website availability, scalability, fault tolerance, and elasticity.
- Stored web files on GitHub for version control and collaboration.
- Secured application communications using a Certificate Manager.
- Configured Simple Notification Service (SNS) to alert about activities within the Auto Scaling Group.
- Registered the domain name and set up a DNS record using Route 53.
- Used EFS for a shared file system.
- Used RDS for the database.

## Deployment Script

Below is the script used to install WordPress on the EC2 instance:

```bash
# Create to root user
sudo su

# Update the software packages on the EC2 instance 
sudo yum update -y

# Create an html directory 
sudo mkdir -p /var/www/html

# Environment variable
EFS_DNS_NAME=fs-064e9505819af10a4.efs.us-east-1.amazonaws.com

# Mount the EFS to the html directory 
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

# Install the Apache web server, enable it to start on boot, and then start the server immediately
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

# Install PHP 8 along with several necessary extensions for WordPress to run
sudo dnf install -y \
php \
php-cli \
php-cgi \
php-curl \
php-mbstring \
php-gd \
php-mysqlnd \
php-gettext \
php-json \
php-xml \
php-fpm \
php-intl \
php-zip \
php-bcmath \
php-ctype \
php-fileinfo \
php-openssl \
php-pdo \
php-tokenizer

# Install the MySQL version 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 

# Install the MySQL server
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm 
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server 

# Start and enable the MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html 

# Download WordPress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# Create the wp-config.php file
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Edit the wp-config.php file
sudo vi /var/www/html/wp-config.php

# Restart the webserver
sudo service httpd restart
```

## Project Repository

All reference diagrams and scripts used to deploy the web app on an EC2 instance can be found in the GitHub repository: [https://github.com/huandovu/Host-a-WordPress-Website-on-AWS]

## Conclusion

This project showcases the deployment of a highly available and scalable WordPress website on AWS using various DevOps practices and AWS services.

---
