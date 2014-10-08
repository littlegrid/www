.. index::
   single: Entry processor; Entry processor failover example
   single: Failover; Entry processor example
   single: Stop cluster member; Entry processor example
   single: Shutdown cluster member; Entry processor example

.. _failover-testing:

Failover testing
================

These tests demonstrate how littlegrid enables you to perform failover testing, there are two
ways a cluster member can leave the cluster, either by shutting down (leaving the cluster in
a polite and orderly way) or by stopping a member (the cluster member leaves immediately without
notifying the other cluster members).

With these tests, the standard Java Executor framework is used to essentially perform 'deferred'
events, this is where the action to be performed is delayed, in this case allowing the execution
of an entry processor to start before the stop or shutdown is performed.

The entry processor in this example sleeps for a period that is longer than the deferred event
time, so for example if the entry processor sleeps for 5 seconds, but the deferred event to stop
the cluster member is performed after 2 seconds, thus making it possible to observe the behaviour
of Coherence when performing a stop or shutdown and an 'active' execution of an entry processing
is being performed.

.. code-block:: java

    import com.tangosol.net.CacheFactory;
    import com.tangosol.net.DistributedCacheService;
    import com.tangosol.net.NamedCache;
    import com.tangosol.util.InvocableMap;
    import com.tangosol.util.processor.AbstractProcessor;
    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.littlegrid.ClusterMemberGroup;
    import org.littlegrid.ClusterMemberGroupUtils;
    
    import java.util.concurrent.Executors;
    import java.util.concurrent.ScheduledExecutorService;
    import java.util.concurrent.TimeUnit;
    
    import static org.junit.Assert.assertTrue;
    
    /**
     * Entry processor failover integration tests - these tests demonstrate the different
     * behaviour when performing a stop or shutdown on a cluster member whilst it is executing
     * an entry processor, the tests also demonstrate how to use the standard Java Executor
     * framework to essentially perform 'deferred' events.
     */
    public class EntryProcessorFailoverIntegrationTest {
        private static final int SECONDS_DELAY_BEFORE_DEFERRED_ACTION = 2;
        private static final int SECONDS_TO_SLEEP_IN_ENTRY_PROCESSOR = SECONDS_DELAY_BEFORE_DEFERRED_ACTION + 3;
    
        private ClusterMemberGroup memberGroup;
        private NamedCache cache;
    
    
        @Before
        public void beforeTest() {
            memberGroup = ClusterMemberGroupUtils.newBuilder()
                    .setStorageEnabledCount(3)
                    .setCacheConfiguration("simple-cache-config.xml")
                    .setLogLevel(3)
                    .setFastStartJoinTimeoutMilliseconds(1000)
                    .buildAndConfigureForStorageDisabledClient();
    
            cache = CacheFactory.getCache("test");
        }
    
        @After
        public void afterTest() {
            ClusterMemberGroupUtils.shutdownCacheFactoryThenClusterMemberGroups(memberGroup);
        }
    
        @Test
        public void deferredStopAndEntryProcessorFailover() {
            final String key = "abc";
            final int idOfMemberToStop = ((DistributedCacheService) cache.getCacheService()).getKeyOwner(key).getId();
    
            final Runnable runnable = new Runnable() {
                @Override
                public void run() {
                    // With a stop, the Coherence member leaves the cluster immediately without
                    // telling the other members - hence it is like it has been killed
                    memberGroup.stopMember(idOfMemberToStop);
                }
            };
    
            final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(1);
            executorService.schedule(runnable, SECONDS_DELAY_BEFORE_DEFERRED_ACTION, TimeUnit.SECONDS);
    
            final Integer idOfMemberThatCompletedProcessing = (Integer) cache.invoke(key, new SleepyProcessor());
    
            // The member being stopped whilst executing the entry processor is *not* expected
            // to be the member that originally started the processing - i.e. the processing should
            // be completed on the member who originally had the backup
            assertTrue(idOfMemberThatCompletedProcessing != idOfMemberToStop);
        }
    
        @Test
        public void deferredShutdownAndEntryProcessorFailover() throws InterruptedException {
            final String key = "abc";
            final int idOfMemberToShutdown = ((DistributedCacheService) cache.getCacheService()).getKeyOwner(key).getId();
    
            final Runnable runnable = new Runnable() {
                @Override
                public void run() {
                    // With a shutdown, Coherence should let the entry processor finish on the original
                    // member before the member leaves the cluster
                    memberGroup.shutdownMember(idOfMemberToShutdown);
                }
            };
    
            final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(1);
            executorService.schedule(runnable, SECONDS_DELAY_BEFORE_DEFERRED_ACTION, TimeUnit.SECONDS);
    
            final Integer idOfMemberThatCompletedProcessing = (Integer) cache.invoke(key, new SleepyProcessor());
    
            // The member being shutdown whilst executing the entry processor is expected
            // to be the member that originally started the processing
            assertTrue(idOfMemberThatCompletedProcessing == idOfMemberToShutdown);
        }
    
        /**
         * Simple entry processor that sleeps enabling it to be failed over in
         * certain circumstances.
         */
        public static class SleepyProcessor extends AbstractProcessor {
            @Override
            public Object process(final InvocableMap.Entry entry) {
                final int memberId = CacheFactory.ensureCluster().getLocalMember().getId();
    
                CacheFactory.log("============ About to sleep");
    
                try {
                    TimeUnit.SECONDS.sleep(SECONDS_TO_SLEEP_IN_ENTRY_PROCESSOR);
                } catch (InterruptedException e) {
                    throw new IllegalStateException(e);
                }
    
                CacheFactory.log("============ Finished sleeping - survived!  ID of member that completed processing: "
                        + memberId);
    
                return memberId;
            }
        }
    }


With the below simple cache configuration:

.. code-block:: xml

    <cache-config>
        <caching-scheme-mapping>
            <cache-mapping>
                <cache-name>*</cache-name>
                <scheme-name>simple-distributed-scheme</scheme-name>
            </cache-mapping>
        </caching-scheme-mapping>

        <caching-schemes>
            <distributed-scheme>
                <scheme-name>simple-distributed-scheme</scheme-name>
                <backing-map-scheme>
                    <local-scheme/>
                </backing-map-scheme>
                <autostart>true</autostart>
            </distributed-scheme>
        </caching-schemes>
    </cache-config>

This first test demonstrates that when a stop is performed, the member originally executing the entry processor
stops and failover occurs, with the new (now) owning member performing the execution.

.. code-block:: bash

    Running EntryProcessorFailoverIntegrationTest
    23-Apr-2012 22:45:53 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: File 'littlegrid/littlegrid-builder-override.properties' found and '4' properties loaded
    23-Apr-2012 22:45:53 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: Properties file(s) not found, no properties loaded: 'littlegrid-builder-override.properties'
    23-Apr-2012 22:45:53 org.littlegrid.impl.DefaultClusterMemberGroupBuilder loadAndSetBuilderKeysAndValues
    INFO: Prefixed 'littlegrid.builder.override' environment variables found '0'
    23-Apr-2012 22:45:53 org.littlegrid.impl.DefaultClusterMemberGroupBuilder loadAndSetBuilderKeysAndValues
    INFO: Prefixed 'littlegrid.builder.override' system properties found '0'
    23-Apr-2012 22:45:53 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: Properties file(s) not found, no properties loaded: 'littlegrid-builder-system-property-mapping-override.properties' 'littlegrid/littlegrid-builder-system-property-mapping-override.properties'
    23-Apr-2012 22:45:53 org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ littlegrid 2.9 starting - Storage-enabled: 3, Extend proxy: 0, Storage-enabled Extend proxy: 0, Custom configured: 0, JMX monitor: 0 ___
    23-Apr-2012 22:45:53 org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: System properties to be set.: {com.sun.management.jmxremote=false, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=true, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=22000, tangosol.coherence.log=stdout, tangosol.coherence.log.level=3, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.role=DedicatedStorageEnabledMember, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=22000}
    23-Apr-2012 22:45:53 org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: Max memory: 736MB, current: 736MB, free memory: 728MB
    2012-04-23 22:45:54.429/0.792 Oracle Coherence 3.7.1.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/tangosol-coherence.xml"
    2012-04-23 22:45:54.432/0.795 Oracle Coherence 3.7.1.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational overrides from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 3.7.1.0 Build 27797
    Grid Edition: Development mode
    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    2012-04-23 22:45:54.578/0.941 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded cache configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:45:55.037/1.400 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-1-thread-1, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2012-04-23 22:45:55.224/1.587 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-1-thread-1, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 511 packets (7.98MB); actual size is 1%, 7 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:45:56.919/3.282 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): Created a new cluster "littlegridCluster" with Member(Id=1, Timestamp=2012-04-23 22:45:55.267, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2) UID=0x7F00000100000136E12B0743EB9A55F0
    2012-04-23 22:45:56.924/3.287 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-1-thread-1, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    ... start-up of Coherence members
    2012-04-23 22:46:00.944/7.307 Oracle Coherence GE 3.7.1.0 <Info> (thread=main, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    MasterMemberSet(
    ThisMember=Member(Id=4, Timestamp=2012-04-23 22:46:00.721, Address=127.0.0.1:22006, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=StorageDisabledClient)
    OldestMember=Member(Id=1, Timestamp=2012-04-23 22:45:55.267, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    ActualMemberSet=MemberSet(Size=4
    Member(Id=1, Timestamp=2012-04-23 22:45:55.267, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=2, Timestamp=2012-04-23 22:45:58.81, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=3, Timestamp=2012-04-23 22:45:58.823, Address=127.0.0.1:22004, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=4, Timestamp=2012-04-23 22:46:00.721, Address=127.0.0.1:22006, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=StorageDisabledClient)
    )
    MemberId|ServiceVersion|ServiceJoined|MemberState
    1|3.7.1|2012-04-23 22:45:55.267|JOINED,
    2|3.7.1|2012-04-23 22:45:58.81|JOINED,
    3|3.7.1|2012-04-23 22:45:58.823|JOINED,
    4|3.7.1|2012-04-23 22:46:00.934|JOINED
    RecycleMillis=1200000
    RecycleSet=MemberSet(Size=0
    )
    )

    TcpRing{Connections=[3]}
    IpMonitor{AddressListSize=0}

    2012-04-23 22:46:01.196/7.559 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=4): Loaded POF configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-pof-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:01.228/7.591 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=4): Loaded included POF configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/coherence-pof-config.xml"
    2012-04-23 22:46:01.396/7.759 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=2): ============ About to sleep
    23-Apr-2012 22:46:03 org.littlegrid.impl.DefaultClusterMemberGroup stopMember
    INFO: About to stop cluster member with id '2'
    2012-04-23 22:46:03.486/9.849 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=1): Restored from backup 128 partitions
    2012-04-23 22:46:03.497/9.860 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=1): ============ About to sleep
    2012-04-23 22:46:08.498/14.861 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=1): ============ Finished sleeping - survived!  ID of member that completed processing: 1
    23-Apr-2012 22:46:08 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Restoring system properties back to their original state before member group started
    23-Apr-2012 22:46:08 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Shutting down '3' cluster member(s) in group
    2012-04-23 22:46:08.583/14.946 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 129 primary partitions, 128 backup partitions
    2012-04-23 22:46:10.585/16.948 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 129 primary partitions, 110 backup partitions
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: ___ Group of cluster member(s) shutdown ___


This second test demonstrates that when a shutdown is performed, the member originally executing the entry processor
receives the shutdown - but it completes the execution of the entry processor, basically it doesn't
leave the cluster until its work is done.

.. code-block:: bash

    23-Apr-2012 22:46:10 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: File 'littlegrid/littlegrid-builder-override.properties' found and '4' properties loaded
    23-Apr-2012 22:46:10 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: Properties file(s) not found, no properties loaded: 'littlegrid-builder-override.properties'
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroupBuilder loadAndSetBuilderKeysAndValues
    INFO: Prefixed 'littlegrid.builder.override' environment variables found '0'
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroupBuilder loadAndSetBuilderKeysAndValues
    INFO: Prefixed 'littlegrid.builder.override' system properties found '0'
    23-Apr-2012 22:46:10 org.littlegrid.support.PropertiesUtils loadProperties
    INFO: Properties file(s) not found, no properties loaded: 'littlegrid-builder-system-property-mapping-override.properties' 'littlegrid/littlegrid-builder-system-property-mapping-override.properties'
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ littlegrid 2.9 starting - Storage-enabled: 3, Extend proxy: 0, Storage-enabled Extend proxy: 0, Custom configured: 0, JMX monitor: 0 ___
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: System properties to be set.: {com.sun.management.jmxremote=false, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=true, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=22000, tangosol.coherence.log=stdout, tangosol.coherence.log.level=3, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.role=DedicatedStorageEnabledMember, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=22000}
    23-Apr-2012 22:46:10 org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: Max memory: 736MB, current: 736MB, free memory: 528MB
    2012-04-23 22:46:10.995/17.358 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-1, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/tangosol-coherence.xml"
    2012-04-23 22:46:10.998/17.361 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-1, member=n/a): Loaded operational overrides from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 3.7.1.0 Build 27797
    Grid Edition: Development mode
    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    2012-04-23 22:46:11.141/17.504 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-1, member=n/a): Loaded cache configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:11.420/17.783 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-1, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2012-04-23 22:46:11.444/17.807 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-1, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (511KB); actual size is 25%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:11.444/17.807 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-1, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (3.99MB); actual size is 3%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:11.444/17.807 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-1, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 511 packets (7.98MB); actual size is 1%, 7 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:13.128/19.491 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): Created a new cluster "littlegridCluster" with Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2) UID=0x7F00000100000136E12B469AEB9A55F0
    2012-04-23 22:46:13.131/19.494 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-1, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    MasterMemberSet(
    ThisMember=Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    OldestMember=Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    ActualMemberSet=MemberSet(Size=1
    Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    )
    MemberId|ServiceVersion|ServiceJoined|MemberState
    1|3.7.1|2012-04-23 22:46:13.128|JOINED
    RecycleMillis=1200000
    RecycleSet=MemberSet(Size=0
    )
    )

    TcpRing{Connections=[]}
    IpMonitor{AddressListSize=0}

    2012-04-23 22:46:13.353/19.716 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=1): Loaded POF configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-pof-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:13.387/19.750 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=1): Loaded included POF configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/coherence-pof-config.xml"
    2012-04-23 22:46:13.720/20.083 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-3, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/tangosol-coherence.xml"
    2012-04-23 22:46:13.724/20.087 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-3, member=n/a): Loaded operational overrides from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 3.7.1.0 Build 27797
    Grid Edition: Development mode
    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    2012-04-23 22:46:13.839/20.202 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-3, member=n/a): Loaded cache configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:13.775/20.139 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-2, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/tangosol-coherence.xml"
    2012-04-23 22:46:13.779/20.142 Oracle Coherence 3.7.1.0 <Info> (thread=pool-3-thread-2, member=n/a): Loaded operational overrides from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 3.7.1.0 Build 27797
    Grid Edition: Development mode
    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    2012-04-23 22:46:13.895/20.258 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-2, member=n/a): Loaded cache configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:14.233/20.596 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-3, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2012-04-23 22:46:14.264/20.627 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-2, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2012-04-23 22:46:14.266/20.629 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-3, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (511KB); actual size is 25%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.266/20.629 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-3, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (3.99MB); actual size is 3%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.267/20.630 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-3, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 511 packets (7.98MB); actual size is 1%, 7 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.293/20.656 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-2, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (511KB); actual size is 25%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.294/20.657 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-2, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (3.99MB); actual size is 3%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.295/20.658 Oracle Coherence GE 3.7.1.0 <Warning> (thread=pool-3-thread-2, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 511 packets (7.98MB); actual size is 1%, 7 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:14.575/20.938 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): This Member(Id=2, Timestamp=2012-04-23 22:46:14.391, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2) joined cluster "littlegridCluster" with senior Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2)
    2012-04-23 22:46:14.608/20.971 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-3, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    MasterMemberSet(
    ThisMember=Member(Id=2, Timestamp=2012-04-23 22:46:14.391, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    OldestMember=Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    ActualMemberSet=MemberSet(Size=2
    Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=2, Timestamp=2012-04-23 22:46:14.391, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    )
    MemberId|ServiceVersion|ServiceJoined|MemberState
    1|3.7.1|2012-04-23 22:46:11.482|JOINED,
    2|3.7.1|2012-04-23 22:46:14.591|JOINED
    RecycleMillis=1200000
    RecycleSet=MemberSet(Size=0
    )
    )

    TcpRing{Connections=[1]}
    IpMonitor{AddressListSize=0}

    2012-04-23 22:46:14.619/20.982 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): This Member(Id=3, Timestamp=2012-04-23 22:46:14.43, Address=127.0.0.1:22004, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2) joined cluster "littlegridCluster" with senior Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2)
    2012-04-23 22:46:14.666/21.029 Oracle Coherence GE 3.7.1.0 <Info> (thread=pool-3-thread-2, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    MasterMemberSet(
    ThisMember=Member(Id=3, Timestamp=2012-04-23 22:46:14.43, Address=127.0.0.1:22004, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    OldestMember=Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    ActualMemberSet=MemberSet(Size=3
    Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=2, Timestamp=2012-04-23 22:46:14.391, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=3, Timestamp=2012-04-23 22:46:14.43, Address=127.0.0.1:22004, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    )
    MemberId|ServiceVersion|ServiceJoined|MemberState
    1|3.7.1|2012-04-23 22:46:11.482|JOINED,
    2|3.7.1|2012-04-23 22:46:14.391|JOINED,
    3|3.7.1|2012-04-23 22:46:14.654|JOINED
    RecycleMillis=1200000
    RecycleSet=MemberSet(Size=0
    )
    )

    TcpRing{Connections=[2]}
    IpMonitor{AddressListSize=0}

    2012-04-23 22:46:14.919/21.282 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=2): Loaded POF configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-pof-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:14.962/21.325 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=3): Loaded POF configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-pof-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:14.978/21.341 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=2): Loaded included POF configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/coherence-pof-config.xml"
    2012-04-23 22:46:15.005/21.368 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=3): Loaded included POF configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/coherence-pof-config.xml"
    23-Apr-2012 22:46:15 org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ Group of cluster member(s) started, member Ids: [1, 3, 2] ___
    23-Apr-2012 22:46:15 org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildAndConfigureForStorageDisabledClient
    INFO: System properties set for client: {com.sun.management.jmxremote=false, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=false, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.extend.enabled=false, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=22000, tangosol.coherence.log=stdout, tangosol.coherence.log.level=3, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.role=StorageDisabledClient, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=22000}
    2012-04-23 22:46:15.399/21.762 Oracle Coherence GE 3.7.1.0 <Info> (thread=main, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar!/tangosol-coherence.xml"
    2012-04-23 22:46:15.401/21.764 Oracle Coherence GE 3.7.1.0 <Info> (thread=main, member=n/a): Loaded operational overrides from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"

    Oracle Coherence Version 3.7.1.0 Build 27797
    Grid Edition: Development mode
    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    2012-04-23 22:46:15.407/21.770 Oracle Coherence GE 3.7.1.0 <Info> (thread=main, member=n/a): Loaded cache configuration from "file:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes/coherence/littlegrid-test-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2012-04-23 22:46:15.418/21.781 Oracle Coherence GE 3.7.1.0 <Warning> (thread=main, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2012-04-23 22:46:15.420/21.783 Oracle Coherence GE 3.7.1.0 <Warning> (thread=main, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (511KB); actual size is 25%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:15.420/21.783 Oracle Coherence GE 3.7.1.0 <Warning> (thread=main, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (3.99MB); actual size is 3%, 2 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:15.420/21.783 Oracle Coherence GE 3.7.1.0 <Warning> (thread=main, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 511 packets (7.98MB); actual size is 1%, 7 packets (127KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2012-04-23 22:46:15.602/21.965 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): Failed to satisfy the variance: allowed=16, actual=47
    2012-04-23 22:46:15.602/21.965 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): Increasing allowable variance to 19
    2012-04-23 22:46:15.960/22.323 Oracle Coherence GE 3.7.1.0 <Info> (thread=Cluster, member=n/a): This Member(Id=4, Timestamp=2012-04-23 22:46:15.757, Address=127.0.0.1:22006, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=StorageDisabledClient, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2) joined cluster "littlegridCluster" with senior Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2)
    2012-04-23 22:46:16.150/22.513 Oracle Coherence GE 3.7.1.0 <Info> (thread=main, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
    WKA{Address=127.0.0.1, Port=22000}
    )

    MasterMemberSet(
    ThisMember=Member(Id=4, Timestamp=2012-04-23 22:46:15.757, Address=127.0.0.1:22006, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=StorageDisabledClient)
    OldestMember=Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    ActualMemberSet=MemberSet(Size=4
    Member(Id=1, Timestamp=2012-04-23 22:46:11.482, Address=127.0.0.1:22000, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=2, Timestamp=2012-04-23 22:46:14.391, Address=127.0.0.1:22002, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=3, Timestamp=2012-04-23 22:46:14.43, Address=127.0.0.1:22004, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=DedicatedStorageEnabledMember)
    Member(Id=4, Timestamp=2012-04-23 22:46:15.757, Address=127.0.0.1:22006, MachineId=60314, Location=site:,machine:localhost,process:23051, Role=StorageDisabledClient)
    )
    MemberId|ServiceVersion|ServiceJoined|MemberState
    1|3.7.1|2012-04-23 22:46:11.482|JOINED,
    2|3.7.1|2012-04-23 22:46:14.391|JOINED,
    3|3.7.1|2012-04-23 22:46:14.43|JOINED,
    4|3.7.1|2012-04-23 22:46:16.136|JOINED
    RecycleMillis=1200000
    RecycleSet=MemberSet(Size=0
    )
    )

    TcpRing{Connections=[3]}
    IpMonitor{AddressListSize=0}

    2012-04-23 22:46:16.283/22.646 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=2): ============ About to sleep
    23-Apr-2012 22:46:18 org.littlegrid.impl.DefaultClusterMemberGroup shutdownMember
    INFO: About to shutdown cluster member '2'
    2012-04-23 22:46:21.284/27.647 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): ============ Finished sleeping - survived!  ID of member that completed processing: 2
    2012-04-23 22:46:21.289/27.652 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 86 primary partitions, 85 backup partitions
    23-Apr-2012 22:46:21 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Restoring system properties back to their original state before member group started
    23-Apr-2012 22:46:21 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Shutting down '3' cluster member(s) in group
    2012-04-23 22:46:21.382/27.745 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 85 primary partitions, 0 backup partitions
    2012-04-23 22:46:21.412/27.775 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 85 primary partitions, 0 backup partitions
    2012-04-23 22:46:21.594/27.957 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 1 primary partitions, 0 backup partitions
    2012-04-23 22:46:21.751/28.115 Oracle Coherence GE 3.7.1.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 1 primary partitions, 0 backup partitions
    23-Apr-2012 22:46:22 org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: ___ Group of cluster member(s) shutdown ___
