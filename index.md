# Fingerprinting Web Application Framework 
## Tools
whatweb
BlindElephant
# Fingerprinting Web Application
WordPress, phpBB, MediaWiki etc. as application components
known headers, cookies & directory structures
specific files and folders (eg. wp-admin)
source code – meta name=”generator” content=”WordPress 3.9.2”
dirbusting – brute force with folder and file names and monitor http responses in order to enumerate server contents
check robots.txt
## Tools
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
# Logging
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
# Test file extensions handling for sensitive information
Standard extensions provide information about the underlying technologies
Extension checking to files being uploaded
- which file extensions are returned as text and which not
- which cause execution on the server side (eg. .pl for perl)
## Mitigation?
Verify directories that allow script execution
never return extensions such as .inc .asa .conf etc
## Tools
- Nessus
- Nikto2
# Review old, backup and unreferenced files for sensitive information
- Renamed old versions of modified files
- Backup files and snapshots
- .tar.gz and .zip archives and ~ files
- login.asp and login.asp.old
- if *viewuser.asp* exists then search for *edituser.asp* as well
- if *app/user* exists then search for *app/admin, app/manager* etc.
- Request all enumerated directories
- Use publicly available information
  - archives of internet search engines (*1998results.asp*)
  - cached versions of google and yahoo
  - linksby third party applications
Search either by hand or by scripting search criteria
## Tools
- Nessus
- Nikto2
- Wikto
### Spidering tools
- ~~sam spade~~
- Spike proxy
### Remediation ###
Forbid :
- Server inplace editing
- Check other activity (automated snapshots)
- Configuration management
- Never create files under the directory tree
- Deny access to snapshots directories
# Enumerate infrastructure and application admin interfaces
Admin interfaces do not have always sufficient controls to protect from unauthorized access
Discover these interfaces and access functionality intended for privileged users
Test for presence of admin interfaces by :
- Directory and file enumeration
- Identifying filename of administration page
- Comments and links to page source
- Reviewing server and application documentation (default configuration and passwords)
- Publicly availably information
- Searching on alternate server ports
- Tampering parameters (GET, POST, Cookie) to enable admin interface
When discovered if everything else fails, try brute forcing
## Tools ##
- Dirbuster
- THC Hydra
- Dictionaries(net sparker)
- Default password lists
# Test HTTP methods #
HTTP TRACE - Cross Site Tracing (XST) is a form of XSS
From the 8 HTTP methods (HEAD, GET, POST, PUT, DELETE, TRACE, OPTIONS, CONNECT) 4 are dangerous:
- PUT - clients uploads file to server
- DELETE - client deletes a file from server
- CONNECT - use the web server as a proxy
- TRACE - echoes to client whatever string is sent to server (used mostly for debugging) - XST

Many frameworks treat HEAD as GET
There are also arbitary methods such as CATS, JEFF
OPTIONS method requests information about the communication options available
