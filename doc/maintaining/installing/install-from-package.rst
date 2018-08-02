.. include:: /_substitutions.rst

============================
Installing CKAN from package
============================

This section describes how to install CKAN from package. This is the quickest
and easiest way to install CKAN, but it requires **Ubuntu 16.04 64-bit**. 


Host ports requirements:

    +------------+------------+-----------+
    | Service    | Port       | Used for  |
    +============+============+===========+
    | NGINX      | 80         | Proxy     |
    +------------+------------+-----------+
    | Apache2    | 8080       | Web Server|
    +------------+------------+-----------+
    | Solr/Jetty | 8983       | Search    |
    +------------+------------+-----------+
    | PostgreSQL | 5432       | Database  |
    +------------+------------+-----------+
    | Redis      | 6379       | Search    |
    +------------+------------+-----------+


.. _run-package-installer:

---------------------------
1. Install the CKAN package
---------------------------

On your Ubuntu system, open a terminal and run these commands to install
CKAN:


#. Update Ubuntu's package index::

    sudo apt-get update

#. Install the Ubuntu packages that CKAN requires (and 'git', to enable you to install CKAN extensions)::

    sudo apt-get install -y apache2 libapache2-mod-wsgi libpq5 redis-server git-core
    
#. Then stop apache2 service to install nginx
    
    sudo service apache2 stop
    
    sudo apt-get install -y nginx

#. Download the CKAN package:

    - On Ubuntu 16.04:

       .. parsed-literal::

           wget \http://packaging.ckan.org/python-ckan_2.8-xenial_amd64.deb


#. Install the CKAN package:

   - On Ubuntu 16.04:

       .. parsed-literal::

           sudo dpkg -i python-ckan_2.8-xenial_amd64.deb


    .. note:: If you get the following error it means that for some reason the
     Apache WSGI module was not enabled::

        Syntax error on line 1 of /etc/apache2/sites-enabled/ckan_default:
        Invalid command 'WSGISocketPrefix', perhaps misspelled or defined by a module not included in the server configuration
        Action 'configtest' failed.
        The Apache error log may have more information.
           ...fail!

     You can enable it by running these commands in a terminal::

        sudo a2enmod wsgi
        sudo service apache2 restart


-----------------------------------
2. Install and configure PostgreSQL
-----------------------------------

Install postgres, running this command in a terminal::

    sudo apt-get install -y postgresql


Next you’ll need to create a database user if one doesn’t already exist. Create a new PostgreSQL database user called ckan_default, and enter a password for the user when prompted. You’ll need this password later::

  sudo -u postgres createuser -S -D -R -P ckan_default


Create a new PostgreSQL database, called ckan_default, owned by the database user you just created::

  sudo -u postgres createdb -O ckan_default ckan_default -E utf-8


Edit the sqlalchemy.url option in your config file (/etc/ckan/default/production.ini) file and
set the correct password, database and database user.


-----------------------------
3. Install and configure Solr and Jetty
-----------------------------

Install solr, running this command in a terminal::

    sudo apt-get install -y solr-jetty


Edit the Jetty configuration file (/etc/default/jetty8 or /etc/default/jetty) and change the following variables::

  NO_START=0            # (line 4)
  JETTY_HOST=127.0.0.1  # (line 16)
  JETTY_PORT=8983       # (line 19)


Start or restart the Jetty server.

For Ubuntu 16.04::

  sudo service jetty8 restart


Finally, change the solr_url setting in your CKAN configuration file (/etc/ckan/default/production.ini) to point to your Solr server, for example::

  solr_url=http://127.0.0.1:8983/solr



-------------------------------------------------------
4. Update the configuration and initialize the database
-------------------------------------------------------

#. Edit the config file (/ect/ckan/default/production.ini) to set up the following options:

    site_id
      Each CKAN site should have a unique ``site_id``, for example::

        ckan.site_id = default

    site_url
      Provide the site's URL. For example::

        ckan.site_url = http://demo.ckan.org
        
    display_timezone
      Enter a time zone. For example::
      
        ckan.display_timezone = US/Eastern

#. Initialize your CKAN database by running this command in a terminal::

    sudo ckan db init

---------------------------
5. Restart Apache and Nginx
---------------------------

Restart Apache and Nginx by running this command in a terminal::

    sudo service apache2 restart
    sudo service nginx restart

---------------
6. You're done!
---------------

Open http://your-host-address in your web browser. You should see the CKAN front
page:


You can now move on to Getting Started to begin using and customizing
your CKAN site.

