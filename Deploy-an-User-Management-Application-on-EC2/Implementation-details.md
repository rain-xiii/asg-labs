## ** Overview**  

This document outlines the key implementation details for deploying a full-stack CRUD application on Amazon Linux 2 using a hybrid LAMP and Node.js environment. It covers the essential steps involved, including:

- LAMP Setup: Installing and configuring Apache, MySQL (MariaDB), and PHP to establish a robust web server environment.
- Database Management: Setting up a MySQL database and managing it with phpMyAdmin for ease of use.
- Node.js Integration: Installing the Node.js environment to support additional backend functionalities.
- Application Deployment: Deploying a CRUD application that leverages both the LAMP stack and Node.js to demonstrate seamless integration.

## Prerequisites

Before you begin, ensure you have:
1. **An Amazon Linux 2 EC2 Instance**
2. **Proper Security Group Configuration**  
   - Restrict SSH (Port 22) to your trusted IP (e.g., `your-ip/32`)
   - Allow HTTP (Port 80) and Application Port (5000) temporarily during setup
   - **Critical Security Note:**  
     Avoid opening sensitive ports (22, 3306, 5000) to `0.0.0.0/0`. Use:
     - IP whitelisting for essential services
     - AWS Systems Manager Session Manager for SSH access
     - VPN for database access

![Security Setup](/Deploy-an-User-Management-Application-on-EC2/screenshots/Security-group-created.png)

3. Basic AWS CLI knowledge
4. IAM credentials with EC2 permissions

---

## 1. Install LAMP Web Server

### Step 1: Update Packages
```bash
sudo yum update -y
```

### Step 2: Install LAMP Stack
```bash
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
sudo yum install -y httpd mariadb-server
```

### Step 3: Configure Web Server
```bash
sudo systemctl start httpd
sudo systemctl enable httpd
sudo usermod -a -G apache ec2-user  # Replace with your user if different
```
![Enabled Apache Server](/Deploy-an-User-Management-Application-on-EC2/screenshots/Enabled-apache-web-server.png)

### Step 4: Set Directory Permissions
```bash
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;
```
![Grant Permissions](/Deploy-an-User-Management-Application-on-EC2/screenshots/Granted-permissions-apache-and-user.png)

- **Result**

![Apache test page](/Deploy-an-User-Management-Application-on-EC2/screenshots/Apache-test-page-accessed.png)

### Step 5: Test LAMP Server

```bash
echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
```

![Test LAMP Server](/Deploy-an-User-Management-Application-on-EC2/screenshots/Test-lamp-server-with-phpinfo.png)

### Step 6: Secure MariaDB
```bash
sudo systemctl start mariadb
sudo mysql_secure_installation
```
![Database security configuration](/Deploy-an-User-Management-Application-on-EC2/screenshots/Database-server-security-configuration.png)

---

## 2. Install phpMyAdmin

### Step 1: Install Dependencies
```bash
sudo yum install php-mbstring php-xml -y
sudo systemctl restart httpd
```

### Step 2: Download phpMyAdmin
```bash
cd /var/www/html
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.tar.gz
tar -xvzf phpMyAdmin-5.2.1-all-languages.tar.gz
mv phpMyAdmin-5.2.1-all-languages phpmyadmin
rm phpMyAdmin-5.2.1-all-languages.tar.gz
```

Access via `http://your-ec2-ip/phpmyadmin`

![Access phpMyAdmin](/Deploy-an-User-Management-Application-on-EC2/screenshots/phpMyAdmin-interface.png)
---

## 3. Database Setup

### Create Users Table

```sql
USE crud_app;

CREATE TABLE IF NOT EXISTS `user` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `first_name` VARCHAR(50) NOT NULL,
  `last_name` VARCHAR(50) NOT NULL,
  `email` VARCHAR(100) NOT NULL UNIQUE,
  `phone` VARCHAR(20),
  `comments` TEXT,
  `status` ENUM('active','inactive') DEFAULT 'active',
  PRIMARY KEY (`id`)
);
```
![SQL Schema](/Deploy-an-User-Management-Application-on-EC2/screenshots/SQL-schema-created.png)


---

## 4. Install Node.js Environment

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
. ~/.nvm/nvm.sh
nvm install 16
node -v  # Verify installation
```

---

## 5. Deploy CRUD Application

### Step 1: Clone Repository
```bash
cd /var/www/html
git https://github.com/First-Cloud-Journey/000004-EC2.git
cd 000004-EC2
```

### Step 2: Install Dependencies
```bash
npm init
npm install express dotenv express-handlebars body-parser mysql
```
![npm init](/Deploy-an-User-Management-Application-on-EC2/screenshots/Run-npm-init.png)

### Step 3: Configure Environment
Create `.env` file:
```env
DB_HOST='localhost'
DB_NAME='crud_app'
DB_USER='app_user'
DB_PASS='StrongPassword123!'
PORT=5000
```

### Step 4: Start Application
```bash
npm install --save-dev nodemon
npm start
```
Access your application via port 5000 `http://your-ec2-ip:5000`

![Final Result](/Deploy-an-User-Management-Application-on-EC2/screenshots/Successed-to-deploy-application.png)

---


## Final Architecture

```
[User Browser]
  ↓ HTTPS
[EC2 Instance (Amazon Linux 2)]
  ├── Apache (Port 80/443) → phpMyAdmin
  ├── Node.js (Port 5000) → CRUD App
  └── MariaDB (Localhost:3306)
```

---

**Important Security Note:**  
This configuration is for demonstration purposes. For production environments:
- Use RDS instead of local MariaDB
- Implement Auto Scaling Groups
- Set up proper CI/CD pipeline
- Enable AWS WAF and Shield
- Regular security audits

Let me know if you need any modifications or want to add specific implementation details!