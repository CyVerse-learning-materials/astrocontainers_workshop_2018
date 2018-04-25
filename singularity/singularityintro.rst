**Introduction to Singularity**
-------------------------------

|singularity|

1. Prerequisites
================

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor. Prior experience developing web applications could be helpful but is not required.

.. Note:: 
      
      *Important*: Docker and Singularity are `friends <http://singularity.lbl.gov/docs-docker>`_ but they have distinct differences. 
      
     `Singularity Related Resources <https://cyverse-container-camp-workshop-2018.readthedocs-hosted.com/en/latest/useful_resources/usefulresources_singularity.html>`_
      
      **Docker**:
      
      * Inside a Docker container the user has escalated privileges, effectively making them root on the host system. This is not supported by most administrators of High Performance Computing (HPC) centers.
      
      **Singularity**:
     
      * Work on HPC
      * Same user inside and outside the container
      * User only has root privileges if elevated with `sudo`
      * Run (and modify!) existing Docker containers

Singularity uses a 'flow' whereby you can (1) create and modify images on your dev system, (2) build containers using recipes or pulling from repositories, and (3) execute containers on production systems. 

|singularityflow|

2. Singularity Installation
===========================

Singularity homepage: `http://singularity.lbl.gov/ <http://singularity.lbl.gov/>`_

While Singularity is more likely to be used on a remote system, e.g. HPC or cloud, you may want to develop your own containers first on a local machine or dev system. 

Exercise 1 (15-20 mins)
~~~~~~~~~~~~~~~~~~~~~~~

2.1 Setting up your Laptop
~~~~~~~~~~~~~~~~~~~~~~~~~~

To Install Singularity on your laptop or desktop PC follow the instructions from Singularity: (`Mac <http://singularity.lbl.gov/install-mac>`_, `Windows <http://singularity.lbl.gov/install-windows>`_, `Linux <http://singularity.lbl.gov/install-linux>`_)

  * running a VM is required on Mac OS X, Singularityware `VagrantBox <https://app.vagrantup.com/singularityware/boxes/singularity-2.4/versions/2.4>`_
  
2.2 HPC
~~~~~~~

Load the Singularity module on a HPC

If you are interested in working on HPC, you may need to contact your systems administrator and request they install `Singularity  <http://singularity.lbl.gov/install-request>`_. 

Most HPC systems are running Environment Modules with the simple command `module`. You can check to see what is available:

.. code-block:: bash

  $ module avail

If Singularity is installed:

.. code-block:: bash

	$ module load singularity

2.3 XSEDE Jetstream / CyVerse Atmosphere Clouds
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

CyVerse staff have deployed an Ansible playbooks called `ez` installation which includes `Singularity <https://cyverse-ez-quickstart.readthedocs-hosted.com/en/latest/#>`_ that only requires you to type a short line of code.

Start a featured instance on Atmosphere or Jetstream.

Type in the following:

.. code-block:: bash

    $ ezs 
    
    * Updating ez singularity and installing singularity (this may take a few minutes, coffee break!)
    Cloning into '/opt/cyverse-ez-singularity'...
    remote: Counting objects: 11, done.
    remote: Total 11 (delta 0), reused 0 (delta 0), pack-reused 11
    Unpacking objects: 100% (11/11), done.
    Checking connectivity... done.

2.4 Check Installation
~~~~~~~~~~~~~~~~~~~~~~

Singularity should now be installed on your laptop or VM, or loaded on the HPC, you can check the installation with:

.. code-block:: bash

    $ singularity pull shub://vsoch/hello-world
    Progress |===================================| 100.0%
    Done. Container is at: /tmp/vsoch-hello-world-master.simg
   
    $ singularity run vsoch-hello-world-master.simg
    RaawwWWWWWRRRR!!

View the Singularity help:

.. code-block:: bash

	$ singularity --help
	
	USAGE: singularity [global options...] <command> [command options...] ...

	GLOBAL OPTIONS:
	    -d|--debug    Print debugging information
	    -h|--help     Display usage summary
	    -s|--silent   Only print errors
	    -q|--quiet    Suppress all normal output
	       --version  Show application version
	    -v|--verbose  Increase verbosity +1
	    -x|--sh-debug Print shell wrapper debugging information

	GENERAL COMMANDS:
	    help       Show additional help for a command or container                  
	    selftest   Run some self tests for singularity install                      

	CONTAINER USAGE COMMANDS:
	    exec       Execute a command within container                               
	    run        Launch a runscript within container                              
	    shell      Run a Bourne shell within container                              
	    test       Launch a testscript within container                             

	CONTAINER MANAGEMENT COMMANDS:
	    apps       List available apps within a container                           
	    bootstrap  *Deprecated* use build instead                                   
	    build      Build a new Singularity container                                
	    check      Perform container lint checks                                    
	    inspect    Display container's metadata                                     
	    mount      Mount a Singularity container image                              
	    pull       Pull a Singularity/Docker container to $PWD                      

	COMMAND GROUPS:
	    image      Container image command group                                    
	    instance   Persistent instance command group                                


	CONTAINER USAGE OPTIONS:
	    see singularity help <command>

	For any additional help or support visit the Singularity
	website: http://singularity.lbl.gov/


3. Downloading Singularity containers
=====================================

The easiest way to use a Singularity container is to `pull` an existing container from one of the Container Registries maintained by the Singularity group.

Exercise 2 (~10 mins)
~~~~~~~~~~~~~~~~~~~~~

3.1: Pulling a Container from Singularity Hub 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can use the `pull` command to download pre-built images from a number of Container Registries, here we'll be focusing on the `Singularity-Hub <https://www.singularity-hub.org>`_ or `DockerHub <https://hub.docker.com/>`_.

Container Registries: 

* `shub` - images hosted on Singularity Hub
* `docker` - images hosted on Docker Hub
* `localimage` - images saved on your machine
* `yum` - yum based systems such as CentOS and Scientific Linux
* `debootstrap` - apt based systems such as Debian and Ubuntu
* `arch` - Arch Linux
* `busybox` - BusyBox
* `zypper` - zypper based systems such as Suse and OpenSuse

In this example I am pulling a base Ubuntu container from Singularity-Hub:

.. code-block:: bash

    $ singularity pull shub://singularityhub/ubuntu
  
You can rename the container using the `--name` flag:
  
.. code-block:: bash

    $ singularity pull --name ubuntu_test.simg shub://singularityhub/ubuntu
    
After your image has finished downloading it should be in the present working directory, unless you specified to download it somewhere else.

.. code-block:: bash


	$ singularity pull --name ubuntu_test.simg shub://singularityhub/ubuntu
	Progress |===================================| 100.0% 
	Done. Container is at: /home/***/ubuntu_test.simg
	$ singularity run ubuntu_test.simg 
	This is what happens when you run the container...
	$ singularity shell ubuntu_test.simg 
	Singularity: Invoking an interactive shell within container...

	Singularity ubuntu_test.simg:~> cat /etc/*release
	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=14.04
	DISTRIB_CODENAME=trusty
	DISTRIB_DESCRIPTION="Ubuntu 14.04 LTS"
	NAME="Ubuntu"
	VERSION="14.04, Trusty Tahr"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu 14.04 LTS"
	VERSION_ID="14.04"
	HOME_URL="http://www.ubuntu.com/"
	SUPPORT_URL="http://help.ubuntu.com/"
	BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
	Singularity ubuntu_test.simg:~> 

Exercise 2.2: Pulling container from Docker Hub
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This example pulls a container from DockerHub

Build to your container by pulling an image from Docker:

.. code-block:: bash

	$ singularity pull docker://ubuntu:16.04
	WARNING: pull for Docker Hub is not guaranteed to produce the
	WARNING: same image on repeated pull. Use Singularity Registry
	WARNING: (shub://) to pull exactly equivalent images.
	Docker image path: index.docker.io/library/ubuntu:16.04
	Cache folder set to /home/.../.singularity/docker
	[5/5] |===================================| 100.0% 
	Importing: base Singularity environment
	Importing: /home/.../.singularity/docker/sha256:1be7f2b886e89a58e59c4e685fcc5905a26ddef3201f290b96f1eff7d778e122.tar.gz
	Importing: /home/.../.singularity/docker/sha256:6fbc4a21b806838b63b774b338c6ad19d696a9e655f50b4e358cc4006c3baa79.tar.gz
	Importing: /home/.../.singularity/docker/sha256:c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49.tar.gz
	Importing: /home/.../.singularity/docker/sha256:4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af.tar.gz
	Importing: /home/.../.singularity/docker/sha256:06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58.tar.gz
	Importing: /home/.../.singularity/metadata/sha256:c6a9ef4b9995d615851d7786fbc2fe72f72321bee1a87d66919b881a0336525a.tar.gz
	WARNING: Building container as an unprivileged user. If you run this container as root
	WARNING: it may be missing some functionality.
	Building Singularity image...
	Singularity container built: ./ubuntu-16.04.simg
	Cleaning up...
	Done. Container is at: ./ubuntu-16.04.simg
	
Note, there are some Warning messages concerning the build from Docker.

The example below does the same as above, but renames the image.	

.. code-block:: bash

	$ singularity pull --name ubuntu_docker.simg docker://ubuntu
   	Importing: /home/***/.singularity/docker/sha256:c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49.tar.gz
	Importing: /home/***/.singularity/docker/sha256:4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af.tar.gz
	Importing: /home/***/.singularity/docker/sha256:06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58.tar.gz
	Importing: /home/***/.singularity/metadata/sha256:c6a9ef4b9995d615851d7786fbc2fe72f72321bee1a87d66919b881a0336525a.tar.gz
	WARNING: Building container as an unprivileged user. If you run this container as root
	WARNING: it may be missing some functionality.
	Building Singularity image...
	Singularity container built: ./ubuntu_docker.simg
	Cleaning up...
	Done. Container is at: ./ubuntu_docker.simg

When we run this particular Docker container without any runtime arguments, it does not return any notifications, and the Bash prompt does not change the prompt.

.. code-block:: bash

	$ singularity run ubuntu_docker.simg 
	$ cat /etc/*release
	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=16.04
	DISTRIB_CODENAME=xenial
	DISTRIB_DESCRIPTION="Ubuntu 16.04.3 LTS"
	NAME="Ubuntu"
	VERSION="16.04.3 LTS (Xenial Xerus)"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu 16.04.3 LTS"
	VERSION_ID="16.04"
	HOME_URL="http://www.ubuntu.com/"
	SUPPORT_URL="http://help.ubuntu.com/"
	BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
	VERSION_CODENAME=xenial
	UBUNTU_CODENAME=xenial

Whoa, we're inside a container!?!

This is the OS on the VM I tested this on:

.. code-block:: bash 

	$ exit
	exit
	$ cat /etc/*release
	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=16.04
	DISTRIB_CODENAME=xenial
	DISTRIB_DESCRIPTION="Ubuntu 16.04.1 LTS"
	NAME="Ubuntu"
	VERSION="16.04.1 LTS (Xenial Xerus)"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu 16.04.1 LTS"
	VERSION_ID="16.04"
	HOME_URL="http://www.ubuntu.com/"
	SUPPORT_URL="http://help.ubuntu.com/"
	BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
	VERSION_CODENAME=xenial
	UBUNTU_CODENAME=xenial

Here we are back in the container:

.. code-block:: bash

	$ singularity shell ubuntu_docker.simg 
	Singularity: Invoking an interactive shell within container...

	Singularity ubuntu_docker.simg:~> cat /etc/*release
	DISTRIB_ID=Ubuntu
	DISTRIB_RELEASE=16.04
	DISTRIB_CODENAME=xenial
	DISTRIB_DESCRIPTION="Ubuntu 16.04.3 LTS"
	NAME="Ubuntu"
	VERSION="16.04.3 LTS (Xenial Xerus)"
	ID=ubuntu
	ID_LIKE=debian
	PRETTY_NAME="Ubuntu 16.04.3 LTS"
	VERSION_ID="16.04"
	HOME_URL="http://www.ubuntu.com/"
	SUPPORT_URL="http://help.ubuntu.com/"
	BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
	VERSION_CODENAME=xenial
	UBUNTU_CODENAME=xenial
	Singularity ubuntu_docker.simg:~> 

When invoking a container, make sure it executes and exits, or notifies you it is running. 

Keeping track of downloaded images may be necessary if space is a concern. 

By default, Singularity uses a temporary cache to hold Docker tarballs:

.. code-block:: bash

  $ ls ~/.singularity
  
You can change these by specifying the location of the cache and temporary directory on your localhost:

.. code-block:: bash

  $ sudo mkdir tmp
  $ sudo mkdir scratch
  
  $ SINGULARITY_TMPDIR=$PWD/scratch SINGULARITY_CACHEDIR=$PWD/tmp singularity --debug pull --name ubuntu-tmpdir.simg docker://ubuntu

As an example, using Singularity we can run a UI program that was built from Docker, here I show the IDE RStudio `tidyverse` from `Rocker <https://hub.docker.com/r/rocker/rstudio/>`_ 

.. code-block:: bash

	$ singularity exec docker://rocker/tidyverse:latest R

`"An Introduction to Rocker: Docker Containers for R by Carl Boettiger, Dirk Eddelbuettel" <https://journal.r-project.org/archive/2017/RJ-2017-065/RJ-2017-065.pdf>`_ 

4. Building Singularity containers locally
==========================================

Like Docker which uses a `dockerfile` to build its containers, Singularity uses a file called `Singularity`

When you are building locally, you can name this file whatever you wish, but a better practice is to put it in a directory and name it `Singularity` - as this will help later on when developing on Singularity-Hub and Github.

Create Container and add content to it:

.. code-block:: bash

	$ singularity image.create ubuntu14.simg
	Creating empty 768MiB image file: ubuntu14.simg
	Formatting image with ext3 file system
	Image is done: ubuntu14.simg

	$ singularity build ubuntu14.simg docker://ubuntu:14.04
	Building into existing container: ubuntu14.simg
	Docker image path: index.docker.io/library/ubuntu:14.04
	Cache folder set to /home/.../.singularity/docker
	[5/5] |===================================| 100.0% 
	Importing: base Singularity environment
	Importing: /home/.../.singularity/docker/sha256:c954d15f947c57e059f67a156ff2e4c36f4f3e59b37467ff865214a88ebc54d6.tar.gz
	Importing: /home/.../.singularity/docker/sha256:c3688624ef2b94ab3981564e23e1f48df8f1b988519373ccfb79d7974017cb85.tar.gz
	Importing: /home/.../.singularity/docker/sha256:848fe4263b3b44987f0eacdb2fc0469ae6ff04b2311e759985dfd27ae5d3641d.tar.gz
	Importing: /home/.../.singularity/docker/sha256:23b4459d3b04aa0bc7cb7f7021e4d7bbb5e87aa74a6a5f57475a0e8badbd9a26.tar.gz
	Importing: /home/.../.singularity/docker/sha256:36ab3b56c8f1a3188464886cbe41f42a969e6f9374e040f13803d796ed27b0ec.tar.gz
	Importing: /home/.../.singularity/metadata/sha256:c6a9ef4b9995d615851d7786fbc2fe72f72321bee1a87d66919b881a0336525a.tar.gz
	WARNING: Building container as an unprivileged user. If you run this container as root
	WARNING: it may be missing some functionality.
	Building Singularity image...
	Singularity container built: ubuntu14.simg
	Cleaning up...

Note, `image.create` uses an ext3 file system

Create a container using a custom Singularity file:

.. code-block:: bash

	$ singularity build --name ubuntu.simg Singularity

In the above command:

-	`--name` will create a container named  `ubuntu.simg`

Pull a Container from Docker and make it writable using the `--writable` flag:

.. code-block:: bash
	
	$ sudo singularity build --writable ubuntu.simg  docker://ubuntu
	
	Docker image path: index.docker.io/library/ubuntu:latest
	Cache folder set to /root/.singularity/docker
	Importing: base Singularity environment
	Importing: /root/.singularity/docker/sha256:1be7f2b886e89a58e59c4e685fcc5905a26ddef3201f290b96f1eff7d778e122.tar.gz
	Importing: /root/.singularity/docker/sha256:6fbc4a21b806838b63b774b338c6ad19d696a9e655f50b4e358cc4006c3baa79.tar.gz
	Importing: /root/.singularity/docker/sha256:c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49.tar.gz
	Importing: /root/.singularity/docker/sha256:4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af.tar.gz
	Importing: /root/.singularity/docker/sha256:06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58.tar.gz
	Importing: /root/.singularity/metadata/sha256:c6a9ef4b9995d615851d7786fbc2fe72f72321bee1a87d66919b881a0336525a.tar.gz
	Creating empty Singularity writable container 120MB
	Creating empty 150MiB image file: ubuntu.simg
	Formatting image with ext3 file system
	Image is done: ubuntu.simg
	Building Singularity image...
	Singularity container built: ubuntu.simg
	Cleaning up...
	
	$ singularity shell ubuntu.simg 
	
	Singularity: Invoking an interactive shell within container...

	Singularity ubuntu.simg:~> apt-get update                
	
	Reading package lists... Done
	W: chmod 0700 of directory /var/lib/apt/lists/partial failed - SetupAPTPartialDirectory (1: Operation not permitted)
	E: Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)
	E: Unable to lock directory /var/lib/apt/lists/
	Singularity ubuntu.simg:~> exit   
	exit
	
	$ sudo singularity shell ubuntu.simg 
	
	Singularity: Invoking an interactive shell within container...

	Singularity ubuntu.simg:~> apt-get update
	
	Hit:1 http://archive.ubuntu.com/ubuntu xenial InRelease
	Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
	Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]           
	Get:4 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
	Get:5 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [73.2 kB]
	Get:6 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]          
	Get:7 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [585 kB]                  
	Get:8 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [405 kB]
	Get:9 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [3486 B]
	Get:10 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
	Get:11 http://archive.ubuntu.com/ubuntu xenial/multiverse amd64 Packages [176 kB]
	Get:12 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [241 kB]
	Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [953 kB]
	Get:14 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [13.1 kB]
	Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [762 kB]
	Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [18.5 kB]
	Get:17 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [5153 B]
	Get:18 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [7168 B]
	Fetched 23.2 MB in 4s (5569 kB/s)                    
	Reading package lists... Done
	
	Singularity ubuntu.simg:~> apt-get install curl --fix-missing

When I try to install software to the image without `sudo` it is denied, because root is the owner of the container. When I use `sudo` I can install software to the container. The software remain in the container after closing the container and restart. 

.. Note::

    Bootstrapping `bootstrap` command is deprecated (v2.4), use `build` instead.
    
    To install a container with Ubuntu from the ubuntu.com reposutiry you need to use `debootstrap`

 
Exercise 3: Creating the Singularity file (30 minutes)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Recipes <http://singularity.lbl.gov/docs-recipes>`_ can use any number of container registries for bootstrapping a container. 

(Advanced) the `Singularity` file can be hosted on Github and will be auto-detected by Singularity-Hub when you set up your Container Collection.

Building your own containers requires that you have `sudo` privileges - therefore you'll need to develop these on your local machine or on a VM that you can gain root access on.

- The Header  

The top of the file, selects the base OS for the container. `Bootstrap:` references the repository (e.g. `docker`, `debootstrap`, `sub`). `From:` selects the name of the owner/container.

.. code-block:: bash

	Bootstrap: shub
	From: vsoch/hello-world

Using `debootstrap` with a build that uses a mirror:

.. code-block:: bash

	BootStrap: debootstrap
	OSVersion: xenial
	MirrorURL: http://us.archive.ubuntu.com/ubuntu/

Using a `localimage` to build:

.. code-block:: bash

	Bootstrap: localimage
	From: /path/to/container/file/or/directory

Using CentOS-like container:

.. code-block:: bash

	Bootstrap: yum
	OSVersion: 7
	MirrorURL: http://mirror.centos.org/centos-7/7/os/x86_64/
	Include:yum

Note: to use `yum` to build a container you should be operating on a RHEL system, or an Ubuntu system with `yum` installed. 

The container registries which Singularity uses are listed above in Section 3.1.

- Sections

The Singularity file uses sections to specify the dependencies, environmental settings, and runscripts when it build.

*  %help - create text for a help menu associated with your container
*  %setup - executed on the host system outside of the container, after the base OS has been installed.
*  %files - copy files from your host system into the container
*  %labels - store metadata in the container
*  %environment - loads environment variables at the time the container is run (not built)
*  %post - set environment variables during the build
*  %runscript - executes a script when the container runs
*  %test - runs a test on the build of the container

- Apps

In Singularity 2.4+ we can build a container which does multiple things, e.g. each app has its own runscripts. These use the prefix `%app` before the sections mentioned above. The `%app` architecture can exist in addition to the regular `%post` and `%runscript` sections.

.. code-block:: bash

	Bootstrap: docker
	From: ubuntu
	
	% environment
	
	%labels
	
	##############################
	# foo
	##############################

	%apprun foo
    	    exec echo "RUNNING FOO"

	%applabels foo
   	    BESTAPP=FOO
   	    export BESTAPP

	%appinstall foo
 	    touch foo.exec

	%appenv foo
    	    SOFTWARE=foo
   	    export SOFTWARE

	%apphelp foo
   	    This is the help for foo.

	%appfiles foo
	    avocados.txt


	##############################
	# bar
	##############################

	%apphelp bar
    	    This is the help for bar.

	%applabels bar
   	    BESTAPP=BAR
   	    export BESTAPP

	%appinstall bar
    	    touch bar.exec

	%appenv bar
    	    SOFTWARE=bar
    	    export SOFTWARE

- Setting up Singularity file system

`%help` section can be as verbose as you want

.. code-block:: bash

	Bootstrap: docker
	From: ubuntu
	
	%help
	This is the container help section.
	
`%setup` commands are executed on the localhost system outside of the container - these files could include necessary build dependencies. We can copy files to the `$SINGULARITY_ROOTFS` file system can be done during `%setup`

`%files` include any files that you want to copy from your localhost into the container.

`%post` includes all of the environment variables and dependencies that you want to see installed into the container at build time.

`%environment` includes the environment variables which we want to be run when we start the container

`%runscript` does what it says, it executes a set of commands when the container is run.

Example Singularity file bootstrapping a `Docker <https://hub.docker.com/_/ubuntu/>`_ Ubuntu (16.04) image. 

.. code-block:: bash

    BootStrap: docker
    From: ubuntu:16.04

    %post
        apt-get -y update
        apt-get -y install fortune cowsay lolcat

    %environment
        export LC_ALL=C
        export PATH=/usr/games:$PATH

    %runscript
        fortune | cowsay | lolcat 
    
    %labels
    	Maintainer Tyson Swetnam
	Version v0.1
    
Build the container:

.. code-block:: bash

    singularity build --name cowsay_container.simg Singularity

Run the container:

.. code-block:: bash

    singularity run cowsay.simg

If you build a `squashfs` container, it is immutable (you cannot `--writable` edit it)

5. Running Singularity Containers
=================================

Commands:

`exec` - command allows you to execute a custom command within a container by specifying the image file.

`shell` - command allows you to spawn a new shell within your container and interact with it.

`run` - assumes your container is set up with "runscripts" triggered with the `run` command, or simply by calling the container as though it were an executable.

`inspect` - inspects the container.

`--writable` - creates a writable container that you can edit interactively and save on exit.

`--sandbox` - copies the guts of the container into a directory structure. 

5.1 Using the `exec` command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    $ singularity exec shub://singularityhub/ubuntu cat /etc/os-release


5.2 Using the `shell` command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    $ singularity shell shub://singularityhub/ubuntu


5.3 Using the `run` command
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    $ singularity run shub://singularityhub/ubuntu
    

5.4 Using the `inspect` command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can inspect the build of your container using the `inspect` command

.. code-block:: bash

    $ singularity pull  shub://vsoch/hello-world
    Progress |===================================| 100.0% 
    Done. Container is at: /home/***/vsoch-hello-world-master-latest.simg
    
    $ singularity inspect vsoch-hello-world-master-latest.simg 
    {
        "org.label-schema.usage.singularity.deffile.bootstrap": "docker",
        "MAINTAINER": "vanessasaur",
        "org.label-schema.usage.singularity.deffile": "Singularity",
        "org.label-schema.schema-version": "1.0",
        "WHATAMI": "dinosaur",
        "org.label-schema.usage.singularity.deffile.from": "ubuntu:14.04",
        "org.label-schema.build-date": "2017-10-15T12:52:56+00:00",
        "org.label-schema.usage.singularity.version": "2.4-feature-squashbuild-secbuild.g780c84d",
        "org.label-schema.build-size": "333MB"
    }

5.5 Using the `--sandbox` and `--writable` commands
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As of Singularity v2.4 by default `build` produces immutable images in the 'squashfs' file format. This ensures reproducible and verifiable images.

Creating a `--writable` image must use the `sudo` command, thus the owner of the container is `root`

.. code-block:: bash

   	$ sudo singularity build --writable ubuntu-master.simg shub://singularityhub/ubuntu
	Cache folder set to /root/.singularity/shub
	Progress |===================================| 100.0% 
	Building from local image: /root/.singularity/shub/singularityhub-ubuntu-master-latest.simg
	Creating empty Singularity writable container 208MB
	Creating empty 260MiB image file: ubuntu-master.simg
	Formatting image with ext3 file system
	Image is done: ubuntu-master.simg
	Building Singularity image...
	Singularity container built: ubuntu-master.simg
	Cleaning up...

You can convert these images to writable versions using the `--writable` and `--sandbox` commands. 

When you use the `--sandbox` the container is written into a directory structure. Sandbox folders can be created without the `sudo` command.

.. code-block:: bash

    	$ singularity build --sandbox lolcow/ shub://GodloveD/lolcow
	WARNING: Building sandbox as non-root may result in wrong file permissions
	Cache folder set to /home/.../.singularity/shub
	Progress |===================================| 100.0% 
	Building from local image: /home/.../.singularity/shub/GodloveD-lolcow-master-latest.simg
	WARNING: Building container as an unprivileged user. If you run this container as root
	WARNING: it may be missing some functionality.
	Singularity container built: lolcow/
	Cleaning up...
	@vm142-73:~$ cd lolcow/
	@vm142-73:~/lolcow$ ls
	bin  boot  dev  environment  etc  home  lib  lib64  media  mnt  opt  proc  run  sbin  singularity  srv  sys  tmp  usr  var

5.6 Test
~~~~~~~~

Singularity can test the build of your container.

You can bypass the test by using `--no-test`.


5.7 Bind Paths
~~~~~~~~~~~~~~

When Singularity creates the new file system inside a container it ignores directories that are not part of the standard kernel, e.g. `/scratch`, `/xdisk`, `/global`, etc. These paths can be added back into the container by binding them when the container is run.

.. code-block:: bash

	$ singularity shell --bind /xdisk ubuntu14.simg
	
The system administrator can also define what is added to a container. This is important on campus HPC systems that often have a `/scratch` or `/xdisk` directory structure. By editing the `/etc/singularity/singularity.conf` a new path can be added to the system containers.

5.8 Overlay
~~~~~~~~~~~

You can make changes to an immutable container which only persist for the duration of the container being run.

First, download a container.

Next, create a new image in the ext3 format.

.. code-block:: bash

	$ singularity image.create blank_slate.simg

Now, overlay your blank image file name with the container you just downloaded.

.. code-block:: bash
	
	$ sudo singularity shell --overlay blank_slate.simg ubuntu14.simg

*note: using the `sudo` command to make the container writable*


.. |singularity| image:: ../img/singularity.png
  :height: 200
  :width: 200

.. |singularityflow| image:: http://singularity.lbl.gov/assets/img/diagram/singularity-2.4-flow.png
  :height: 600
