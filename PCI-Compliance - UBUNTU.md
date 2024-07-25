To analyze the URL

https://observatory.mozilla.org/analyze

**Start Here:**

#### Firewall CSF Install

Package and dependencies.

> apt -y update && apt -y upgrade

```bash
apt install perl zip unzip libwww-perl liblwp-protocol-https-perl sendmail-bin dnsutils

cd /usr/src && wget https://download.configserver.com/csf.tgz

tar -xzf csf.tgz && cd csf && sh install.sh

perl /usr/local/csf/bin/csftest.pl && csf -v
```

```bash
sed -i 's/TESTING = "1"/TESTING = "0"/g' /etc/csf/csf.conf
sed -i 's/RESTRICT_SYSLOG = "0"/RESTRICT_SYSLOG = "3"/g' /etc/csf/csf.conf
sed -i 's/CT_LIMIT = "0"/CT_LIMIT = "50"/g' /etc/csf/csf.conf
sed -i 's/CT_PERMANENT = "0"/CT_PERMANENT = "1"/g' /etc/csf/csf.conf
sed -i 's/CT_BLOCK_TIME = "1800"/CT_BLOCK_TIME = "600"/g' /etc/csf/csf.conf
sed -i 's/CONNLIMIT = ""/CONNLIMIT ="22;15"/g' /etc/csf/csf.conf

systemctl stop sendmail && systemctl disable sendmail && csf -r
```

## Apache Security Headers

check online

* https://www.serpworx.com/check-security-headers/
* https://securityheaders.com/

Load header moduels first

​`a2enmod headers`​ 

```bash
in File /etc/apache2/conf-enabled/security.conf

<IfModule mod_headers.c>
Header always set X-XSS-Protection: "1; mode=block"
Header always set X-Content-Type-Options: "nosniff"
Header always set X-Frame-Options: "SAMEORIGIN"
Header always set Content-Security-Policy: "default-src 'self'"
Header always set Referrer-Policy: "strict-origin-when-cross-origin"
Header always set Strict-Transport-Security: "max-age=31536000; includeSubDomains; preload"
Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure #Session Cookie Does Not Contain the "Secure" Attribute
<IfModule mod_headers.c>

<DirectoryMatch "/\.git">
   Require all denied
</DirectoryMatch>

ServerTokens Prod
ServerSignature Off
TraceEnable Off
```

systemctl restart apache2

##### Or using .htaccess file in virtualhost home directory

```bash
in file -- .htaccess 

<IfModule mod_headers.c>
Header set X-Frame-Options "sameorigin"
Header set X-XSS-Protection "1; mode=block"
Header set X-Content-Type-Options "nosniff"
Header set X-Permitted-Cross-Domain-Policies "none"
Header set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
Header set Content-Security-Policy "default-src * data:; script-src https: 'unsafe-inline' 'unsafe-eval'; style-src https: 'unsafe-inline'"
Header set Referrer-Policy "no-referrer-when-downgrade"
Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure #Session Cookie Does Not Contain the "Secure" Attribute
#Header set Feature-Policy "camera 'none'; fullscreen 'self'; geolocation *; microphone 'self' https://www.mokpay.com/*"
#Header set Expect-CT: max-age=31536000, report-uri="https://your.report-uri.com/r/d/ct/report"
<IfModule mod_headers.c>
```

##### Update Apache2 version to latest

```bash
apt update && apt upgrade

apt install dirmngr ca-certificates software-properties-common apt-transport-https
add-apt-repository ppa:ondrej/apache2 -y
apt update

apt install apache2

```
