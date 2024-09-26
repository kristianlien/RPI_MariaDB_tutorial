# How to install Ubuntu and MariaDB on Raspberry Pi, and connect to it using Python


## In this tutorial, we will:
1. Install Ubuntu with RPI Imager
2. Set up RPI with SSH 
3. Install MariaDB on RPI
4. Connect to MariaDB on an external client


## 1. Ubuntu install 
Firstly, download the appropriate version of the Raspberry Pi Imager from the [official website](https://www.raspberrypi.com/software/)

Run the .exe file, and follow the installation wizard

![Screenshot of the Raspberry Pi Imager Software](https://files.catbox.moe/1coiwd.png)

Choose your Raspberry Pi model under **"Raspberry Pi Device"**, and choose the SD card you wish to install Ubuntu on under "Storage"

Then, press the **"Choose OS"** button under **"Operating System"**

![Screenshot of the operating system list on the RPI imaging software](https://files.catbox.moe/vj69h1.png)

Then, choose the **"Other general-purpose OS"** option

![Screenshot showing "Ubuntu" under "Other general-purpose OS" menu](https://files.catbox.moe/fht9vb.png)

From this menu, we will choose **"Ubuntu"**.

Download the Ubuntu version appropriate for your device. For most Pi models, the "Ubuntu Desktop" option will be optimal.

After choosing your OS, run through the RPI imaging software. **Note that the installation process will wipe all data on the SD card.**

When the OS is written and verified on the SD Card, you will get a popup in the software.
![Screenshot of popup in the rpi imager, saying "Write successfull, you can now remove the SD card from the reader, along with OS version](https://files.catbox.moe/c3l40x.png)

Now, eject the SD card and install it in your Raspberry Pi and turn it on. For initial setup, you will have to connect the Pi to a keyboard and a screen. After Ubuntu setup, we can use SSH.

Run trough the Ubuntu Setup on your Pi. Make sure to connect the Pi to a network, as this is needed to SSH. The network doesn't need internet access.

After Ubuntu is setup, do CTRL+ALT+T to open the terminal and type in:

```console
IP A
``` 

Then you'll see your private IP, after "INET". Your private IP always follows the format 10.x.x.x. Write this down, it will be important.

## 2. RPI SSH Setup

In order to install the openssh package, first we update the package list. To do this, run the following command in the terminal:
```console
sudo apt update
```
Afterwards, install the openssh-server package:
```console
sudo apt install openssh-server
```

You can check if the installation was successful by running:
```console
sudo systemctl status ssh
```
From this command you should get something similar to:
```Console
ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enab
   Active: active (running) since Thu 2021-02-04 22:12:56 AEDT; 2min 36s ago
```
The "Active: active" part indicates that openssh is running.

Lastly, we need to allow ssh through the Ubuntu Firewall (UFW):
```console
sudo ufw allow ssh
```


Now, go on your client PC, and open cmd by pressing WIN+R and typing "cmd".
The command to SSH into your Pi is:
```console
SSH "username"@"your Pi's IP address"
```
(replace "username" and "your Pi's IP address" with your Ubuntu username, and your Pi's private IP respectively)

You might then be prompted to whether you want to connect. If so, type yes.

Now, type in your Ubuntu user password that correlates with the username you used to SSH, and you now have an active SSH connection to your Pi. 

## 3. Install MariaDB on Pi

To install the MariaDB-server package, we first update the packages:
```console
sudo apt update
```
Next, we install MariaDB:
```console
sudo apt install mariadb-server
```
Now, open MariaDB:
```console
sudo mariadb
```
Congratulations, you can now run SQL commands. Set up your users and permissions at this stage.


This is a basic format for setting up users in SQL:

```sql
CREATE USER 'your_username'@'%' IDENTIFIED BY 'your_password';
```
Replace the usernames and passwords to your liking. The "%" sign indicates that the user can connect using any IP address. 

If you want to limit it to one IP-address, you can do so by replacing the "%" sign with the IP address the user will connect from.

Now, we'll set up permissions for this user.

```sql
GRANT ALL PRIVILEGES ON *.* TO 'your_username'@'%';
```
You can limit the permissions to your liking, in this example' we're giving all permissions to this user.

If you want to limit the user's access to specific databases, you can replace the star signs with your database and table.

For example, if you want the user to have permissions on "main_db" database on the "users" table, you set it to main_db.users

Lastly, we "flush" privileges to save our changes.
```sql
FLUSH PRIVILEGES;
```

## 4. Connect to MariaDB on an external client
Now, we're gonna connect to our MariaDB server with a python script.
We're gonna be using the mysql-connector library for this.


First, make sure you have Python installed on your machine, and in PATH. Then, open CMD, and install the mysql-connector library:
```console
pip install mysql-connector-python
```

Now, in our python script, lets set up the connection to our server:
```python
import mysql.connector

connection = mysql.connector.connect(
    host="10.x.x.x", #Your Pi's local IP address
    user="your_username", #The user you set up previosuly that you want the script to use
    password="your_password", #The password for the user
    database="your_database" #The database you want the script to use
)

cursor = connection.cursor() #To run SQL commands, we use the connection.cursor() function

cursor.execute("YOUR SQL COMMAND HERE")
```
<br>

## Congratulations, you have now connected your python script to a MariaDB server running on a Raspberry Pi.









