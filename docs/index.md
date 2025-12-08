# NiFi Environment Setup
**John Calandra | www.jctechdocs.com**  
**email: john@jctechdocs.com**  
**05 Dec 2025**  

--------------------------  

## Overview
This article will show you how to set up an envrionment on which you can build an Apache NiFi pipeline. It is assumed you have knowledge of Linux.

The following technologies/versions are used in this article:

* Oracle VirtualBox/7.1.12
* Ubuntu Linux/24.04
* OpenJDK/25
* Apache NiFi/2.6.0
* PostgreSQL/12.12

## Build the Virtual Machines
Build four virtual machines (vm[s]) using VirtualBox and Ubuntu Linux (see my guide titled "Set Up a Linux Virtual Environment on Your Windows PC").

## NiFi Installation

### Notes
* Target Machine: The commands are executed only on the VM dedicated to building the NiFi pipeline, not the others (e.g., the PostgreSQL VMs). 
* Installation Directory: The /opt directory.  This directory is commonly used for installing applications that are not part of the operating system distribution. 
* User Context: The user should be logged in as the default user created during the VM setup. This is critical for the later steps where you set file ownership and permissions (chown <username>:<usergroup> /opt/nifi).  
* Interface: All actions requiring command input will be done in a terminal window. 
* Command Type: All presented commands are Bash commands, which is standard for Ubuntu Linux.
* Replace: 
    * \<username> with your vm username  
    * \<usergroup> with your vm usergroup
    * <12-character minimum user password> with a password
    * To find your vm usergroup run the command: groups  
You are in the sudo group, but for replacing \<usergroup> use the named group.  For example: My vm returns (john) and (sudo), so I replace \<usergroup> with john. 

### Steps
1. Install headless current Java Development Kit (headless means install JDK without a GUI, which is not needed for NiFi)

    a) sudo apt update  
    b) sudo apt install openjdk-25-jdk-headless  
    c) java --version (verify JDK installation)

1. Download NiFi

    a) Open a browser and navigate to Apache Download: https://nifi.apache.org/download/  
    b) Select Binaries/NiFi Standard 2.6.0 (or the most current version).  
    c) The selection is a zip file and will be downloaded to the following directory: /home/\<username>/Downloads directory  
    d) Verify the download: ls ~/Downloads (verify download)

1. Install NiFi into /opt

    a) cd ~/Downloads  
    b) sudo apt install unzip  
    c) sudo unzip nifi-2.6.0-bin.zip -d /opt  
    d) ls -l /opt (verify NiFi installation)

1. Rename/Move the NiFi directory for manageability

    a) cd /opt  
    b) sudo mv nifi-2.6.0/ nifi/

1. Set JAVA_HOME to prevent the JAVA_HOME warning message and ensures stability  

    a) Find your Java installation path: dirname \$(dirname \$(readlink -f $(which javac)))  

    * My vms return: /usr/lib/jvm/java-25-openjdk-amd64  

    b) Set $JAVA_HOME in .bashrc: echo "export JAVA_HOME=/usr/lib/jvm/java-25-openjdk-amd64" >> /home/<username>/.bashrc  

    * .bashrc runs each time you start an interactive shell session and part of what it does is set environment variables like $JAVA_HOME  

    c) Run .bashrc manually: source /home/<username>/.bashrc  

    * Running .bashrc makes $JAVA_HOME available to your environment

1. Set Permissions

    * Grant ownership to your user to allow running NiFi without using sudo, which is a security risk.  
    * The NiFi startup script: /opt/nifi/bin/nifi.sh  
    * All NiFi directories: /opt/nifi, /opt/nifi/logs, /opt/nifi/work, and all repositories  

    a) sudo chown -R <username>:<usergroup> /opt/nifi  
    b) sudo chmod -R 755 /opt/nifi 

1. Set NiFi credentials and start the interface

    a) Set NiFi credentials (This command must be run first to enable secure login to the NiFi UI):  
    /opt/nifi/bin/nifi.sh set-single-user-credentials \<username> <12-character minimum user password>  
    b) Start NiFi: /opt/nifi/bin/nifi.sh start  
    c) Verify NiFi process status: /opt/nifi/bin/nifi.sh status

    * The last line of output includes: INFO [main] org.apache.nifi.bootstratp.Command Status: UP

1. Verify NiFi Interface is accessible and Login

    a) In a browser, enter in address bar: https://localhost:8443/nifi (8443 is NiFi default port)  
    b) Login using the credentials previously set  
    c) You should now see the NiFi interface
   
    ![NiFi interface](nifi_interface_01.png)  

    * If you have accessed the NiFi interface, you are ready to build a pipeline. If not, see the Troubleshooting page.  
    * If you want to stop NiFi: Start NiFi: /opt/nifi/bin/nifi.sh stop
