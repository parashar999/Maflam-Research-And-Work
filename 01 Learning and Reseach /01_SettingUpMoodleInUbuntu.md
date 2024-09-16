

### **LAMP Stack and Moodle Installation Guide**
Reference : https://medium.com/@musaamin/install-moodle-4-3-on-ubuntu-22-04-a-step-by-step-tutorial-f49aec2db4f6, https://docs.moodle.org/404/en/Step-by-step_Installation_Guide_for_Ubuntu
#### **Step 1: Install Apache, MySQL, and PHP 7.4**
1. **Update system packages:**
   ```bash
   sudo apt-get update
   ```
2. **Install Apache, MySQL, and PHP 7.4:**
   ```bash
   sudo apt install apache2 mysql-client mysql-server php7.4 libapache2-mod-php
   ```
   - **Note:** PHP 8 is the default version in Ubuntu 20.04, but Moodle requires PHP 7.4 for compatibility.

3. **Secure MySQL installation:**
   ```bash
   sudo mysql_secure_installation
   ```
   - Follow the prompts to set the MySQL root password and secure the installation.

#### **Step 2: Install Additional Software**
1. **Install required PHP extensions and utilities:**
   ```bash
   sudo apt install graphviz aspell ghostscript clamav php7.4-pspell php7.4-curl php7.4-gd php7.4-intl php7.4-mysql php7.4-xml php7.4-xmlrpc php7.4-ldap php7.4-zip php7.4-soap php7.4-mbstring
   ```

2. **Restart Apache:**
   ```bash
   sudo service apache2 restart
   ```

3. **Install Git:**
   ```bash
   sudo apt install git
   ```

#### **Step 3: Download Moodle**
1. **Clone Moodle repository:**
   ```bash
   cd /opt
   sudo git clone git://git.moodle.org/moodle.git
   ```

2. **Switch to the desired Moodle branch (e.g., MOODLE_400_STABLE):**
   ```bash
   cd moodle
   sudo git branch --track MOODLE_400_STABLE origin/MOODLE_400_STABLE
   sudo git checkout MOODLE_400_STABLE
   ```

#### **Step 4: Move Moodle to the Web Directory**
1. **Copy Moodle to the web directory:**
   ```bash
   sudo cp -R /opt/moodle /var/www/html/
   ```

2. **Set up Moodle data directory:**
   ```bash
   sudo mkdir /var/moodledata
   sudo chown -R www-data /var/moodledata
   sudo chmod -R 777 /var/moodledata
   sudo chmod -R 0755 /var/www/html/moodle
   ```

#### **Step 5: Configure MySQL Server for Moodle**
1. **Edit MySQL configuration to ensure Moodle compatibility:**
   ```bash
   sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
   ```
   Add the following under `[mysqld]`:
   ```bash
   default_storage_engine = innodb
   innodb_file_per_table = 1
   innodb_file_format = Barracuda
   ```
   Save and restart MySQL:
   ```bash
   sudo service mysql restart
   ```

2. **Create Moodle database and user:**
   ```bash
   sudo mysql -u root -p
   CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   CREATE USER 'moodledude'@'localhost' IDENTIFIED BY 'passwordformoodledude';
   GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,CREATE TEMPORARY TABLES,DROP,INDEX,ALTER ON moodle.* TO 'moodledude'@'localhost';
   quit;
   ```

#### **Step 6: Complete Moodle Setup**
1. **Set webroot permissions for Moodle installation:**
   ```bash
   sudo chmod -R 777 /var/www/html/moodle
   ```
2. **Access the Moodle setup via browser:** 
   - Navigate to `http://IP.ADDRESS.OF.SERVER/moodle` to complete the setup.

3. **Revert webroot permissions after installation:**
   ```bash
   sudo chmod -R 0755 /var/www/html/moodle
   ```

#### **Step 7: Optimize Moodle**
1. **Set system paths in Moodle (via Site Administration > Server > System Paths):**
   - **Path to du:** `/usr/bin/du`
   - **Path to aspell:** `/usr/bin/aspell`
   - **Path to dot:** `/usr/bin/dot`

2. **Optional: Configure ClamAV for file scanning:**
   ```bash
   sudo mkdir /var/quarantine
   sudo chown -R www-data /var/quarantine
   ```
   - Set ClamAV path to `/usr/bin/clamscan` and Quarantine directory to `/var/quarantine` in Moodle.

#### **Step 8: Enable Zend OPcache for Performance**
1. **Configure OPcache settings:**
   ```bash
   sudo vi /etc/php7/apache2/conf.d/05-opcache.ini
   ```
2. **Restart Apache:**
   ```bash
   sudo service apache2 restart
   ```

#### **Step 9: Customize Apache Configuration**
1. **Avoid using `/moodle` in the URL by changing the DocumentRoot:**
   ```bash
   sudo vi /etc/apache2/sites-available/000-default.conf
   ```
   Change `DocumentRoot /var/www/html` to:
   ```bash
   DocumentRoot /var/www/html/moodle
   ```

2. **Restart Apache:**
   ```bash
   sudo service apache2 restart
   ```
