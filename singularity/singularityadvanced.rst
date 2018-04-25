**Advanced Singularity**
------------------------

|singularity|


1. Using HPC Environments
=========================

Conducting analyses on high performance computing clusters happens through very different patterns of interaction than running analyses on a VM.  When you login, you are on a node that is shared with lots of people.  Trying to run jobs on that node is not "high performance" at all.  Those login nodes are just intended to be used for moving files, editing files, and launching jobs.

Most jobs on an HPC cluster are neither interactive, nor realtime.  When you submit a job to the scheduler, you must tell it what resources you need (e.g. how many nodes, what type of nodes) and what you want to run.  Then the scheduler finds resources matching your requirements, and runs the job for you when it can.

For example, if you want to run the command:

.. code-block:: bash

  singularity exec docker://python:latest /usr/local/bin/python

On an HPC system, your job submission script would look something like:

.. code-block:: bash

  #!/bin/bash
  #
  #SBATCH -J myjob                      # Job name
  #SBATCH -o output.%j                  # Name of stdout output file (%j expands to jobId)
  #SBATCH -p development                # Queue name
  #SBATCH -N 1                          # Total number of nodes requested (68 cores/node)
  #SBATCH -n 17                         # Total number of mpi tasks requested
  #SBATCH -t 02:00:00                   # Run time (hh:mm:ss) - 4 hours

  module load tacc-singularity
  singularity exec docker://python:latest /usr/local/bin/python

This example is for the Slurm scheduler, a popular one used by all TACC systems.  Each of the #SBATCH lines looks like a comment to the bash kernel, but the scheduler reads all those lines to know what resources to reserve for you.

It is usually possible to get an interactive session as well.  At TACC, the command "idev" is used to get an interactive development session.  For example:

.. code-block:: bash

  idev -m 240 -p skx-normal

Just running "idev" gives you one hour on a development node.  The example above will give you a 240 minute interactive session on the "skx-normal" partition of the system (which in this case have 48 Skylake CPU cores per node).

.. Note::

  Every HPC cluster is a little different, but they almost universally have a "User's Guide" that serves both as a quick reference for helpful commands and contains guidelines for how to be a "good citizen" while using the system.  For TACC's Stampede2 system, the user guide is at: `https://portal.tacc.utexas.edu/user-guides/stampede2 <https://portal.tacc.utexas.edu/user-guides/stampede2>`_

How do HPC systems fit into the development workflow?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A few things to consider when using HPC systems:

#. Using 'sudo' is not allowed on HPC systems, and building a Singularity container from scratch requires sudo.  That means you have to build your containers on a different development system.  You can pull a docker image on HPC systems
#. If you need to edit text files, command line text editors don't support using a mouse, so working efficiently has a learning curve.  There are text editors that support editing files over SSH.  This lets you use a local text editor and just save the changes to the HPC system.
#. Singularity is in the process of changing image formats.  Depending on the version of Singularity running on the HPC system, new squashFS or .simg formats may not work.


2. Singularity and MPI
======================

Singularity supports MPI fairly well.  Since (by default) the network is the same insde and outside the container, the communication between containers usually just works.  The more complicated bit is making sure that the container has the right set of MPI libraries.  MPI is an open specification, but there are several implementations (OpenMPI, MVAPICH2, and Intel MPI to name three) with some non-overlapping feature sets.  If the host and container are running different MPI implementations, or even different versions of the same implementation, hilarity may ensue.

The general rule is that you want the version of MPI inside the container to be the same version or newer than the host.  You may be thinking that this is not good for the portability of your container, and you are right.  Containerizing MPI applications is not terribly difficult with Singularity, but it comes at the cost of additional requirements for the host system.

.. Note::

  Many HPC Systems, like Stampede2, have highspeed, low latency networks that have special drivers.  Infiniband, Ares, and OmniPath are three different specs for these types of networks.  When running MPI jobs, if the container doesn't have the right libraries, it won't be able to use those special interconnects to communicate between nodes.

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

  mpirun -np 4 singularity exec ./mycontainer.img /app.py arg1 arg2

This will use the **host MPI** libraries to run in parallel, and assuming the image has what it needs, can work across many nodes.

For a single node, you can also use the **container MPI** to run in parallel (usually you don't want this)

.. code-block:: bash

  singularity exec ./mycontainer.img mpirun -np 4 /app.py arg1 arg2


3. Singularity and GPU Computing
================================

GPU support in Singularity is fantastic

Since Singularity supported docker containers, it has been fairly simple to utilize GPUs for machine learning code like TensorFlow. From Maverick, which is TACC’s GPU system:

.. code-block:: bash

  # Work from a compute node
  idev -m 60
  # Load the singularity module
  module load tacc-singularity
  # Pull your image
  singularity pull docker://nvidia/caffe:latest
  
  singularity exec --nv caffe-latest.img caffe device_query -gpu 0

Please note that the --nv flag specifically passes the GPU drivers into the container. If you leave it out, the GPU will not be detected.

.. code-block:: bash

  singularity exec caffe-latest.img caffe device_query -gpu 0

For TensorFlow, you can directly pull their latest GPU image and utilize it as follows.

.. code-block:: bash

  # Change to your $WORK directory
  cd $WORK
  #Get the software
  git clone https://github.com/tensorflow/models.git ~/models
  # Pull the image
  singularity pull docker://tensorflow/tensorflow:latest-gpu
  # Run the code
  singularity exec --nv tensorflow-latest-gpu.img python $HOME/models/tutorials/image/mnist/convolutional.py

.. Note::

    You probably noticed that we check out the models repository into your $HOME directory. This is because your $HOME and $WORK directories are only available inside the container if the root folders /home and /work exist inside the container. In the case of tensorflow-latest-gpu.img, the /work directory does not exist, so any files there are inaccessible to the container.

You may be thinking “what about overlayFS??”. Stampede2 supports it, but the Linux kernel on the other systems does not support overlayFS, so it had to be disabled in our Singularity install.  This may change as new Singularity versions are released.

Hands-On Exercise
~~~~~~~~~~~~~~~~~

Build a Singularity container that implements a simple Tensorflow image classifier.

The image classifier script is available "out of the box" here:
`https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/imagenet/classify_image.py <https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/imagenet/classify_image.py>`_

Tensorflow has working Docker containers on DockerHub that you can use to support all the dependencies.  For example, the first line of your Dockerfile might look like:

.. code-block:: bash

  FROM tensorflow/tensorflow:1.5.0-py3

When running the image classifier, the non-containerized version would be invoked with something like:

.. code-block:: bash

  python /classify_image.py --model_dir /model --image_file cat.png

You can use a Singularity file or a Dockerfile to help you.  For reference, you can lookback at the "Singularity Intro" section on building Singularity images, yesterday's material on building Dockerfiles, or the respective manual pages:

- `http://singularity.lbl.gov/docs-build-container <http://singularity.lbl.gov/docs-build-container>`_
- `https://docs.docker.com/engine/reference/builder/ <https://docs.docker.com/engine/reference/builder/>`_





.. |singularity| image:: ../img/singularity.png
  :height: 200
  :width: 200
