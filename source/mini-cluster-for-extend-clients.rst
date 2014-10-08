.. index::
   single: .Net; Extend proxy
   single: C++; Extend proxy
   single: Mini-cluster; example

.. _mini-cluster-for-extend-clients:

.Net/C++ Extend client tutorial
===============================

TODO: CREATE WINDOWS BATCH FILES FOR TUTORIAL

.. warning:: This is a new page and it is actively being worked on and so the content is likely to change whilst it gets refined and improved - any feedback or comments are welcome :-)

This tutorial is aimed at non-Java developers that need to launch a simple cluster comprising of at least
one Extend proxy and at least one storage-enabled member (cache server), thus enabling them to having a fully
working 'mini-cluster' for their .Net or C++ Extend client code to connect to.

Assumptions
-----------

Before we get started - let's state a few assumptions:

* You have Java installed

(ideally a Java Development Kit - JDK) as this includes a server mode for Java.

See note #1, maybe assume not and that that is something they can add.

* You have a Coherence JAR (littlegrid works with Coherence 3.5.3 or later)
* You have a littlegrid JAR - `download latest stable release <http://search.maven.org/remotecontent?filepath=org/littlegrid/littlegrid/2.15.2/littlegrid-2.15.2.jar>`_.


This tutorial uses a sample littlegrid project called *small-sample*, the tutorial aims to get you up
and running as quickly as possible.

link to download ZIP

link to download JAR


3. So, the files we are interested in just to launch the mini-cluster are:

    launch-mini-cluster.sh
    littlegrid-builder-override.properties
    small-sample-datagrid-cluster-1.0-SNAPSHOT.jar


4. Before proceeding further some environment variables need to be set, these are used so that the batch file doesn't need hard-coded paths for Coherence and littlegrid.

set COHERENCE_JAR=.../coherence.jar
set LITTLEGRID_JAR=../littlegrid...version-blah.jar



Remember the server flag might not work


Telnet



Maven


