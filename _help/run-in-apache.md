---
layout: tutorial
title:  "Run Geokey in Apache"
---

This guide walks you through the setup for running GeoKey in Apache using mod_wsgi. We assume that you have already [installed Geokey](how-to-install.html).

### Installing Apache and mod-wsgi

1. Just one step here:

    ```
    sudo apt-get install apache2 libapache2-mod-wsgi
    ```

### Create directories the serve static files

We now create the directories that are used to serve static files like images, CSS and JavaScript and media uploaded by users.

1. Head to Apaches www directory

    ```
    cd /var/www
    ```

2. Create a directory for Geokey specific files

    ```
    sudo mkdir geokey
    ```

3. Create a directory each for static files and one for assets uploaded by users

    ```
    sudo mkdir geokey/static
    sudo mkdir geokey/media
    ```

4. Open your `local_settings.py`

    ```
    sudo vim /home/django/runner/local_settings.py
    ```

5. Add the change the `static` and `media` settings to:

    ```
    STATIC_ROOT = '/var/www/geokey/static/'
    STATIC_URL = '/static/'

    MEDIA_ROOT = '/var/www/geokey/media/'
    MEDIA_URL = '/media/'
    ```

6. Collect the static files from the python packages. They will automatically be stored under `/var/www/geokey/static/`

    ```
    cd /home/django/runner
    python manage.py collectstatic
    ```

    (Depending on your user role, you might need to add temporarily writing permission to `/var/www/geokey/static`)

7. Finally, allow Apache to write in you media directory.

    ```
    chgrp -R www-data /var/www/geokey/media/
    chmod -R g+w /var/www/geokey/media/
    ```

### Configure Apache virtual host

We are going to use the default virtual host provided by the Apache installation. The same configuration can be applied to other virtual hosts on your system.

1. Open the apache config file in your text editor (we use vim, because it's awesome):

    ```
    vim /etc/apache2/sites-available/default
    ```

2. Add the following lines just below `<VirtualHost *:80>`:

    ```
    WSGIDaemonProcess geokey python-path=/home/django:/home/django/env/lib/python2.7/site-packages
    WSGIProcessGroup geokey
    WSGIScriptAlias / /home/django/wsgi.py
    WSGIPassAuthorization On
    ```

    This first line creates a new WSGI daemon that runs GeoKey in Apache. Its name (in our case `geokey`) can be anything, but you should use a descriptive name. The `python-path` directive contains both the path of your Django project and the path to the Python packages inside your virtual environment.

    The third line tells the virtual host to use the WSGI daemon we just created.

    The fourth line tells Apache and mod_wsgi where to find the WSGI configuration.

    The last line enables WSGI to pass authorisation credentials to the Django application. This is necessary, because Django would not be able to authenticate users otherwise.

3. We then have to tell Apache where to find static and media files from your Django project. (Remember, we previously created these directories and copied the static files.)

    ```
    Alias /static/ /var/www/geokey/static/
    <Location "/static/">
        Options -Indexes
    </Location>

    Alias /media/ /var/www/geokey/media/
    <Location "/media/">
        Options -Indexes
    </Location>
    ```

5. Finally, we need to tell WSGI where to find configuration files for our Django project. Open the WSGI configuration in your text editor:

    ```
    sudo vim /home/django/runner/wsgi.py
    ```

6. Add following line just below `from django.core.wsgi import get_wsgi_application`

    ```
    import sys
    sys.path.append('/home/django/runner')
    ```

7. You should be all set now. Now restart Apache

    ```
    sudo service apache2 restart
    ```

    and point your browser to your domain. You should see the admin landing page now.


### Enabling Youtube upload

Videos are – by default – uploaded to your Youtube account.

1. You will need to obtain Youtube API keys following [this guide](https://developers.google.com/youtube/registering_an_application).

2. The add the credentials to `local_settings`:

    ```
    YOUTUBE_AUTH_EMAIL = 'your-email@example.com'
    YOUTUBE_AUTH_PASSWORD = 'password'
    YOUTUBE_DEVELOPER_KEY = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
    YOUTUBE_CLIENT_ID = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com'
    ```

### Enabling email

GeoKey uses e-mail in various cases; for instance, when users want to reset their password. We therefore need to setup [Postfix](http://www.postfix.org/) to send e-mail from your server.

1. First install Postfix:

    ```
    apt-get update
    apt-get install postfix
    ```

2. Open project configuration in your text editor:

    ```
    vim /home/django/runner/local_settings.py
    ```

3. Add a default e-mail address that is used as sender for e-mails send by Django.

    ```
    DEFAULT_FROM_EMAIL = 'sender@example.com'
    ```
