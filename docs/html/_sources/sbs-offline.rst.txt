.. highlight:: shell

.. _sbs_offline:


SBS offline and related replay
*******************************
To run SBS related replay we have a SBS-offline container.
SBS-offline is a extention on analyzer for SBS experiment. Github link `here <https://github.com/JeffersonLab/SBS-offline>`_

The container for SBS-offline are at dockerHUB : `here <https://hub.docker.com/r/jeffersonlab/halla-sbs-offline/tags>`_

Building the SBS-offline apptainer image
----------------------------------------

.. code-block:: shell

    $ apptainer build sbs_offline.sif docker://jeffersonlab/halla-sbs-offline:v1.0.0

Here we are using the v0.1.0 version of the SBS-offline which is also version of container.
I have build my image with the name at "`sbs_offline.sif`"


Lets write a bash script to run on ifarm. Filename for this example is run_gmn_replay_apptainer.sh .
To make it work with you, please change the paths in "modify as you need" part of code.

.. code-block:: bash

    #!/bin/sh                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 

    # Modify as you need   
    # change DATA_DIR to ${PWD} if running with swif                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
    #------------------------------------------------------------                                                                                                                                                                                                                                                                                                                                                                                                                                                             
    SBS_REPLAY="/path/to/SBS-replay" 
    DB_DIR="${SBS_REPLAY}/DB "                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
    script="${SBS_REPLAY_DIR}/replay/replay_gen.C"                         # script to run                                                                                                                                                                                                                                                                                                                                                                                                                    
    APPTAINER_IMAGE="/path/to/my/apptainer/sbs_offline.sif"         # apptainer image location

    DATA_DIR="/cache/mss/halla/sbs/GEnII/raw"
    OUT_DIR="/path/to/output/GMN_REPLAYS/rootfiles"
    LOG_DIR="/path/to/log/GMN_REPLAYS/logs"
    ANALYZER_CONFIGPATH="${SBS_REPLAY}/replay" 
    #--------------------------------------------------------------

    #create directory if not exists                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              
    mkdir -p ${OUT_DIR}
    mkdir -p ${LOG_DIR}

    env_file="env_file.env"

    echo "SBS_REPLAY=$SBS_REPLAY" > "$env_file"
    echo "DB_DIR=$DB_DIR" >> "$env_file"
    echo "DATA_DIR=$DATA_DIR" >> "$env_file"
    echo "OUT_DIR=$OUT_DIR" >> "$env_file"
    echo "LOG_DIR=$LOG_DIR" >> "$env_file"
    echo "ANALYZER_CONFIGPATH=$ANALYZER_CONFIGPATH" >> "$env_file"

    runnum=$1
    maxevents=$2
    #firstevent=$3                                                                                                                           
    #prefix=$4                                                                                                                                                                                                                                                                                                                                                                                                                                              
    #firstsegment=$5                                                                                                                                                                                                                                                                                                                                                                                                          
    #maxsegments=$6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                

    runStr="apptainer exec \
        --env-file env_file.env \
        --bind ${DATA_DIR} \
        --bind ${LOG_DIR} \
        --bind ${SBS_REPLAY_DIR} \
        --bind ${BASEDIR} \
        --bind ${script} \
        ${APPTAINER_IMAGE} bash -c \"cd $SBS/run_replay_here/ && analyzer -b -q ${script}+\(${runnum},${maxevents}\)\"" #,${firstevent}\)\"",${prefix},${firstsegment},${maxsegments}\)\""
    eval ${runStr}


swif2 jobs
----------
To use the above script with swif jobs. Please replace the DATA_DIR path to following.

.. code-block:: bash

    DATA_DIR=${PWD}

How to use swif is explained in `this <https://scicomp.jlab.org/docs/swif2>`_ .
Input file is specified in swif as :

.. code-block:: bash

    -input <filename> mss:mss/halla/sbs/GEnII/raw/<filename>

Here , replace <filename> with actual filename. This will then will automatically show up in the working directory.
On the script make sure to have DATA_DIR specifies as $PWD before moving into any other direrectory.
Example swif command line to submit a jobs is:

.. code-block:: bash

    $ swif2 add-job myworkflow -account halla -partition production -shell /bin/bash -input <filename> mss:mss/halla/sbs/GEnII/raw/<filename> -cores 1 /path/to/run_gmn_replay_apptainer.sh 345 100

Your std out and err of the job will writeen to /farm_out/$USER/<foo>.{out,err} by default.
Replace filename with properfile name and add proper run number or othe option to scripts.