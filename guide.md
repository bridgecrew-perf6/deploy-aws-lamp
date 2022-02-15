**Refer**
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-lamp-amazon-linux-2.html
- https://www.samirdixit.com/plugin/article/view/6/how-to-setup-lamp-server-on-amazon-linux-2-ami
- https://tecadmin.net/install-mysql-on-amazon-linux/

**1. SSH**
* Way 1:
    + chmod 600 file-key-pair.pem
    + ssh "aws-name"@123.456.789.123 -i file-key-pair.pem

* Way 2:
  - cp keys.pem ~/.ssh/
    + Path ~./ssh/config
    + ssh server_name
  
  - Create file : nano config
  - add content :
    + Host server_name
    + HostName 123.456.789.123
    + User username
    + Port 22
    + IdentityFile ~/.ssh/keys.pem

**2. PHP**
+ sudo yum install -y amazon-linux-extras
+ sudo amazon-linux-extras | grep php
+ sudo amazon-linux-extras enable php7.4
+ sudo yum install php php7.4-{pear,cgi,common,curl,mbstring,gd,mysqlnd,gettext,bcmath,json,xml,fpm,intl,zip,imap}

**3. Composer and git**
+ curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
+ OR : cd ~ && sudo curl -sS https://getcomposer.org/installer | sudo php && sudo mv composer.phar /usr/local/bin/composer && sudo ln -s /usr/local/bin/composer /usr/bin/composer
+ sudo yum install git

**4. Apache**
* Install:
  - sudo yum install -y httpd
  - sudo systemctl start httpd
  - sudo systemctl enable httpd
  - sudo systemctl is-enabled httpd
* Config path:
  - /etc/httpd/conf.d/
  - In forder conf.d : Add file manual.conf
    + nano manual.conf
     + <VirtualHost *:80>
	   + ServerName localhost
		 + DocumentRoot /var/www/html/primo-members-api/public
		 + <Directory /var/www/html/primo-members-api/public/>
		 + Options Indexes FollowSymLinks MultiViews
		 + AllowOverride All
		 + Order allow,deny
		 + allow from all
		 + Require all granted
		 + <\/Directory>
		 + ErrorLog /var/log/httpd/example.com-error_log
		 + CustomLog /var/log/httpd/example.com-access_log combined
     + <\/VirtualHost>
        
    + add more VirtualHost phpMyadmin port : 8089
     + <VirtualHost *:89>
		 + ServerName localhost
		 + DocumentRoot /var/www/html/phpMyAdmin
		 + <Directory /var/www/html/phpMyAdmin/>
		 + AllowOverride None
		 + Require all granted
		 + <\/Directory>
		 + <\/VirtualHost>
 
* Restart service Apache
  - sudo systemctl restart httpd

**5. Mysql install**
* Install:
  - sudo yum install mysql
* Check connect
  - mysql -h hostname -u username -p
  - show databases;
  - create database mydb;

* Create user:
  - If mariadb:
    + CREATE USER 'primo'@'localhost' IDENTIFIED BY 'Qwer-1234'; 
	  + ALTER USER 'primo'@'localhost' IDENTIFIED BY 'Qwer-1234';
	  + GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'primo'@'localhost' WITH GRANT OPTION;
	  + FLUSH PRIVILEGES;
    + exit;
   
  - If mysql:
    + CREATE USER 'primo'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Qwer-1234';
	  + ALTER USER 'primo'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Qwer-1234';
	  + GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'primo'@'localhost' WITH GRANT OPTION;
	  + FLUSH PRIVILEGES;
	  + exit;

**6. Install source**
  - Way 1 : SSH
    + in /var/www/html
    + git init
    + git remote add origin git@github.com:User/UserRepo.git
    + cp .env.example .env
    + composer install
    + sudo chmod -R 775 storage/

  - Way 2 : Token
    + in /var/www/html
    + git init
    + git clone git@github.com:User/UserRepo.git
    + Enter : user / pass_token
    + cp .env.example .env
    + composer install
    + sudo chmod -R 775 storage/

**7. EC2 and Mysql RDS**
* EC2
  - permit port 80 and 443
* RDS
  - permit 3306 with ec2's ip
  (at VPC security groups -> Inbound rules)
  - EX :
    + mysql/Aurora : 3306 
	 + Custom : 10.100.0.137/32 ( => 10.100.0.137 login SSH : ec2-user@ip-10-100-0-137 )
