# Fast and Secure NGINX Configuration Template - By Esad Cetiner

This configuration template aims to be a baseline for building a secure and performant NGINX configuration. This can be used either as-is, or used as a reference guide to harden your own configuration. Some config options are commented out by default for various reasons or are split into snippets to optionally include in your server blocks.

## Features

- HTTPs redirection
- HTTPs only mode with Strict Transport Security (Disabled by default)
- Modern encryption with A/A+ on [ssllabs.com](https://www.ssllabs.com/)
- Post-Quantum Cryptography
- A/A+ on [security-headers](https://securityheaders.com/) when using the [security header template file](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/security-headers.template)
- Protections against common misconfigurations
- Clickjacking protection (Disabled by default)
- XSS protection (Disabled by default)
- Prevent host header spoofing
- DoS mitigations (Rate limiting rules will need to be tuned to your needs)
- [Hide NGINX](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/hide-nginx.conf) and minimize information leakage via headers
- Disable page indexing for Search Engines ([Disabled by default](https://github.com/EsadCetiner/Secure-Nginx-Config/blob/main/snippets/no-robots.conf))
- Enhanced performance
- Prioritize ChaCha20 encryption for clients that don't support AES-NI
- 0-RTT (Disabled by default)
- Brotli Compression (Disabled by default)

## Requirements

- ECC certificates are recommended, they are more secure and performant than RSA.
- Nginx more_set_headers module installed
- This config has only been tested against Ubuntu/Debian, although this should techincally work for any Linux distro.

## Before you install

- [NGINX can be vulnerable to DNS spoofing under very highly specific conditions](https://blog.zorinaq.com/nginx-resolver-vulns/), this template configures NGINX's resolver to use your localhost sub resolver (which is located at 127.0.0.53 for most users or 127.0.0.11 for docker) to prevent this problem. DNS resolution will fail if your DNS stub resolver is not at 127.0.0.53, monitor your error.log file and update the `resolver` directive to use the correct IP address if your having issues.
- You should read and test the template configs provided, some settings may break NGINX and need to adjusted/disabled.

### OCSP stapling support

A major certificate authority [Let's Encrypt has ended support for OCSP (including OCSP stapling) on August 2025](https://letsencrypt.org/2024/12/05/ending-ocsp/) due to the operational costs and poor adoption of OCSP stapling. Other certificates authories may follow, so OCSP stapling is not enabled out of the box. It can be enabled by uncommenting the OCSP settings in `nginx.conf`. 

### Legacy DHE support

Modern clients make use of ECDHE and don't need Diffie Helman with RSA, by default support is not enabled and DHE ciphers are not allowed.

## Additional resources:

This template is a good starting point, but is not exhaustive of all the pitfalls that come with NGINX. The following resources can further help to secure and harden your NGINX configuration.

[Gixy-Next:](https://github.com/MegaManSec/Gixy-Next) is a fork of [Yandex Gixy](https://github.com/yandex/gixy), a static analysis tool for NGINX. It can detect common misconfigurations such as HTTP splitting, Host header spoofing, SSRF, and Directory Traversal. Yandex Gixy is currently unmaintained, Gixy-Next is actively maintained and has more functionality than Yandex Gixy.

[NGINX CIS Benchmark:](https://www.cisecurity.org/benchmark/nginx) The CIS NGINX benchmark is a community compiled list of recommendations for configuring NGINX securely.

[Pitfalls and Common Mistakes:](https://web.archive.org/web/20220505132803/https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/) This NGINX blog post covers some common mistakes made by both new and inexperienced users.

[ModSecurity WAF:](https://modsecurity.org) ModSecurity is an open source Web Application Firewall that can protect against a wide range of attacks, including the OWASP top 10, common misconfigurations, and data leakage. ModSecurity requires a ruleset to be effective such as [OWASP CRS](https://coreruleset.org/). A WAF requires active false positive tuning and may be too much work for lower threat models.

[CrowdSec:](https://www.crowdsec.net/) CrowdSec is an open source log analysis tool with Crowdsourced IP threat intelligence. It scans your logs for common indicators of attacks, bans the offending IP address, then shares that information with other CrowdSec users. All CrowdSec are given an free community IP blocklist that is powered from the signals of all CrowdSec users.
