# Session Management Testing
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

### Tests
The tester must know URLs in the authenticated area. If he posseses valid credentials he can assume both roles (attacker and victim). If he doesn't, a real attack has to be organized (_social engineering_)

#### Steps of a test case
- set u = http://......../action
- build an html page containing the http request for _u_ (dpecify all parameters)
- make sure the valid user is logged in the application
- induce him into following _u (the link)_
- observe the result (_did the web server execute the request?_)

### Tools
- WebScarab Spider
- CSRF Tester (OWASP)
- Cross Site Requester (http://bl0g.yehg.net/)
- Cross Frame Louder (http://bl0g.yehg.net/)
- Pinata-csrf-tool

## Testing for logout functionality
Reducing to a minimum the lifetime of the session tokens decreases the likelihood of a successfull session hijacking attack.\
Secure session termination requires
- availability of user interface controls that allow the user to manually log out
- session timeout after a given ammount of time
- proper invalidation of server side session state

A common mistake is that upon session termination the client side session token is set to a new value while the server side remains active and can be reused.

Single Sign On (SSO) system instead of application specific auth schemes causes the coexistence of multiple sessions which have to be terminated separately.

### Tests
- Testing for logout user interface. View each page from the prespective of a user who has the intention to log out (eg. Logout button)
- Testing for server side session termination
  1. Store cookie values that are used to identify a session
  2. Log out and observe session cookies
  3. Navigate to a page visible only to authenticated users
  4. If new cookie session cookie is set, restore the old value and reload page
- Testing for session timeout - determine timeout value by sending delayed requests
- Testing for session termination in SSO systems by performing a log out to a specific application

### Tools
Burp Suite - Repeater

## Test Session Timeout
All applications should implement idle (inactivity) timeout. Session timeout management and expiration must be enforced on server side. After timeout is expired, the user's session must be invalidated and delete every data stored on the client. \
All session tokens must be properly destroyed and proper controls are forced on the server in order to prevent reuse of these tokens.

### Tests
- Is the timeout enforced by the client of by the server?
  If cookie is _persistent_ (stores data about time, eg login time, last access, expiration date) timeout is enforced by the client  (modify this)

## Testing for Session Puzzling
Session variable overloading vulnerability occurs when an application uses the same session variable for more than one purpose. An attacker can potentially access pages in an order unanticipated by the developers, so that the session is set in one context and then used in another. \
For example,an authentication bypass attack vector could be executed by accessing a publicly accessible entry point (such as oassword recovery page) that populates the session with an identical session variable.

### Tests
Enumerate all sesison variables used by the application and in which context they are valid. \
In the password recovery entry point, the user could be requested to provide username, email etc. \
The session might populate these values

## Testing for Reflected Cross Site Scripting
It is not persistent and only impacts users who open a maliciously crafted link. \
Unvalidated input is sent through requests back to the client. Attacker creates and tests an offending URI (social engineering). Attacker's code is usually in Javascript. Commot attacks install key loggers, steal victim cookies, steal clipboard content, change page content etc.

In some case

