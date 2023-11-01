.. highlight:: shell

.. _setup:


Docker/Podman Setup
*******************
Docker has to installed on your machine. Please refer to official docker `documentation <https://docs.docker.com/get-docker/>`_ on installation.
You can also use `Podman <https://podman.io/>`_  in the same wa, simply replace commad ``docker`` with ``podman``.

In the following instruction, make sure to replace the docker tag with the tag you want from Tags list in `docker hub <https://hub.docker.com/r/jeffersonlab/hallc-hcana/tags>`_ . These commands are for ``hcana-0.98`` tags of hcana.

Once you have installed Docker on your computer, you can fetch an hcana image and create and start a container using the ``docker run`` command:

.. code-block:: shell

    $ docker run --name hcana -it -d docker.io/jeffersonlab/hallc-hcana:hcana-0.98

Here, we fetch the ``hallc-hcana:hcana-0.98`` Docker image from Docker Hub and name the container "hcana".

To check if your container is running just run following command:

.. code-block:: shell

    $ docker ps

you should see a container running.

To go to the shell of the container run the following command

.. code-block:: shell

    $ docker exec -it hcana bash



Once you have executed the command, you should see the command prompt within Docker.
To check where you are in the container just run ``pwd`` and you will see something like

.. code-block:: shell

    $  pwd
    /hcana-hcana-xxx/myworkdir

the ``xxx`` in our case will be ``0.98``

Running hcana Command
---------------------

To run the hcana command prompt, simply type ``hcana`` in the Docker command prompt as shown below:

.. code-block:: shell

    # hcana
    DB_DIR set to DBASE
      ************************************************
      *                                              *
      *            W E L C O M E  to  the            *
      *          H A L L C ++  A N A L Y Z E R       *
      *                                              *
      *  hcana release         0.98      12 Oct 2023 *
      *  PODD release         1.7.7      12 Oct 2023 *
      *  ROOT               6.24/08      Sep 29 2022 *
      *                                              *
      *            For information visit             *
      *      http://hallcweb.jlab.org/hcana/docs/    *
      *                                              *
      ************************************************
    hcana [0]

Exiting/Stopping the Container
------------------------------

To exit the container, simply type ``exit``.
To stop the container run the following after exiting from the container

.. code-block:: shell

    $ docker stop hcana

Restarting the Container
-------------------------

If you want to restart the container (e.g., the one named "hcana") and return to your work, use the following command:

.. code-block:: shell

   $ docker start -i hcana


Coppying files from local to 

Running Graphics Display
------------------------

**For macOS:** Install XQuartz and enable "Allow connections from network clients." Then, run the following command:

.. code-block:: shell

    $ ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')

This will grab your IP address on the local network. Run echo $ip to make sure it was successful. If nothing is displayed, replace en0 with en1 or a higher number in the command.

.. code-block:: shell
        
        $ xhost + $ip
    
This will start XQuartz and whitelist your local IP address. Finally, you can start up ROOT with the following command:

.. code-block:: shell

    $ docker run -name hcana -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$ip:0 docker.io/jeffersonlab/hallc-hcana::hcana-0.98


**For Linux:** You can use X11 forwarding. make sure you are in an X11 session run the following command:

.. code-block:: shell

    $ ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
    $ docker run -name hcana  -it -d -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY==$ip:0 docker.io/jeffersonlab/hallc-hcana::hcana-0.98

Mounting volume to docker
-------------------------
You may want to run your code macro in the hcana docker and also provide a input file to that code.
You can do that by mounting you local directory to docker container.

If you want to mount a certain diretory you can do that by using ``-v`` option.
For example to mount local home directory to home diretory of docker  you can do:

.. code-block:: shell

    $ docker run  -name hcana  -it -d -v /home:/home docker.io/jeffersonlab/hallc-hcana::hcana-0.98 

Copying files to and from docker
--------------------------------
If you don't want to mount the directory or just want to move a file to docker container, you can use ``docker cp`` command.


copy to container:

.. code-block:: shell

    $ docker cp my_local_file hcana:/directory/inside/docker

copy from container

.. code-block:: shell

    $ docker cp hcana:/directory/inside/docker/file /my/local/directory




Removing the container
----------------------
If you want to remove the container as awhole you can use 

.. code-block:: shell

    $ docker rm hcana

**Keep in mind that you will lose all the files in container. Be sure to copy files you want from docker container using** ``docker cp`` **command as mentioned in previous section**

