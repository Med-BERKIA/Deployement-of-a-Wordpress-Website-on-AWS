# Deployement-of-a-Wordpress-Website-on-AWS
I deployed a Wordpress Website on AWS using a three tier architecture
# Hosting a WordPress Website on AWS

![Reference Architecture](Deploiement%20of%20a%20Wordpress%20Website%20on%20AWS.png)

This project demonstrates how to design, deploy, and operate a **highly available, secure, and scalable WordPress website on AWS**. The solution leverages multiple AWS services across two Availability Zones to ensure fault tolerance, performance, and security.

The repository includes:

* Reference architecture diagram
* EC2 user-data and configuration scripts
* Auto Scaling launch template scripts
* WordPress configuration examples

---

## 📌 Project Overview

The WordPress application is hosted on EC2 instances running in **private subnets**, fronted by an **Application Load Balancer**, and backed by **Amazon RDS** for the database and **Amazon EFS** for shared storage. Traffic is securely routed using **Route 53** and **AWS Certificate Manager (ACM)**.

---

## 🏗️ Architecture Highlights

* Multi-AZ deployment for high availability
* Private subnets for application and database layers
* Public subnets for ALB and NAT Gateway
* Auto Scaling Group for elasticity
* Shared file system using Amazon EFS
* Secure HTTPS access using ACM

---

## 🧩 AWS Services Used

### 1. Amazon VPC

* Custom VPC with public and private subnets
* Deployed across **two Availability Zones**

### 2. Internet Gateway

* Enables internet access for public-facing resources

### 3. Security Groups

* Acts as a virtual firewall for EC2, ALB, RDS, and EFS

### 4. NAT Gateway

* Allows private EC2 and database instances to access the internet securely

### 5. EC2 Instance Connect Endpoint

* Secure access to EC2 instances without public IPs

### 6. EC2 (Web Servers)

* WordPress hosted on EC2 instances
* Instances deployed in **private subnets**

### 7. Application Load Balancer (ALB)

* Distributes traffic across EC2 instances
* Integrated with Auto Scaling Group

### 8. Auto Scaling Group & Target Group

* Automatically scales EC2 instances
* Ensures high availability and fault tolerance

### 9. Amazon EFS

* Shared file system for WordPress content
* Mounted on all EC2 instances

### 10. Amazon RDS

* Managed relational database for WordPress

### 11. AWS Certificate Manager (ACM)

* SSL/TLS certificates for HTTPS

### 12. Amazon SNS

* Notifications for Auto Scaling events

### 13. Amazon Route 53

* Domain registration and DNS routing

### 14. GitHub

* Version control for web files and scripts

---

## ⚙️ WordPress Installation Script (EC2)

```bash
sudo yum update -y
sudo mkdir -p /var/www/html

EFS_DNS_NAME=fs-064e9505819af10a4.efs.us-east-1.amazonaws.com
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

sudo dnf install -y php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-json php-xml php-fpm php-zip

wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

sudo chown apache:apache -R /var/www/html
sudo service httpd restart
```

---

## 🚀 Auto Scaling Group Launch Template Script

```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

sudo dnf install -y php php-cli php-curl php-mysqlnd php-gd php-xml

EFS_DNS_NAME=fs-02d3268559aa2a318.efs.us-east-1.amazonaws.com
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a

chown apache:apache -R /var/www/html
sudo service httpd restart
```

---

## 🔐 HTTPS Configuration (wp-config.php)

```php
/* SSL Settings */
define('FORCE_SSL_ADMIN', true);

if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
    $_SERVER['HTTPS'] = '1';
}
```

### Accessing wp-config.php

```bash
sudo su
cd /var/www/html
vi wp-config.php
service httpd restart
```

---

## ✅ Key Outcomes

* Secure WordPress deployment using private subnets
* High availability through Multi-AZ and Auto Scaling
* Centralized storage with EFS
* HTTPS traffic using ALB and ACM
* Production-ready cloud architecture

---

## 📌 Conclusion

This project demonstrates a **production-grade WordPress architecture on AWS**, applying best practices in networking, security, scalability, and automation. It is suitable for real-world workloads and serves as a strong portfolio project for Cloud and DevOps roles.
