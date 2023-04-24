# Fast and Secure Nginx Configuration Template - By Esad Cetiner
The goal of this project is to provide a fast and secure by default Nginx template, along with additional code snippets found inside ``configurations`` designed to help further improve performance/security for more specific use cases.

Feel free to use this however you like!

## Requirements
- A certificate with OCSP Stapling
- Nginx more_set_headers module installed
- This has only been tested on Ubuntu/Debian so Ubuntu/Debian is recommended, although there is nothing stopping you from using this on Arch/Cent OS etc

## How to install
1. You'll need to make sure that the ``nginx-extras`` package is installed, for Ubuntu/Debian this is ``sudo apt install nginx-extras``.
2. Clone this repository ``git clone https://github.com/EsadCetiner/Secure-Nginx-Config/``
3. Remove the default nginx.conf file ``sudo rm /etc/nginx/nginx.conf``
4. Replace it with the one from this repository ``sudo mv Secure-Nginx-Config/nginx.conf /etc/nginx/nginx.conf``
5. Move code snippets to nginx folder ``sudo mv Secure-Nginx-Config/configurations /etc/nginx/``
6. Move Custom error pages to webroot ``sudo mv Secure-Nginx-Config/error_pages /var/www/``
7. Replace ``ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;`` inside ``nginx.conf`` with the path to your certificate file (chain.pem for let's encrypt) 

### Hiding Nginx
To hide Nginx include this code ``include /etc/nginx/configurations/hide-nginx.conf;`` inside your server block ``server { }`` to hide Nginx.

Please note that security through obscurity is not a replacement for proper security controls

### Serve Security Headers
Most security headers will need to be tuned for each Website, you can serve some basic security headers that should work fine for most people but you may need to fine tune things so it can work for you.

To server basic security headers add this code ``include /etc/nginx/configurations/security-headers.conf;`` inside your server block ``server { }``

### HTTPS only mode
HSTS is a security header that tells browsers to only connect over HTTPS, this helps prevent MITM attacks like SSL stripping.

To activate HTTPS only mode add this code ``include /etc/nginx/configurations/ssl.conf;`` inside your server block ``server { }`` then register your domain at https://hstspreload.org, and please make sure you only include this for port 443.

### Fix Nextcloud "CONNECTION CLOSED" Error
Uploading large files to Nextcloud may cause a "Connection Closed" error, to fix this simply add this code ``include /etc/nginx/configurations/nextcloud_fix.conf;`` to your server block ``server { }``, this will increase the max upload size to 10GB. 
