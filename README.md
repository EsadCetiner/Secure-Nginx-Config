# Fast and Secure Nginx Configuration Template - By Esad Cetiner
This project's goal is to provide a fast and secure by default Nginx configuration template and encourage secure design that minimizes the chances of misconfigurations and their impacts. Additional code snippets can be found inside the snippets folder which add additional functionality to improve performance/security but aren't enabled by default for various reasons, such as potentially being insecure, not working out of the box, or only being useful for more specific use cases.

## Features
- HTTPS redirection
- DoS mitigation (Will need to be tuned for your use case, but fine out of the box)
- Brotli Compression (Disabled by default)
- Disable page indexing
- 0-RTT (Disabled by default due to potential security concerns)
- Hide Nginx
- HSTS HTTPS only mode
- A+ Score on SSL Labs
- OCSP Stapling
- File caching for frequently accessed files
- Prevent access to sensitive files (such as database dumps)
- Default empty webroot (Prevents accidentally exposing your entire server's file system to the internet)

## Requirements
- A certificate with OCSP Stapling
- Nginx more_set_headers module installed
- This has only been tested on Ubuntu/Debian so Ubuntu/Debian is recommended, although there is nothing stopping you from using this on Arch/Cent OS etc

## How to install
1. You'll need to make sure that the ``nginx-extras`` package is installed, for Ubuntu/Debian this is ``sudo apt install nginx-extras``.
2. Clone this repository ``git clone https://github.com/EsadCetiner/Secure-Nginx-Config/``
3. Remove the default nginx.conf file ``sudo rm /etc/nginx/nginx.conf``
4. Replace it with the one from this repository ``sudo mv Secure-Nginx-Config/nginx.conf /etc/nginx/nginx.conf``
5. Move code snippets to nginx folder ``sudo mv Secure-Nginx-Config/snippets /etc/nginx/``
6. Move Custom error pages to webroot ``sudo mv Secure-Nginx-Config/error_pages /var/www/``
7. Replace ``ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;`` inside ``nginx.conf`` with the path to your certificate file (chain.pem for let's encrypt) 
8. Create an empty webroot to prevent accidential misconfiguration of webroot ``mkdir -p /var/www/empty-webroot/``

### Hiding Nginx
To hide Nginx include this code ``include /etc/nginx/snippets/hide-nginx.conf;`` inside your server block ``server { }`` to hide Nginx.

Please note that security through obscurity is not a replacement for proper security controls

### Serve Security Headers
Most security headers will need to be tuned for each Website, you can serve some basic security headers that should work fine for most people but you may need to fine tune things so it can work for you.

To server basic security headers add this code ``include /etc/nginx/snippets/security-headers.conf;`` inside your server block ``server { }``

### Prevent your site from being indexed by Google
To prevent your website from showing up in google search results add this include ``include /etc/nginx/snippets/no-robots.conf;`` inside your server block ``server { }``, please note that it will take time for your site to disapear from Google Search if it's already been indexed.

### HTTPS only mode
HSTS is a security header that tells browsers to only connect over HTTPS, this helps prevent MITM attacks like SSL stripping.

To activate HTTPS only mode add this code ``include /etc/nginx/snippets/ssl.conf;`` inside your server block ``server { }`` then register your domain at https://hstspreload.org, and please make sure you only include this for port 443.

### Fix Nextcloud "CONNECTION CLOSED" Error
Uploading large files to Nextcloud may cause a "Connection Closed" error, to fix this simply add this code ``include /etc/nginx/snippets/nextcloud_fix.conf;`` to your server block ``server { }``, this will increase the max upload size to 10GB. 

### Enable Brotli compression
By default, Nginx uses gzip which has inferior compression and uses up more CPU cycles, Brotli can typically improve page load times by 5-10%.

To enable Brotli, make sure you have the brotli module installed https://github.com/google/ngx_brotli and uncomment the load_module for brotli in ``nginx.conf``. Then add an include for the brotli code snippet ``include /etc/nginx/snippets/brotli.conf;`` in your HTTP block ``http { }`` inside ``nginx.conf``.

### Enable 0-RTT
0-RTT is a feature introduced in TLSv1.3 to improve the initial TLS connection speed, especially if it's combined with HTTP3. This feature is disabled by default since it opens up the risk of replay attacks (See: https://blog.cloudflare.com/even-faster-connection-establishment-with-quic-0-rtt-resumption/), you should consider what your performance and security needs are before enabling 0-RTT.

To enable 0-RTT, add an include for the 0-rtt code snippet in your http block ``include /etc/nginx/snippets/0-rtt.conf;``.

### Protect sensitive files
You can prevent the access of sensitive files stored in webroot such as htacess, database dumps and configuration files containing secrets. This feature tries to be false positive free but it may have poor coverage as a result.

**WARNING:** Do not store anything sensitive in your webroot if you can help it, this feature is meant to mitigate accidental misconfigurations.

To enable this feature, add an include into your server block ``include /etc/nginx/snippets/protect-sensitive-files.conf;``.

### Additional resources:
**Yandex Gixy:** Yandex Gixy is a static analysis tool for Nginx, it can detect misconfigurations like HTTP splitting, host header spoofing and SSRF. This project passes all tests from gixy out of the box. https://github.com/yandex/gixy
