.. highlight:: shell

.. _example_replay:


Example
********
To use the hcana container for replay or any other analysis, its no different than using your own build hcana.

Lets see it via a example. The official ``hcana`` repo (`here <https://github.com/JeffersonLab/hcana/tree/develop>`_) provides a example file ``hodtest.C`` in ``examples`` subdirectory, where it decods hits.

In your local system or ifarm git clone the `hcana <https://github.com/JeffersonLab/hcana/tree/develop>`_ repo.
Download the file `daq04_50017.log.0.gz <https://userweb.jlab.org/~saw/software/daq04_50017.log.0.gz>`_ and place it in tthe ``examples`` subdirectory.
Then uncompress the data file as:

.. code-block:: shell

    $ gunzip daq04_50017.log.0.gz

If you want to use a different data file, edit the "THaRun" line in file ``hodtest.C`` to point to the run file name.

Lets explore both docker and apptainer to run the example.

Docker
------
If you have a hcana docker container running. You can copy the daq04_50017.log.0.gz into the docker container using

.. code-block:: shell

    $ docker cp /your/directory/to/daq04_50017.log.0.gz hcana:/hcana-hcana-0.98/examples/

Now got to the docker container shell as:

.. code-block:: shell

        $ docker exec -it hcana bash

Go into the example directory as ``cd /hcana-hcana-0.98/examples/`` and run following commands

.. code-block:: shell

    $ hcana
    $ .x hodtest.C


Running this script will create a root file, "hodtest.root." The contents of this file are controled by "output.def".

.. code-block:: shell
     
    $ .x hitmaps_hms.C

Apptainer
---------
Go to the shell of the hcana apptainer as:

.. code-block:: shell

    $ apptainer shell --cleanenv hcana-0.98.sif

If your hcana repo is in home directory then it will be available in apptainer, if in other place you can use ``--bind`` option of apptainer.
Now got into the ``examples`` subdirectory of hcana repo in the apptainer shell.
In the shell run ``hcana`` which will take you to the hcana command prompt.
You should see ``hcana [0]`` in the shell.

Now run the following command.


.. code-block:: shell
     
    $ .x hodtest.C


Running this script will create a root file, "hodtest.root." The contents of this file are controled by "output.def".

To create histogram run:


.. code-block:: shell
     
    $ .x hitmaps_hms.C


