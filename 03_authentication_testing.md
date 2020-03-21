# Authentication Testing
## Testing for credentials trasported over an encrypted channel
Do data travel between client and server through unencrypted channel or HTTPS
#### Encrypted is not always safe. It depends on the algorith and the robustness of keys
Always check Request headers, POST address and Referer.
## Testing for default credentials
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
### Tools
- Burp Intruder
- THC Hydra
- Brutus
- Nikto 2
## Testing for weak lock out mechanism
Usually lockout is excuted after 3 to 5 unsuccessfull attemps in order to
- Protect accounts from unauthorized access
- Protect users from being denied authorized access
Tester need to access an account that can afford to lock.
Examine what happens when you enter
- Icorrect password 3 times and then the correct one
- Icorrect password 4 times and then the correct one
- Icorrect password 5 times and then the correct one
- Also try time gaps 5,10, 15 minutes)
## Testing for bypassing authentication schema
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
### Tools
- Webscarab
- Webgoat

(see section Gray Box Testing)

## Testing for vulnerable Remeber Password functionality
- Browser _Remember me_ functionality
- API custom _Remember me_ functionality
- Passwords inside a cookie
- Credentials are sent during login

## Testing for browser cache weakness
__Back button is history, not cache__
- Check with a proxy so that every page or request with sensitive data does not cache them
- Check HTTP headers
  - Cache control : no-cache
  - Expires : 0
  - Pragma : no-cache
### Tools
- Cache Viewer 2 (Firefox addon)

## Testing for weak password policy
- What characters are permitted and forbidden for a password (length, upper/lower case, digits etc)
- How often a user has to change password
- How often a user can reuse a password
- How different must the next password be
- Can password contain username

## Testing for weak security question/answer
Upon account creation it is required the user to select _pregenerated_ or _custom_ security questions 
- Pregenerated - obtain a list of questions by creating a new account
- Custom - Does the system allow creation of custom questions
- Can the be brute forcable?
- How many guesses do you have

## Testing for weak password change or reset functionalities
- Check if non admin users can change or reset password for other accounts
- Is the password change/reset vulnerable to [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery)?
- What information is required to reset a password (eg. security questions)
- How passwords are communicated to the user?
  - Plain text showing
  - Forcing to change
- Are reset passwords generated randomly?
- Does this process requests information before changing password?
- Is the old password requested in order to complete the change?

## Testing for weaker authentication in alternative channel
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
