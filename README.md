# Fast and Secure NGINX Configuration Template - By Esad Cetiner
This project's goal is to provide a fast and secure by default NGINX configuration template and encourage secure design that minimizes the chances of misconfigurations and their impacts. Code snippets are also provided for additional functionality for you to include in your server blocks.

## Features
- HTTPs redirection
- DoS mitigations (Rate limiting rules will need to be tuned to your needs)
- Brotli Compression (Disabled by default)
- Disable page indexing for Search Engines (Disabled by default)
- 0-RTT (Disabled by default)
- Hide NGINX and minimize information leakage via headers
- HTTPs only mode with Strict Transport Security (Disabled by default)
- Prevent host header spoofing
- A/A+ Score on SSL Labs (A+ requires HTTPs only mode)
- [Prevent DNS Spoofing](https://blog.zorinaq.com/nginx-resolver-vulns/)
- Prioritize ChaCha20 encryption for clients that don't support AES-NI (Disabled by default)
- OCSP Stapling
- File caching for frequently accessed files
- Restrict access to sensitive files
- Default empty webroot (Prevents accidentally exposing your entire server's file system to the internet)
- Security header template (Get a higher score in https://securityheaders.com)

## Requirements
- A certificate with OCSP Stapling
- Nginx more_set_headers module installed
- This has only been tested on Ubuntu/Debian so Ubuntu/Debian is recommended, although there is nothing stopping you from using this on Arch/Cent OS etc

## Before you install

To prevent DNS spoofing, the resolver directive within the http block is set to use an localhost DNS stub resolver (which is 127.0.0.53 for most users, 127.0.0.11 for docker). DNS resolution will fail if your DNS stub resolver is not at 127.0.0.53, monitor your error.log file and update the `resolver` directive to use the correct IP address if your having issues.

Don't set the resolver directive to a public DNS server, only use the localhost DNS resolver.

See: https://blog.zorinaq.com/nginx-resolver-vulns/

## How to install

1. It's recommended to start with an server without NGINX installed and without any NGINX related configuration.
2. Then, install the following NGINX packages: `apt install nginx-common libnginx-mod-http-headers-more-filter ssl-cert git` this will install the bare minimum for NGINX to operate and has smallest possible attack surface out of the box. If you need to use additional NGINX modules then you should also install those also.
3. Clone this repository `git clone https://github.com/esadcetiner/secure-nginx-config/`.
4. Replace the stock `nginx.conf` file with `cp secure-nginx-config/nginx.conf /etc/nginx/nginx.conf`.
5. Move snippets to NGINX directory `cp -r secure-nginx-config/snippets/ /etc/nginx/`.
6. Move error pages to webroot `cp -r secure-nginx-config/error_pages/ /var/www/`.
7. Create an empty webroot `mkdir -p /var/www/empty-webroot/` (Never place anything in this directory).
8. Update `ssl_trusted_certificate` directive inside `nginx.conf` with the path to your certificate file for OCSP stapling. Please make sure your certificate supports OCSP stapling.
9. You should now be done now. Please [consult the documentation](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/docs.md) on information about the included snippets and additional features.

## Additional resources:

This configuration template is a good starting point for configuring NGINX securely, but it's not a silver bullet. You can find additional resources below to ensure your configuring NGINX properly.

[**Yandex Gixy:**](https://github.com/yandex/gixy) Yandex Gixy is a static analysis tool for Nginx, it can detect misconfigurations like HTTP splitting, host header spoofing and SSRF. This project passes all tests from gixy out of the box.

[**NGINX CIS Benchmark:**](https://www.cisecurity.org/benchmark/nginx) The CIS NGINX benchmark is a community compiled list of recommendations for a secure NGINX deployment, many of the recomendations are already applied here.

[**Pitfalls and Common Mistakes:**](https://web.archive.org/web/20220505132803/https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/) This NGINX blog post covers some common mistakes made by both new and inexperienced users, some of these are prevented by secure by default design.
