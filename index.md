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
Firstly, identify alternative channels such as
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

_Does an alternative channel has any extra functionality (eg. password reset)_
_Alternative channels should always be mentioned in test report even as "information only"_
# AUTHORIZATION TESTING
# Testing directory traversla/file include (aka ../)
2 different stages
- Input vectors enumeration
  - Are there request parameters that could be used for file related operations?
  - Are there any unusual file extensions?
  - Are there any interesting variable names (?item=......, file=......, ?home=...... etc)
  - Identify cookies for dynamic page generation or templates (TEMPLATE = flower, TEMPLATE = GreenDotRed etc)
- Testing techniques
  - ?item=../../../../etc/passwd
  - Cookie:.....PSTYLE=../../../../etc/passwd
  - Include files and scripts on external website
    - ?file=http://......./malicious.txt
    - ?home=main.cgi
  - Usual encodings
    - . -> dot
    - %00 -> null
  - Try encodings
    - url encoding and double url encoding
    - unicode/utf8 encoding
      - ../
      - .. \ and more
   - Extraneous parent directory markers that may or may not exist
   - Windows API
   - Windows UNC filepaths (used to reference files on SMB)
     - \\server\path\file.abc
     - \\?\server\path\file.abc
   - Windows NT device Namespace
   - Drive letters (C: etc)
   - Volumes (\\.\GLOBALROOT\Device\HarddiskVolume1\ , \\.\CDROM0, etc)
   - Grep source for 
     - PHP - include(), include_once(), require(), require_once(), fopen(), readfile()
     - Java - java.io.File(),java.io.FileReader()
     - ASP - include file, include virtual
   - Analyze functions - eg replace(filename, "/", "\\") -> file= ..//..//
## Tools
- DotDotPwn - The directory traversla fuzzer
- WFuzz - Path traversal Fuzz strings
# Testing for bypassing authorization schema
- Is it possible to access a resource if the user is not authenticated?
- Is it possible to access a resource after logout?
- Is it possible to access functions and resources that should not be accessible to a user with different privilege?

_eg. adduser.jsp is part of an administration menu. Is it possible to access it by requesting the URL?_

# Testing for privilege escalation
Escalating privileges from one stage to another
- Vertical - to more privileged accounts
- Horizontal - to similar privileged accounts

Users
- create information in database (add contact, make payment etc)
- receive information (account statements, order details etc)
- delete information (drop user, delete messages etc)

All the above information should be recorded
The tester should access these functions as another user
_?.....userID=fakeuser&role=3&group=grp001_

# Testing for insecure direct object references
Accessing resources (db entries, files etc) directly by modifying the value of a parameter \
It is used to directly point an object \
Map out all the locations where user input is used to reference to objects directly (eg locations where user input is used to access a database row) \
Best way : 2 or more users to cover different objects and functions 
- eg. foo.bar/...?invoice=12345 -> modify this to see other user's invoices
- eg. foo.bar/changepassword?user=someuser -> modify this to change other user's password
- eg. foo.bar/...?menuitem=12 -> modify this to access restricted menus
# SESSION MANAGEMENT TESTING
Mechanism by which a web app controls user's state
__HTTP is stateless__
Multiple user requests have to be associated accross a session
# Testing session management schema
Apps must avoid continuous authentication for each page. \
Cookies and other session tokens are created in a secure way. \
Cookies provide data to the app about who the user is, what actions have been performed so far, preferences etc. \
Tampering with cookies may result in session hijacking. Steps of the attack
- cookie collection - number of samples
- cookie reverse engineering - analysis of cookie generation algorithm
- cookie manipulation - forge a valid cookie (maybe use cookie brute forcing)

* Cookie overflow: Overflow a memory area and possibly inject malicious code
## Tests
Questions for the tester:
- Are all "Set-Cookie" directives "Secure" ?
- Cookie operations over unencrypted transport
- Can the cookie be forced over unencrypted transport?
- Are there any persistent cookies?
- Check "Expires"
- What "Cache-Control" settings are used to protect cookies?

### Cookie collection (surf the application)
- How does the application creates and manages cookies?
- How many cookies are used by the application?
- Which pages generate them and modifies them?
- Which parts require cookies in order to accessed and utilized \
__Access a page with and without a cookie__
_Everything must be noted down to a spreadsheet_

### Session Analysis
Common session tokens are Cookies, SessionIDs and Hidden Fields \
All these should be tested for randomness, uniqueness and resistance to cryptographic analysis
- Token structure and information leakage eg. "192.168.1.5:owaspuser:password:12345" can be encoded to _Hex, Base64, MD5 etc._
  - Hybrid tokens include IP, user id and encoded info
  - 32 bit token - 16 bit static and 16 bit variable data
- What parts of session id are static
- What clear text confidential information is stored
- Is there a pattern in session id (whole or partialy)
### Session ID predictability and randomness
- Compare session ids the same login conditions - eg. same username 
- Try to establish a high number of simultaneous connections in order to gather samples _in the same time window_
- Are variavle parts incremental in nature?
- Is time used as a seed (many systems use it)
- Can the next ID be deduced or guessed ?

### Cookie reverse engineering
Cookie's characteristics for secure session management
- Unpredictability with random values and cryptography
- Tamper resistance - eg. cookie "isAdmin=No - the application must append an encrypted hash of its value
- Expiration - critical cookies must be valid for a short period of time and be deleted afterwards.
- Secure flag - enable it to allow transmission only in and encrypted channel

Always record the exact time when a cookie has been obtained (time could be a part of the cookie value). \
Session id must be at least _50_ characters long.

### Tools
- Burp Sequencer
- FoundStone Cookie Digger
- Yegh's Hijack __check again__

## Testing for cookies attributes
Cookie attributes :
- secure - send only over HTTPS
- HttpOnly - cookie cannot be accessed via client side script (eg. Javascript)
- domain - it is used to compareagainst the domain of the server in which the URL is being requested (if not set default the server hostname)
- path - In addition to the domain, path that the cookie is valid
- expires - if not set, cookie is valid in the current browser session and will be deleted when the session ends.

### Tools 
- Tamper Data (Firefox addon)

## Testing for session fixation 
Occurs when application does not renew its session cookie after a successfull user authentication. Session fixation vulnerabilities can occur when :
- a web app authenticates a user without first invalidating the existing sesison id (continuing use of existing session id)
- attacker forces a known session id so, when user authenticates, attacker gains access to the session

Attacker creates a new session and records the session id. He then causes the vivtim to authenticate using the same session id.

### Tests
- Make a request (GET example.com)
- Server sends cookie and responds
- Tester sends a POST request to authenticate
- Server responses OK _but does not set a new session id or cookie_

### Tools
Hijack (numeric session hijacking tool)

## Testing for exposed session variables
Cookie, SessionID, hidden field, if exposed will enable attacker to impersonate a user. Protect for eavesdropping at all times. Each time session id data is passed between client and server, _protocol, cache, privacy and body directives_ should be examined

### Tests
- Testing for encryption and reuse of session tokens vulnerabilities. Protect from eavesdropping with SSL encryption. _Transport encryption and Session ID hashing are 2 different things._
  - Replace _https://_ with _http://_
  - Session id must be monitored as the user switches from secure to non-secure elements.
- Testing for proxies & caching vulnerabilites. Clients ofetn access applications through corporate ISPs or other proxies. Session id should never be cached and sent over unencrypted transport.
  - Cache-Control : no cache
  - Expires : 0
  - Cache-control : mae-age=0
- Testing for GET & POST vulnerabilities. GET requests should not be used. Cross site scripting (XSS) attacks are easy by sending a link to the victim. _Server should not accept GET requests (...login.asp?Login=username&Password=12345&SessionID=1234567)_
- Testing for transport vulnerabilities. All app- client interaction should be tested on:
  - how are sessiond ids transferred (GET, POST, form field)
  - are session ids always sent over encrypted transport?
  - can the app be manipulated to send session ids unencrypted?
  - what _cache-control_ directives are applied to requests and respones (regarding session ids)
  - can a POST be interchanged with GET?

## Testing for CSRF
CSRF attack forces user to execute unwanted actions on a web app he has already authenticated (with a little help from social engineering). CSRF relies on
- web browser behavior on session handling (cookies and auth information)
- knowledge by the attacker of valid web app urls
- application session management relying only on information known by the browser
- existence of HTML tags whose presence can cause immediate access to a _http/https_ resource (<img>).

If the victim has authenticated, every next request causes the cookie be automatically sent with it. 
A GET request could be originated by user
- who is using the web app
- who types url directly in the browser
- following an external link

A web app cannot distinguish the above invocations
### Scenario
- Victim surfs on a firewall web interface. User authenticates and session information is stored to a cookie
- An authenticated user can delete a single rule or all of the ("\*")
- Delete rule page uses a form with a GET request (_...../delete?rule=1_ or _...../delete?rule=*_)
- Attacker can manually submit the url, or redirect the user or access this url with an embedded image tag 
_<img src=...../delete?rule=*_

If a user is logged id in the admin interface the request will succeed. \
This attack can also succeed behind a firewall as the link must be reachable only by the victim and not the attacker.

 




