# Authorization Testing

## Testing directory traversla/file include (aka ../)
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

## Testing for privilege escalation
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

## Testing for insecure direct object references
Accessing resources (db entries, files etc) directly by modifying the value of a parameter \
It is used to directly point an object \
Map out all the locations where user input is used to reference to objects directly (eg locations where user input is used to access a database row) \
Best way : 2 or more users to cover different objects and functions 
- eg. foo.bar/...?invoice=12345 -> modify this to see other user's invoices
- eg. foo.bar/changepassword?user=someuser -> modify this to change other user's password
- eg. foo.bar/...?menuitem=12 -> modify this to access restricted menus
