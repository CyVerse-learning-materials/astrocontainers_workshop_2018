**Introduction to Docker**
--------------------------

|docker|

1. Prerequisites
================

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor. Prior experience in developing web applications will be helpful but is not required.

2. Docker Installation
======================

Getting all the tooling setup on your computer can be a daunting task, but not with Docker. Getting Docker up and running on your favorite OS (Mac/Windows/Linux) is very easy.

The getting started guide on Docker has detailed instructions for setting up Docker on `Mac <https://docs.docker.com/docker-for-mac/install/>`_/`Windows <https://docs.docker.com/docker-for-windows/install/>`_/`Linux <https://docs.docker.com/install/linux/docker-ce/ubuntu/>`_.

.. Note::

	If you're using Docker for Windows make sure you have `shared your drive <https://docs.docker.com/docker-for-windows/#shared-drives>`_.

	If you're using an older version of Windows or MacOS you may need to use `Docker Machine <https://docs.docker.com/machine/overview/>`_ instead.

	All commands work in either bash or Powershell on Windows.

.. Note::

	Depending on how you've installed Docker on your system, you might see a ``permission denied`` error after running the above command. If you're on Linux, you may need to prefix your Docker commands with sudo. Alternatively to run docker command without sudo, you need to add your user (who has root privileges) to docker group.
	For this run:

	Create the docker group::

		$ sudo groupadd docker

	Add your user to the docker group::

		$ sudo usermod -aG docker $USER

	Log out and log back in so that your group membership is re-evaluated

Once you are done installing Docker, test your Docker installation by running the following command to make sure you are using version 1.13 or higher:

.. code-block:: bash

	$ docker --version
	Docker version 17.09.0-ce, build afdb6d4

When run without ``--version`` you should see a whole bunch of lines showing the different options available with ``docker``. Alternatively you can test your installation by running the following:

.. code-block:: bash

	$ docker run hello-world
	Unable to find image 'hello-world:latest' locally
	latest: Pulling from library/hello-world
	03f4658f8b78: Pull complete
	a3ed95caeb02: Pull complete
	Digest: sha256:8be990ef2aeb16dbcb9271ddfe2610fa6658d13f6dfb8bc72074cc1ca36966a7
	Status: Downloaded newer image for hello-world:latest

	Hello from Docker.
	This message shows that your installation appears to be working correctly.

	To generate this message, Docker took the following steps:
	 1. The Docker client contacted the Docker daemon.
	 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
	 3. The Docker daemon created a new container from that image which runs the
	    executable that produces the output you are currently reading.
	 4. The Docker daemon streamed that output to the Docker client, which sent it
	    to your terminal.
	.......

3. Running Docker containers
============================

Now that you have everything setup, it's time to get our hands dirty. In this section, you are going to run a container from `Alpine Linux <http://www.alpinelinux.org/>`_ (a lightweight linux distribution) image on your system and get a taste of the ``docker run`` command.

But wait, what exactly is a container and image?

**Containers** - Running instances of Docker images — containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS.

**Images** - The file system and configuration of our application which are used to create containers. To find out more about a Docker image, run ``docker inspect hello-world``. In the demo above, you could have used the ``docker pull`` command to download the ``hello-world`` image. However when you executed the command ``docker run hello-world``, it also did a ``docker pull`` behind the scenes to download the ``hello-world`` image with ``latest`` tag (we will learn more about tags little later).

Now that we know what a container and image is, let's run the following command in our terminal:

.. code-block:: bash

	$ docker run alpine ls -l
	total 52
	drwxr-xr-x    2 root     root          4096 Dec 26  2016 bin
	drwxr-xr-x    5 root     root           340 Jan 28 09:52 dev
	drwxr-xr-x   14 root     root          4096 Jan 28 09:52 etc
	drwxr-xr-x    2 root     root          4096 Dec 26  2016 home
	drwxr-xr-x    5 root     root          4096 Dec 26  2016 lib
	drwxr-xr-x    5 root     root          4096 Dec 26  2016 media
	........

Similar to ``docker run hello-world`` command in the demo above, ``docker run alpine ls -l`` command fetches the ``alpine:latest`` image from the Docker registry first, saves it in our system and then runs a container from that saved image.

When you run ``docker run alpine``, you provided a command ``ls -l``, so Docker started the command specified and you saw the listing

You can use the ``docker images`` command to see a list of all images on your system

.. code-block:: bash

	$ docker images
	REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	alpine                 	latest              c51f86c28340        4 weeks ago         1.109 MB
	hello-world             latest              690ed74de00f        5 months ago        960 B

Let's try something more exciting.

.. code-block:: bash

	$ docker run alpine echo "Hello world"
	Hello world

OK, that's some actual output. In this case, the Docker client dutifully ran the ``echo`` command in our ``alpine`` container and then exited it. If you've noticed, all of that happened pretty quickly. Imagine booting up a virtual machine, running a command and then killing it. Now you know why they say containers are fast!

Try another command.

.. code-block:: bash

	$ docker run alpine sh

Wait, nothing happened! Is that a bug? Well, no. These interactive shells will exit after running any scripted commands such as ``sh``, unless they are run in an interactive terminal - so for this example to not exit, you need to ``docker run -it alpine sh``. You are now inside the container shell and you can try out a few commands like ``ls -l``, ``uname -a`` and others.

Before doing that, now it's time to see the ``docker ps`` command which shows you all containers that are currently running.

.. code-block:: bash

	$ docker ps
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

Since no containers are running, you see a blank line. Let's try a more useful variant: ``docker ps -a``

.. code-block:: bash

	$ docker ps -a
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
	36171a5da744        alpine              "/bin/sh"                5 minutes ago       Exited (0) 2 minutes ago                        fervent_newton
	a6a9d46d0b2f        alpine             "echo 'hello from alp"    6 minutes ago       Exited (0) 6 minutes ago                        lonely_kilby
	ff0a5c3750b9        alpine             "ls -l"                   8 minutes ago       Exited (0) 8 minutes ago                        elated_ramanujan
	c317d0a9e3d2        hello-world         "/hello"                 34 seconds ago      Exited (0) 12 minutes ago                       stupefied_mcclintock

What you see above is a list of all containers that you ran. Notice that the STATUS column shows that these containers exited a few minutes ago.

If you want to run scripted commands such as ``sh``, they should be run in an interactive terminal. In addition, interactive terminal allows you to run more than one command in a container. Let's try that now:

.. code-block:: bash

	$ docker run -it alpine sh
	/ # ls
	bin    dev    etc    home   lib    media  mnt    proc   root   run    sbin   srv    sys    tmp    usr    var
	/ # uname -a
	Linux de4bbc3eeaec 4.9.49-moby #1 SMP Wed Sep 27 23:17:17 UTC 2017 x86_64 Linux

Running the ``run`` command with the ``-it`` flags attaches us to an interactive ``tty`` in the container. Now you can run as many commands in the container as you want. Take some time to run your favorite commands.

Exit out of the container by giving the ``exit`` command.

.. code-block:: bash

	/ # exit

.. Note::

	If you type ``exit`` your **container** will exit and is no longer active. To check that, try the following::

		$ docker ps -l
		CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                          PORTS                    NAMES
		de4bbc3eeaec        alpine                "/bin/sh"                3 minutes ago       Exited (0) About a minute ago                            pensive_leavitt

	If you want to keep the container active, then you can use keys ``ctrl +p, ctrl +q``. To make sure that it is not exited run the same ``docker ps -a`` command again::

		$ docker ps -l
		CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                         PORTS                    NAMES
		0db38ea51a48        alpine                "sh"                     3 minutes ago       Up 3 minutes                                            elastic_lewin

	Now if you want to get back into that container, then you can type ``docker attach <container id>``. This way you can save your container::

		$ docker attach 0db38ea51a48

4. Managing data in Docker
==========================

It is possible to store data within the writable layer of a container, but there are some limitations:

- The data doesn’t persist when that container is no longer running, and it can be difficult to get the data out of the container if another process needs it.

- A container’s writable layer is tightly coupled to the host machine where the container is running. You can’t easily move the data somewhere else.

Docker offers three different ways to mount data into a container from the Docker host: **volumes**, **bind mounts**, or **tmpfs volumes**. When in doubt, volumes are almost always the right choice.

4.1 Volumes
~~~~~~~~~~~

**Volumes** are created and managed by Docker. You can create a volume explicitly using the ``docker volume create`` command, or Docker can create a volume during container creation. When you create a volume, it is stored within a directory on the Docker host (``/var/lib/docker/`` on Linux and check for the location on mac in here https://timonweb.com/posts/getting-path-and-accessing-persistent-volumes-in-docker-for-mac/). When you mount the volume into a container, this directory is what is mounted into the container. A given volume can be mounted into multiple containers simultaneously. When no running container is using a volume, the volume is still available to Docker and is not removed automatically. You can remove unused volumes using ``docker volume prune`` command.

|volumes|

Volumes are often a better choice than persisting data in a container’s writable layer, because using a volume does not increase the size of containers using it, and the volume’s contents exist outside the lifecycle of a given container. While bind mounts (which we will see later) are dependent on the directory structure of the host machine, volumes are completely managed by Docker. Volumes have several advantages over bind mounts:

- Volumes are easier to back up or migrate than bind mounts.
- You can manage volumes using Docker CLI commands or the Docker API.
- Volumes work on both Linux and Windows containers.
- Volumes can be more safely shared among multiple containers.
- A new volume’s contents can be pre-populated by a container.

.. Note::

	If your container generates non-persistent state data, consider using a ``tmpfs`` mount to avoid storing the data anywhere permanently, and to increase the container’s performance by avoiding writing into the container’s writable layer.

4.1.1 Choose the -v or –mount flag for mounting volumes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Originally, the ``-v`` or ``--volume`` flag was used for standalone containers and the ``--mount`` flag was used for swarm services. However, starting with Docker 17.06, you can also use ``--mount`` with standalone containers. In general, ``--mount`` is more explicit and verbose. The biggest difference is that the ``-v`` syntax combines all the options together in one field, while the ``--mount`` syntax separates them. Here is a comparison of the syntax for each flag.

.. Tip::

 	New users should use the ``--mount`` syntax. Experienced users may be more familiar with the ``-v`` or ``--volume`` syntax, but are encouraged to use ``--mount``, because research has shown it to be easier to use.

``-v`` or ``--volume``: Consists of three fields, separated by colon characters (:). The fields must be in the correct order, and the meaning of each field is not immediately obvious.
- In the case of named volumes, the first field is the name of the volume, and is unique on a given host machine.
- The second field is the path where the file or directory are mounted in the container.
- The third field is optional, and is a comma-separated list of options, such as ``ro``.

``--mount``: Consists of multiple key-value pairs, separated by commas and each consisting of a ``<key>=<value>`` tuple. The ``--mount`` syntax is more verbose than ``-v`` or ``--volume``, but the order of the keys is not significant, and the value of the flag is easier to understand.
- The type of the mount, which can be **bind**, **volume**, or **tmpfs**.
- The source of the mount. For named volumes, this is the name of the volume. For anonymous volumes, this field is omitted. May be specified as **source** or **src**.
- The destination takes as its value the path where the file or directory is mounted in the container. May be specified as **destination**, **dst**, or **target**.
- The readonly option, if present, causes the bind mount to be mounted into the container as read-only.

.. Note::

	The ``--mount`` and ``-v`` examples have the same end result.

4.1.2. Create and manage volumes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Unlike a bind mount, you can create and manage volumes outside the scope of any container.

Let's create a volume

.. code-block:: bash

	$ docker volume create my-vol

List volumes:

.. code-block:: bash

	$ docker volume ls

	local               my-vol

Inspect a volume by looking at the Mount section in the `docker volume inspect`

.. code-block:: bash

	$ docker volume inspect my-vol
	[
	    {
	        "Driver": "local",
	        "Labels": {},
	        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
	        "Name": "my-vol",
	        "Options": {},
	        "Scope": "local"
	    }
	]

Remove a volume

.. code-block:: bash

	$ docker volume rm my-vol

4.1.3 Populate a volume using a container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This example starts an ``nginx`` container and populates the new volume ``nginx-vol`` with the contents of the container’s ``/var/log/nginx`` directory, which is where Nginx stores its log files.

.. code-block:: bash

	$ docker run -d -p 8891:80 --name=nginxtest --mount source=nginx-vol,target=/var/log/nginx nginx:latest

So, we now have a copy of Nginx running inside a Docker container on our machine, and our host machine's port 5000 maps directly to that copy of Nginx's port 80. Let's use curl to do a quick test request:

.. code-block:: bash

	$ curl localhost:8891
	<!DOCTYPE html>
	<html>
	<head>
	<title>Welcome to nginx!</title>
	<style>
	    body {
	        width: 35em;
	        margin: 0 auto;
	        font-family: Tahoma, Verdana, Arial, sans-serif;
	    }
	</style>
	</head>
	<body>
	<h1>Welcome to nginx!</h1>
	<p>If you see this page, the nginx web server is successfully installed and
	working. Further configuration is required.</p>

	<p>For online documentation and support please refer to
	<a href="http://nginx.org/">nginx.org</a>.<br/>
	Commercial support is available at
	<a href="http://nginx.com/">nginx.com</a>.</p>

	<p><em>Thank you for using nginx.</em></p>
	</body>
	</html>

You'll get a screenful of HTML back from Nginx showing that Nginx is up and running. But more interestingly, if you look in the ``nginx-vol`` volume on the host machine and take a look at the ``access.log`` file you'll see a log message from Nginx showing our request.

.. code-block:: bash

	cat nginx-vol/_data/access.log

Use ``docker inspect nginx-vol`` to verify that the volume was created and mounted correctly. Look for the Mounts section:

.. code-block:: bash

	"Mounts": [
	            {
	                "Type": "volume",
	                "Name": "nginx-vol",
	                "Source": "/var/lib/docker/volumes/nginx-vol/_data",
	                "Destination": "/var/log/nginx",
	                "Driver": "local",
	                "Mode": "z",
	                "RW": true,
	                "Propagation": ""
	            }
	        ],

This shows that the mount is a volume, it shows the correct source and destination, and that the mount is read-write.

After running either of these examples, run the following commands to clean up the containers and volumes.

.. code-block:: bash

	$ docker stop nginxtest

	$ docker rm nginxtest

	$ docker volume rm nginx-vol

4.2 Bind mounts
~~~~~~~~~~~~~~~

**Bind mounts:** When you use a bind mount, a file or directory on the host machine is mounted into a container.

.. tip::

	If you are developing new Docker applications, consider using named **volumes** instead. You can’t use Docker CLI commands to directly manage bind mounts.

|bind_mount|

.. Warning::

	One side effect of using bind mounts, for better or for worse, is that you can change the host filesystem via processes running in a container, including creating, modifying, or deleting important system files or directories. This is a powerful ability which can have security implications, including impacting non-Docker processes on the host system.

	If you use ``--mount`` to bind-mount a file or directory that does not yet exist on the Docker host, Docker does not automatically create it for you, but generates an error.

4.2.1 Start a container with a bind mount
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

	$ mkdir data

	$ docker run -d -p 8891:80 --name devtest --mount type=bind,source="$(pwd)"/data,target=/var/log/nginx nginx:latest

Use `docker inspect devtest` to verify that the bind mount was created correctly. Look for the "Mounts" section

.. code-block:: bash

	$ docker inspect devtest

	"Mounts": [
	            {
	                "Type": "bind",
	                "Source": "/Users/upendra_35/Documents/git.repos/flask-app/data",
	                "Destination": "/var/log/nginx",
	                "Mode": "",
	                "RW": true,
	                "Propagation": "rprivate"
	            }
	        ],

This shows that the mount is a bind mount, it shows the correct source and target, it shows that the mount is read-write, and that the propagation is set to rprivate.

Stop the container:

.. code-block:: bash

	$ docker rm -f devtest

4.2.2 Use a read-only bind mount
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For some development applications, the container needs to write into the bind mount, so changes are propagated back to the Docker host. At other times, the container only needs read access.

This example modifies the one above but mounts the directory as a read-only bind mount, by adding ``ro`` to the (empty by default) list of options, after the mount point within the container. Where multiple options are present, separate them by commas.

.. code-block:: bash

	$ docker run -d -p 8891:80 --name devtest --mount type=bind,source="$(pwd)"/data,target=/var/log/nginx,readonly nginx:latest

Use ``docker inspect devtest`` to verify that the bind mount was created correctly. Look for the Mounts section:

.. code-block:: bash

	"Mounts": [
            {
                "Type": "bind",
                "Source": "/Users/upendra_35/Documents/git.repos/flask-app/data",
                "Destination": "/var/log/nginx",
                "Mode": "",
                "RW": false,
                "Propagation": "rprivate"
            }
        ],

Stop the container:

.. code-block:: bash

	$ docker rm -f devtest

Remove the volume:

.. code-block:: bash

	$ docker volume rm devtest

4.3 tmpfs
~~~~~~~~~

**tmpfs mounts:** A tmpfs mount is not persisted on disk, either on the Docker host or within a container. It can be used by a container during the lifetime of the container, to store non-persistent state or sensitive information. For instance, internally, swarm services use tmpfs mounts to mount secrets into a service’s containers.

|tmpfs|

**Volumes** and **bind mounts** are mounted into the container’s filesystem by default, and their contents are stored on the host machine. There may be cases where you do not want to store a container’s data on the host machine, but you also don’t want to write the data into the container’s writable layer, for performance or security reasons, or if the data relates to non-persistent application state. An example might be a temporary one-time password that the container’s application creates and uses as-needed. To give the container access to the data without writing it anywhere permanently, you can use a tmpfs mount, which is only stored in the host machine’s memory (or swap, if memory is low). When the container stops, the tmpfs mount is removed. If a container is committed, the tmpfs mount is not saved.

.. code-block:: bash

	$ docker run -d -p 8891:80 --name devtest --mount type=tmpfs,target=/var/log/nginx nginx:latest

Use `docker inspect devtest` to verify that the bind mount was created correctly. Look for the Mounts section:

.. code-block:: bash

	$ docker inspect devtest

	"Mounts": [
	            {
	                "Type": "tmpfs",
	                "Source": "",
	                "Destination": "/var/log/nginx",
	                "Mode": "",
	                "RW": true,
	                "Propagation": ""
	            }
	        ],

You can see from the above output that the ``Source`` filed is empty which indicates that the contents are not avaible on Docker host or host file system.

Stop the container:

.. code-block:: bash

	$ docker rm -f devtest

Remove the volume:

.. code-block:: bash

	$ docker volume rm devtest

Use case 1: Processing VLBI data with Docker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

TODO: use HOPS to fringe fit VLBI data.

5. Expose container ports
=========================

TODO: exposing ports

Use case 2: Improving your data science workflow using Docker containers (Containerized Data Science)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For a data scientist, running a container that is already equipped with the libraries and tools needed for a particular analysis eliminates the need to spend hours debugging packages across different environments or configuring custom environments.

But why Set Up a Data Science Environment in a Container?

- One reason is speed. We want data scientists using our platform to launch a Jupyter or RStudio session in minutes, not hours. We also want them to have that fast user experience while still working in a governed, central architecture (rather than on their local machines).

- Containerization benefits both data science and IT/technical operations teams. In the DataScience.com Platform, for instance, we allow IT to configure environments with different languages, libraries, and settings in an admin dashboard and make those images available in the dropdown menu when a data scientist launches a session. These environments can be selected for any run, session, scheduled job, or API. (Or you don’t have to configure anything at all. We provide plenty of standard environment templates to choose from.)

- Ultimately, containers solve a lot of common problems associated with doing data science work at the enterprise level. They take the pressure off of IT to produce custom environments for every analysis, standardize how data scientists work, and ensure that old code doesn’t stop running because of environment changes. To start using containers and our library of curated images to do collaborative data science work, request a demo of our platform today.

- Configuring a data science environment can be a pain. Dealing with inconsistent package versions, having to dive through obscure error messages, and having to wait hours for packages to compile can be frustrating. This makes it hard to get started with data science in the first place, and is a completely arbitrary barrier to entry.

Thanks to the rich ecosystem, there are already several readily available images for the common components in data science pipelines. Here are some Docker images to help you quickly spin up your own data science pipeline:

- `MySQL <https://hub.docker.com/_/mysql/>`_
- `Postgres <https://hub.docker.com/_/postgres/>`_
- `Redmine <https://hub.docker.com/_/redmine/>`_
- `MongoDB <https://hub.docker.com/_/mongo/>`_
- `Hadoop <https://hub.docker.com/r/sequenceiq/hadoop-docker/>`_
- `Spark <https://hub.docker.com/r/sequenceiq/spark/>`_
- `Zookeeper <https://hub.docker.com/r/wurstmeister/zookeeper/>`_
- `Kafka <https://github.com/spotify/docker-kafka>`_
- `Cassandra <https://hub.docker.com/_/cassandra/>`_
- `Storm <https://github.com/wurstmeister/storm-docker>`_
- `Flink <https://github.com/apache/flink/tree/master/flink-contrib/docker-flink>`_
- `R <https://github.com/rocker-org/rocker>`_

Motivation: Say you want to play around with some cool data science libraries in Python or R but what you don’t want to do is spend hours on installing Python or R, working out what libraries you need, installing each and every one and then messing around with the tedium of getting things to work just right on your version of Linux/Windows/OSX/OS9 — well this is where Docker comes to the rescue! With Docker we can get a Jupyter ‘Data Science’ notebook stack up and running in no time at all. Let’s get started! We will see few examples of thse in the following sections...

.. Note::

	The above code can be found in this `github <https://github.com/upendrak/jupyternotebook_docker>`_

1. Launch a Jupyter notebook conatiner

Docker allows us to run a ‘ready to go’ Jupyter data science stack in what’s known as a container:

1.1 Create a `docker-compose.yml` file

.. code-block:: bash

	$ mkdir jn && cd jn

.. code-block:: bash

	version: '2'

	services:
	  datascience-notebook:
	    image: jupyter/datascience-notebook
	    volumes:
	      - .:/data
	    ports:
	      - 8888:8888
	    container_name:   datascience-notebook-container

.. Note::

	The ``jupyter/datascience-notebook`` image can be found on dockerhub

|jn_ss|

1.2 Run container using docker-compose file

.. code-block:: bash

	$ docker-compose up
	Creating datascience-notebook-container ...
	Creating datascience-notebook-container ... done
	Attaching to datascience-notebook-container
	datascience-notebook-container | Execute the command: jupyter notebook
	datascience-notebook-container | [I 08:44:31.312 NotebookApp] Writing notebook server cookie secret to /home/jovyan/.local/share/jupyter/runtime/notebook_cookie_secret
	datascience-notebook-container | [W 08:44:31.332 NotebookApp] WARNING: The notebook server is listening on all IP addresses and not using encryption. This is not 	recommended.
	datascience-notebook-container | [I 08:44:31.370 NotebookApp] JupyterLab alpha preview extension loaded from /opt/conda/lib/python3.6/site-packages/jupyterlab
	datascience-notebook-container | JupyterLab v0.27.0
	datascience-notebook-container | Known labextensions:
	datascience-notebook-container | [I 08:44:31.373 NotebookApp] Running the core application with no additional extensions or settings
	datascience-notebook-container | [I 08:44:31.379 NotebookApp] Serving notebooks from local directory: /home/jovyan
	datascience-notebook-container | [I 08:44:31.379 NotebookApp] 0 active kernels
	datascience-notebook-container | [I 08:44:31.379 NotebookApp] The Jupyter Notebook is running at: http://[all ip addresses on your 	system]:8888/?token=dfb50de6c1da091fd62336ac52cdb88de5fe339eb0faf478
	datascience-notebook-container | [I 08:44:31.379 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
	datascience-notebook-container | [C 08:44:31.380 NotebookApp]
	datascience-notebook-container |
	datascience-notebook-container |     Copy/paste this URL into your browser when you connect for the first time,
	datascience-notebook-container |     to login with a token:
	datascience-notebook-container |         http://localhost:8888/?token=dfb50de6c1da091fd62336ac52cdb88de5fe339eb0faf478

The last line is a URL that we need to copy and paste into our browser to access our new Jupyter stack:

.. code-block:: bash

	http://localhost:8888/?token=dfb50de6c1da091fd62336ac52cdb88de5fe339eb0faf478

.. warning::

	Do not copy and paste the above URL in your browser as this URL is specific to my environment.

Once you’ve done that you should be greeted by your very own containerised Jupyter service!

|jn_login|

To create your first notebook, drill into the work directory and then click on the ‘New’ button on the right hand side and choose ‘Python 3’ to create a new Python 3 based Notebook.

|jn_login2|

Now you can write your python code. Here is an example

|jn_login3|

|jn_login3.5|

To shut down the container once you’re done working, simply hit Ctrl-C in the terminal/command prompt. Your work will all be saved on your actual machine in the path we set in our Docker compose file. And there you have it — a quick and easy way to start using Jupyter notebooks with the magic of Docker.

2. Launch a R-Studio container

Next, we will see a Docker image from Rocker which will allow us to run RStudio inside the container and has many useful R packages already installed.

|rstudio_ss|

.. code-block:: bash

	$ docker run --rm -d -p 8787:8787 rocker/rstudio:3.4.3

.. Note::

	 ``–rm`` ensures that when we quit the container, the container is deleted. If we did not do this, everytime we run a container, a version of it will be saved to our local computer. This can lead to the eventual wastage of a lot of disk space until we manually remove these containers.

The command above will lead RStudio-Server to launch invisibly. To connect to it, open a browser and enter http://localhost:8787, or <ipaddress>:8787 on cloud

|rstudio_login2|

Enter ``rstudio`` as username and password. Finally Rstudio shows up and you can run your R command from here

|rstudio_login|

3. Machine learning using Docker

In this simple example we’ll take a sample dataset of fruits metrics (like size, weight, texture) labelled apples and oranges. Then we can predict the fruit given a new set of fruit metrics using scikit-learn’s decision tree

You can find the above code in this `github repo <https://github.com/upendrak/scikit_tree_docker>`_

1. Create a directory that consists of all the files

.. code-block:: bash

	$ mkdir scikit_docker && cd scikit_docker

2. Create ``requirements.txt`` file — Contains python modules and has nothing to do with Docker inside the folder - ``scikit_docker``.

.. code-block:: bash

	numpy
	scipy
	scikit-learn

3. Create a file called ``app.py`` inside the folder — ``scikit_docker``

.. code-block:: bash

	from sklearn import tree
	#DataSet
	#[size,weight,texture]
	X = [[181, 80, 44], [177, 70, 43], [160, 60, 38], [154, 54, 37],[166, 65, 40], [190, 90, 47], [175, 64, 39], [177, 70, 40], [159, 55, 37], [171, 75, 42], [181, 85, 43]]

	Y = ['apple', 'apple', 'orange', 'orange', 'apple', 'apple', 'orange', 'orange', 'orange', 'apple', 'apple']

	#classifier - DecisionTreeClassifier
	clf_tree = tree.DecisionTreeClassifier();
	clf_tree = clf_tree.fit(X,Y);

	#test_data
	test_data = [[190,70,42],[172,64,39],[182,80,42]];

	#prediction
	prediction_tree = clf_tree.predict(test_data);

	# Write output to a file
	with open("output.txt", 'w') as fh_out:
		fh_out.write("Prediction of DecisionTreeClassifier:")
		fh_out.write(str(prediction_tree))

4. Create a Dockerfile that contains all the instructions for building a Docker image inside the project directory

.. code-block:: bash

	# Use an official Python runtime as a parent image
	FROM python:3.6-slim
	MAINTAINER Upendra Devisetty <upendra@cyverse.org>
	LABEL Description "This Dockerfile is used to build a scikit-learn’s decision tree image"

	# Set the working directory to /app
	WORKDIR /app

	# Copy the current directory contents into the container at /app
	ADD . /app

	# Install any needed packages specified in requirements.txt
	RUN pip install -r requirements.txt

	# Define environment variable
	ENV NAME World

	# Run app.py when the container launches
	CMD ["python", "app.py"]

5. Create a Docker compose YAML file

.. code-block:: bash

	version: '2'
	services:
	    datasci:
	        build: .
	        volumes:
	            - .:/app

5. Now Build and Run the Docker image using `docker-compose up` command to predict the fruit given a new set of fruit metrics

.. code-block:: bash

	$ docker-compose up
	Building datasci
	Step 1/8 : FROM python:3.6-slim
	 ---> dc41c0491c65
	Step 2/8 : MAINTAINER Upendra Devisetty <upendra@cyverse.org>
	 ---> Running in 95a4da823100
	 ---> 7c4d5b78bb0a
	Removing intermediate container 95a4da823100
	Step 3/8 : LABEL Description "This Dockerfile is used to build a scikit-learn’s decision tree image"
	 ---> Running in e8000ae57a7d
	 ---> d872e29971e3
	Removing intermediate container e8000ae57a7d
	Step 4/8 : WORKDIR /app
	 ---> 083eb3e4fb16
	Removing intermediate container c965871286f9
	Step 5/8 : ADD . /app
	 ---> 82b1dbdbe759
	Step 6/8 : RUN pip install -r requirements.txt
	 ---> Running in 3c82f7d5dd95
	Collecting numpy (from -r requirements.txt (line 1))
	  Downloading numpy-1.14.0-cp36-cp36m-manylinux1_x86_64.whl (17.2MB)
	Collecting scipy (from -r requirements.txt (line 2))
	  Downloading scipy-1.0.0-cp36-cp36m-manylinux1_x86_64.whl (50.0MB)
	Collecting scikit-learn (from -r requirements.txt (line 3))
	  Downloading scikit_learn-0.19.1-cp36-cp36m-manylinux1_x86_64.whl (12.4MB)
	Installing collected packages: numpy, scipy, scikit-learn
	Successfully installed numpy-1.14.0 scikit-learn-0.19.1 scipy-1.0.0
	 ---> 3d402c23203f
	Removing intermediate container 3c82f7d5dd95
	Step 7/8 : ENV NAME World
	 ---> Running in d0468b521e81
	 ---> 9cd31e8e7c95
	Removing intermediate container d0468b521e81
	Step 8/8 : CMD python app.py
	 ---> Running in 051bd2235697
	 ---> 36bb4c3d9183
	Removing intermediate container 051bd2235697
	Successfully built 36bb4c3d9183
	Successfully tagged scikitdocker_datasci:latest
	WARNING: Image for service datasci was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
	Creating scikitdocker_datasci_1 ...
	Creating scikitdocker_datasci_1 ... done
	Attaching to scikitdocker_datasci_1
	scikitdocker_datasci_1 exited with code 0

Use ``docker-compose rm`` to remove the container after docker-compose finish running

.. code-block:: bash

	docker-compose rm
	Going to remove scikitdocker_datasci_1
	Are you sure? [yN] y
	Removing scikitdocker_datasci_1 ... done

You will find the ouput file in the ``scikit_docker`` folder with the following contents

.. code-block:: bash

	$ cat output.txt
	Prediction of DecisionTreeClassifier:['apple' 'orange' 'apple']

.. |docker| image:: ../img/docker.png
  :width: 750
  :height: 700

.. |volumes| image:: ../img/volumes.png
  :width: 750
  :height: 700

.. |bind_mount| image:: ../img/bind_mount.png
  :width: 750
  :height: 700

.. |tmpfs| image:: ../img/tmpfs.png
  :width: 750
  :height: 700

.. |jn_ss| image:: ../img/jn_ss.png
  :width: 750
  :height: 700

.. |jn_login| image:: ../img/jn_login.png
  :width: 750
  :height: 700

.. |jn_login2| image:: ../img/jn_login2.png
  :width: 750
  :height: 700

.. |jn_login3| image:: ../img/jn_login3.png
  :width: 750
  :height: 700

.. |jn_login3.5| image:: ../img/jn_login3.5.png
  :width: 750
  :height: 700

.. |rstudio_ss| image:: ../img/rstudio_ss.png
  :width: 750
  :height: 700

.. |rstudio_login2| image:: ../img/rstudio_login2.png
  :width: 750
  :height: 700

.. |rstudio_login| image:: ../img/rstudio_login.png
  :width: 750
  :height: 700
