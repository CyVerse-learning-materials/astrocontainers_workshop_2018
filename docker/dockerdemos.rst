**Additional Docker Demo's**
----------------------------

1. Portainer
============

`Portainer <https://portainer.io/>`_ is an open-source lightweight management UI which allows you to easily manage your Docker hosts or Swarm cluster.

- Simple to use: It has never been so easy to manage Docker. Portainer provides a detailed overview of Docker and allows you to manage containers, images, networks and volumes. It is also really easy to deploy, you are just one Docker command away from running Portainer anywhere.

- Made for Docker: Portainer is meant to be plugged on top of the Docker API. It has support for the latest versions of Docker, Docker Swarm and Swarm mode.

7.1 Installation
~~~~~~~~~~~~~~~~

Use the following Docker commands to deploy Portainer. Now the second line of command should be familiar to you by now. We will talk about first line of command in the Advanced Docker session.

.. code-block:: bash

	$ docker volume create portainer_data

	$ docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

- If you are on mac, you'll just need to access the port 9000 (http://localhost:9000) of the Docker engine where portainer is running using username ``admin`` and password ``tryportainer``

- If you are running Docker on Atmosphere/Jetstream or on any other cloud, you can open ``ipaddress:9000``. For my case this is ``http://128.196.142.26:9000``

.. Note::

	The `-v /var/run/docker.sock:/var/run/docker.sock` option can be used in mac/linux environments only.

.. image:: ../img/portainer_demo.png
  :width: 550
  :height: 500
  :scale: 100%
  :align: center

2 Play-with-docker (PWD)
========================

`PWD <http://www.play-with-docker.com/>`_ is a Docker playground which allows users to run Docker commands in a matter of seconds. It gives the experience of having a free Alpine Linux Virtual Machine in browser, where you can build and run Docker containers and even create clusters in `Docker Swarm Mode <https://docs.docker.com/engine/swarm/>`_. Under the hood, Docker-in-Docker (DinD) is used to give the effect of multiple VMs/PCs. In addition to the playground, PWD also includes a training site composed of a large set of Docker labs and quizzes from beginner to advanced level available at `training.play-with-docker.com <http://training.play-with-docker.com/>`_.

2.1 Installation
~~~~~~~~~~~~~~~~

You don't have to install anything to use PWD. Just open ``https://labs.play-with-docker.com/`` and start using PWD

.. Note::

	You can use your Dockerhub credentials to log-in to PWD

.. image:: ../img/pwd.png
  :width: 550
  :height: 500
  :scale: 100%
  :align: center

