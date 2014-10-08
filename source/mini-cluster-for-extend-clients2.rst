Launch standalone mini-cluster for Extend clients
=================================================

TODO: CREATE WINDOWS BATCH FILES FOR TUTORIAL

.. warning:: This is a new page and it is actively being worked on and so the content is likely to change whilst it gets refined and improved - any feedback or comments are welcome :-)

This tutorial is aimed at non-Java developers that need to launch a simple cluster comprising of at least
one Extend proxy and at least one storage-enabled member (cache servers), thus enabling them to having a fully
working 'mini-cluster' for their .Net or C++ Extend client code to connect to.


Assumptions
-----------

Before we get started - let's state a few assumptions:

* You have Java installed (ideally a Java Development Kit - JDK) as this includes a server mode for Java.

See note #1, maybe assume not and that that is something they can add.


* You have a Coherence JAR (littlegrid works with Coherence 3.5.3 or later)
* You have a littlegrid JAR - `download latest stable release <http://search.maven.org/remotecontent?filepath=org/littlegrid/littlegrid/2.15.2/littlegrid-2.15.2.jar>`_.

Additionally, you may have been given some project related JARs by the Java developers in your
team (these JARs will typically contain the domain objects, entry processors and other bespoke classes
used by your application).

This tutorial uses a sample littlegrid project called *small-sample*, the tutorial consists of two parts:

* A quick start tutorial for those wishing to get up and running as quickly as possible and who either don't want to build the sample project just yet or maybe can't build as they don't have Maven installed.
* A slightly more in-depth tutorial for those wishing to build the sample project before running a mini-cluster.

All the source code for this tutorial is available, including a full Maven build and unit tests for an Account
class that is a POF object.  Finally, the entire configuration and code is tested using a Java integration
test - this uses littlegrid like the Java developers you use it, i.e. native littlegrid.

link to download ZIP

Ok, so let's get going - for now, we will assume that you don't have Maven and so you don't have a way to
build the JAR file required for this tutorial - the JAR file can be downloaded from here, it contains an
Account object and the Coherence cache configuration required for the Coherence cluster members (storage-enabled
cache servers and Extend proxies).

link to download JAR

1. Firstly download the ZIP file, it contains all the source code and also the custom application JAR containing the Account class.

.. note:: Normally this type of ZIP **wouldn't contain the built JAR containing the Account class**, however in order to make the tutorial self-contained the built JAR is included.

2. Unzip the ZIP file, you should see a directory structure like the below

.. code-block:: bash

    ├── build-and-launch-mini-cluster.sh
    ├── launch-mini-cluster.sh
    ├── littlegrid-builder-override.properties
    ├── pom.xml
    ├── small-sample-datagrid-cluster
    │   ├── pom.xml
    │   ├── src
    │   │   ├── main
    │   │   │   ├── java
    │   │   │   │   └── org
    │   │   │   │       └── littlegrid
    │   │   │   │           └── sample
    │   │   │   │               └── account
    │   │   │   │                   └── model
    │   │   │   │                       └── Account.java
    │   │   │   └── resources
    │   │   │       └── coherence
    │   │   │           ├── small-sample-cache-config.xml
    │   │   │           └── small-sample-pof-config.xml
    │   │   └── test
    │   │       ├── java
    │   │       │   └── org
    │   │       │       └── littlegrid
    │   │       │           └── sample
    │   │       │               ├── account
    │   │       │               │   └── model
    │   │       │               │       └── AccountSerializerTest.java
    │   │       │               └── testsupport
    │   │       │                   └── serializer
    │   │       │                       └── AbstractSerializerTest.java
    │   │       └── resources
    │   └── target
    │       └── small-sample-datagrid-cluster-1.0-SNAPSHOT.jar
    │
    ├── small-sample-datagrid-cluster-integration-tests
    │   ├── pom.xml
    │   ├── src
    │   │   └── test
    │   │       ├── java
    │   │       │   └── org
    │   │       │       └── littlegrid
    │   │       │           └── sample
    │   │       │               ├── account
    │   │       │               │   └── AccountClusterIntegrationTest.java
    │   │       │               └── testsupport
    │   │       │                   └── cluster
    │   │       │                       └── AbstractClusterIntegrationTest.java
    │   │       └── resources
    │   │           └── littlegrid
    │   │               └── small-sample-littlegrid-builder.properties


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


