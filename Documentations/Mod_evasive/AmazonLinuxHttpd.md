yum update
amazon-linux-extras
amazon-linux-extras enable epel


yum install httpd -y

systemctl enable --now httpd

systemctl status httpd

#Install mod_evasive on Amazon Linux 2

sudo yum --enablerepo=epel install mod_evasive -y

#Check If the mod_evasive module is loaded on apache or not 
httpd -M | grep ev


#Instaall Fail2ban 
yum install fail2ban -y
systemctl enable --now fail2ban.service
systemctl status fail2ban.service


#fail2ban configuration for httpd
[Fail2ban Configuration for httpd](https://www.xmodulo.com/configure-fail2ban-apache-http-server.html#google_vignette)

nano /etc/fail2ban/jail.d/apache.conf

#Enable Apache Jails on CentOS/RHEL or Fedora
```

    # detect password authentication failures
[apache]
enabled  = true
port     = http,https
filter   = apache-auth
logpath  = /var/log/httpd/*error_log
maxretry = 6

# detect spammer robots crawling email addresses
[apache-badbots]
enabled  = true
port     = http,https
filter   = apache-badbots
logpath  = /var/log/httpd/*access_log
bantime  = 172800
maxretry = 1

# detect potential search for exploits and php vulnerabilities
[apache-noscript]
enabled  = true
port     = http,https
filter   = apache-noscript
logpath  = /var/log/httpd/*error_log
maxretry = 6

# detect Apache overflow attempts
[apache-overflows]
enabled  = true
port     = http,https
filter   = apache-overflows
logpath  = /var/log/httpd/*error_log
maxretry = 2

# detect failures to find a home directory on a server
[apache-nohome]
enabled  = true
port     = http,https
filter   = apache-nohome
logpath  = /var/log/httpd/*error_log
maxretry = 2

# detect failures to execute non-existing scripts that
# are associated with several popular web services
# e.g. webmail, phpMyAdmin, WordPress
port     = http,https
filter   = apache-botsearch
logpath  = /var/log/httpd/*error_log
maxretry = 2
```

#Install Mod_Security

yum install mod_security.x86_64 -y

#Install php8.1
amazon-linux-extras enable php8.1
 yum clean metadata -y
  yum install php-cli php-pdo php-fpm php-json php-mysqlnd -y

#Install Composer 
curl -sS https://getcomposer.org/installer | php -- --install-dir=/var/www/html/Dogparkbackend --filename=composer.phar






