Django on OpenShift Express
============================

This git repository helps you get up and running quickly w/ a Django installation
on OpenShift Express.  The Django project name used in this repo is 'openshift'
but you can feel free to change it. Now the backend is PostgreSQL.

When you push this application up for the first time, the sqlite database is
copied from wsgi/openshift/sqlite3.db.  This is the stock database that is created
when 'python manage.py syncdb' is run with only the admin app installed.

You can delete the database from your git repo after the first push (you probably
should for security).  On subsequent pushes, a 'python manage.py syncdb' is
executed to make sure that any models you added are created in the DB.  If you
do anything that requires an alter table, you could add the alter statements
in GIT_ROOT/.openshift/action_hooks/alter.sql and then use
GIT_ROOT/.openshift/action_hooks/deploy to execute that script (make sure to
back up your database w/ 'rhc app snapshot save' first :) )


Running on OpenShift
----------------------------

Create an account at http://openshift.redhat.com/

Create a python-2.6 application

    rhc app create -a django -t python-2.6

Add PostgreSQL Database service

    rhc app cartridge add -a django -c postgresql-8.4

Add this upstream seambooking repo

    cd django
    git remote add upstream -m master git@github.com:drivard/openshift-django-postgresql.git
    git pull -s recursive -X theirs upstream master
    
Then push the repo upstream

    git push

Create the Django admin user

Find your openshift app UUID

    rhc domain show  --timeout 1000

Connect through ssh to your app

    ssh $uuid@django-$yournamespace.rhcloud.com

Get in the app directory

    cd django/repo/wsgi/openshift

Export the python egg cache directory

    export PYTHON_EGG_CACHE=$OPENSHIFT_APP_DIR/virtenv/lib/python-2.6

Create the admin user

    virtenv/bin/python repo/wsgi/openshift/manage.py syncdb

That's it, you can now checkout your application at (Using the user you just created.):

    http://django-$yournamespace.rhcloud.com

