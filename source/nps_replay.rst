.. highlight:: shell

.. _nps_replay:

NPS REPLAY with apptainer
**************************
To run NPS replay we have a NPSlib container.
NPSlib is a extention on hcana fro NPS experiment. Github link `here <https://github.com/JeffersonLab/NPSlib>`_

The container for NPSlib are at dockerHUB : https://hub.docker.com/r/jeffersonlab/hallc-npslib/tags 

Building the NPSlib image
-------------------------

.. code-block:: shell

    $ apptainer build nps.sif docker://jeffersonlab/hallc-npslib:v0.1.0

Here we are using the v0.1.0 version of the NPSlib which is also version of container.
I have build my image with the name at "`nps.sif`"


Lets write a bash script to run on ifarm. Filename for this eaxmple is run_nps_replay_apptainer.sh
To make it owrk with you, please change the paths in "modify as you need" part of code.

.. code:: bash


    #!/bin/bash

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
    DATA_DIR=/cache/hallc/c-nps/raw                       # raw data directory.
    LOG_DIR=/path/to/log/dir/nps_log             # REPORT output directory
    OUT_DIR=/path/to/output/root/dir/nps_root            # OUTPUT rootfiles
    #--------------------------------------------------

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

    runStr="apptainer exec  --bind ${DATA_DIR} --bind ${OUT_DIR} --bind ${LOG_DIR} --bind ${SCRDIR}  ${APPTAINER_IMAGE} bash -c \"hcana -q ${SCRIPT}\(${runNum},${nEvent}\)\""
    eval ${runStr}


Now in ifarm you can just to following to run a single jobs.
Add corresponding run_num and num_events that you want to analyze.

.. code:: bash

    $ bash run_nps_replay_apptainer.sh <run_num> <num_events>

swif2 jobs
----------
To use the above script with swif jobs. Please replace the DATA_DIR path to following.

.. code:: bash

    DATA_DIR=${PWD}

Example swif command line to submit a jobs is:

.. code:: bash

    $ swif2 add-job pantatestwork2 -account hallc -partition production -shell /bin/bash -input nps_345.dat.0 mss:/mss/hallc/c-nps/raw/nps_345.dat.0 -cores 1 -stdout /volatile/hallc/nps/panta/my.out -stderr /volatile/hallc/nps/panta/my.err /work/hallc/nps/panta/nps_replay/jobs/run_swif.sh 345 100


hcswif
------
**Comming soon**