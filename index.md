# Fingerprinting Web Application Framework 
## Tools
- whatweb
- BlindElephant
# Fingerprinting Web Application
WordPress, phpBB, MediaWiki etc. as application components \
known headers, cookies & directory structures \
specific files and folders (eg. wp-admin) \
source code – meta name=”generator” content=”WordPress 3.9.2” \
dirbusting – brute force with folder and file names and monitor http responses in order to enumerate server contents \
check robots.txt 
## Tools
- whatweb
- BlindElephant
- wappalyzer
- FuzzDB wordlists of predicatble files/folders
# Map Application Architecture
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
# Test Network Infrastructure Configuration
- determine different elements that make up the infrastructure
- review all elements for known vulnerabilities
- review all administrative tools
- review authentication system
- maintain a list of defined ports required for the application
# Test Application Platform Configuration
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
# Logging
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
### Remediation
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
## Tools 
- Dirbuster
- THC Hydra
- Dictionaries(net sparker)
- Default password lists
# Test HTTP methods
HTTP TRACE - Cross Site Tracing (XST) is a form of XSS
From the 8 HTTP methods (HEAD, GET, POST, PUT, DELETE, TRACE, OPTIONS, CONNECT) 4 are dangerous:
- PUT - clients uploads file to server
- DELETE - client deletes a file from server
- CONNECT - use the web server as a proxy
- TRACE - echoes to client whatever string is sent to server (used mostly for debugging) - XST

Many frameworks treat HEAD as GET
There are also arbitary methods such as CATS, JEFF \
OPTIONS method requests information about the communication options available
# Test HTTP Strict Transport Security
HSTS header informs everyone that all exchanged traffic must always sent over __https__
- max_age : the number of seconds that the browser should automatically convert all _http_ requests to _https_
- include subdomains

Security issues that can be produced :
1. Attackers sniffing the network traffic (through unencrypted channel)
2. Attackers exploit with mitm attacks (if accepting untrusted certificates)
3. Users' mistake (use http instead of https)

## Check for HSTS
`curl -s -D domain | grep Strict`
# Test Rich Internet Application (RIA) cross domain policy
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
## Tools
- Nikto
- Zed attack proxy
- W3af
# Test and validate user registration process
1. Same person register multiple times
2. Same person - different roles
3. Can identity be forged or faked
# Test account provisioning process
1. Opportunity for an attacker to create a valid account without proper identification and authorization
2. Can an administrator provision other administrators?
# Testing for account enumeration and guessable user account
Useful for password brute forcing if given a valid username \
_Applications often reveal if a username exists on system_ \
Obtain a list of users of a system by brute forcing default credentials
- Test for valid user with wrong password
- Test for a nonexistent username
- Analyze URLs foo.com/index?user=baduser&error=0, foo.com/index?user=gooduser&error=1
- URI probing - different responses for different directory requests (existing or not)
  - foo.com/account1 - 403 Forbidden
  - foo.com/account2 - 404 Not Found
- Analyze web page titles (_invalid user / invalid authentication_)
- Analyze message from recovery facility (eg. password recovery) (_valid or invlalid username_)
- Friendly 404 error message (_200 OK with image_) - user does not exists
- Patterns for users _U001, U002, ..._ Are these ids associated with credit card numbers or real names (_eg. ndale_)
### Attention, accounts may be blocked
## Tools
- WebScarab
- curl
# Testing for weak or unenforced username policy
eg. _Nikos Dalezios -> ndale, Joe Bloggs - jbloggs_
# Testing for credentials trasported over an encrypted channel
Do data travel between client and server through unencrypted channel or HTTPS
### Encrypted is not always safe. It depends on the algorith and the robustness of keys
Always check Request headers, POST address and Referer.
# Testing for default credentials
When an application interface is identified (eg. Cisco router web interface) __check for default usernames and passwords__.
if not successfull, at least find a vslid username. \
Common usernames are admin, administrator, root, system, guest, operator, super, test, test1  etc.
- Application admin users are often named after the application or organization (google/google)
- Contacts are often used as usernames (John Doe -> jdoe@example.com)
- Search social media for administrator's name
- Check usernames with blank passwords
- View html source and comments
- Check urls (startupurl=/admin.asp else index.asp)
- New account and default password
- Look for user registration page to understand password format and length
- How are usernames generated?
- Create many accounts quickly
## Tools
- Burp Intruder
- THC Hydra
- Brutus
- Nikto 2
# Testing for weak lock out mechanism
Usually lockout is excuted after 3 to 5 unsuccessfull attemps in order to
- Protect accounts from unauthorized access
- Protect users from being denied authorized access
Tester need to access an account that can afford to lock.
Examine what happens when you enter
- Icorrect password 3 times and then the correct one
- Icorrect password 4 times and then the correct one
- Icorrect password 5 times and then the correct one
- Also try time gaps 5,10, 15 minutes)
# Testing for bypassing authentication schema
Skipping login page and directly calling an internal page that is supposed to be accessed only after authentication.\
It is often possible to bypass auth by tampering with requests (_tricking the web app to think that the user is already authenticated_) by modifying URL parameters, manipulating a form, counterfeiting sessions etc. Test by
- Direct page request (forced browsing) through address bar or curl
- Parameter modification
- Session ID prediction
- SQL injection
### Parameter modification
Change _http://foo.bar/page=index?authenticated=no_ to _http://foo.bar/page=index?authenticated=yes_
### Session ID prediction
If session id is predictable, a malicious user could be able to find a valid session ID. Cookie stored values may change only partially (_restrict brute force_)
## Tools
- Webscarab
- Webgoat

(see section Gray Box Testing)
# Testing for vulnerable Remeber Password functionality
- Browser _Remember me_ functionality
- API custom _Remember me_ functionality
- Passwords inside a cookie
- Credentials are sent during login
# Testing for browser cache weakness
__Back button is history, not cache__
- Check with a proxy so that every page or request with sensitive data does not cache them
- Check HTTP headers
  - Cache control : no-cache
  - Expires : 0
  - Pragma : no-cache
## Tools
- Cache Viewer 2 (Firefox addon)
# Testing for weak password policy
- What characters are permitted and forbidden for a password (length, upper/lower case, digits etc)
- How often a user has to change password
- How often a user can reuse a password
- How different must the next password be
- Can password contain username
# Testing for weak security question/answer
Upon account creation it is required the user to select _pregenerated_ or _custom_ security questions 
- Pregenerated - obtain a list of questions by creating a new account
- Custom - Does the system allow creation of custom questions
- Can the be brute forcable?
- How many guesses do you have
# Testing for weak password change or reset functionalities
- Check if non admin users can change or reset password for other accounts
- Is the password change/reset vulnerable to [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery)?
- What information is required to reset a password (eg. security questions)
- How passwords are communicated to the user?
  - Plain text showing
  - Forcing to change
- Are reset passwords generated randomly?
- Does this process requests information before changing password?
- Is the old password requested in order to complete the change?
# Testing for weaker authentication in alternative channel
Firstly, identify alternative channels suck as
- other website or web app
- mobile app
- accessibility optimized version
- alternative country and language
- parallel web sites (may have different functionality)
- development versions
- site versions without cookies, javascript, flash or java plugins
Indetify alternative channels by
- Reading Home, Contact, FAQ etc
- HTTP proxy logs search for strings "mobile", "android", "ipad", "mobile", "auth", "sso" etc
- Use search engines to find different sites from the same organization
- Does an alternative channel has any extra functionality (eg. password reset)

_Alternative channels should always be mentioned in test report even as "information only"_

 




