# Apache2_Modsecurity
Apache2 Modsecurity:

sudo apt install libapache2-mod-security2 -y
sudo a2enmod headers
sudo systemctl restart apache2


#Configuring ModSecurity:

sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf

#File: /etc/modsecurity/modsecurity.conf

# -- Rule engine initialization ----------------------------------------------

# Enable ModSecurity, attaching it to every transaction. Use detection
# only to start with, because that minimises the chances of post-installation
# disruption.
#
SecRuleEngine On
...

sudo systemctl restart apache2


#Setting Up the OWASP ModSecurity Core Rule Set


sudo rm -rf /usr/share/modsecurity-crs
sudo apt install git

#Clone the OWASP-CRS GitHub repository into the /usr/share/modsecurity-crs directory:
sudo git clone https://github.com/coreruleset/coreruleset /usr/share/modsecurity-crs

#Rename the crs-setup.conf.example to crs-setup.conf:
sudo mv /usr/share/modsecurity-crs/crs-setup.conf.example /usr/share/modsecurity-crs/crs-setup.conf

#Rename the default request exclusion rule file:
sudo mv /usr/share/modsecurity-crs/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example /usr/share/modsecurity-crs/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf




#Enabling ModSecurity in Apache 2

#Using a text editor such as vim, edit the /etc/apache2/mods-available/security2.conf file to include the OWASP-CRS files you have downloaded:
/etc/apache2/mods-available/security2.conf

<IfModule security2_module>
        SecDataDir /var/cache/modsecurity
        Include /usr/share/modsecurity-crs/crs-setup.conf
        Include /usr/share/modsecurity-crs/rules/*.conf
</IfModule>



#In /etc/apache2/sites-enabled/000-default.conf file VirtualHost block, include the SecRuleEngine directive set to On.

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        SecRuleEngine On
</VirtualHost>



#Restart the apache2 service to apply the configuration:

sudo systemctl restart apache2


#Testing ModSecurity

curl http://<SERVER-IP/DOMAIN>/index.php?exec=/bin/bash

#If ModSecurity has been configured correctly and is actively blocking attacks, the following error is returned:

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
<hr>
<address>Apache/2.4.25 (Debian) Server at 96.126.105.75 Port 80</address>
</body></html>
