# Attacking DVWA: A Walkthrough of Common Web Exploits

[**DVWA (Damn Vulnerable Web Application)**](https://github.com/digininja/DVWA) is an intentionally vulnerable web app for practicing common web exploits in a safe environment.
In this repository, DVWA is set up locally and each vulnerability is explained with practical examples.

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/DVWA.png" alt="DVWA Logo" width="300">
</div>

## Project Structure

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/project-structure.png" alt="Project Structure" width="140">
</div>

All challenge walkthroughs can be found in the `vulnerabilities` directory.  
Each vulnerability contains a `low.md`, `mid.md`, and `high.md` file, matching the DVWA security levels.

## Setting up DVWA

### 1. Create two virtual machines
Set up two virtual machines (e.g., Kali Linux or Ubuntu).
One will be used for attacking purposes, the other will host DVWA.

---

### 2. Download DVWA to the victim machine
Install Git:
```
sudo apt update
sudo apt install git
```

Navigate to the web root:
```
cd /var/www/html
```

Clone the DVWA repository:
```
sudo git clone https://github.com/digininja/DVWA.git
```

---

### 3. Configuration

Set permissions:
```
sudo chown -R www-data:www-data DVWA
sudo find DVWA -type d -exec chmod 755 {} \;
sudo find DVWA -type f -exec chmod 644 {} \;
```

Navigate to the config folder:
```
cd DVWA/config
```

Copy the _config.inc.php.dist_ file and name it _config.inc.php_:
```
cp config.inc.php.dist config.inc.php
```

In the file _config.inc.php_, note the db_user and the db_password or change it to:
```
$_DVWA[ 'db_user' ]     = getenv('DB_USER') ?: 'dvwa';
$_DVWA[ 'db_password' ] = getenv('DB_PASSWORD') ?: 'password';
```

**Start and configure MySQL**  

Start MySQL:
```
sudo systemctl start mysql
```

If not installed:
```
sudo apt install mysql-server
```

Check status:
```
sudo systemctl status mysql
```

Log in to the MySQL server and press Enter if no password is set:
```
sudo mysql -u root -p
```

Create DVWA database:
```
create database dvwa;
```

Create a user (the credentials must match those in the _config.inc.php_ file):
```
create user 'dvwa'@'127.0.0.1' identified by 'password';
```

Grant privileges to the user:
```
grant all privileges on dvwa.* to 'dvwa'@'127.0.0.1';
```

Exit the database:
```
exit
```

**Start and configure Apache**  

Start Apache:
```
sudo systemctl start apache2
```

If not installed:
```
sudo apt install apache2
```

Check status:
```
sudo systemctl status apache2
```

**Configure PHP settings**  

Go to the PHP config directory (adjust version if needed):
```
cd /etc/php/8.4/apache2
```

Edit _php.ini_ and set:
```
allow_url_fopen = On
allow_url_include = On
```

Restart Apache:
```
sudo systemctl restart apache2
```

**Access DVWA in browser**  

Open:
- http://127.0.0.1/DVWA
- or http://localhost/DVW

Login with your credentials.
Click “Create / Reset Database” on the setup page, then log in again.

✅ DVWA is now ready!

---

### OPTIONAL: 4. Configure host-only network

<div align="center">
    <img src="https://github.com/j0wittmann/Attacking-DVWA/blob/main/Network%20Plan.png" alt="Network Plan" width="500">
</div>

This setup uses two virtual machines:
- **Kali Attacker** machine with:
  - **NAT**: for internet access via VM host
  - **Host-only**: to reach the DVWA machine
- **DVWA victim** machine with:
  - **Host-only**: no internet access
 
**Interface configuration**

Edit /etc/network/interfaces on both VMs:  

**On DVWA victim:**
```
auto eth0
iface eth0 inet static
address 172.16.99.128
netmask 255.255.255.0
```

**On Kali Attacker (e.g., eth1 as host-only):**
```
auto eth1
iface eth1 inet static
address 172.16.99.129
netmask 255.255.255.0
```

Keeping the **DVWA machine** in a host-only network ensures:
- A controlled lab environment
- No internet access for the victim
- Safe and reproducible testing conditions
