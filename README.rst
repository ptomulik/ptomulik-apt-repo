ptomulik-apt-repo
=================

Basic directory structure and few scripts to create and maintain Debian package
repository.


Installation
------------

On bare Debian server
`````````````````````

This assumes the repo to be installed under ``/home/www/pkg.example.com/`` on
a machine runing Debian.

1. Install apache2_ and reprepro_::

      sudo apt-get install apache2 reprepro

2. Create directory for the repo::

      sudo mkdir -p /home/www/pkg.example.com/

3. Change ownership, such that the package will be handled by an ordinary
   user::

      sudo chown ptomulik:ptomulik /home/www/pkg.example.com/

4. Download tarball from github and unpack it to repository dir::

      (cd /home/www/pkg.example.com && wget -O - https://github.com/ptomulik/ptomulik-apt-repo/archive/master.tar.gz | tar --strip-components 1 -zxf - )

5. Configure apache to serve the repository::

      cd /home/www/pkg.example.com/
      sudo cp share/apache2-site.conf /etc/apache2/sites-available/001-pkg.conf

6. Adjust your site settings in ``/etc/apache2/sites-available/001-pkg.conf``
   (``ServerName``, ``ServerAdmin`` and ``DocRoot``)::

      sudo vim /etc/apache2/sites-available/001-pkg.conf

   note: you should at least adjust ``ServerName``, otherwise default apache
   site will be served.

7. Enable ``mod_rewrite``::

      sudo a2enmod rewrite

8. Enable the site::

      # sudo a2dissite 000-default # this is optional, disables default site
      sudo a2ensite 001-pkg
      sudo service apache2 reload


On existing WWW server without shell access
```````````````````````````````````````````

In this setup we end up with the installation split among two parts.

- a http server, which only serves the packages uploaded to repository,
- a PC running Debian which holds some scripts and is used for repo maintenance.

We assume that we have no access to shell on the WWW server, thus all the
maintenance actions will be performed on our PC running Debian. We'll achieve
this by mounting remote docroot with ``sshfs`` to our local installation.

It's assumed, that the server is named ``pkg.example.com``, user is
``ptomulik`` and the document root on the WWW server used for apt repository
(as seen throught ``sftp``) is ``/www/pkg.example.com/apt``.

1. On PC install ``sshfs``::

      sudo apt-get install sshfs

2. On PC create directory, where the repository will be held, together with
   docroot::

      mkdir -p ~/ptomulik-apt-repo/docroot

3. Mount the server's document root do our ``docroot``::

      sshfs ptomulik@pkg.example.com:/www/pkg.exapmle.com/apt ~/ptomulik-apt-repo/docroot

4. Download tarball from github and unpack it to repository dir::

      (cd ~/ptomulik-apt-repo && wget -O - https://github.com/ptomulik/ptomulik-apt-repo/archive/master.tar.gz | tar --strip-components 1 -zxf - )

5. That's all for now, you may unmount the remote docroot::

      fusermount -u ~/ptomulik-apt-repo/docroot

   From now on, every maintenance on the repository requires mounting the
   docroot again, as in point 3.

Maintenance
-----------

In the following examples ``$REPOROOT`` points to the ``docroot`` directory
within our installation.

Creating symlinks between codenames and suites
``````````````````````````````````````````````

This creates symlinks in repository, such that ``unstable`` points to ``sid``
etc.::

    (cd ${REPOROOT}/debian/ && reprepro createsymlinks)

Processing incomming packages
`````````````````````````````

Processing all incoming packages in ``debian`` repository::

    (cd "${REPOROOT}/debian && reprepro processincoming incoming)

The same for ``ubuntu``::

    (cd "${REPOROOT}/ubuntu && reprepro processincoming incoming)


.. _apache2: http://httpd.apache.org/
.. _reprepro: http://mirrorer.alioth.debian.org/
