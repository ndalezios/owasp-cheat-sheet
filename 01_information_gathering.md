# Information Gathering
## Search engine discovery for information leakage
## Fingerprint Web Server
## Review Webserver Metafiles for information leakage
## Enumerate Application on Webserver
## Review webpage comments and metadata for information leakage
## Identify application entry points
## Map execution paths through application
## Fingerprinting Web Application Framework
### Tools
- whatweb
- BlindElephant
## Fingerprinting Web Application
WordPress, phpBB, MediaWiki etc. as application components \
known headers, cookies & directory structures \
specific files and folders (eg. wp-admin) \
source code – meta name=”generator” content=”WordPress 3.9.2” \
dirbusting – brute force with folder and file names and monitor http responses in order to enumerate server contents \
check robots.txt 
### Tools
- whatweb
- BlindElephant
- wappalyzer
- FuzzDB wordlists of predicatble files/folders
## Map Application Architecture
It is important to map the network and application architecture as it can vary from a simple webapp and a single server to a complex one (online banking) with multiple servers involved. \
A complex one usually need 
- multiple DMZs 
- reverse proxy
- frontend web server
- Application server
- Database server
- LDAP server
Tester assumes that there is a single server and starts asking simple questions (is there a firewall protecting the server?)
- web server banner analysis to detect a reverse proxy in front of web server
- web server answers to requests and comparing responses with the expected 404 but different error message appears
- network load balancer detector – perform multiple requests and examine if the requests are going to the same or different servers
- which database server is being used
