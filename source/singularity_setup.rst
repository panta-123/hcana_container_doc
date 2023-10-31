.. highlight:: shell

.. _singularity_setup:


Apptainer/Singularity Setup
****************************
Apptainer (formerly known as singularity) has to be installed in you local system.Please refer to official isntallation `instruction <https://apptainer.org/docs/user/latest/quick_start.html#quick-installation>`_.

Test you installation by running following command.

.. code-block:: shell

    $ apptainer --version

you should see the version of apptainer in the command line.


Building the image
------------------
To build the apptainer image for hcana, run the following command.

.. code-block:: shell

    $ apptainer build hcana-0.98.sif docker://jeffersonlab/hallc-hcana:hcana-0.98

we set the name of the apptainer container as ``hcana-0.98.sif``.

Running the image
-----------------
To use the apptainer imgae that you just built and named as ``hcana-0.98.sif`` , run the following command.

.. code-block:: shell

    $ apptainer shell --cleanenv hcana-0.98.sif

Now you will be in the apptainer shell. 

To run ``hcana`` command prompt just type following on the shell.


.. code-block:: shell

    Apptainer> hcana


your ``home`` directory is available automatically.

To make any directory to be available inside the container you can use ``--bind`` option. For example to access /mydirectory in singularity.


.. code-block:: shell

    $ apptainer shell --cleanenv --bind /mydirectory hcana-0.98.sif

ifarm
-----
apptainer is availabe by default on the ifarm node. Login to ifarm and just type:

.. code-block:: shell

    $ apptainer --version

You can use the same instruction as above to use hcana container. If you want to use already build image it is availabe as ``/group/hallc/panta/hcana-0.98.sif``.

To use pre-build image just do the following:

.. code-block:: shell

    $ apptainer shell --cleanenv  /group/hallc/panta/hcana-0.98.sif

For example to access group area or any other in apptainer use ``--bind <your_directrory_to bind>`` as:

.. code-block:: shell

    $ apptainer shell --cleanenv --bins /group/hallc /group/hallc/panta/hcana-0.98.sif

