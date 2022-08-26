This is a modified version of https://github.com/stylersnico/nginx-secure-config
I've made some changes based on my personal use case and added some additional enhancements, such as being able to hide Nginx.

Feel free to use this however you like

This Configuration files requires the nginx-full and nginx-common package 

``sudo apt install nginx-full nginx-common``

Installation
clone the repo

``git clone https://github.com/EsadCetiner/Secure-Nginx-Config/``

Remove your Nginx conf file

``sudo rm /etc/nginx/nginx.conf``

Replace Nginx conf file with new one

``sudo mv Secure-Nginx-Config/nginx.conf /etc/nginx/nginx.conf``

Move Configurations file to nginx directory

``sudo mv Secure-Nginx-Config/configurations /etc/nginx/``

Reload nginx to apply your new configuration

``sudo systemctl reload nginx``

To hide Nginx error pages add this to your virtual host file

``include /etc/nginx/hide-error-pages.conf;``

To add some basic security headers add this to your virtual hosts file

``include /etc/nginx/configurations/security-headers.conf;``
