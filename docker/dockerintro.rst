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

2.1 Testing Docker installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

3. Running Docker containers from prebuilt images
=================================================

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

5. Deploying static website with Docker
=======================================

Great! so you have now looked at ``docker run``, played with a Docker containers and also got the hang of some terminology. Armed with all this knowledge, you are now ready to get to the real stuff — deploying web applications with Docker.

Let's start by taking baby-steps. First, we'll use Docker to run a static website in a container. The website is based on an existing image and in the next section we will see how to build a new image and run a website in that container. We'll pull a Docker image from Dockerhub, run the container, and see how easy it is to set up a web server.

.. Note::

	Code for this section is in this repo in the `static-site directory <https://github.com/docker/labs/tree/master/beginner/static-site>`_

The image that you are going to use is a single-page website that was already created for this demo and is available on the Dockerhub as `dockersamples/static-site <https:/hub.docker.com/community/images/dockersamples/static-site>`_. You can pull and run the image directly in one go using ``docker run`` as follows.

.. code-block:: bash

	$ docker run -d dockersamples/static-site

.. Note::

	The ``-d`` flag enables detached mode, which detaches the running container from the terminal/shell and returns your prompt after the container starts.

So, what happens when you run this command?

Since the image doesn't exist on your Docker host (laptop/computer), the Docker daemon first fetches it from the registry and then runs it as a container.

Now that the server is running, do you see the website? What port is it running on? And more importantly, how do you access the container directly from our host machine?

Actually, you probably won't be able to answer any of these questions yet! ☺ In this case, the client didn't tell the Docker Engine to publish any of the ports, so you need to re-run the ``docker run`` command to add this instruction.

Let's re-run the command with some new flags to publish ports and pass your name to the container to customize the message displayed. We'll use the ``-d`` option again to run the container in detached mode.

First, stop the container that you have just launched. In order to do this, we need the container ID.

Since we ran the container in detached mode, we don't have to launch another terminal to do this. Run ``docker ps`` to view the running containers.

.. code-block:: bash

	$ docker ps
	CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
	a7a0e504ca3e        dockersamples/static-site   "/bin/sh -c 'cd /usr/"   28 seconds ago      Up 26 seconds       80/tcp, 443/tcp     stupefied_mahavira

Check out the CONTAINER ID column. You will need to use this CONTAINER ID value, a long sequence of characters, to identify the container you want to stop, and then to remove it. The example below provides the CONTAINER ID on our system; you should use the value that you see in your terminal.

.. code-block:: bash

	$ docker stop a7a0e504ca3e
	$ docker rm   a7a0e504ca3e

.. Note::

	A cool feature is that you do not need to specify the entire **CONTAINER ID**. You can just specify a few starting characters and if it is unique among all the containers that you have launched, the Docker client will intelligently pick it up.

Now, let's launch a container in detached mode as shown below:

.. code-block:: bash

	$ docker run --name static-site -d -P dockersamples/static-site
	e61d12292d69556eabe2a44c16cbd54486b2527e2ce4f95438e504afb7b02810

In the above command:

-	``-d`` will create a container with the process detached from our terminal
-	``-P`` will publish all the exposed container ports to random ports on the Docker host
-	``--name`` allows you to specify a container name

Now you can see the ports by running the ``docker port`` command.

.. code-block:: bash

	$ docker port static-site
	443/tcp -> 0.0.0.0:32770
	80/tcp -> 0.0.0.0:32773

If you are running Docker for Mac, Docker for Windows, or Docker on Linux, open a web browser and go to port 80 on your host. The exact address will depend on how you're running Docker

- Laptop or Native linux: ``http://localhost:[YOUR_PORT_FOR 80/tcp]``. On my system this is ``http://localhost:32773``.

|static_site_docker|

- Cloud server: If you are running the same set of commands on Atmosphere/Jetstream or on any other cloud service, you can open ``ipaddress:[YOUR_PORT_FOR 80/tcp]``. On my Atmosphere instance this is ``http://128.196.142.26:32769/``. We will see more about deploying Docker containers on Atmosphere/Jetstream Cloud in the Advanced Docker session.

|static_site_docker1|

.. Note::

	``-P` `will publish all the exposed container ports to random ports on the Docker host. However if you want to assign a fixed port then you can use ``-p`` option. The format is ``-p <host port>:<container port>``. For example:

.. code-block:: bash

	$ docker run --name static-site2 -d -p 8088:80 dockersamples/static-site

If you are running Docker for Mac, Docker for Windows, or Docker on Linux, you can open ``http://localhost:[YOUR_PORT_FOR 80/tcp]``. For our example this is ``http://localhost:8088``.

If you are running Docker on Atmosphere/Jetstream or on any other cloud, you can open ``ipaddress:[YOUR_PORT_FOR 80/tcp]``. For our example this is ``http://128.196.142.26:8088/``

If you see “Hello Docker!” then you’re done!

Let's stop and remove the containers since you won't be using them anymore.

.. code-block:: bash

	$ docker stop static-site static-site2
	$ docker rm static-site static-site2

Let's use a shortcut to both stop and delete that container from your system:

.. code-block:: bash

	$ docker rm -f static-site static-site2

Run ``docker ps`` to make sure the containers are gone.

.. code-block:: bash

	$ docker ps
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

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

.. |static_site_docker| image:: ../img/static_site_docker.png
  :width: 750
  :height: 700

.. |static_site_docker1| image:: ../img/static_site_docker1.png
  :width: 750
  :height: 700
