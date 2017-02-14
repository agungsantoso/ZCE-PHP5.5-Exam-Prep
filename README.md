# ZCE PHP 5.5 Exam Prep

## TOC
- [Object-Oriented Programming](#object-oriented-programming)
    - [Instantiation](#instantiation)
    - [Modifiers/Inheritance](#modifiers/inheritance)
    - [Interfaces](#interfaces)
    - [Exceptions](#exceptions)
    - [Autoload](#autoload)
    - [Reflection](#reflection)
    - [Type Hinting](#type-hinting)
    - [Class Constants](#class-constants)
    - [Late Static Binding](#late-static-binding)
    - [Magic (_*) Methods](#magic-(_*)-methods)
    - [Instance Methods & Properties](#instance-methods-&-properties)
    - [SPL](#spl)
    - [Traits](#traits)
- [Security](#security)
    - [Configuration](#configuration)
    - [Session Security](#session-security)
    - [Cross-Site Scripting](#cross-site-scripting)
    - [Cross-Site Request Forgeries](#cross-site-request-forgeries)
    - [SQL Injection](#sql-injection)
    - [Remote Code Injection](#remote-code-injection)
    - [Email Injection](#email-injection)
    - [Filter Input](#filter-input)
    - [Escape Output](#escape-output)
    - [Encryption, Hashing algorithms](#encryption,-hashing-algorithms)
    - [File Uploads](#file-uploads)
    - [Database Storage](#database-storage)
    - [Avoid Publishing Your Password Online](#avoid-publishing-your-password-online)

## Object-Oriented Programming

### Instantiation


### Modifiers/Inheritance


### Interfaces


### Exceptions


### Autoload


### Reflection


### Type Hinting


### Class Constants


### Late Static Binding


### Magic (_*) Methods


### Instance Methods & Properties


### SPL


### Traits



## Security

### Configuration

Always keep up to date, unless you have very strong reason.

1.	Errors and warnings
    * Production:
    ```
    display_errors: Off
    log_errors: On
    error_reporting: E_ALL & ~E_DEPRECATED & ~E_STRICT
    ```
    * Development:
    ```
    error_reporting: E_ALL
    ```
2.	Disabling functions and classes
    * use `disable_functions` & `disable_classes`
    * function to disable:
        * `exec`
        * `passthru`
        * `shell_exec`
        * `system`
    * class to disable:
        * `DirectoryIterator`
        * `Directory`
3.	PHP as an Apache module
    * setup user for Apache
    * use `open_basedir` to limit directory access
4.	PHP as a CGI binary
    * `cgi.force_redirect` make php can’t be accessed directly from url
    * `doc_root`
    * `user_dir`

### Session Security

1.	Session Hijacking
    * an attacker gets a hold of a session identifier and is able to send requests as if they were that user.
    * To prevent, set below directive to `On`
        * `session.cookie_secure`
        * `session.cookie_httponly`
2.	Session Fixation
    * an attacker explicitly sets the session identifier of a session for a user.
    * call `session_regenerate_id()` everytime privilage change
    * set below directive to `On`
        * `session.use_strict_mode`
        * `session.use_cookies`
        * `session.use_only_cookies`
3.	Improving Session Security
    * check IP address, remains same between calls
    * use short seesion timeout, reduce window fixation
    * provide logout that calls session_destroy

### Cross-Site Scripting

An attack where malcious code is injected onto an otherwise benign site.  

Types of XSS attacks:  
* Stored  
    opponent is able to place input into stored location on the server (ex: user comment)
* Reflected  
    opponet is able to get webiste to output something directly (ex: form fill error)
* DOM  
    attack that rest entirely in the page  

Other classification:
* Server side  
    sent from server
* Client side  
    update DOM from client

1. Mitigating XSS attack
    * Never output unescaped data, use 
        * `htmlspecialchars()`
        * `htmlentities()`
        * `strip_tags()`
    * Most safe method to escaping output `filter_var($string, FILTER_SANITIZE_STRING)`

### Cross-Site Request Forgeries

Exploit the trust that a website has in a client.  
To mitigate: generate unique and very random token.

### SQL Injection

Attackers is able to insert malcious command into a SQL statement for execution by the database.  
To mitigate use:
* `mysqli_real_escape_string()`
* `PDO::quote()`

### Remote Code Injection

An attack where an opponent is able to get the server to include and execute their code

1. Malcious system calls  
    * Function susceptible to remote code injection exploit:
        * `eval()`
        * `exec()`
        * `system()`  
    * To mitigate:
        * `escapeshellargs()`
        * `escapeshellcmd()`
        * disable them ini `php.ini`
2. `preg_replace` and `/e`
    * using `e` modifier on `preg_replace()` cause php execute result
    * to mitigate use `preg_quote()` but has side-effect of eliminating regex, you might need to escape manually
3. Gaming include and require
    * `include()` and `require()` allow posibility including files specified by url if `allow_url_fopen` is `On`
    * to mitigate 
        * set `allow_url_fopen` to `Off`
        * use `basename()` against variable to include

### Email Injection

An attack by injecting code to email field in php form.  
To mitigate:
* Make sure mail server not configured as open relay, means everybody can use it to send email 
* Close port 25 on firewall, so outside hosts unable to reach
* use `suhosin.mail_protect`
* use tarpit to slow bots down

### Filter Input

* Use `filter_var()` to filter input, list of filter can be found [here](https://secure.php.net/manual/en/filter.filters.php)
* function to check individual type of string `ctype_...()`

### Escape Output

You must make sure that output is safe for the client.  
Use `filter_var()` with `FILTER_SANITIZE_STRING` or `htmlspecialchars()`, `strip_tags()` and `htmlentities()`
1. Log files as output
    * make sure you obfuscate it 
    * don't log sensitive information

### Encryption, Hashing algorithms

_Encryption_ is two-way operation, you can encrypt and decrypt.  
_Hashing_ is one-way operation, difficult and time consuming to reverse to it's original string.

1. Hash functions 
    * don't use `MD5` or `SHA1`
    * use `password_hash()` and `crypt()` 
    * use `password_info()` to retreive information about how hash was calculated
    * use `password_need_rehash()` to check if the supplied hash implements algorithm and options provided
2. Salting passwords
    * additional string added to password
    * `password_hash()` automatically add it if not supplied
    * `crypt()` not automatically generate a salt
3. Checking password
    * use `password_verify()` to be safed from timing attack when comparing hash created by `password_hash()`
    * `hash_equals()` is safe way to compare against password generated using `crypt()`
4. A Quick note on error messages
    * give less information on sign in error message, don't say just incorrect username, but should be username or password incorrect

### File Uploads

* treat `$_FILES[]` superglobal as supicious
* use `is_uploaded_file()` to make sure file is actually uploaded
* use `move_uploaded_file()` to move it from temporary directory to final location
* when referring to a file use `basename()`
* don't trust MIME supplied by user, use `finfo_file()` otherwise
* use `getimagesize()` to confirm that is a valid image
* make sure folder only has access for webserver
* if you don't need the files move folder outside root

### Database Storage

* separate database for different code environments
* prevent internet from having access to database server
* use firewall to close port from outside traffic
* each application should have its own username and password
* avoid predictable username
* examine database logs from time to time

### Avoid Publishing Your Password Online

* make sure configuration file is ignore by git
* use IAM on AWS that allow access to service you want to use