.. highlight:: shell

.. _hcana_container:


Hall C Analyzer (hcana) Container
*********************************

Introduction
------------

The Hall C Analyzer (hcana) Container is a pre-configured environment for running Jefferson Lab's Hall C Analyzer software. 
This container provides a convenient and consistent way to run hcana with all its dependencies, including PODD, ROOT, etc. It is hosted on Docker Hub for easy access.

Docker Hub Repository
---------------------

The hcana Container is hosted on Docker Hub. You can find it at the following repository:

- Repository URL: https://hub.docker.com/r/jeffersonlab/hallc-hcana

Contents
--------

The hcana Container includes the following key components:

1. **ROOT**: A powerful data analysis framework that is essential for high-energy physics and nuclear physics analyses.

2. **PODD**: specific PODD (Hall A Analyzer) commit that is used for the hcana.

3. **HCANA**: The Hall C Analyzer software, which is a part of the Jefferson Lab data analysis framework.

Ways to Use the hcana Container
-------------------------------

You can use the hcana Container in various ways, depending on your requirements and your preferred containerization tool.
The containerization tool supported are decribed below:

1. **Docker**: Docker is one of the most popular containerization tool. Docker provides a straightforward method for running containerized applications. 

|

2. **Singularity**: Singularity is a containerization tool that allows you to use containers, even on systems where you do not have administrative privileges. 

|

3. **Podman**: Podman is a containerization tool designed as a drop-in replacement for Docker.


Conclusion
----------

The Hall C Analyzer (hcana) Container provides a versatile environment for running JLab HallC analysis. 
Whether you prefer Docker, Singularity, or Podman, you can easily use this container to access the powerful tools and libraries required for your work.
