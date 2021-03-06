.. _adminguide:

Administrator Guide
===================

.. contents::
    :local:
    :depth: 2

.. _fileandfolder:

Files and Folders
-----------------

Birdhouse is a framework with several compartments. They can be installed according to the specefic needs of the user. Here is a short overview in order of the most important files and folders:

Environment
...........

Three folder locations have to be pointed out:

* **repository clones:**  The fetched code by ``git clone``. It is recommended to store the repositories in ``~/birdhouse``

* **anaconda**: By default, the installation process creates a folder ``~/anaconda`` for general anaconda-specific software (see also :ref:`anaconda`_ ).

* **conda environments:** All birds (repositories) are built with their own environment to avoid missmatch of dependencies.
By default, the conda environments are in ``~/.conda/envs/``.

To change the default settings, create a ``Makefile.config`` with::

  cp Makefile.config.example Makefile.config

and change the paths accordingly to your needs.

Furthermore, in ``environment.yml``, the conda packages can be defined. It is recommended to pin the version. The bird-specific packages are defined here, while in ``requirements/conda_pinned``, general versions are set.

There are **log files** situated at:: ``~/birdhouse/var/log/pywps/``




.. _birdhouse_ecosystem:

Set up a birdhouse ecosystem server
-----------------------------------

If you are already familliar with installing single standalone WPS (follow the installation guides in the documentations of e.g. emu), then you are ready to set up a birdhouse containing flyingpigeon (providing scientific analyses methods), malleefowl (to search and fetch data) and the pheonix (a graphic interface for a web browser including a WMS).

general remarks:
..................

| Check the :ref:`requirements`_ of your system!
| The installation is done as **normal user**, root rights are causing conflicts.


clone the repositories from gitHub:
...................................

It is recommended to collect the repositories in a seperate folder (e.g. birdhouse, but can have a name of your choice)::

  mkdir birdhouse
  cd birdhouse


* **fetch the source code:**

|  git clone https://github.com/bird-house/flyingpigeon.git
|  git clone https://github.com/bird-house/pyramid-phoenix.git
|  git clone https://github.com/bird-house/malleefowl.git

* **phoenix password**

To be able to log into the Phoenix GUI once the services are running, it is necessary to generate a password:
go into the pyramid-phoenix folder and run::

  make passwd

This will automatically write a password hash into pyramid-phoenix/custom.cfg


* **installation**

You can run the installation with default settings.
It will create an anaconda environment into your HOME direcory and deploy all required software dependecies there.
  *read the ''changing the default configuration' first if you would like to change the defaults.*

In **all** of the tree folders (malleefowl, flyingpigeon and pyramid-phoenix) run::

  make install

This installation will take some minutes to fetch all dependencies and install them into seperate conda environments.
With the default settings, the installation creates the following folders::

  ls ~/anaconda/
contains general software required by anaconda
::
  ls ~/.conda/envs/
contains the seperate environments of the birds for their specific software dependencies
::
  ls ~/birdhouse/var/
the local cache for fetched input files, output files and logs. This folder is growing (while fetching files and storing job outputs) under productive usage of birdhouse.

* **start the services**

in **one** of the birds run::

  make start
or::

  make restart

and to check if the services are running, run::

  make status

* **launching the Phoenix GUI**

If the services are running, you can launch the GUI in a common web browser. By default, phoenix is set to port 8081::

  firefox http://localhost:8081
or::

  firefox https://localhost:8443/

Now you can log in (upper right corner) with your Phoenix password created previously.
Phoenix is just a graphical interface with no more function than looking nice ;-).

* **register a service in the GUI**

Your first administrator step is to register flyingpigeon as a service. For that, log in with your phoenix password.
In the upper right corner is a tool symbol to open the 'settings'. Click on 'Services' and the 'Register a Service'.

flyingpigeon is per default at port 8093.

the appropriate url is::

  http://localhost:8093/wps

Provide service title and name as you like:
Service Title: Flyingpigeon
Service Name: flyingpigeon

check 'Service Type' : 'Web Processing Service' (default) and register.

Optionally, you can check 'Public access?', to allow unregistered users to launch jobs. (**NOT recommended**)


* **launching a job**

Now your birdhouse ecosysem is set up. The also installed malleefowl is already running in the background and will do a lot of work silently. Ther is **no need to register malleefowl** manually!

Launching a job can be performed as a process (Process menu) or with the wizard. To get familliar with the processes provided by each of the birds, read the approriate documentation for each of the services listed in the `overview: <http://birdhouse.readthedocs.io/en/latest/index.html>`_

* **changing the default configuration:**

The default configuration can be changed by creating a Makefile.config file. There is an example provided to be used::

  cp Makefile.config.example Makefile.config
and set the appropriate path. You have to **do this in all** bird repositories.

Furthermore, you might change the hostname (to provide your service to the outside), ESGF-node connection, the port or the log-level for more/less information in the administrator logfiles.
Here is an example pyramid-phoenix/custom.cfg:

| [settings]
| hostname = localhost
| http-port = 8081
| https-port = 8443
| log-level = DEBUG
| # run 'make passwd' and to generate password hash
| phoenix-password = sha256:513....
| # generate secret
| # python -c "import os; print(''.join('%02x' % ord(x) for x in os.urandom(16)))"
| phoenix-secret = d5e8417....30
| esgf-search-url = https://esgf-data.dkrz.de/esg-search
| wps-url = http://localhost:8091/wps
| # register at github: https://github.com/settings/applications/new
| github-consumer-key = 86......02
| github-consumer-secret = 2c.........6d4

* **Administration HELP:**

In case of questions or trouble shooting, feel welcome to join the birdhouse chat and get into contact with the developers directly:

`Birdhouse-Chatroom <https://gitter.im/bird-house/birdhouse>`_
