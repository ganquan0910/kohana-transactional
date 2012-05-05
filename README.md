What's this?
============
A module for the [Kohana PHP framework](http://kohanaframework.org/) that provides declarative transactions for controller actions, similar to the `@Transactional` annotation of the Spring framework in Java.

Actions that are declared to be transactional have all their DB access (including ORM-based) contained in one transaction, which is rolled back if the action results in an exception, and committed if it ends regularly or `die`/`exit` is called (for PHP 5.4 and greater, a HTTP response code >= 400 triggers a rollback instead).

Why you should use it
=====================
Transactions are necessary to guarantee the consistency of the database. Managing them manually is tedious and error-prone. This module can make your entire application transactionally safe by adding a single line of code! 

How to use it
=============
After adding the module, simply add a public property named `_transactional` to your controller. It can have the following values:

* `TRUE` makes all actions in that controller transactional
* `FALSE` makes all actions non-transactional (can be used to override parent class setting)
* An array containing the names of actions (without the `action_` prefix) makes exactly those actions transactional
* the setting can be inherited and (if necessary) overridden in subclasses

So if you have a base class all your controllers inherit from, then making all your actions transactional is a matter of adding this single line:

	public $_transactional = TRUE;

Compatibility
=============
The module comes with a test application (see `tests` folder). These tests have been successfully run on the following configurations:

* Windows 7 Home Premium 64 bit, running a XAMPP 1.7.7 installation consisting of PHP 5.3.8 and Apache 2.2.21
	* Kohana 3.2.0
		* MySQL 5.5
		* PostgreSQL 9.1
		* SqLite 3.7.3
	* Kohana 3.1.4
		* MySQL 5.5 and 5.1.53
		* PostgreSQL 9.1
		* SqLite 3.7.3

* Windows 7 Home Premium 64 bit, running a XAMPP 1.8b3 installation consisting of PHP 5.4.0 and Apache 2.4.1
	* Kohana 3.2.0
		* MySQL 5.5

Caveats
=======
* The module is based on the assumption that all DB access which happens while serving one HTTP request should be in one transaction. In most cases, this works out just fine. If you need more fine-grained control, you have to use manual transaction management for those actions.
* Obviously, it only works with DB engines that support transactions (i.e. not MyISAM).
* In PHP versions up to 5.3, transactions are always committed in a shutdown function when `die` or `exit` is called (necessary since Kohana does that after redirecting a request). If your application uses `die` or `exit` for error conditions, transactions will still be committed - so use exceptions instead.
* PHP 5.4 introduces the `http_response_code()` function which allows more sophisticated behaviour: Response codes smaller than 400 cause a commit, 400 or greater causes a rollback. This allows the use of `die` or `exit` for error conditions, if an appropriate HTTP response code is set.
