# Information Gathering
## Search engine discovery for information leakage
- __Direct__ methods - Searching indexes and content from caches
- __Indirect__ methods - Design and configuration information by searching forums,newsgroups etc.
Check _robots.txt_.If it is not updated it is possible to contain sensitive web content.
### Search operators
Use a search engine to search for
- network diagrams & configurations
- archived posts and emails by admins and devs
- login procedures and username formats
- error message content
- development, test and unstable versions

Search engines:
- [Baidu](https://www.baidu.com/)
- [BinSearch](https://www.usenet.com/binsearch/)
- [Bing](https://www.bing.com/)
- [Duck Duck Go](https://duckduckgo.com/)
- [ixQuick](https://www.startpage.com/)
- [Google](https://www.google.com/)
- [Shodan](https://www.shodan.io/)
- [PunkSpider](https://punkspider.org/)

Use _site:_ and _cache:_ operators
### Google Hacking Database
It is a list of useful search queries arranged in several categories such as
- footholds
- username files
- sensitive directories
- ...

### Tools
- [FoundStone SiteDigger](https://foundstone-sitedigger.updatestar.com/)
- Google Hacking Database
- [Stach & Liu Google Hacking Diggity Project](https://resources.bishopfox.com/resources/tools/google-hacking-diggity/attack-tools/)
- [PunkSpider](https://punkspider.org/)


## Fingerprint Web Server
- Black box - check the _Server_ field in the HTTP Response with _netcat_
- HTTP header field ordering - every webserver has different inner ordering (use _netcat_)
- Malformed requests test - send malformed requests of nonexisting pages to the server (_netcat_). Every server responds differently

_Automated tools can be used instead of using netcat each time_

### Tools
- httprint
- httprecon
- [Netcraft](https://www.netcraft.com/)
- [Desenmascarame](https://desenmascara.me/)

## Review Webserver Metafiles for information leakage
### robots.txt
- retrieve a web page and recursively traverse all the hyperlinks. _Disallow_ directive is important (which dirs are prohibited) web spiders and crawlers can intentionally ignore _Disallow_. Retrieve any _robots.txt_ from webroot with _wget_ or _curl_ 
- Analyze robots.txt with _Google Webmaster Tools_

### \<META>
\<Meta>  is located in \<head>. If there is no "\<META NAME=ROBOTS........>" the exclusion protocol is _"INDEX, FOLLOW"_. \
Web spiders can ignore the "\<META NAME=ROBOTS>"

### Tools  
- curl
- wger
- [RockSpider](https://github.com/cmlh/rockspider/)
  
## Enumerate Application on Webserver
Find out what other apps are hosted on a web server (many apps or misconfigured apps have known vulnerabilities)\
altough it's a bit uncommon for a web server to host multiple apps.
1. Different base URL - there is nothing forcing the app to start at "/" (_example.com/url1, example.com/url2_ ...). 
_So, example.com is not a meaningful page_. Or it is?
No certain way and no fix criteria. If the webserver is misconfigured may allow directory browsing. These apps may 
be referenced by other web pages and may have beed spidered and indexed (serach with _site:_ directive eg. _site:www.example.com_)
as this may reveal a non obvious application. Do some directory style searching or intelligent guessing (you could find 
a Tomcat administrative interface)
2. Non-standard ports - 80 for http and 443 for https are usually used, but arbitary TCP ports can also be used eg. _example.com:2000_.
Use a port scanner like _nmap_ (_-sV_ for service recognition) to scan the whole 64K TCP port range.
3. Virtual hosts - 1 IP to N symbolic names. Use the following techniques to identify names associated to an IP address:
   - DNS zone transfers - first determine the nameservers serving the ip. If symbolic name is known use _nsllokup, host, dig_ etc. If no
     symbolic name is known but target contains at least one, try to test that (_see page 36_)
   - DNS inverse queries
   - Web based DNS searches - Netcraft Search DNS Service. The tester may get a list of names belonging to a specific domain
   - Reverse-IP services - the tester queries a web-based application instead of a nameserver (_see page 37_)
   - Googling 
     

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
