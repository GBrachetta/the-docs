======
Django
======

********
Settings
********

common.py
=========

.. code-block:: python

    import os
    from pathlib import Path

    from dotenv import load_dotenv

    load_dotenv()

    BASE_DIR = Path(__file__).resolve().parent.parent.parent

    SECRET_KEY = os.environ.get("SECRET_KEY")  # Add in .env

    INSTALLED_APPS = [
        "django.contrib.admin",
        "django.contrib.auth",
        "django.contrib.contenttypes",
        "django.contrib.sessions",
        "django.contrib.messages",
        "django.contrib.staticfiles",
        "core",
        # Add here your new common apps for both dev and prod
    ]

    MIDDLEWARE = [
        "django.middleware.security.SecurityMiddleware",
        "django.contrib.sessions.middleware.SessionMiddleware",
        "django.middleware.common.CommonMiddleware",
        "django.middleware.csrf.CsrfViewMiddleware",
        "django.contrib.auth.middleware.AuthenticationMiddleware",
        "django.contrib.messages.middleware.MessageMiddleware",
        "django.middleware.clickjacking.XFrameOptionsMiddleware",
    ]

    ROOT_URLCONF = "boilerplate.urls"

    TEMPLATES = [
        {
            "BACKEND": "django.template.backends.django.DjangoTemplates",
            "DIRS": [os.path.join(BASE_DIR, "templates")],
            "APP_DIRS": True,
            "OPTIONS": {
                "context_processors": [
                    "django.template.context_processors.debug",
                    "django.template.context_processors.request",
                    "django.contrib.auth.context_processors.auth",
                    "django.contrib.messages.context_processors.messages",
                    "django.template.context_processors.media",  # Media
                ],
            },
        },
    ]

    WSGI_APPLICATION = "boilerplate.wsgi.application"

    AUTH_PASSWORD_VALIDATORS = [
        {
            "NAME": "django.contrib.auth.password_validation.UserAttributeSimilarityValidator",  # nopep8
        },
        {
            "NAME": "django.contrib.auth.password_validation.MinimumLengthValidator",  # nopep8
        },
        {
            "NAME": "django.contrib.auth.password_validation.CommonPasswordValidator",  # nopep8
        },
        {
            "NAME": "django.contrib.auth.password_validation.NumericPasswordValidator",  # nopep8
        },
    ]

    LANGUAGE_CODE = "en-us"
    TIME_ZONE = "Europe/Amsterdam"  # Change to your time zone
    USE_I18N = True
    USE_L10N = True
    USE_TZ = True

    # Static and Media
    STATICFILES_DIRS = [os.path.join(BASE_DIR, "static")]
    STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles")
    STATIC_URL = "/static/"
    MEDIA_ROOT = os.path.join(BASE_DIR, "media")
    MEDIA_URL = "/media/"

development.py
==============

.. code-block:: python

    import os
    # Import below from the common config as needed, and append to the list (+=)
    from boilerplate.settings.common import INSTALLED_APPS, MIDDLEWARE

    DEBUG = True

    ALLOWED_HOSTS = [
        os.environ.get("DOMAIN_NAME"),  # Add in .env
        "localhost",
        "127.0.0.1",
    ]

    INSTALLED_APPS += [
        "debug_toolbar",
        "mail_panel",
        # Add here your new apps for dev only
    ]

    MIDDLEWARE.insert(
        0,
        "debug_toolbar.middleware.DebugToolbarMiddleware",
    )

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": "db.sqlite3",
        }
    }

    EMAIL_BACKEND = "mail_panel.backend.MailToolbarBackend"
    DEFAULT_FROM_EMAIL = "admin@mail.com"

    DEBUG_TOOLBAR_PANELS = [
        "ddt_request_history.panels.request_history.RequestHistoryPanel",
        "debug_toolbar.panels.history.HistoryPanel",
        "debug_toolbar.panels.versions.VersionsPanel",
        "debug_toolbar.panels.timer.TimerPanel",
        "debug_toolbar.panels.settings.SettingsPanel",
        "debug_toolbar.panels.headers.HeadersPanel",
        "debug_toolbar.panels.request.RequestPanel",
        "debug_toolbar.panels.sql.SQLPanel",
        "debug_toolbar.panels.staticfiles.StaticFilesPanel",
        "debug_toolbar.panels.templates.TemplatesPanel",
        "debug_toolbar.panels.cache.CachePanel",
        "debug_toolbar.panels.signals.SignalsPanel",
        "debug_toolbar.panels.logging.LoggingPanel",
        "mail_panel.panels.MailToolbarPanel",
        "debug_toolbar.panels.redirects.RedirectsPanel",
        "debug_toolbar.panels.profiling.ProfilingPanel",
    ]

    INTERNAL_IPS = ["127.0.0.1", "localhost"]  # For debug-toolbar

production.py
=============

.. code-block:: python

    import os
    import dj_database_url
    # Import below from the common config as needed, and append to the list (+=)
    from boilerplate.settings.common import INSTALLED_APPS

    DEBUG = False

    ALLOWED_HOSTS = os.environ.get("DOMAIN_NAME")

    INSTALLED_APPS += [
        # Add here your new apps for prod only
        "storages",
    ]

    DATABASES = {
        "default": dj_database_url.parse(os.environ.get("DATABASE_URL"))
    }  # Add variable in Heroku

    EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
    EMAIL_USE_TLS = True
    EMAIL_PORT = 587
    EMAIL_HOST = "your_smtp_server"  # For ex. 'smtp.google.com'
    EMAIL_HOST_USER = os.environ.get("EMAIL_HOST_USER")  # Add in Heroku
    EMAIL_HOST_PASSWORD = os.environ.get("EMAIL_HOST_PASSWORD")  # Add in Heroku
    DEFAULT_FROM_EMAIL = os.environ.get("EMAIL_HOST_USER")  # Add in Heroku

    if "USE_AWS" in os.environ:
        AWS_S3_OBJECT_PARAMETERS = {
            "Expires": "Thu, 31 Dec 2099 20:00:00 GMT",
            "CacheControl": "max-age=94608000",
        }
        AWS_STORAGE_BUCKET_NAME = "your-aws-bucket-name"
        AWS_S3_FILE_OVERWRITE = False
        AWS_DEFAULT_ACL = None
        AWS_S3_REGION_NAME = "eu-central-1"
        AWS_ACCESS_KEY_ID = os.environ.get("AWS_ACCESS_KEY_ID")  # Add in Heroku
        AWS_SECRET_ACCESS_KEY = os.environ.get("AWS_SECRET_ACCESS_KEY")  # ditto
        AWS_S3_CUSTOM_DOMAIN = f"{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com"
        STATICFILES_STORAGE = "custom_storages.StaticStorage"
        STATICFILES_LOCATION = "static"
        DEFAULT_FILE_STORAGE = "custom_storages.MediaStorage"
        MEDIAFILES_LOCATION = "media"
        STATIC_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/{STATICFILES_LOCATION}/"
        MEDIA_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/{MEDIAFILES_LOCATION}/"

