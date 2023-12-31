.. highlight:: shell

.. _nps_replay:

NPS REPLAY with apptainer
**************************
To run NPS replay we have a NPSlib container.
NPSlib is a extention on hcana for NPS experiment. Github link `here <https://github.com/JeffersonLab/NPSlib>`_

The container for NPSlib are at dockerHUB : https://hub.docker.com/r/jeffersonlab/hallc-npslib/tags 

Building the NPSlib image
-------------------------

.. code-block:: shell

    $ apptainer build nps.sif docker://jeffersonlab/hallc-npslib:v0.1.0

Here we are using the v0.1.0 version of the NPSlib which is also version of container.
I have build my image with the name at "`nps.sif`"


Lets write a bash script to run on ifarm. Filename for this eaxmple is run_nps_replay_apptainer.sh .
To make it work with you, please change the paths in "modify as you need" part of code.
Following asummes that your replay takes the env var argument as for raw data , rootfile and report output as shwown in `here <https://github.com/JeffersonLab/nps_replay/pull/37/files>`_

.. code-block:: bash

    #!/usr/bin/bash

    spec="nps"
    SPEC="NPS"

    # Two input args
    runNum=$1    # RUN Number
    nEvent=$2    # Number of events in that run

    # Modify as you need
    #--------------------------------------------------
    SCRDIR="/path/to/nps_replay"                  # my replay directory
    SCRIPT="SCRIPTS/${SPEC}/eel108_replay.C"      # replay script to run, relative to nps_replay directory
    APPTAINER_IMAGE="/path/to/apptainer/nps.sif"  # apptainer image location
    # for the following make sure your replay script take following as env varibales.
    # e.g. in https://github.com/JeffersonLab/nps_replay/pull/37/files 
 
    DATA_DIR=/cache/hallc/c-nps/raw               # raw data directory.
    LOG_DIR=/path/to/REPORT_OUTPUT/dir        # REPORT output directory
    OUT_DIR=/path/to/output/root/dir              # OUTPUT rootfiles
    #--------------------------------------------------

    # making sure the directory exists.
    mkdir -f ${OUT_DIR}
    mkdir -f ${LOG_DIR}

    cd $SCRDIR

    # Check if apptainer is available
    if command -v apptainer > /dev/null 2>&1; then
        echo "apptainer is already available."
    else
        # Load apptainer if not available
        echo "Loading apptainer..."
        eval module load apptainer
    fi
    # Set the output filename
    output_file="output.env"

    # Write each environment variable name and value to the output file
    echo "DATA_DIR=${DATA_DIR}" >> "${output_file}"
    echo "LOG_DIR=${LOG_DIR}" >> "${output_file}"
    echo "OUT_DIR=${OUT_DIR}" >> "${output_file}"

    echo "Environment variables written to ${output_file}"

    echo 
    echo "---------------------------------------------------------------------------------------------"
    echo "NPS SINGLE REPLAY for ${runNum}. NEvent=${nEvent} using NPSlib container=${APPTAINER_IMAGE}"
    echo "----------------------------------------------------------------------------------------------"
    echo 

    runStr="apptainer exec  --env-file output.env --bind ${DATA_DIR} --bind ${OUT_DIR} --bind ${LOG_DIR} --bind ${SCRDIR}  ${APPTAINER_IMAGE} bash -c \"hcana -q ${SCRIPT}\(${runNum},${nEvent}\)\""
    eval ${runStr}


if your replay doesn't take env variable for raw data, output rootfile and report . **Make sure you have corrresponding link directory in the replay directory**.
Use following as run_nps_replay_apptainer.sh for link directory structure in replay:

.. code-block:: bash

    #!/usr/bin/bash

    spec="nps"
    SPEC="NPS"

    # Two input args
    runNum=$1    # RUN Number
    nEvent=$2    # Number of events in that run

    # Modify as you need
    #--------------------------------------------------
    SCRDIR="/path/to/nps_replay"                  # my replay directory
    SCRIPT="SCRIPTS/${SPEC}/eel108_replay.C"      # replay script to run, relative to nps_replay directory
    APPTAINER_IMAGE="/path/to/apptainer/nps.sif"  # apptainer image location
    DATA_DIR=/path/to/RAWDIR
    OUT_DIR=/path/to/output/root/dir
    LOG_DIR=/path/to/REPORT_OUTPUT/dir
    #--------------------------------------------------

    mkdir -f ${OUT_DIR}
    mkdir -f ${LOG_DIR}

    cd $SCRDIR

    # Check if apptainer is available
    if command -v apptainer > /dev/null 2>&1; then
        echo "apptainer is already available."
    else
        # Load apptainer if not available
        echo "Loading apptainer..."
        eval module load apptainer
    fi

    echo 
    echo "---------------------------------------------------------------------------------------------"
    echo "NPS SINGLE REPLAY for ${runNum}. NEvent=${nEvent} using NPSlib container=${APPTAINER_IMAGE}"
    echo "----------------------------------------------------------------------------------------------"
    echo 

    runStr="apptainer exec --bind ${DATA_DIR} --bind ${OUT_DIR} --bind ${LOG_DIR} --bind ${SCRDIR}  ${APPTAINER_IMAGE} bash -c \"hcana -q ${SCRIPT}\(${runNum},${nEvent}\)\""
    eval ${runStr}


Make the file executables as:

.. code-block:: bash

    $ chmod +x run_nps_replay_apptainer.sh

Now in ifarm you can just to following to run a single jobs.
Add corresponding run_num and num_events that you want to analyze.

.. code-block:: bash

    $ bash run_nps_replay_apptainer.sh <run_num> <num_events>

Debug using interactive shell
------------------------------
To debug anything or execute command manually inside the image you envoke inetractive shell using the following.

.. code-block:: bash

    $ apptainer shell --env-file output.env --bind ${DATA_DIR} --bind ${OUT_DIR} --bind ${LOG_DIR} --bind ${SCRDIR}  ${APPTAINER_IMAGE} bash

swif2 jobs
----------
To use the above script with swif jobs. Please replace the DATA_DIR path to following.

.. code-block:: bash

    DATA_DIR=${PWD}

How to use swif is explained in `this <https://scicomp.jlab.org/docs/swif2>`_ .
Input file is specified in swif as :

.. code-block:: bash

    -input <filename> mss:/mss/hallc/c-nps/raw/<filename>

Here , replace <filename> with actual filename. For example nps_345.dat.0. This will then will automatically show up in the working directory.
On the script make sure to have DATA_DIR specifies as $PWD before moving into any other direrectory.
Example swif command line to submit a jobs is:

.. code-block:: bash

    $ swif2 add-job myworkflow -account hallc -partition production -shell /bin/bash -input nps_345.dat.0 mss:/mss/hallc/c-nps/raw/nps_345.dat.0 -cores 1 /path/to/run_nps_replay_apptainer.sh 345 100

Your std out and err of the job will writeen to /farm_out/$USER/<foo>.{out,err} by default.

hcswif
------
**Comming soon**
