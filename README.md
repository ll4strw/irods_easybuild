**irods_easybuild** provides instructions and a collection of EasyBuild
config files to build [iRODS](https://irods.org) using 
[EasyBuild](https://easybuild.io/).

EasyBuild is a software build and installation framework that allows you to manage (scientific) software on High Performance Computing (HPC) systems in an efficient way.

The Integrated Rule-Oriented Data System (iRODS) is open source data management software used by research, commercial, and governmental organizations worldwide.

### Advantages

Automated and **reprducible** way to install iRODS (or any of its components) and its external dependencies on any GNU/Linux system.
For instance, think of the deployment of `iRODS-icommands` on any GNU/Linux distribution.

### Installation

- Install and setup EasyBuild as described [here](https://docs.easybuild.io/installation/)
- Clone this repository

  ```
     git clone git@github.com:ll4strw/irods_easybuild.git
  ```
- Install iRODS or only iRODS client libraries

  ```
     ml load EasyBuild
     cd irods_easybuild.git
     eb --robot-paths=easyconfigs  -r irods-4.3.0-GCC-11.2.0.eb [--try-amend=build_cmd='make irods_client ']

  ```

- Install iRODS-icommands

  ```
     eb --robot-paths=easyconfigs  -r irods-icommands-4.3.0-GCC-11.2.0.eb 

  ```

### Test

  ```
     ml purge
     ml load irods-icommands/4.3.0-GCC-11.2.0
     iinit
  ```

