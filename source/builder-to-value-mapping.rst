.. index::
   single: Builder; Value mapping
   single: API; Controlling the builder
   single: System properties; Controlling the builder
   single: Environment variables; Controlling the builder
   single: littlegrid-builder-override.properties; Controlling the builder
   single: littlegrid-builder-default.properties; Controlling the builder

.. _builder-to-value-mapping:

Controlling and configuring littlegrid
======================================

littlegrid can be configured/controlled using a variety of methods, such as:

* `The Builder API <http://www.littlegrid.net/javadoc/2.15.2/org/littlegrid/ClusterMemberGroup.Builder.html>`_
* Properties file
* System properties
* Environment variables


The order of configuration priority is as below, so - any configuration you specify in code
is used in preference to any other of the configurations mechanisms, with the littlegrid builder
default properties file being the lowest priority:

1) Your code, so for instance:

.. code-block:: java

    memberGroup = ClusterMemberGroupUtils.newBuilder()
        .setLogLevel(9)


2) System properties prefixed with ``littlegrid.builder``, such as:

.. code-block:: bash

    -Dlittlegrid.builder.LogLevel=6



3) Environment variables prefixed with ``littlegrid_builder``, e.g. for Windows:

.. code-block:: bash

    set littlegrid_builder.LogLevel=6


or Linux (Bash):

.. code-block:: bash

    export littlegrid_builder.LogLevel=6


4) Your optional ``littlegrid-builder-override.properties`` (loaded from the root of a class-path or from a directory called ``littlegrid`` below the root of the class-path):

.. code-block:: bash

    littlegrid/littlegrid-builder-override.properties


or:

.. code-block:: bash

    littlegrid-builder-override.properties


5) The built-in ``littlegrid-builder-default.properties`` (loaded from the littlegrid JAR file) - this contains all the default properties used to configure littlegrid.


.. note:: Whatever mechanism you use to configure littlegrid, it essentially keeps an internal mapping of its *builder name* to the specified value.


The below are typically default properties that are used to configure littlegrid, any of which can
be overridden using the above described methods:

==============================================  ==========================
littlegrid builder name                         Default value
==============================================  ==========================
ExceptionReporterInstanceClassName              org.littlegrid.impl.DefaultBuildExceptionReporter
CallbackHandlerInstanceClassName                org.littlegrid.impl.DefaultCallbackHandler

ClusterMemberInstanceClassName                  org.littlegrid.impl.DefaultClusterMember
CustomConfiguredClusterMemberInstanceClassName  org.littlegrid.impl.DefaultClusterMember
NumberOfThreadsInStartUpPool                    4

CacheConfiguration
OverrideConfiguration
ClientCacheConfiguration
ClientOverrideConfiguration
CustomConfiguredCacheConfiguration

FastStartJoinTimeoutMilliseconds                0

SuggestedSleepAfterStopDuration35x              120
SuggestedSleepAfterStopDuration36x              3
SuggestedSleepAfterStopDurationDefault          3

JarsToExcludeFromClassPath
CoreJarsToExcludeFromClassPath                  alt-rt.jar,charsets.jar,deploy.jar,javaws.jar,jce.jar,jfr.jar,jsse.jar, management-agent.jar,plugin.jar,resources.jar,rt.jar,dnsns.jar, localedata.jar,sunec.jar,sunjce_provider.jar,sunpkcs11.jar, zipfs.jar,classes.jar,ui.jar

CustomConfiguredCount                           0
StorageEnabledCount                             0
StorageEnabledExtendProxyCount                  0
ExtendProxyCount                                0
JmxMonitorCount                                 0

ClusterName                                     littlegridCluster
SiteName                                        DefaultSite
RackName                                        DefaultRack
MachineName                                     DefaultMachine
CustomConfiguredRoleName                        CustomConfiguredMember
StorageEnabledRoleName                          DedicatedStorageEnabledMember
StorageEnabledExtendProxyRoleName               StorageEnabledExtendProxyMember
ExtendProxyRoleName                             DedicatedExtendProxyMember
JmxMonitorRoleName                              DedicatedJmxMonitorMember
StorageDisabledClientRoleName                   StorageDisabledClient
ExtendClientRoleName                            ExtendClient

WkaPort                                         22000
WkaAddress                                      127.0.0.1
Ttl                                             0
ExtendPort                                      23000

LogDestination                                  stdout
LogLevel                                        3
==============================================  ==========================
