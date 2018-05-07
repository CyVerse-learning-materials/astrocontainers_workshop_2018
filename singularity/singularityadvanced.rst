**Advanced Singularity**
------------------------

|singularity|

1. Using HPC Environments
=========================

Conducting analyses on high performance computing clusters happens through very different patterns of interaction than running analyses on a VM.  When you login, you are on a node that is shared with lots of people.  Trying to run jobs on that node is not "high performance" at all.  Those login nodes are just intended to be used for moving files, editing files, and launching jobs.

Most jobs on an HPC cluster are neither interactive, nor realtime.  When you submit a job to the scheduler, you must tell it what resources you need (e.g. how many nodes, what type of nodes) and what you want to run.  Then the scheduler finds resources matching your requirements, and runs the job for you when it can.

You can run a simple command on the login node as opposed to a large job:

.. code-block:: bash

  module load singularity
  singularity exec docker://python:latest python --version


.. Note::

The "User's Guide" for Ocelote can be found at: `https://docs.hpc.arizona.edu <https://docs.hpc.arizona.edu>`_  

How do HPC systems fit into the development workflow?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A few things to consider when using HPC systems:

#. Using 'sudo' is not allowed on HPC systems, and building a Singularity container from scratch requires sudo.  That means you have to build your containers on a different development system.  You can pull a docker image on HPC systems.
#. If you need to edit text files, command line text editors don't support using a mouse, so working efficiently has a learning curve.  There are text editors that support editing files over SSH.  This lets you use a local text editor and just save the changes to the HPC system.
#. Singularity has changed image formats.  Depending on the version of Singularity running on the HPC system, new squashFS or .simg formats may not work. The images take a lot less space

Tutorial #1
~~~~~~~~~~~

This is a review of knowledge already covered in this workshop.  

.. Note::

#. Build this where you have root authority
#. Some packages are more complex than "pip install numpy"
#. Include your bind points

.. code-block:: bash

  BootStrap: yum
  OSVersion: 7
  MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
  Include: yum wget
  # best to build up container using kickstart mentality. 
  # ie, to add more packages to image,
  # re-run bootstrap command again. 
  # bootstrap on existing image will build on top of it, not overwriting it/restarting from scratch
  # singularity .def file is like kickstart file
  # unix commands can be run, but if there is any error, the bootstrap process ends
  %setup
   # commands to be executed on host outside container during bootstrap
  %post
    # commands to be executed inside container during bootstrap
    # add python and install some packages
    yum -y install vim wget python epel-release
    yum -y install python-pip
     # install tensorflow
    pip install --upgrade pip
    pip install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0-cp27-none-linux_x86_64.whl
    pip install --upgrade numpy scipy scikit-learn matplotlib astropy
     # create bind points for storage.
     mkdir /extra
     mkdir /xdisk
     exit 0
  %runscript
   # commands to be executed when the container runs
   echo "Arguments received: $*"
   exec /usr/bin/python "$@"
  %test
   # commands to be executed within container at close of bootstrap process
   
Create container

.. code-block:: bash

  singularity build astropy.img astropy.recipe
   
The next step is to copy this singularity to one of your directories on Ocelote. For example:

.. code-block:: bash

  scp astropy.img chrisreidy@filexfer.hpc.arizona.edu:

Log into home directory on Ocelote then "mv" file to /extra/chrisreidy/singularity
Test with these commands

.. code-block:: bash

  $ module load singularity
  $ singularity exec astropy.img python --version
  Python 3.6.4
  
On an HPC system, your job submission script would look something like:

.. code-block:: bash

  ###========================================
  #!/bin/bash
  #PBS -N singularity-job
  #PBS -W group_list=GroupName
  #PBS -q windfall
  #PBS -l select=1:ncpus=1:mem=6gb
  #PBS -l walltime=01:00:00
  #PBS -l cput=12:00:00
  module load singularity
  cd /extra/chrisreidy/singularity
  date
  singularity exec astropy.img python --version
  date

This example uses PBS which is the schduler available on Ocelote.  ElGato uses LSF which has the same functions but different syntax.

It is usually possible to get an interactive session as well. For example:

.. code-block:: bash

   qsub -I -N jobname -W group_list=YourGroup -q windfall -l select=1:ncpus=28:mem=168gb -l cput=1:0:0 -l walltime=1:0:0


2. Singularity and MPI
======================

Singularity supports MPI fairly well.  Since (by default) the network is the same insde and outside the container, the communication between containers usually just works.  The more complicated bit is making sure that the container has the right set of MPI libraries.  MPI is an open specification, but there are several implementations (OpenMPI, MVAPICH2, and Intel MPI to name three which are available on Ocelote) with some non-overlapping feature sets.  If the host and container are running different MPI implementations, or even different versions of the same implementation, tragedy may ensue.

The general rule is that you want the version of MPI inside the container to be the same version or newer than the host.  You may be thinking that this is not good for the portability of your container, and you are right.  Containerizing MPI applications is not terribly difficult with Singularity, but it comes at the cost of additional requirements for the host system.

.. Note::

  Many HPC Systems, like Ocelote, have highspeed, low latency networks that have special drivers. Ocelote and ElGato use Infiniband. When running MPI jobs, if the container doesn't have the right libraries, it won't be able to use those special interconnects to communicate between nodes.

Because you may have to build your own MPI enabled Singularity images (to get the versions to match), here is a 2.3 compatible example of what it may look like:

.. code-block:: bash

  # Copyright (c) 2015-2016, Gregory M. Kurtzer. All rights reserved.
  #
  # "Singularity" Copyright (c) 2016, The Regents of the University of     California,
  # through Lawrence Berkeley National Laboratory (subject to receipt of any
  # required approvals from the U.S. Dept. of Energy).  All rights reserved.

  BootStrap: debootstrap
  OSVersion: xenial
  MirrorURL: http://us.archive.ubuntu.com/ubuntu/


  %runscript
      echo "This is what happens when you run the container..."


  %post
      echo "Hello from inside the container"
      sed -i 's/$/ universe/' /etc/apt/sources.list
      apt update
      apt -y --allow-unauthenticated install vim build-essential wget     gfortran bison libibverbs-dev libibmad-dev libibumad-dev librdmacm-dev     libmlx5-dev libmlx4-dev
      wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/    mvapich2-2.1.tar.gz
      tar xvf mvapich2-2.1.tar.gz
      cd mvapich2-2.1
      ./configure --prefix=/usr/local
      make -j4
      make install
      /usr/local/bin/mpicc examples/hellow.c -o /usr/bin/hellow

You could also build in everything in a Dockerfile and convert the image to Singularity at the end.

Once you have a working MPI container, invoking it would look something like:

.. code-block:: bash

  module load mvapich2
  mpirun -np 4 singularity exec ./mycontainer.img /app.py arg1 arg2

This will use the **host MPI** libraries to run in parallel, and assuming the image has what it needs, can work across many nodes.

For a single node, you can also use the **container MPI** to run in parallel (usually you don't want this)

.. code-block:: bash

  module load mvapich2
  singularity exec ./mycontainer.img mpirun -np 4 /app.py arg1 arg2


3. Singularity and GPU Computing
================================

GPU support in Singularity is fantastic

Since Singularity supported docker containers, it has been fairly simple to utilize GPUs for machine learning code like TensorFlow. On Ocelote we have downloaded Docker images from Nvidia for most ML workflows, and converted them to Singularity.  They are kept in /unsupported/singularity/nvidia, and can be copied to your own directories. 

Tutorial #2
~~~~~~~~~~~
This example is a case of running a simple container using an interactive session.  You don't need to know anything about machine learning.  From Ocelote:

.. code-block:: bash

  cd /extra/netid
  mkdir astro
  cd astro
  cp /unsupported/singularity/nvidia/nvidia-tensorflow.18.03-py3.simg .
  cp /unsupported/singularity/nvidia/tensorflow_example.py .
  # Work from a compute node. This step is likely to take more than a minute depending on how busy the scheduler is.
  qsub -I -N jobname -m bea -W group_list=YourGroup -q windfall -l select=1:ncpus=28:mem=168gb:ngpus=1 -l cput=1:0:0 -l walltime=1:0:0
  # Load the singularity module
  module load singularity
  cd /extra/netid/astro
  singularity exec --nv nvidia-tensorflow.18.03-py3.simg python tensorflow_example.py

Please note that the --nv flag specifically passes the GPU drivers into the container. If you leave it out, the GPU will not be detected.

Tutorial #3
~~~~~~~~~~~

This example is a little different.  It demonstrates the ability to pull a Docker image, embed it in Singularity and run it on a GPU node.
For TensorFlow, you can directly pull their latest GPU image and utilize it as follows.

.. code-block:: bash

  # Start an interactive session after you are on the login node.  Edit as needed:   
  qsub -I -N jobname -W group_list=YourGroup -q windfall -l select=1:ncpus=28:mem=168gb:ngpus=1 -l cput=1:0:0 -l walltime=1:0:0
  # Get the software
  git clone https://github.com/tensorflow/models.git ~/models
  # Pull the image
  singularity pull docker://tensorflow/tensorflow:latest-gpu
  # Run the code
  singularity exec --nv tensorflow-latest-gpu.img python $HOME/models/tutorials/image/mnist/convolutional.py

.. Note::

    You probably noticed that we check out the models repository into your $HOME directory. This is because your $HOME and $WORK directories are only available inside the container if the root folders /home and /work exist inside the container. In the case of tensorflow-latest-gpu.img, the /work directory does not exist, so any files there are inaccessible to the container.

