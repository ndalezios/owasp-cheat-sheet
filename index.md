# Fingerprinting Web Application Framework 
Tools
whatweb
BlindElephant
# Fingerprinting Web Application
WordPress, phpBB, MediaWiki etc. as application components
known headers, cookies & directory structures
specific files and folders (eg. wp-admin)
source code – meta name=”generator” content=”WordPress 3.9.2”
dirbusting – brute force with folder and file names and monitor http responses in order to enumerate server contents
check robots.txt
Tools
whatweb
BlindElephant
wappalyzer
FuzzDB wordlists of predicatble files/folders
# Map Application Architecture
It is important to map the network and application architecture as it can vary from a simple webapp and a single server to a complex one (online banking) with multiple servers involved. 
A complex one usually need multiple DMZs 
reverse proxy
frontend web server
Application server
Database server
LDAP server
Tester assumes that there is a single server and starts asking simple questions (is there a firewall protecting the server?)
web server banner analysis to detect a reverse proxy in front of web server
web server answers to requests and comparing responses with the expected 404 but different error message appears
network load balancer detector – perform multiple requests and examine if the requests are going to the same or different servers
which database server is being used
# Test Network Infrastructure Configuration
determine different elements that make up the infrastructure
review all elements for known vulnerabilities
review all administrative tools
review authentication system
maintain a list of defined ports required for the application
# Test Application Platform Configuration
Typical servers contain a lot of functionality – although not essential elements should be removed before deployment.
comment review – web server static and dynamic contents
server configuration guidelines
enable only needed server modules
handle server errors with custom pages – no errors of application returned to user
server software is running with minimized privileges in the OS
server software logs legitimate and non legitimate access and errors
server is configured to handle overloads and prevent DoS attacks
encrypt and use strong password when exporting keys
maintain restricted access to configuration and keys
Logging
Application logs produce debug output
Server and Application logs
do they contain sensitive information
are they stored on a dedicated server
can log usage generate DoS
how they are rotated and reviewed
are the data being logged validated before logging
Logs often contain
debug information
stack traces
usernames
system component names
internal IP addresses
email addresses, names, phone numbers
business data
Logs should always be stored on a separate partition


