.. highlight:: shell

.. _sbs_offline:


g4sbs and container
********************
g4sbs is simulation software for hall A SBS experiment. `Github <https://github.com/JeffersonLab/g4sbs>`_ 

It has following dependency:

* `Geant4 <https://geant4.web.cern.ch/>`_ 
* `root <https://root.cern/>`_ 

Since simulation doesn't need inputfile (mostly), it is great candidate to be run on offsite computing center.
But the installation of g4sbs along with its dependency can be tedious. Thus we made a container that has ready to use g4sbs in it.

You can find a test container in docker hub: `apanta123/g4sbs:centostrypythia <https://hub.docker.com/repository/docker/apanta123/g4sbs/general>`_
Here the version of dockerhub includes Geant4 : 10.7.3 , root 6.24, pythia6, g4sbs uconn_dev branch.

Building the g4sbs apptainer image
-----------------------------------

.. code-block:: shell

    $ apptainer build sbs_offline.sif docker://apanta123/g4sbs:centostrypythia

I have build my image with the name at "`g4sbs.sif`"


Running in ifarm
-----------------
You can use following script with either swif2 or slurm to submit job to ifarm.

.. code-block:: bash

        #!/bin/bash

        BASEDIR="/path/to/scipt/dir/"
        APPTAINER_IMAGE="/path/to/apptainer/image/g4sbs.sif"  # apptainer image location
        SCRIPT="gen_1018_example.mac"  # script should be in BASEDIR

        cd $BASEDIR

        output_file="output.env"

        variables=(
            "G4NEUTRONHPDATA=\"/cvmfs/geant4.cern.ch/share/data/G4NDL4.6\""
            "G4LEDATA=\"/cvmfs/geant4.cern.ch/share/data/G4EMLOW7.13\""
            "G4LEVELGAMMADATA=\"/cvmfs/geant4.cern.ch/share/data/PhotonEvaporation5.7\""
            "G4RADIOACTIVEDATA=\"/cvmfs/geant4.cern.ch/share/data/RadioactiveDecay5.6\""
            "G4PARTICLEXSDATA=\"/cvmfs/geant4.cern.ch/share/data/G4PARTICLEXS3.1\""
            "G4PIIDATA=\"/cvmfs/geant4.cern.ch/share/data/G4PII1.3\""
            "G4REALSURFACEDATA=\"/cvmfs/geant4.cern.ch/share/data/RealSurface2.2\""
            "G4SAIDXSDATA=\"/cvmfs/geant4.cern.ch/share/data/G4SAIDDATA2.0\""
            "G4ABLADATA=\"/cvmfs/geant4.cern.ch/share/data/G4ABLA3.1\""
            "G4INCLDATA=\"/cvmfs/geant4.cern.ch/share/data/G4INCL1.0\""
            "G4ENSDFSTATEDATA=\"/cvmfs/geant4.cern.ch/share/data/G4ENSDFSTATE2.3\""
            "GEANT4_DATA_DIR=\"/cvmfs/geant4.cern.ch/share/data\""
        )

        # Iterate through each variable and append to the output file
        for var in "${variables[@]}"; do
            echo "$var" >> "$output_file"
        done
        module load apptainer 

        echo 
        echo "------"
        echo "g4sbs"
        echo "------"
        echo
        runStr="apptainer exec  --env-file output.env --bind ${APPTAINER_IMAGE} --bind ${BASEDIR} --bind /cvmfs ${APPTAINER_IMAGE} bash -c \"g4sbs ${SCRIPT}\"" 
        eval "$runStr"


Running in OSG
---------------
Open Science Grid (`OSG <https://osg-htc.org/>`_) is a distributed computing infrastructure that provides a shared pool of high-throughput \
computing resources to researchers across various scientific disciplines.

To submit job to OSG resources, we have put the apptainer image to oasis.opensciencegrid.org  `cvmfs <https://cernvm.cern.ch/fs/>`_.
Location of image is: ``/cvmfs/oasis.opensciencegrid.org/jlab/halla/sbs/containers/g4sbs_pythia.sif``

The submit node for OSg jobs is ``scosg20``. 

* You login as:

.. code-block:: shell

    $ ssh ifarm
    $ shh scosg20

* Then cd into the osgpool directory and make a directory with username. Also create other directory needed for later.

.. code-block:: shell

    $ cd /osgpool/halla
    $ mkdir $USER
    $ mkdir -p logs output error

* Get a grid certificate of Jlab CILOGON if not done so.
* Make a .cilogon diretcory in scosg20 and put the usercert.pem and userkey.pem in that directory. Your directory should look like following:

.. code-block:: shell

    $  ls $HOME/.cilogon/
    usercert.pem userkey.pem

* Create a bash script to run the simulation. Name the file as ``run_g4sbs.sh``

.. code-block:: bash

    #!/bin/bash

    # apptainer image location
    APPTAINER_IMAGE="/cvmfs/oasis.opensciencegrid.org/jlab/halla/sbs/containers/g4sbs_pythia.sif"   
    SCRIPT="gen_1018_example.mac"   # script to run

    output_file="output.env"  # Replace with your desired output file

    variables=(
        "G4NEUTRONHPDATA=\"/cvmfs/geant4.cern.ch/share/data/G4NDL4.6\""
        "G4LEDATA=\"/cvmfs/geant4.cern.ch/share/data/G4EMLOW7.13\""
        "G4LEVELGAMMADATA=\"/cvmfs/geant4.cern.ch/share/data/PhotonEvaporation5.7\""
        "G4RADIOACTIVEDATA=\"/cvmfs/geant4.cern.ch/share/data/RadioactiveDecay5.6\""
        "G4PARTICLEXSDATA=\"/cvmfs/geant4.cern.ch/share/data/G4PARTICLEXS3.1\""
        "G4PIIDATA=\"/cvmfs/geant4.cern.ch/share/data/G4PII1.3\""
        "G4REALSURFACEDATA=\"/cvmfs/geant4.cern.ch/share/data/RealSurface2.2\""
        "G4SAIDXSDATA=\"/cvmfs/geant4.cern.ch/share/data/G4SAIDDATA2.0\""
        "G4ABLADATA=\"/cvmfs/geant4.cern.ch/share/data/G4ABLA3.1\""
        "G4INCLDATA=\"/cvmfs/geant4.cern.ch/share/data/G4INCL1.0\""
        "G4ENSDFSTATEDATA=\"/cvmfs/geant4.cern.ch/share/data/G4ENSDFSTATE2.3\""
        "GEANT4_DATA_DIR=\"/cvmfs/geant4.cern.ch/share/data\""
    )

    for var in "${variables[@]}"; do
        echo "$var" >> "$output_file"
    done

    echo 
    echo "------"
    echo "g4sbs"
    echo "------"
    echo
    export $(xargs < output.env)
    runStr="g4sbs ${SCRIPT}" 
    eval "$runStr"

* Before submitting job you need to has x509 proxy. Use voms command to generate proxy.

    .. code-block:: shell

        $ voms-proxy-init --cert $HOME/.cilogon/usercert.pem --key $HOME/.cilogon/userkey.pem --hours 24

    You will get a proxy file in ``/tmp`` directory starting with ``x509up_``.


* Create a submit file for OSG submission and name it as ``OSG.sub``

.. code-block:: bash

    executable = run_g4sbs.sh  
    output = output/stdout.$(PROCESS)
    error = error/stderr.$(PROCESS)
    log = logs/stdlog$(PROCESS).log
    notification = never
    universe = vanilla
    should_transfer_files = yes
    x509userproxy = /tmp/x509up_uxxxxx  # replace me with actual name
    transfer_input_files = gen_1018_example.mac  # your .mac script to run
    WhenToTransferOutput = ON_EXIT
    on_exit_remove = (ExitBySignal == False) && (ExitCode == 0)
    on_exit_remove = true
    RequestCPUs = 1
    Requirements = HAS_SINGULARITY == True
    +SingularityImage = "/cvmfs/oasis.opensciencegrid.org/jlab/halla/sbs/containers/g4sbs_pythia.sif"
    +SingularityBindCVMFS = True


* submit the job
    Use ``condor_submit`` to submit the job.

.. code-block:: shell

    $ condor_submit osg.sub

* To check the job status
