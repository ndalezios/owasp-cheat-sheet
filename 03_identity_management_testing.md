# Identity Management Testing
## Test and validate user registration process
1. Same person register multiple times
2. Same person - different roles
3. Can identity be forged or faked
# Test account provisioning process
1. Opportunity for an attacker to create a valid account without proper identification and authorization
2. Can an administrator provision other administrators?

## Testing for account enumeration and guessable user account
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
### Tools
- WebScarab
- curl

## Testing for weak or unenforced username policy
eg. _Nikos Dalezios -> ndale, Joe Bloggs - jbloggs_
