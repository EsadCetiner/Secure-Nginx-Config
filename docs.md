# Documentation

Here you can find documentation about additional code snippets included with this template. They are not activated for various reasons, but are still bundled along with this config.

## Brotli:

Brotli provides better compression rates than gzip with fewer CPU cycles, it's recommend to consider enabling this feature.

NOTE: Brotli is not included in some distro's repositories (Ubuntu 22.04 does not have brotli but Ubuntu 24.04 does), you may have to use a 3rd party repo or compile it yourself.

To activate this feature, Install the `libnginx-mod-http-brotli-filter` and `libnginx-mod-http-brotli-static` packages and uncomment the Brotli config under `Compression Settings - Brotli`

`sudo apt install libnginx-mod-http-brotli-filter libnginx-mod-http-brotli-static`

## Hiding Nginx

NGINX error pages by default will display NGINX at the bottom of each and every page unless you bring your own custom error pages. Bare bone error pages are provided, if you want pretty error pages then you'll have to make them yourself or find one that you like.

To activate this feature, add the following code within your NGINX server block `include /etc/nginx/snippets/hide-nginx.conf;`, please make sure bundled error pages are in `/var/www/error_pages/`.

NOTE: A dedicated attacker will likely be able to fingerprint your web server. This will not protect you from an vulnerability in NGINX but it will annoy an attacker.

## Security header template

Security headers need to be tailored for your environment as it's impossible to create an perfect security header that works for everybody. A template for security header along with instructions are provided [in this example snippet](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/security-headers.template).

## Prevent your site from being indexed by Search Engines

You can use this snippet to prevent your admin panel or similar from being discovereable via Google dorking. Please be aware that this isn't the only way your site can be discovered, there are other methods such as DNS enumeration and [certificate transparency](https://crt.sh/) that can be used. You shouldn't rely on security through obscurity but this can annoy an attacker and stop bots scanning the internet.

To activate this feature, add the following code within your NGINX server block `include /etc/nginx/snippets/no-robots.conf;`.

## Enable 0-RTT

0-RTT is a feature introduced in TLSv1.3 to improve TLS's initial connection speed, this can have an especially big impact if combined with HTTP/3. This feature is disabled by default since it [opens up risks to replay attacks](https://blog.cloudflare.com/even-faster-connection-establishment-with-quic-0-rtt-resumption/), you should consider the potential security impacts before enabling this.

To activate this feature, uncomment the two directives for 0-RTT under `Performance and Cache`

**NOTE:** This option is not configureable per server block

## Protect sensitive files

This feature helps prevent common misconfigurations by blocking access to common config files that may contain secrets and hidden dot files.

To activate this feature, add the following code within your NGINX server block `include /etc/nginx/snippets/protect-sensitive-files.conf;`.

## Prioritize ChaCha20 for clients that don't support AES-NI

Some clients such as smartphones do not support AES-NI and can have slow decryption speeds, NGINX and OpenSSL can be configured to prefer ChaCha20 for clients that don't support AES-NI as an alternative to AES for improved performance.

**WARNING:** This feature may not work depending on how NGINX is compiled, you might have to change your OpenSSL's config directly.

To activate this feature, uncomment the `ssl_conf_command` directive in `nginx.conf` under `TLS Settings`.
