.. _getting-started:

Getting started with littlegrid
===============================

This getting started will only take about 1-2 minutes to complete, but by the end you would have
run an entire Coherence cluster in a single JVM and performed operations against a cache.

Before getting started, you'll need to download
`Coherence <http://www.oracle.com/technetwork/middleware/coherence/downloads/index.html>`_ and
follow :ref:`getting-littlegrid` (littlegrid is Maven and Gradle friendly).  Once you've got Coherence
and littlegrid, you're ready to create a simple JUnit test - you can use your favourite IDE or even Notepad
if you want :-).

.. index::
   single: JUnit; Simple example
   single: Getting started; Simple example

.. note:: Here's a JUnit example - note the use of @BeforeClass and @AfterClass, this enables the cluster to be re-used across the tests in the class.

.. code-block:: java

    import com.tangosol.net.CacheFactory;
    import com.tangosol.net.NamedCache;
    import org.junit.AfterClass;
    import org.junit.BeforeClass;
    import org.junit.Test;
    import org.littlegrid.ClusterMemberGroup;
    import org.littlegrid.ClusterMemberGroupUtils;

    import static org.junit.Assert.assertEquals;

    /**
     * Simple integration test example.
     */
    public final class SimpleStorageEnabledIntegrationTest {
        private static ClusterMemberGroup memberGroup;

        @BeforeClass
        public static void beforeTests() {
            memberGroup = ClusterMemberGroupUtils.newBuilder()
                    .setStorageEnabledCount(2)
                    .buildAndConfigureForStorageDisabledClient();
        }

        @AfterClass
        public static void afterTests() {
            ClusterMemberGroupUtils.shutdownCacheFactoryThenClusterMemberGroups(memberGroup);
        }

        @Test
        public void simpleExample() {
            final NamedCache cache = CacheFactory.getCache("test");
            cache.put("key", "Hello World");

            assertEquals(1, cache.size());
        }
    }


The above simple test actually does quite a bit with just a couple of lines of code, firstly it starts
two cache servers (storage-enabled cluster members) and then the test itself joins the cluster as a
storage-disabled client where it then performs simple actions against the test cache.

.. note:: Notice how the littlegrid specific code is both fluent (i.e. simple to understand) and also there isn't much of it - littlegrid is fairly non-intrusive to introduce into an existing code-base.

You should see some output similar to the below:

.. code-block:: bash

    Sep 10, 2014 6:53:50 PM org.littlegrid.impl.DefaultClusterMemberGroupBuilder <init>
    INFO: ___ littlegrid 2.16-SNAPSHOT (http://www.littlegrid.org) - initialised.  Builder values: {default file=50, override file=3, 'littlegrid_builder_' environment variables=0, 'LITTLEGRID_BUILDER_' environment variables=0, 'littlegrid.builder.' system properties=2}.  Builder to Coherence system property mapping values: {default file=41, override file=0} ___
    Sep 10, 2014 6:53:50 PM org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ littlegrid 2.16-SNAPSHOT starting - Storage-enabled: 2, Extend proxy: 0, Storage-enabled proxy: 0, JMX: 0, Custom configured: 0 ___
    Sep 10, 2014 6:53:50 PM org.littlegrid.impl.DefaultConfigurer getSystemPropertiesForTcmpClusterMember
    WARNING: Fast-start join timeout specified.  Note: the fast-start Coherence override file will now be configured to be used
    Sep 10, 2014 6:53:50 PM org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: System properties to be set.: {com.sun.management.jmxremote=false, littlegrid.join.timeout.milliseconds=100, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=true, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=40100, tangosol.coherence.log=stdout, tangosol.coherence.log.level=3, tangosol.coherence.machine=DefaultMachine, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.rack=DefaultRack, tangosol.coherence.role=DedicatedStorageEnabledMember, tangosol.coherence.site=DefaultSite, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=40100, tangosol.pof.enabled=false}
    Sep 10, 2014 6:53:50 PM org.littlegrid.impl.DefaultClusterMemberGroup outputMemoryMessage
    INFO: Max memory: 736MB, current: 736MB, free memory: 720MB
    2014-09-10 18:53:50.644/0.803 Oracle Coherence 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/.m2/repository/com/oracle/coherence/coherence/12.1.3-0-0/coherence-12.1.3-0-0.jar!/tangosol-coherence.xml"
    2014-09-10 18:53:50.647/0.806 Oracle Coherence 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational overrides from "file:/home/jhall/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 12.1.3.0.0 Build 52031
     Grid Edition: Development mode
    Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.

    2014-09-10 18:53:50.970/1.129 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded cache configuration from "file:/home/jhall/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"
    2014-09-10 18:53:51.550/1.709 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Created cache factory com.tangosol.net.ExtensibleConfigurableCacheFactory
    2014-09-10 18:53:52.208/2.367 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2014-09-10 18:53:52.250/2.409 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (512KB); actual size is 40%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-10 18:53:52.250/2.409 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (4MB); actual size is 5%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-10 18:53:52.251/2.410 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 128 packets (8MB); actual size is 2%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-10 18:53:52.527/2.686 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=Cluster, member=n/a): Created a new cluster "littlegridCluster" with Member(Id=1, Timestamp=2014-09-10 18:53:52.302, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:32042, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2)
    2014-09-10 18:53:52.529/2.688 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
      WKA{Address=127.0.0.1, Port=40100}
      )

    MasterMemberSet(
      ThisMember=Member(Id=1, Timestamp=2014-09-10 18:53:52.302, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:32042, Role=DedicatedStorageEnabledMember)
      OldestMember=Member(Id=1, Timestamp=2014-09-10 18:53:52.302, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:32042, Role=DedicatedStorageEnabledMember)
      ActualMemberSet=MemberSet(Size=1
        Member(Id=1, Timestamp=2014-09-10 18:53:52.302, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:32042, Role=DedicatedStorageEnabledMember)
        )
      MemberId|ServiceVersion|ServiceJoined|MemberState
        1|12.1.3|2014-09-10 18:53:52.302|JOINED
      RecycleMillis=1200000
      RecycleSet=MemberSet(Size=0
        )
      )

    TcpRing{Connections=[]}
    IpMonitor{Addresses=0, Timeout=15s}

    ...
    Sep 10, 2014 6:53:55 PM org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ Group of 2 cluster member(s) started in 5177ms, member Ids are: [1, 2] ___
    Sep 10, 2014 6:53:55 PM org.littlegrid.impl.DefaultConfigurer getSystemPropertiesForTcmpClusterMember
    WARNING: Fast-start join timeout specified.  Note: the fast-start Coherence override file will now be configured to be used
    Sep 10, 2014 6:53:55 PM org.littlegrid.impl.DefaultConfigurer configureFor
    INFO: System properties set for client/member: {com.sun.management.jmxremote=false, littlegrid.join.timeout.milliseconds=100, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=false, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.extend.enabled=false, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=40100, tangosol.coherence.log=stdout, tangosol.coherence.log.level=3, tangosol.coherence.machine=DefaultMachine, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.rack=DefaultRack, tangosol.coherence.role=StorageDisabledClient, tangosol.coherence.site=DefaultSite, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=40100, tangosol.pof.enabled=false}
    Sep 10, 2014 6:53:57 PM org.littlegrid.ClusterMemberGroupUtils shutdownCacheFactoryThenClusterMemberGroups
    INFO: About to shutdown cache factory
    Sep 10, 2014 6:53:58 PM org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Restoring system properties back to their original state before member group started
    2014-09-10 18:53:58.135/8.294 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 129 primary partitions, 128 backup partitions
    2014-09-10 18:53:58.264/8.423 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 0 primary partitions, 128 backup partitions
    Sep 10, 2014 6:54:00 PM org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: ___ Group of 2 cluster member(s) shutdown in 2392ms ___


Notice how littlegrid has done the following:

* Ensured that WKA (Well Known Addresses) are being used and that the network traffic is restricted to this machine - this prevents accidental clustering.
* Given the members sensible rolenames, such as DedicatedStorageEnabledMember.
* Used different port numbers to avoid clustering with vanilla Coherence instances you might have already running.

.. note:: littlegrid has ways of making tests start faster and re-using an existing cluster across tests for even faster testing.
