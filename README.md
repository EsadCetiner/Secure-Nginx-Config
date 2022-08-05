This is a modified version of https://github.com/stylersnico/nginx-secure-config
I've made some changes based on my personal use case and added some additional enhancements, such as being able to hide Nginx.

Feel free to use this however you like

To hide Nginx error pages add this to your virtual host file

include /etc/nginx/hide-error-pages.conf;
