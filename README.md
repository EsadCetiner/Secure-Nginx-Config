# Fast and Secure NGINX Configuration Template - By Esad Cetiner
This project's goal is to provide a fast and secure by default NGINX configuration template and encourage secure design that minimizes the chances of misconfigurations and their impacts. Code snippets are also provided for additional functionality for you to include in your server blocks. Note that some features are disabled by default, you can enable them by uncommenting the relevant code in `nginx.conf`.

## Features
- HTTPs redirection
- HTTPs only mode with Strict Transport Security (Disabled by default)
- Modern encryption with A/A+ on [ssllabs.com](https://www.ssllabs.com/)
- A/A+ on [security-headers](https://securityheaders.com/) when using the [security header template file](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/security-headers.template)
- Protections against common misconfigurations
- [Restrict access to sensitive files](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/protect-sensitive-files.conf)
- Clickjacking protection (Disabled by default)
- XSS protection (Disabled by default)
- Prevent host header spoofing
- DoS mitigations (Rate limiting rules will need to be tuned to your needs)
- [Hide NGINX](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/hide-nginx.conf) and minimize information leakage via headers
- Disable page indexing for Search Engines ([Disabled by default](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/no-robots.conf))
- Enhanced performance
- Prioritize ChaCha20 encryption for clients that don't support AES-NI (Disabled by default)
- 0-RTT (Disabled by default)
- Brotli Compression (Disabled by default)

## Requirements
- ECC certificates are recommended, they are more secure and performant than RSA.
- Nginx more_set_headers module installed
- This has only been tested on Ubuntu/Debian so Ubuntu/Debian is recommended, although there is nothing stopping you from using this on Arch/Cent OS etc

## Before you install

NGINX can be vulnerable to DNS spoofing under specific conditions (Although rare), the resolver directive within the http block is set to use an localhost DNS stub resolver (which is 127.0.0.53 for most users, 127.0.0.11 for docker) to prevent this problem. DNS resolution will fail if your DNS stub resolver is not at 127.0.0.53, monitor your error.log file and update the `resolver` directive to use the correct IP address if your having issues.

See: https://blog.zorinaq.com/nginx-resolver-vulns/

## How to install

1. It's recommended to start with an server without NGINX installed and without any NGINX related configuration.
2. Then, install the following NGINX packages: `apt install nginx-common libnginx-mod-http-headers-more-filter ssl-cert git` this will install the bare minimum for NGINX to operate and has smallest possible attack surface out of the box. If you need to install additional NGINX modules then make sure you keep those to a minimum.
3. Clone this repository `git clone https://github.com/esadcetiner/secure-nginx-config/`.
4. Replace the stock `nginx.conf` file with `cp secure-nginx-config/nginx.conf /etc/nginx/nginx.conf`.
5. Move snippets to NGINX directory `cp -r secure-nginx-config/snippets/ /etc/nginx/`.
6. Move error pages to webroot `cp -r secure-nginx-config/error_pages/ /var/www/`.
7. Create an empty webroot `mkdir -p /var/www/empty-webroot/` (Never place anything in this directory).
8. You should now be done now. Please [consult the documentation](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/docs.md) on information about the included snippets and additional features.
9. (Optional) If you need to enable Diffie-Helman Ephemeral for legacy clients, then you must generate an Diffie-Helamn parameters file(`openssl dhparam -out /etc/ssl/dhparams-4096.pem 4096`) and uncomment the `ssl_dhparam` directive in `nginx.conf` to [avoid logjam](https://weakdh.org/sysadmin.html). Only ECDHE is enabled for key exchange by default for better performance and security.

Once you've finished the installation, please monitor your logs. Although this config is designed to be as painless as possible, there still is a possibility something might break.

### OCSP stapling

A major certificate authority [Let's Encrypt is ending support for the OCSP protocol (And by extention OCSP stapling) on August 2025](https://letsencrypt.org/2024/12/05/ending-ocsp/) due to the operational costs associated with the protocol and poor adoption of OCSP stapling. It's likely that other certificate authorities will follow suit. OCSP stapling is no longer a requirement to use this template following this news, you can uncomment the OCSP stapling settings in `nginx.conf` if you still wish to use this feature.

## Additional resources:

This configuration template is a good starting point for configuring NGINX securely, but it's not a silver bullet. You can find additional resources below to ensure NGINX is configured properly along with tools that give you extra hardening on top of NGINX.

[Yandex Gixy:](https://github.com/yandex/gixy) Yandex Gixy is a static analysis tool for Nginx, it can detect misconfigurations like HTTP splitting, host header spoofing and SSRF. This project passes all tests from gixy out of the box.

[NGINX CIS Benchmark:](https://www.cisecurity.org/benchmark/nginx) The CIS NGINX benchmark is a community compiled list of recommendations for a secure NGINX deployment, many of the recomendations are already applied here.

[Pitfalls and Common Mistakes:](https://web.archive.org/web/20220505132803/https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/) This NGINX blog post covers some common mistakes made by both new and inexperienced users, some of these are prevented by secure by default design.

[ModSecurity WAF:](https://modsecurity.org) ModSecurity is an open source Web Application Firewall that can protect against a wide range of attacks, including the OWASP top 10, common misconfigurations, and data leakage. ModSecurity requires a ruleset to be effective, the most popular [one is OWASP CRS](https://coreruleset.org/).

[CrowdSec:](https://www.crowdsec.net/) CrowdSec is an open source log analysis tool with Crowdsourced IP threat intelligence. It scans your logs for common indicators of attacks (i.e brute force attacks, multiple 404 errors, scanning for sensitive files, common exploits, etc), bans the offending IP address, and then shares that information with other CrowdSec users. All CrowdSec are given an free community IP blocklist that is powered from the signals of all CrowdSec users.
