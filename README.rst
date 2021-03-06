A drop in replacement for Django's built-in runserver command. Features an extendable interface for handling things such as real-time logging. It will also use the werkzeug interactive debugger if it is available.

.. image:: http://www.pastethat.com/media/files/2010/02/10/Screen_shot_2010-02-10_at_10.05.31_PM.png
   :alt: devserver screenshot

Included modules:

* SQLRealTime: Outputs queries as they happen to the terminal, including time taken.
* SQLSummary: Outputs a summary of your SQL usage.
* ProfileSummary: Outputs a summary of the request performance.
* CacheSummary: Outputs a summary of your cache calls at the end of the request.
* MemoryUse: Outputs a notice when memory use is increased (at the end of a request cycle).

Installation
------------

To install the latest stable version::

	pip install git+git://github.com/dcramer/django-devserver#egg=django-devserver


django-devserver has some optional dependancies, which we highly recommend installing.

* ``pip install sqlparse`` -- pretty SQL formatting
* ``pip install werkzeug`` -- interactive debugger
* ``pip install guppy`` -- tracks memory usage (required for MemoryUseModule)

You will need to include ``devserver`` in your ``INSTALLED_APPS``::

	INSTALLED_APPS = (
	    'devserver',
	    ...
	)

Specify modules to load via the ``DEVSERVER_MODULES`` setting::

	DEVSERVER_MODULES = (
	    'devserver.modules.sql.SQLRealTimeModule',
	    'devserver.modules.sql.SQLSummaryModule',
	    'devserver.modules.profile.ProfileSummaryModule',

	    # Modules not enabled by default
	    'devserver.modules.profile.MemoryUseModule',
	    'devserver.modules.cache.CacheSummaryModule',
	)

Disable SQL query truncation (used in SQLRealTimeModule) with the ``DEVSERVER_TRUNCATE_SQL`` setting::

	DEVSERVER_TRUNCATE_SQL = False

Usage
-----

Once installed, using the new runserver replacement is easy::

	python manage.py rundevserver

Note: This will *force* ``settings.DEBUG`` to ``True``.

Building Modules
----------------

Building modules in devserver is quite simple. In fact, it resembles the middleware API almost identically.

Let's take a sample module, which simple tells us when a request has started, and when it has finished::

	from devserver.modules import DevServerModule
	
	class UselessModule(DevServerModule):
	    logger_name = 'useless'
	    
	    def process_init(self, request):
	        self.logger.info('Request started')
	    
	    def process_complete(self, request):
	        self.logger.info('Request ended')

There are additional arguments which may be sent to logger methods, such as ``duration``::

	# duration is in milliseconds
	self.logger.info('message', duration=13.134)
