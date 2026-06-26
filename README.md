# zabbix-configure-by-docker-
The easiest and most professional way to install Zabbix via Docker is by using Docker Compose.
Structured exactly to show the step-by-step roadmap of what we will do first,
what we will need....
1. Ubuntu platform
2. mysql server
3. phpmyadmin
4. zabbix

----------------Lets Start------------------

Step 1: Install MySQL Server

Open your terminal and run the following commands one by one:
sudo apt update
sudo apt install mysql-server -y
sudo mysql_secure_installation

You will be prompted with a few questions:

    VALIDATE PASSWORD COMPONENT: Type Y if you want to enforce strong passwords, or N for standard passwords.
    New Password: Set a strong password for your root user.
    For the remaining prompts (Remove anonymous users, Disallow root login remotely, Remove test database), it is generally safe to type Y.
To enable password-based login for the root user, follow these steps:
    sudo mysql
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password_here';
    FLUSH PRIVILEGES;

    EXIT;
--Test the Root Login---
mysql -u root -p
Then root password is here....
 EXIT;

Step 2: Install phpMyAdmin

sudo apt update
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
 sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
  sudo phpenmod mbstring
  sudo systemctl restart apache2
  sudo a2enconf phpmyadmin
  sudo systemctl reload apache2
  systemctl reload apache2
Open your web browser and navigate to your server's IP address or domain followed by
http://your_server_ip/phpmyadmin
Username: root
Password: The exact password you set for MySQL in our previous task.Then login successfully.........

Step 3: Install zabbix
First, create a directory on your server, then create a file named docker-compose.yml inside that directory and paste the following code.
mkdir zabbix
root@test:~/zabbix# vi docker-compose.yml
------------------------------------------
 restart: unless-stopped
    networks:
      - zabbix-net

  # ৩. Zabbix Server Container
  zabbix-server:
    image: zabbix/zabbix-server-mysql:ubuntu-latest
    container_name: zabbix-server
    ports:
      - "10051:10051"
    volumes:
      - ./zabbix_export:/var/lib/zabbix/export
    environment:
      - DB_SERVER_HOST=zabbix-db
      - MYSQL_DATABASE=zabbix
      - MYSQL_USER=zabbix
   	  -	 MYSQL_PASSWORD=zabbix_password
      - MYSQL_ROOT_PASSWORD=root_password
      - ZBX_JAVAGATEWAY=zabbix-java-gateway
    depends_on:
      - zabbix-db
    restart: unless-stopped
    networks:
      - zabbix-net

  # ৪. Zabbix Web Frontend (Nginx + PHP)
  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:ubuntu-latest
    container_name: zabbix-web
    ports:
      - "8081:8081" 
    environment:
      - DB_SERVER_HOST=zabbix-db
      - MYSQL_DATABASE=zabbix
      - MYSQL_USER=zabbix
	  -	 MYSQL_PASSWORD=zabbix_password
      - MYSQL_ROOT_PASSWORD=root_password
      - PHP_TZ=Asia/Dhaka
    depends_on:
      - zabbix-db
      - zabbix-server
    restart: unless-stopped
    networks:
      - zabbix-net

networks:
  zabbix-net:
    driver: bridge
------------------------------------
apt  install docker.io
docker compose up -d
http://your-server-ip:8081

Username: Admin 
Password: zabbix
----------------------------------------------------------Enjoy-------------------------------------------------

