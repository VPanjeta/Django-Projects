Liveblog
========

Illustrates a "liveblog" using Channels. This is a page that shows a series
of short-form posts in descending date order, with new ones appearing at the
top as they're posted.

The site supports multiple liveblogs at once, and clients only listen for new
posts on the blog they're currently viewing.

When you view a liveblog page, we open a WebSocket to Django, and the consumer
there adds it to a Group based on the liveblog slug it used in the URL of the
socket. Then, in the ``save()`` method of the ``Post`` model, we send notifications
onto that Group that all currently connected clients pick up on, and insert
the new post at the top of the page.

Updates are also supported - the notification is sent with an ID, and if a post
with that ID is already on the page, the JavaScript just replaces its content
instead.


Installation
------------

Manual installation
~~~~~~~~~~~~~~~~~~~~~~

Make a new virtualenv for the project, and run::

    pip install -r requirements.txt

Then, you'll need Redis running locally; the settings are configured to
point to ``localhost``, port ``6379``, but you can change this in the
``CHANNEL_LAYERS`` setting in ``settings.py``.

Finally, run::

    python manage.py migrate
    python manage.py createsuperuser
    python manage.py runserver

Docker installation
~~~~~~~~~~~~~~~~~~~~~~

Run the app::

    docker-compose up -d

You can omit the `-d` if you want to have the container log directly to
your terminal.   You can always see the logs with::

    docker-compose logs web

The app will now be running on: http://{your-docker-ip}:8000

The migration is done as part of the Dockerfile and shouldn't need to be
repeated (but it's ok to do so if you get an error).

Then create the superuser::

    docker-compose run --rm web python manage.py createsuperuser

How to Use
-------------------------------------------------------------------------

For Docker, replace `localhost` below with `{your-docker-ip}`.

Then, log into http://localhost:8000/admin/ and make a new Liveblog object.

Open a new window, go to http://localhost:8000/, and click on your new liveblog
to see its posts page.

Now, in the admin, make some new Posts against your blog, and watch them appear
in your new window. Edit them, and they'll update themselves live on the page too.