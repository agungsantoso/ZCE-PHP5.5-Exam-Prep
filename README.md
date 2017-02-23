v
<!-- TOC -->

- [Object-Oriented Programming](#object-oriented-programming)
    - [Declaring Classes and Instantiating Objects](#declaring-classes-and-instantiating-objects)
    - [Visibility or Access Modifiers](#visibility-or-access-modifiers)
    - [Instance Properties and Methods](#instance-properties-and-methods)
    - [Static Methods and Properties](#static-methods-and-properties)
    - [Working with Objects](#working-with-objects)
    - [Constructors and Destructors](#constructors-and-destructors)
    - [Inheritance](#inheritance)
    - [Interfaces](#interfaces)
    - [Exceptions](#exceptions)
    - [Reflection](#reflection)
    - [Type Hinting](#type-hinting)
    - [Class Constants](#class-constants)
    - [Late Static Binding](#late-static-binding)
    - [Magic (_*) Methods](#magic-_-methods)
    - [Standard PHP Library (SPL)](#standard-php-library-spl)
    - [Generators](#generators)
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
    - [Encryption, Hashing algorithms](#encryption-hashing-algorithms)
    - [File Uploads](#file-uploads)
    - [Database Storage](#database-storage)
    - [Avoid Publishing Your Password Online](#avoid-publishing-your-password-online)

<!-- /TOC -->

## Object-Oriented Programming

### Declaring Classes and Instantiating Objects

Class declared using `class` keyword

```php 
<?php 
class ExampleClass
{
    // class code
}
```

Class instatiate using `new` keyword

```php 
<?php 
$exampleObject = new ExampleClass();
```

1. Autoloading Classes  
    * Autoloading is accomplished using `spl_auto_register()` function

    ```php 
    <?php 
    function my_autoloader($class) {
        include 'classes/' .  $class . '.class.php';
    }

    spl_autoload_register('my_autoloader');

    // Or using anonymous function as of PHP 5.3.0
    spl_auto_register(function($class) {
        include 'classes/' . $class . '.class.php';
    })
    ```

    * If unable to find after autoload function then PHP will throw Fatal Error

### Visibility or Access Modifiers 

* `public` means can be accessed from everywhere 
* `protected` can be accessed form within the class and it's children 
* `private` can be accessed form within the class itself
* if don't explicitly specified it will default to `public`

### Instance Properties and Methods 

1. Properties  
Declared using modifiers followed by the name of the property

```php 
<?php 
class Property 
{
    public $email;
    protected $name = 'Alice';
    private $balance = 60 * 5;
}
```

2. Methods  
Declared using modifier followed by function declaration.  
Default to public if not explicitly specified.

```php 
<?php 
class MethodExample 
{
    private $name;

    public function setName($name) {
        $this->name = $name;
    }
}
```
Non static object can be accesed using `$this` pseudo-variable

### Static Methods and Properties 
Declaring method or property as static make it available without needing a concrete implementation of the class.

```php 
<?php 
class MyClass
{
    public static function sayHello() {
        echo "Hello World" . PHP_EOL;
    }

    public function someFunction() {
        self::sayHello();
    }
    MyClass::sayHello(); // Hello World
    $object = new MyClass();
    $object->someFunction(); // Hello World
}
```

### Working with Objects 

1. Copying Objects   
    * Objects always passed by reference to copy object use `clone` keyword 

    ```php 
    <?php 
    $objectCopy = clone $originalObject;
    ```
    * Using `clone` php will create _shallow copy_ not _deep copy_.  
    * When cloning php will call `__clone()` method in the object.

2. Serializing Objects  
    * Accomplished with `serialize()` and `unserialize()` functions.  
    * When object serialized it will call `__sleep()`, when object unserialized it will call `__wakeup()`
    * Resource and outside reference won't be serialized, but circular reference inside object will be retained
    * When unserialize, the object must have the class declared, if not it will create `__PHP_Incomplete_Class_Name` which has no method 
3. Casting Objects to String 
    * declare `__toString()` method, if not it will generate catchable fatal error

### Constructors and Destructors

Constructor is method that is run when an object is instantiated from the class. Destructor is when unloaded.
In _PHP 4_ constructor has same name with class.

1. Constructor parameters  
If Constructor has parameter you must pass it when instantiating

### Inheritance 

If you extend class, the child will inherit all public properties and method of the parent class.

1. The "Final" Keyword
* Can be appllied to class or method to prevent class being extended and method being overrideen
* java `final` is `const` in PHP
2. Overriding
* If you override, parent method won't be called
* class child can't have lower visibility than parent

### Interfaces

* Use to specify what method a class must implement 
* class can't implement many interface (separated by comma) but can only extend one class

### Exceptions

Standard Exception Type:
* `Exception` implements `Throwable`
    * `ErrorException` extends `Exception`
* `Error` implements `Throwable`
    * `ArithmeticError`
        * `DivisionByZeroError`
    * `AssertionError`
    * `ParseError`
    * `TypeError`

SPL Exception:
* `LogicException` extends `Exception`
    * `BadFunctionCallException`
        * `BadMethodCallException`
    * `DomainException`
    * `InvalidArgumentException`
    * `LengthException`
    * `OutOfRangeException`
* `RuntimeException` extends `Exception`
    * `OutOfBoundsException`
    * `OverflowException`
    * `RangeException`
    * `UnderflowException` 
    * `UnexpectedValueException`

```php 
<?php 
class ParentException extends Exception {}
class ChildException extends ParentException {}

try {
    throw ChildException('My Message');
} catch(ParentException $e) {
    echo "Parent Exception:" . $e->getMessage();
} catch(ChildException $e) {
    echo "Child Exception:" . $e->getMessage();
} catch(Exception $e) {
    echo "Exception:" . $e->getMessage();
}
```

1. Extending Exception Classes 
* Only class that inherits from the Base Exception can be used with `throw` keyword
* A `try` block must have at least one `catch` block
2. Finally
* `finally` keyword used to denote a code block that will __always executed__

### Reflection

Allow the ability to inspect elements at runtime and retreive information about them. Usually used in unit testing, to test private properties.  
Several reflection classes:
* `ReflectionClass`
* `ReflectionObject`
* `ReflectionMethod`
* `ReflectionFunction`
* `ReflectionProperty`

```php 
<?php 
$reflectionObject = new ReflectionClass('Exception');
print_r($reflectionObject->getMethods());
```

### Type Hinting

Specify variable type that a parameter to a function is expected to be.  
If wrong parameter type passed:
* In PHP5: recoverable Fatal Error 
* In PHP7: TypeErrorException
Type hints:
* In PHP5: composite type and callables
* In PHP7: scalar type variable
If class used as type hint, all its children and implementations wil valid

### Class Constants

Constant is value that is immutable

```php 
<?php 
class MathsHelper
{
    const PI = 4;

    public function squareTheCircle($radius) {
        return $radius * (self::PI ** 2);
    }

    echo MathsHelper::PI;
}
```

* Class constants all _public_ and accesible from all scopes
* Class constants accesed using scope resolution operator (`::`)
* Class Constants may only contain _scalar_ value

### Late Static Binding 

Method to reference the _called class_ (not the _calling class_) in the context of _static inheritance_. Rather than creating new _reserved word_, the decision was made to use `static` keyword.

1. Forwarding Calls
* is a static call that is introduced by `parent::`, `static::` or `forward_static_call()`
* `self::` can also be forwarding calls if the class _falls back_ to _inherited_ class because it doesn't have the method

### Magic (_*) Methods 

1. `__get` and `__set`  
    called when trying to read (get) or write (set) inaccessible properties
2. `__isset` and `__unset`  
    triggered by calling `isset()`,`empty()` or `unset()`
3. `__call` and `__callStatic`  
    called when trying to call non-existing method on an object
4. `__invoke`  
    called when trying to execute object as function
5. `__debugInfo`  
    called by `var_dump()` when dumping object
6. More magic functions  
    `__construct()`, `destruct()`  
    `__sleep()`, `__wake()`  
    `__clone()`, `__toString()` 

### Standard PHP Library (SPL)

Collection of classes and interfaces that are recipes for solving common programming problems.

| Categories | Used for |
|:-----------|:---------|
| Data Structures | Standard data structures |
| Iterators | |
| Exceptions | |
| File Handling | |
| ArrayObject | Accessing object with array functions |
| SplObserver and SplObject | Implementing the observer pattern | 

### Generators

Provide easy way to create iterator object.  
Saves processing time and memories.  
Use empty `return` statement to terminate the generator.

1. Yield keyword  
    Used to yield value back while pausing executon

2. Yielding with keys  
```php
<?php
function myGenerator() {
    yield $key => $value;
} 
```
if key not explicitly yield, PHP will pair yielded value with increasing sequential keys

3. Yielding `NULL`  
    yield without argument causes it to yield `NULL` value with increasing sequential key 
    
4. Yielding by Reference  
```php 
<?__PHP_Incomplete_Class_Name
function &referenceGenerator() {
    yield $value;
}
```

### Traits  

Created to alleviate some of limitations of a single inheritance language.

1. Declaring and Using Traits  
    Use `trait` to declare a trait and `use` to include it in a class. A class may use multiple traits.
```php 
<?php 
trait Singleton
{
    private static $instance;

    public static function getInstance() {
        if(!(self::instance instanceof self)) {
            self::instance = new self;
        }
        return self::instance;
    }

    class UsingTraitExample
    {
        use Singleton;
    }

    $object = UsingTraitExample::getInstance();
    var_dump($object instanceof UsingTraitExample); // true

    exit;
}
```
2. Namespacing Traits   
    PHP will generate fatal error if traits have conflicting name, use namespace to avoid this.  
    
3. Inheritance and Precedence  
    * Trait can be used inside another.  
    * Method declared _in a class_ override __method__ in _trait_, however method in _trait_ override __method__ _inherited_.
    * Precedence in traits:  
        CLASS MEMBERS > TRAIT METHODS > INHERITED METHODS

4. Confilict Resolution  
    PHP will generate fatal error if two traits attempts to insert a method with the __same name__
    * use `insteadof` to specify with method to use 
    * use `as` to alias method
    
```php 
<?php 
trait Dog
{
    public function makeNoise() {
        echo "Woof";
    }

    public function wantWalkies() {
        echo "Yes please!";
    }
}

trait Cat 
{
    public function makeNoise() {
        echo "Purr";
    }

    public function wantWalkies() {
        echo "No thanks!";
    }
}

class DomesticPet
{
    use Dog, Cat {
        Cat::makeNoise insteadof Dog;
        Cat::wantWalkies as kittyWalk;
        Dog::wantWalkies insteadof Cat;
    }
}

$obj = new DomesticPet();
$obj->makeNoise(); // Purr 
$obj->wantWalkies(); // Yes please!
$obj->kittyWalk(); // No thanks!
```

5. Visibility  
    Applay visibility modifier to functions by extending `use` keyword    
```php 
<?php 
trait Example
{
    public function myFuntion() {
        
    }
}

class VisibilityExample
{
    use Example {
        myFunction as protected;
    }
}

$obj = new VisibilityExample();
$obj->myFunction(); // PHP Fatal Error: Call to protected method
```



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