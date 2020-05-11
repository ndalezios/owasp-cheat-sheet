# Configuration and Deployment Management Testing
## Test Network Infrastructure Configuration
- determine different elements that make up the infrastructure
- review all elements for known vulnerabilities
- review all administrative tools
- review authentication system
- maintain a list of defined ports required for the application
## Test Application Platform Configuration
Typical servers contain a lot of functionality – although not essential elements should be removed before deployment.
- comment review – web server static and dynamic contents
- server configuration guidelines
- enable only needed server modules
- handle server errors with custom pages – no errors of application returned to user
- server software is running with minimized privileges in the OS
- server software logs legitimate and non legitimate access and errors
- server is configured to handle overloads and prevent DoS attacks
- encrypt and use strong password when exporting keys
- maintain restricted access to configuration and keys
## Logging
Application logs produce debug output
### Server and Application logs
1. do they contain sensitive information
2. are they stored on a dedicated server
3. can log usage generate DoS
4. how they are rotated and reviewed
5. are the data being logged validated before logging
Logs often contain :
- debug information
- stack traces
- usernames
- system component names
- internal IP addresses
- email addresses, names, phone numbers
- business data

Logs should always be stored on a separate partition

## Test file extensions handling for sensitive information
Standard extensions provide information about the underlying technologies
Extension checking to files being uploaded
- which file extensions are returned as text and which not
- which cause execution on the server side (eg. .pl for perl)
### Mitigation?
Verify directories that allow script execution
never return extensions such as .inc .asa .conf etc
### Tools
- Nessus
- Nikto2
## Review old, backup and unreferenced files for sensitive information
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
### Tools
- Nessus
- Nikto2
- Wikto
### Spidering tools
- ~~sam spade~~
- Spike proxy
### Remediation
Forbid :
- Server inplace editing
- Check other activity (automated snapshots)
- Configuration management
- Never create files under the directory tree
- Deny access to snapshots directories
## Enumerate infrastructure and application admin interfaces
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
### Tools
- Dirbuster
- THC Hydra
- Dictionaries(net sparker)
- Default password lists
## Test HTTP methods
HTTP TRACE - Cross Site Tracing (XST) is a form of XSS
From the 8 HTTP methods (HEAD, GET, POST, PUT, DELETE, TRACE, OPTIONS, CONNECT) 4 are dangerous:
- PUT - clients uploads file to server
- DELETE - client deletes a file from server
- CONNECT - use the web server as a proxy
- TRACE - echoes to client whatever string is sent to server (used mostly for debugging) - XST

Many frameworks treat HEAD as GET
There are also arbitary methods such as CATS, JEFF \
OPTIONS method requests information about the communication options available
## Test HTTP Strict Transport Security
HSTS header informs everyone that all exchanged traffic must always sent over __https__
- max_age : the number of seconds that the browser should automatically convert all _http_ requests to _https_
- include subdomains

Security issues that can be produced :
1. Attackers sniffing the network traffic (through unencrypted channel)
2. Attackers exploit with mitm attacks (if accepting untrusted certificates)
3. Users' mistake (use http instead of https)

### Check for HSTS
`curl -s -D domain | grep Strict`

## Test Rich Internet Application (RIA) cross domain policy
Oracle, Java, Flash & Silverlight have adopted _crossdomain.xml_ to allow cross domain access to data. \
Poorly configured access for crossdomain data can enable
1. Cross site request forgery attacks
2. Access sensitive data
- crossdomain.xml
- clientaccesspolicy.xml

Master policy files are located at domain's root \
- Abuse by overlying policy
- Generate responses that may be treated as policy files
- Upload files that may me treated as policy files
- Try to retrieve policy files
### Tools
- Nikto
- Zed attack proxy
- W3af
