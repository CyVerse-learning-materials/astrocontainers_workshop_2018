**Booting an Atmosphere computer instance for your use!**
=========================================================

What we're going to do here is walk through of how to start up a running
computer (an "instance") on the CyVerse Atmosphere Cloud service.

Below, we've provided screenshots of the whole process. You can click
on them to zoom in a bit. The important areas to fill in are highlighted.

First, go to the `Atmosphere <https://atmo.cyverse.org/application/images>`_ application and then click `login`

.. important::

  You will need to have access to the Atmosphere workshop cloud. If you are not able to log-in for some reason, please let us know and we will fix it immediately.

1. Fill in the username and password and click "LOGIN"

Fill in the username, which is your CyVerse username,
and then enter the password (which is your CyVerse password).

|atmo-1|
           
2. Select Projects and "Create New Project"

- Now, this is something you only need to do once.

- We'll do this with Projects, which gives you a bit of a workspace in which to keep things that belong to "you".

- Click on the "Projects" tab on the top and then click "CREATE NEW PROJECT"

|atmo_cp0|

- Enter the name "CCW2018" into the Project Name, and something simple like "Container Camp Workshop 2018" into the description. Then click 'create'.

|atmo_cp|

3. Select the newly created project

- Click on your newly created project!
           
- Now, click 'New' and then "Instance" from the dropdown menu to start up a new virtual machine.

|atmo_launch0|

- Find the "Ubuntu 16.04" image, click on it

|atmo_launch1|

- Name it something simple such as "workshop tutorial" and select 'tiny1 (CPU: 1, Mem: 4GB, Disk: 30GB)'.

- Leave rest of the fields as default.

|atmo_launch|

- Wait for it to become active

- It will now be booting up! This will take 2-10 minutes, depending.
Just wait! Don't reload or anything.

|atmo-6|

- Click on your new instance to get more information!

- Now, you can either click "Open Web Shell", *or*, if you know how to use ssh,
you can ssh in with your CyVerse username on the IP address of the machine 

|atmo-7|

**Deleting your instance**

- To completely remove your instance, you can select the "delete" buttom from the instance details page. 

- This will open up a dialogue window. Select the "Yes, delete this instance" button.

|atmo-8|

- It may take Atmosphere a few minutes to process your request. The instance should disappear from the project when it has been successfully deleted. 

|atmo-9|

.. Note::

  It is advisable to delete the machine if you are not planning to use it in future to save valuable resources. However if you want to use it in future, you can suspend it.

.. |atmo-1| image:: ../img/atmo-1.png
  :width: 750
  :height: 700

.. |atmo_cp0| image:: ../img/atmo_cp0.png
  :width: 750
  :height: 700

.. |atmo_cp| image:: ../img/atmo_cp.png
  :width: 750
  :height: 700

.. |atmo_launch0| image:: ../img/atmo_launch0.png
  :width: 750
  :height: 700

.. |atmo_launch1| image:: ../img/atmo_launch1.png
  :width: 750
  :height: 700

.. |atmo_launch| image:: ../img/atmo_launch.png
  :width: 750
  :height: 700

.. |atmo-6| image:: ../img/atmo-6.png
  :width: 750
  :height: 700

.. |atmo-7| image:: ../img/atmo-7.png
  :width: 750
  :height: 700

.. |atmo-8| image:: ../img/atmo-8.png
  :width: 750
  :height: 700

.. |atmo-9| image:: ../img/atmo-9.png
  :width: 750
  :height: 700
