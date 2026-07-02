# Apache Web Server Virtual Hosting 2 websites 

*********************************************************************************************************
Scenario
''' Website 1 → site1.com'''
''' Website 2 → site2.com
Both hosted on same Apache server
IP-based or Name-based Virtual Hosting (Name-based used)
*********************************************************************************************************
1️⃣ Install Apache
yum install httpd -y
systemctl start  httpd
systemctl enable httpd
*********************************************************************************************************
2️⃣ Add in firewall:==>
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
*********************************************************************************************************
3️⃣check kis port par runnig hai http 

netstat -tunlp | grep httpd

*********************************************************************************************************
4️⃣ check location of every file  

grep -i -n listen  /etc/httpd/conf/httpd.conf
grep -i -n documentroot  /etc/httpd/conf/httpd.conf
grep -i -n directoryindex  /etc/httpd/conf/httpd.conf

*********************************************************************************************************
5️⃣  Reload service
systemctl reload httpd

*********************************************************************************************************
6️⃣ check syntax sahi hai ya nahi  
httpd -t 

*********************************************************************************************************
7️⃣ Connect both machine using  /etc/hosts 
vim /etc/hosts
192.168.0.1  station6.example.com    example.com
192.168.0.2  station2.example.com    example.com
*********************************************************************************************************
ping station1.example.com
*********************************************************************************************************
Connect both machine using  /etc/hosts

And ping them http://192.168.0.1 , http://station1.example.com
*********************************************************************************************************

8️⃣ Create Sample Index Files  

echo "welcome to google site "  >>   /var/www/google/google.html    
echo "welcome to yahoo site "   >>   /var/www/yahoo/yahoo.html
*********************************************************************************************************
9️⃣ Create Virtual Host Files          Make Httpd.conf file  

vim /etc/httpd/conf/httpd.conf

<VirtualHost 192.168.0.1:80>
ServerAdmin root@station1.example.com
DocumentRoot /var/www/html
ServerName station1.example.com
ServerAlias example.com
</VirtualHost>

<VirtualHost 192.168.0.1:80>
ServerAdmin root@station1.example.com
DocumentRoot /var/www/google
DirectoryIndex google.html
ServerName google.example.com
ServerAlias google.com
ErrorLog  "logs/google_access_log" combined
CustomLog "logs/google_error_log"
</VirtualHost>

<VirtualHost 192.168.0.1:80>
ServerAdmin root@station1.example.com
DocumentRoot /var/www/yahoo
DirectoryIndex yahoo.html
ServerName yahoo.example.com
ServerAlias yahoo.com
ErrorLog  "logs/yahoo_access_log" combined
CustomLog "logs/yahoo_error_log"
</VirtualHost>

<Directory /var/www/google>
require all granted
</Directory>
<Directory /var/www/yahoo>
require all granted
</Directory>

*********************************************************************************************************
🔟 Restart services :

systemctl restart httpd
systemctl enable httpd
httpd -t
*********************************************************************************************************

1️⃣1️⃣ Add website in hosts file :->
vim /etc/hosts

192.168.0.1 station1.example.com  example.com
192.168.0.1 google.example.com  google.com
192.168.0.1 yahoo.example.com  yahoo.com

*********************************************************************************************************
ping google.example.com
*********************************************************************************************************
1️⃣2️⃣ Check chal raha hai ya nahi  using curl
curl -i http://192.168.0.1
curl -i http://google.example.com
curl -i http://yahoo.example.com
*********************************************************************************************************
1️⃣3️⃣ Check logs Created or not 
 cd /var/log/httpd

*********************************************************************************************************

1️⃣4️⃣   How we can set allow and deny rules for website browsing

vim /etc/httpd/conf/httpd.conf

<Directory /var/www/google>
require all granted
</Directory>

<Directory /var/www/yahoo>
require all granted
order allow,deny
allow from all
deny from <ip>
</Directory>
*********************************************************************************************************

1️⃣5️⃣ How we can apply username and password on any website  
    Jis Hosting website par lagana hai uss ki file me  jao Like: Google ,Yahoo   

cd /var/www/google/
vim .htaccess

authname        "this is my secure web page"
authtype        basic
authuserfile   /etc/httpd/googlepassword
require        valid-user
*********************************************************************************************************

1️⃣6️⃣  Abhi Apache ko nahi bataya ki   .htaccess    banaya hai waha se information le lena Batana padega 
       allowoverride authconfig       --  Ye keyword  samaj jata hai ki apne .htaccess file bana di hai isse information lena hai



vim /etc/httpd/conf/httpd.conf

<Directory /var/www/google>
require all granted
allowoverride authconfig
</Directory>

<Directory /var/www/yahoo>
require all granted
</Directory>
*********************************************************************************************************
   Set password on file  
      /etc/httpd/googlepassword            ==name same hona chahiye as per you mention in file

*********************************************************************************************************
1️⃣7️⃣  Add user in password protected website

htpasswd -c -m /etc/httpd/googlepassword <username>                                                         
htpasswd  -m /etc/httpd/googlepassword <username>           ( second user add without   " -c "  Nahi toh previous ko format mar dega  )
*********************************************************************************************************
 Read googlepassword file:

cat /etc/httpd/googlepassword

*********************************************************************************************************
1️⃣8️⃣ Restart Services and Check:

systemctl restart httpd
httpd -t
*******************************************************************************************************************************************************************************************************************************************************************
                                         We can run any website on any another port number
                                                      Change 80 to 85
                                                   Check 85 free hai ya nahi

1️⃣9️⃣ Check port is active or not

netstat -tunlp | grep 85
netstat -tunlp | grep httpd

2️⃣0️⃣ add another port in file 
vim /etc/httpd/conf/httpd.conf
Listen 85

2️⃣1️⃣ Change port 80 to 85  
vim /etc/httpd/conf/httpd.conf

<VirtualHost 192.168.0.1:85>
ServerAdmin root@station1.example.com
DocumentRoot /var/www/yahoo
DirectoryIndex yahoo.html
ServerName yahoo.example.com
ServerAlias yahoo.com
ErrorLog  "logs/yahoo_access_log" combined
CustomLog "logs/yahoo_error_log"
</VirtualHost>
*********************************************************************************************************
2️⃣2️⃣   add port in firewall
firewall-cmd --permanent --add-port=85/tcp
firewall-cmd --reload
*********************************************************************************************************
###### selinux me koi error hoga toh yaha dekhege

 Tail -f /var/log/audit/audit.log 
*********************************************************************************************************
######  open selinux  browser  ⇒ pata chal jati hai kaha error hai ⇒ gui mode hai
Sealert -b
*********************************************************************************************************
###### check Kis kis port par allow hai selinux

semanage port -l | grep http
*********************************************************************************************************
######  Add port 85 in Selinux  

semanage port -a -t http_port_t -p tcp 85
*********************************************************************************************************
###### check port added

semanage port -l | grep http
*********************************************************************************************************
systemctl restart httpd
*********************************************************************************************************
###### Running in port  85 

****************************************************************************************************************************************************************************************************************************************************************


                                                             production-ready method to generate SSL without Certbot and configure Apache for 2 websites with HTTP → HTTPS redirection.



📌 Scenario
Web Server: Apache
SSL Tool: OpenSSL
Websites:
site1.example.com
site2.example.com
Redirect HTTP → HTTPS
*********************************************************************************************************
1️⃣ Install Required Packages:
yum install mod_ssl openssl -y
*********************************************************************************************************
2️⃣ Create SSL Directory
sudo mkdir -p /etc/httpd/ssl       
*********************************************************************************************************
3️⃣ Generate Private Key & Certificate (OpenSSL)
🔹 Site 1:
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/httpd/ssl/site1.key \
-out /etc/httpd/ssl/site1.crt


🔹 Site 2:
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/httpd/ssl/site2.key \
-out /etc/httpd/ssl/site2.crt

*********************************************************************************************************
4️⃣ Create Website Directories
mkdir -p /var/www/site1
mkdir -p /var/www/site2
*********************************************************************************************************
5️⃣ Create HTTP VirtualHost Redirect HTTP -> HTTPS:    for site1:->

vim  /etc/httpd/conf.d/site1.conf

<VirtualHost 192.168.0.1:80>
    ServerName site1.example.com
    Redirect permanent / https://site1.example.com/
</VirtualHost>

<VirtualHost 192.168.0.1:443>
ServerAdmin root@station6.example.com
DocumentRoot /var/www/site1
DirectoryIndex site1.html
ServerName site1.example.com
ServerAlias site1.com
ErrorLog  "logs/site1_access_log" combined
CustomLog "logs/site1_error_log"

 SSLEngine on
 SSLCertificateFile /etc/httpd/ssl/site1.crt
 SSLCertificateKeyFile /etc/httpd/ssl/site1.key

</VirtualHost>

 <Directory /var/www/site1>
        AllowOverride All
        Require all granted
   </Directory>

*********************************************************************************************************
5️⃣ Create HTTP VirtualHost Redirect HTTP -> HTTPS:    for site2:->
vim  /etc/httpd/conf.d/site2.conf

<VirtualHost 192.168.0.1:80>
    ServerName site2.example.com
    Redirect permanent / https://site2.example.com/
</VirtualHost>

<VirtualHost 192.168.0.1:443>
ServerAdmin root@station6.example.com
DocumentRoot /var/www/site2
DirectoryIndex site2.html
ServerName site2.example.com
ServerAlias site2.com
ErrorLog  "logs/site2_access_log" combined
CustomLog "logs/site2_error_log"

 SSLEngine on
 SSLCertificateFile /etc/httpd/ssl/site2.crt
 SSLCertificateKeyFile /etc/httpd/ssl/site2.key

</VirtualHost>

 <Directory /var/www/site2>
        AllowOverride All
        Require all granted
   </Directory>
*********************************************************************************************************
6️⃣ Restart Apache
sudo systemctl restart httpd

*********************************************************************************************************
7️⃣ Verify SSL
Browser:
https://site1.example.com
*********************************************************************************************************
🔁 HTTP → HTTPS Test
curl -I http://site1.example.com

*********************************************************************************************************
Redirect  successfull :
http://site1.example.com   -->   https://site1.example.com
********************************************************************************************************
