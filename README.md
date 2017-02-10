# ZCE PHP 5.5 Exam Prep

## TOC
- [Security](#security)
    - [Configuration](#configuration)
    - [Session Security](#session-security)
    - [Cross-Site Scripting](#cross-site-scripting)

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

An attack where malcious code is injected onto an otherwise benign site