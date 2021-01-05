=====
Gists
=====

**************************
Local Postgres with Django
**************************

Create database (use pgAdmin) and give it the name. Make sure user has privileges.
By default, user is ``postgres``.

.. code-block:: python

  DATABASES = {
      "default": {
          "ENGINE": "django.db.backends.postgresql",
          "NAME": "database_name",
          "USER": "postgres",
          "PASSWORD": os.environ.get("POSTGRES_PASSWORD"),
          "HOST": "localhost",
      }
  }


in .env

.. code-block:: python

  POSTGRES_PASSWORD=Your_password


I prefer to load .env this way:

* Create the .env file in the main app folder
* Install ``python-dotenv``
* include in settings.py:

.. code-block:: python

      from dotenv import load_dotenv

      load_dotenv()


* Now .env will load without having to reload the workspace.

***************
Coverage Config
***************

Create a file in the root of the app named ``.coveragerc`` and include

.. code-block:: bash

  [run]
  source = .
  omit = ./venv/*,*tests*,*apps.py,*manage.py,*__init__.py,*migrations*,*asgi*,*wsgi*,*admin.py,*urls.py

  [report]
  omit = ./venv/*,*tests*,*apps.py,*manage.py,*__init__.py,*migrations*,*asgi*,*wsgi*,*admin.py,*urls.py


Then simply run ``coverage run manage.py test``

This creates a report excluding files and folders that don't need testing.


****************
Django reminders
****************

When importing apps, if using signals that modify a Django class, import the full path to the configuration class or the app will use the base one, never registering the signals.

.. note:: Stack Overflow
  `clue <https://stackoverflow.com/questions/61580144/django-create-profile-for-user-signal>`_

In config.py:

.. code-block:: diff

  INSTALLED_APPS = [
  -   'users',
  +   'users.apps.UsersConfig',
  ]

See ``apps.py`` for the configuration class name:

.. code-block:: python

  from django.apps import AppConfig

  class UsersConfig(AppConfig):
      name = 'users'

      def ready(self):
          import users.signals

.. note:: Se also this
  `Link <https://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html>`_ 