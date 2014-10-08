.. index::
   single: Builder; Mapping to system properties
   single: System properties; Mapping to builder

.. _builder-to-system-property-mapping:

Controlling system property names used by littlegrid
====================================================

Coherence provides the ability for you to supply default values in your configuration files (such as cache
configuration and override file) and for those static values (as defined in the file) to be selectively
overridden at runtime, so for instance the address and port that an Extend proxy server listens on might
look something like the below:

.. code-block:: xml

    <tcp-acceptor>
        <local-address>
            <address system-property="tangosol.coherence.extend.address">localhost</address>
            <port system-property="tangosol.coherence.extend.port">9099</port>
        </local-address>
    </tcp-acceptor>


So, in the above, the default Extend port will be ``9099``, unless a different values is supplied using
a Java system property, such as:

.. code-block:: bash

    java -Dtangosol.coherence.extend.port=20123 -cp some-classpath-etc

.. note:: Most Coherence projects use the ability to override values, so they can have the minimum number of configuration files as possible and avoid having specific configuration files per environment.


What?
-----
littlegrid has a fluent API that is based around the builder pattern, the littlegrid ``ClusterMemberGroup.Builder``
allows a value to be set and for setting of values to be chained, e.g.

.. code-block:: java

    memberGroup = ClusterMemberGroupUtils.newBuilder()
        .setWkaPort(15001)
        .setExtendPort(16001)
        ....

The values that are set are then mapped to internal names and littlegrid provides a mechanism to then map
its internal names to Coherence system property names.

.. note:: You can configure littlegrid in a number of ways (Builder, properties file, system properties or environment variables), they all internally map to the builder names, see :ref:`builder-to-value-mapping`

Why?
----
Put simply - each project is different!  Projects often tend to adopt their own conventions and standard naming.

....so, littlegrid provides the ability to map its internal builder names to Coherence system property names to
accommodate different project naming conventions or standards, so for instance if the project is called
something like ``Jupiter``, then sometimes the project team may choose to prefix their system properties, e.g.

.. code-block:: xml

    <address system-property="jupiter.extend.port">localhost</address>


How?
----

littlegrid maintains a mapping of builder keys to system properties - this allows it to
be easily configured to use custom system properties instead of the standard Coherence
system properties.  To provide your own mapping, simply include a file called
``littlegrid-builder-system-property-mapping-override.properties`` on
your **class-path in the root directory** or ``/littlegrid`` directory off the class-path root.

Below is a list of littlegrid builder name to default system property names:

==================================  ================================
littlegrid builder name             Default system property name
==================================  ================================
FastStartJoinTimeoutMilliseconds    littlegrid.join.timeout.milliseconds

CacheConfiguration                  tangosol.coherence.cacheconfig
ClientCacheConfiguration            tangosol.coherence.cacheconfig
OverrideConfiguration               tangosol.coherence.override
ClientOverrideConfiguration         tangosol.coherence.override
CustomConfiguredCacheConfiguration  tangosol.coherence.cacheconfig

DistributedLocalStorage             tangosol.coherence.distributed.localstorage
TcmpEnabled                         tangosol.coherence.tcmp.enabled
ExtendEnabled                       tangosol.coherence.extend.enabled

ClusterName                         tangosol.coherence.cluster
SiteName                            tangosol.coherence.site
RackName                            tangosol.coherence.rack
MachineName                         tangosol.coherence.machine
CustomConfiguredRoleName            tangosol.coherence.role
StorageEnabledRoleName              tangosol.coherence.role
StorageDisabledClientRoleName       tangosol.coherence.role
StorageEnabledExtendProxyRoleName   tangosol.coherence.role
JmxMonitorRoleName                  tangosol.coherence.role
ExtendProxyRoleName                 tangosol.coherence.role
ExtendClientRoleName                tangosol.coherence.role

LocalAddress                        tangosol.coherence.localhost
LocalPort                           tangosol.coherence.localport
WkaAddress                          tangosol.coherence.wka
WkaPort                             tangosol.coherence.wka.port
Ttl                                 tangosol.coherence.ttl
ExtendAddress                       tangosol.coherence.extend.address
ExtendPort                          tangosol.coherence.extend.port

LogLevel                            tangosol.coherence.log.level
LogDestination                      tangosol.coherence.log

CoherenceManagement                 tangosol.coherence.management
CoherenceManagementRemote           tangosol.coherence.management.remote
ManagementJmxRemote                 com.sun.management.jmxremote
==================================  ================================

