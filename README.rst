ptomulik-apt-repo
=================

Basic directory structure and few scripts to create and maintain Debian package
repository.


Installation
------------

This assumes the repo to be installed under ``/home/www/pkg.example.com/`` on
a machine runing Debian.

1. Install apache2_::

      sudo apt-get install apache2

2. Create directory for the repo::

      sudo mkdir -p /home/www/pkg.example.com/

3. Change ownership, such that the package will be handled by an ordinary
   user::

      sudo chown ptomulik:ptomulik /home/www/pkg.example.com/

4. Clone the initial repository structure into your directory::

      cd /home/www/
      git clone git@github.com:ptomulik/ptomulik-apt-repo.git pkg.example.com

   or (over HTTPS)::

      cd /home/www/
      git clone https://github.com/ptomulik/ptomulik-apt-repo.git pkg.example.com

5. Configure apache to serve the repository::

      cd /home/www/pkg.example.com/
      sudo cp share/apache2-site.conf /etc/apache2/sites-available/001-pkg.conf

6. Adjust your site settings in ``/etc/apache2/sites-available/001-pkg.conf``
   (``ServerName``, ``ServerAdmin`` and ``DocRoot``)


7. Enable the site::
      sudo a2dissite 000-default
      sudo s2ensite 001-pkg
      sudo service apache2 reload

Scripts
-------

process-incoming
````````````````

.. _apache2: http://httpd.apache.org/

