.. index::
   single: JUnit; Extend example
   single: Extend; JUnit example

.. _using-extend-proxy:

Using Extend proxy
==================

If you haven't already had a look, the :ref:`getting-started` is the best place to start looking at littlegrid.

1) This example shows you how to run two storage-enabled members, an Extend proxy server and connect
as an Extend client - all in a single JVM using littlegrid.  Save the below class in a file
called: SimpleExtendIntegrationTest.java

.. code-block:: java

    import com.tangosol.net.CacheFactory;
    import com.tangosol.net.NamedCache;
    import com.tangosol.util.aggregator.Count;
    import com.tangosol.util.filter.AlwaysFilter;
    import org.junit.AfterClass;
    import org.junit.Before;
    import org.junit.BeforeClass;
    import org.junit.Test;
    import org.littlegrid.ClusterMemberGroup;
    import org.littlegrid.ClusterMemberGroupUtils;

    import java.util.Collections;

    import static junit.framework.Assert.assertEquals;

    /**
     * Simple Extend littlegrid integration tests.
     */
    public class SimpleExtendIntegrationTest {
        private static final int NUMBER_OF_ITEMS = 250;
        private static ClusterMemberGroup memberGroup;

        private final NamedCache cache = CacheFactory.getCache("simple-extend-example");


        /**
         * Use BeforeClass to start the cluster up before any of the tests run - this ensures
         * we only have the start-up delay only once.
         *
         * Note: apart from starting up and shutting down littlegrid, its code and API shouldn't
         * really be in any of your tests, unless you want to perform a stop or shutdown of a
         * particular member for failover testing.
         */
        @BeforeClass
        public static void beforeTests() {
            // Build the cluster
            //
            // Note: use of the fast-start option and log level set to 6, so that we see the
            // interesting Extend logging of client connections.
            //
            memberGroup = ClusterMemberGroupUtils.newBuilder()
                    .setStorageEnabledCount(2)
                    .setCacheConfiguration("simple-extend-cache-config.xml")
                    .setExtendProxyCount(1)
                    .setClientCacheConfiguration("simple-extend-client-cache-config.xml")
                    .setFastStartJoinTimeoutMilliseconds(1000)
                    .setLogLevel(6)
                    .buildAndConfigureForExtendClient();
        }

        /**
         * Shutdown the cluster, this method also does a CacheFactory.shutdown() for the client
         * to ensure that leaves nicely as well.
         */
        @AfterClass
        public static void afterTests() {
            ClusterMemberGroupUtils.shutdownCacheFactoryThenClusterMemberGroups(memberGroup);
        }

        /**
         * For this example test we are clearing down the cache each time and repopulating it.
         */
        @Before
        public void beforeTest() {
            cache.clear();

            for (int i = 0; i < NUMBER_OF_ITEMS; i++) {
                cache.putAll(Collections.singletonMap(i, i));
            }

            assertEquals(NUMBER_OF_ITEMS, cache.size());
        }

        @Test
        public void putGet() {
            cache.put("key", "value");

            assertEquals(NUMBER_OF_ITEMS + 1, cache.size());
        }

        @Test
        public void aggregateCount() {
            assertEquals(NUMBER_OF_ITEMS, cache.aggregate(AlwaysFilter.INSTANCE, new Count()));
        }
    }


2) Before running the test, you need the cache server (storage-enabled) and Extend proxy
configuration file.  Save the below cache configuration in a file called:
simple-extend-cache-config.xml

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

            <!--
                littlegrid uses system properties to enable Extend and control the address and
                port number that the Extend proxy server runs on.

                Note: if you want to use different system property names, that is fine as littlegrid
                supports a mapping to your system property names.
            -->
            <proxy-scheme>
                <scheme-name>extend-tcp</scheme-name>
                <service-name>ExtendProxyServerService</service-name>
                <acceptor-config>
                    <tcp-acceptor>
                        <local-address>
                            <address system-property="tangosol.coherence.extend.address">127.x.2.3</address>
                            <port system-property="tangosol.coherence.extend.port">9099</port>
                        </local-address>
                    </tcp-acceptor>
                </acceptor-config>
                <autostart system-property="tangosol.coherence.extend.enabled">false</autostart>
            </proxy-scheme>
        </caching-schemes>
    </cache-config>


3) Finally you just need the Extend client cache configuration, save this in a file called:
simple-extend-client-cache-config.xml

.. code-block:: xml

    <cache-config>
        <caching-scheme-mapping>
            <cache-mapping>
                <cache-name>*</cache-name>
                <scheme-name>extend-tcp</scheme-name>
            </cache-mapping>
        </caching-scheme-mapping>

        <caching-schemes>
            <remote-cache-scheme>
                <scheme-name>extend-tcp</scheme-name>
                <service-name>ExtendProxyClientService</service-name>
                <initiator-config>
                    <tcp-initiator>
                        <remote-addresses>
                            <socket-address>
                                <address system-property="tangosol.coherence.extend.address">127.x.2.3</address>
                                <port system-property="tangosol.coherence.extend.port">9099</port>
                            </socket-address>
                        </remote-addresses>
                    </tcp-initiator>
                </initiator-config>
            </remote-cache-scheme>
        </caching-schemes>
    </cache-config>


4) Now just run the example, try it from your IDE :-)  What you'll notice is that littlegrid
has set all the system properties and ports numbers, you don't need to do anything special -
just concentrate on the Coherence bit and let littlegrid start and shutdown the cluster.

.. code-block:: bash

    Sep 11, 2014 8:47:41 AM org.littlegrid.impl.DefaultClusterMemberGroupBuilder <init>
    INFO: ___ littlegrid 2.16-SNAPSHOT (http://www.littlegrid.net) - initialised.  Builder values: {default file=50, override file=3, 'littlegrid_builder_' environment variables=0, 'LITTLEGRID_BUILDER_' environment variables=0, 'littlegrid.builder.' system properties=2}.  Builder to Coherence system property mapping values: {default file=41, override file=0} ___
    Sep 11, 2014 8:47:41 AM org.littlegrid.impl.DefaultClusterMemberGroupBuilder buildClusterMembers
    INFO: ___ littlegrid 2.16-SNAPSHOT starting - Storage-enabled: 2, Extend proxy: 1, Storage-enabled proxy: 0, JMX: 0, Custom configured: 0 ___
    Sep 11, 2014 8:47:41 AM org.littlegrid.impl.DefaultConfigurer getSystemPropertiesForTcmpClusterMember
    WARNING: Fast-start join timeout specified.  Note: the fast-start Coherence override file will now be configured to be used
    Sep 11, 2014 8:47:41 AM org.littlegrid.impl.DefaultClusterMemberGroup outputStartAllMessages
    INFO: System properties to be set.: {com.sun.management.jmxremote=false, littlegrid.join.timeout.milliseconds=1000, tangosol.coherence.cacheconfig=simple-extend-cache-config.xml, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.distributed.localstorage=true, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.localport=40100, tangosol.coherence.log=stdout, tangosol.coherence.log.level=6, tangosol.coherence.machine=DefaultMachine, tangosol.coherence.management=none, tangosol.coherence.management.remote=true, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, tangosol.coherence.rack=DefaultRack, tangosol.coherence.role=DedicatedStorageEnabledMember, tangosol.coherence.site=DefaultSite, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.ttl=0, tangosol.coherence.wka=127.0.0.1, tangosol.coherence.wka.port=40100, tangosol.pof.enabled=false}
    Sep 11, 2014 8:47:41 AM org.littlegrid.impl.DefaultClusterMemberGroup outputMemoryMessage
    INFO: Max memory: 736MB, current: 736MB, free memory: 724MB
    2014-09-11 08:47:42.197/0.938 Oracle Coherence 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational configuration from "jar:file:/home/jhall/.m2/repository/com/oracle/coherence/coherence/12.1.3-0-0/coherence-12.1.3-0-0.jar!/tangosol-coherence.xml"
    2014-09-11 08:47:42.200/0.941 Oracle Coherence 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded operational overrides from "file:/home/jhall/littlegrid/littlegrid-coherence-testsupport/target/classes/littlegrid/littlegrid-fast-start-coherence-override.xml"
    2014-09-11 08:47:42.210/0.951 Oracle Coherence 12.1.3.0.0 <D5> (thread=pool-1-thread-1, member=n/a): Optional configuration override "cache-factory-config.xml" is not specified
    2014-09-11 08:47:42.221/0.962 Oracle Coherence 12.1.3.0.0 <D5> (thread=pool-1-thread-1, member=n/a): Optional configuration override "cache-factory-builder-config.xml" is not specified
    2014-09-11 08:47:42.224/0.965 Oracle Coherence 12.1.3.0.0 <D5> (thread=pool-1-thread-1, member=n/a): Optional configuration override "/custom-mbeans.xml" is not specified
    2014-09-11 08:47:42.225/0.966 Oracle Coherence 12.1.3.0.0 <D6> (thread=pool-1-thread-1, member=n/a): Loaded edition data from "jar:file:/home/jhall/.m2/repository/com/oracle/coherence/coherence/12.1.3-0-0/coherence-12.1.3-0-0.jar!/coherence-grid.xml"

    Oracle Coherence Version 12.1.3.0.0 Build 52031
     Grid Edition: Development mode
    Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.

    2014-09-11 08:47:42.373/1.114 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Loaded cache configuration from "file:/home/jhall/littlegrid/littlegrid-coherence-testsupport/target/test-classes/simple-extend-cache-config.xml"; this document does not refer to any schema definition and has not been validated.
    2014-09-11 08:47:42.598/1.339 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Created cache factory com.tangosol.net.ExtensibleConfigurableCacheFactory
    2014-09-11 08:47:42.975/1.716 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): Local address "127.0.0.1" is a loopback address; this cluster node will not connect to nodes located on different machines
    2014-09-11 08:47:43.018/1.759 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): UnicastUdpSocket failed to set send buffer size to 8 packets (512KB); actual size is 40%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-11 08:47:43.018/1.759 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): UnicastUdpSocket failed to set receive buffer size to 64 packets (4MB); actual size is 5%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-11 08:47:43.019/1.760 Oracle Coherence GE 12.1.3.0.0 <Warning> (thread=pool-1-thread-1, member=n/a): PreferredUnicastUdpSocket failed to set receive buffer size to 128 packets (8MB); actual size is 2%, 3 packets (208KB). Consult your OS documentation regarding increasing the maximum socket buffer size. Proceeding with the actual value may cause sub-optimal performance.
    2014-09-11 08:47:43.019/1.760 Oracle Coherence GE 12.1.3.0.0 <D4> (thread=pool-1-thread-1, member=n/a): TCMP bound to /127.0.0.1:40100 using SystemDatagramSocketProvider
    2014-09-11 08:47:44.304/3.045 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=Cluster, member=n/a): Created a new cluster "littlegridCluster" with Member(Id=1, Timestamp=2014-09-11 08:47:43.072, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember, Edition=Grid Edition, Mode=Development, CpuCount=4, SocketCount=2)
    2014-09-11 08:47:44.306/3.048 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=pool-1-thread-1, member=n/a): Started cluster Name=littlegridCluster

    WellKnownAddressList(Size=1,
      WKA{Address=127.0.0.1, Port=40100}
      )

    MasterMemberSet(
      ThisMember=Member(Id=1, Timestamp=2014-09-11 08:47:43.072, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
      OldestMember=Member(Id=1, Timestamp=2014-09-11 08:47:43.072, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
      ActualMemberSet=MemberSet(Size=1
        Member(Id=1, Timestamp=2014-09-11 08:47:43.072, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
        )
      MemberId|ServiceVersion|ServiceJoined|MemberState
        1|12.1.3|2014-09-11 08:47:43.072|JOINED
      RecycleMillis=1200000
      RecycleSet=MemberSet(Size=0
        )
      )

    ...
    2014-09-11 08:47:48.774/7.515 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=Proxy:ExtendProxyServerService:TcpAcceptor:TcpProcessor, member=3): The specified local address "/127.0.0.1" is a loopback address; clients running on remote machines will not be able to connect to this TcpAcceptor
    2014-09-11 08:47:48.774/7.515 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=3): TcpAcceptor now listening for connections on 127.0.0.1:40150
    2014-09-11 08:47:48.778/7.519 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=3): Started: TcpAcceptor{Name=Proxy:ExtendProxyServerService:TcpAcceptor, State=(SERVICE_STARTED), ThreadCount=8, HungThreshold=0, TaskTimeout=0, Codec=Codec(Format=POF), Serializer=com.tangosol.io.DefaultSerializer, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@75ee2516, LocalAddress=WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40150]]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}, ListenBacklog=0, BufferPoolIn=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited), BufferPoolOut=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited)}
    2014-09-11 08:47:48.787/7.528 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Proxy:ExtendProxyServerService, member=3): Service ExtendProxyServerService joined the cluster with senior service member 3
    2014-09-11 08:47:52.413/11.154 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=ExtendProxyClientService:TcpInitiator, member=n/a): Closed: Channel(Id=1451514861, Open=false)
    2014-09-11 08:47:52.413/11.154 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=ExtendProxyClientService:TcpInitiator, member=n/a): Closed: Channel(Id=1877724955, Open=false)
    2014-09-11 08:47:52.414/11.155 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=ExtendProxyClientService:TcpInitiator, member=n/a): Closed: TcpConnection(Id=0x0000014863AEBE76408233D2B27BE1D22C2AF99E33B2B46780DAC1C45705E036, Open=false, Member(Id=0, Timestamp=2014-09-11 08:47:49.45, Address=127.0.0.1:0, MachineId=0, Location=site:,machine:localhost,process:1473, Role=ExtendClient), LocalAddress=127.0.0.1:35206, RemoteAddress=127.0.0.1:40150)
    2014-09-11 08:47:52.414/11.155 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=ExtendProxyClientService:TcpInitiator, member=n/a): Stopped: TcpInitiator{Name=ExtendProxyClientService:TcpInitiator, State=(SERVICE_STOPPED), ThreadCount=0, Codec=Codec(Format=POF), Serializer=com.tangosol.io.DefaultSerializer, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, ConnectTimeout=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@3458faae, RemoteAddresses=WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40150]]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}}
    2014-09-11 08:47:52.418/11.159 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=3): Closed: Channel(Id=1451514861, Open=false)
    2014-09-11 08:47:52.422/11.163 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=3): Closed: Channel(Id=1877724955, Open=false)
    2014-09-11 08:47:52.423/11.164 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=3): Closed: TcpConnection(Id=0x0000014863AEBE76408233D2B27BE1D22C2AF99E33B2B46780DAC1C45705E036, Open=false, Member(Id=0, Timestamp=2014-09-11 08:47:49.45, Address=127.0.0.1:0, MachineId=0, Location=site:,machine:localhost,process:1473, Role=ExtendClient), LocalAddress=127.0.0.1:40150, RemoteAddress=127.0.0.1:35206)
    2014-09-11 08:47:52.427/11.168 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor:TcpProcessor, member=3): Released: TcpConnection(Id=0x0000014863AEBE76408233D2B27BE1D22C2AF99E33B2B46780DAC1C45705E036, Open=false, Member(Id=0, Timestamp=2014-09-11 08:47:49.45, Address=127.0.0.1:0, MachineId=0, Location=site:,machine:localhost,process:1473, Role=ExtendClient), LocalAddress=127.0.0.1:40150, RemoteAddress=127.0.0.1:35206)
    2014-09-11 08:47:52.430/11.171 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 129 primary partitions, 128 backup partitions
    Sep 11, 2014 8:47:52 AM org.littlegrid.ClusterMemberGroupUtils shutdownCacheFactoryThenClusterMemberGroups
    INFO: About to shutdown cache factory
    Sep 11, 2014 8:47:52 AM org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: Restoring system properties back to their original state before member group started
    2014-09-11 08:47:52.432/11.173 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=2): This member has become the distribution coordinator for MemberSet(Size=2
      Member(Id=1, Timestamp=2014-09-11 08:47:43.072, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
      Member(Id=2, Timestamp=2014-09-11 08:47:46.106, Address=127.0.0.1:40102, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
      )
    2014-09-11 08:47:52.448/11.189 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=2): SimpleAssignmentStrategy suggested the ownership of Ownership{Owners=2, 0} for PartitionSet{0..256}
    2014-09-11 08:47:52.450/11.191 Oracle Coherence GE 12.1.3.0.0 <D4> (thread=DistributedCache, member=2): Asking member 1 for primary ownership of PartitionSet{128..256}
    2014-09-11 08:47:52.476/11.217 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=n/a): Transferring primary PartitionSet{128..256} to member 2 requesting 129
    2014-09-11 08:47:52.542/11.283 Oracle Coherence GE 12.1.3.0.0 <Info> (thread=DistributedCache, member=n/a): Remains to transfer before shutting down: 0 primary partitions, 128 backup partitions
    2014-09-11 08:47:52.543/11.284 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=n/a): Primary[]#000
    Backup[1]#128: 000, 001, 002, 003, 004, 005, 006, 007, 008, 009, 010, 011, 012, 013, 014, 015, 016, 017, 018, 019, 020, 021, 022, 023, 024, 025, 026, 027, 028, 029, 030, 031, 032, 033, 034, 035, 036, 037, 038, 039, 040, 041, 042, 043, 044, 045, 046, 047, 048, 049, 050, 051, 052, 053, 054, 055, 056, 057, 058, 059, 060, 061, 062, 063, 064, 065, 066, 067, 068, 069, 070, 071, 072, 073, 074, 075, 076, 077, 078, 079, 080, 081, 082, 083, 084, 085, 086, 087, 088, 089, 090, 091, 092, 093, 094, 095, 096, 097, 098, 099, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127
    2014-09-11 08:47:54.543/13.284 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=n/a): Service DistributedCache left the cluster
    2014-09-11 08:47:54.547/13.288 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=3): Member 1 left service DistributedCache with senior member 1
    2014-09-11 08:47:54.548/13.289 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=2): Member 1 left service DistributedCache with senior member 1
    2014-09-11 08:47:54.548/13.289 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=2): Service DistributedCache: sending ServiceConfig ConfigSync to all
    2014-09-11 08:47:54.548/13.289 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=n/a): Service Management left the cluster
    2014-09-11 08:47:54.551/13.292 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=2): Member 1 left service Management with senior member 1
    2014-09-11 08:47:54.552/13.293 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Invocation:Management, member=2): Service Management: sending ServiceConfig ConfigSync to all
    2014-09-11 08:47:54.552/13.293 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=2): Service DistributedCache: sending PartitionConfig ConfigSync to all
    2014-09-11 08:47:54.552/13.293 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=3): Service DistributedCache: received ServiceConfig ConfigSync from member 2 containing 2 entries
    2014-09-11 08:47:54.552/13.293 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=3): Member 1 left service Management with senior member 1
    2014-09-11 08:47:54.553/13.294 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Invocation:Management, member=3): Service Management: received ServiceConfig ConfigSync from member 2 containing 1 entries
    2014-09-11 08:47:54.557/13.298 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=3): Service DistributedCache: received PartitionConfig ConfigSync from member 2 containing 257 entries
    2014-09-11 08:47:54.758/13.499 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=2): Member(Id=1, Timestamp=2014-09-11 08:47:54.757, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember) left Cluster with senior member 2
    2014-09-11 08:47:54.759/13.500 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Cluster, member=2): TcpRing disconnected from Member(Id=1, Timestamp=2014-09-11 08:47:54.757, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember) to maintain ring
    2014-09-11 08:47:54.758/13.499 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=3): Member(Id=1, Timestamp=2014-09-11 08:47:54.757, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember) left Cluster with senior member 2
    2014-09-11 08:47:54.760/13.501 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Cluster, member=2): TcpRing connected to Member(Id=3, Timestamp=2014-09-11 08:47:47.981, Address=127.0.0.1:40104, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedExtendProxyMember)
    2014-09-11 08:47:54.760/13.501 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=3): MemberLeft announcement from Member(Id=1, Timestamp=2014-09-11 08:47:54.757, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
    2014-09-11 08:47:54.761/13.502 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=2): MemberLeft announcement from Member(Id=1, Timestamp=2014-09-11 08:47:54.757, Address=127.0.0.1:40100, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
    2014-09-11 08:47:54.771/13.512 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=n/a): Service Cluster left the cluster
    2014-09-11 08:47:54.773/13.514 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=NameService:TcpAcceptor, member=n/a): Stopped: TcpAcceptor{Name=NameService:TcpAcceptor, State=(SERVICE_STOPPED), ThreadCount=0, Codec=Codec(Format=POF), Serializer=com.tangosol.net.internal.NameServicePofContext, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@7a4e2185, LocalAddress=WrapperSocketAddressProvider{Providers=[WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40100]]}]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}, ListenBacklog=0, BufferPoolIn=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited), BufferPoolOut=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited)}
    2014-09-11 08:47:54.778/13.519 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=n/a): Service DistributedCache left the cluster
    2014-09-11 08:47:54.781/13.522 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=3): Member 2 left service DistributedCache with senior member 2
    2014-09-11 08:47:54.781/13.522 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=DistributedCache, member=3): Service DistributedCache: sending ServiceConfig ConfigSync to all
    2014-09-11 08:47:54.784/13.525 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=3): Member 2 left service Management with senior member 2
    2014-09-11 08:47:54.784/13.525 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Invocation:Management, member=3): Service Management: sending ServiceConfig ConfigSync to all
    2014-09-11 08:47:54.781/13.522 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=n/a): Service Management left the cluster
    2014-09-11 08:47:54.987/13.728 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=3): Member(Id=2, Timestamp=2014-09-11 08:47:54.987, Address=127.0.0.1:40102, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember) left Cluster with senior member 3
    2014-09-11 08:47:54.988/13.729 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Cluster, member=3): TcpRing disconnected from Member(Id=2, Timestamp=2014-09-11 08:47:54.987, Address=127.0.0.1:40102, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember) to maintain ring
    2014-09-11 08:47:54.988/13.729 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=3): MemberLeft announcement from Member(Id=2, Timestamp=2014-09-11 08:47:54.987, Address=127.0.0.1:40102, MachineId=30438, Location=site:DefaultSite,rack:DefaultRack,machine:DefaultMachine,process:1473, Role=DedicatedStorageEnabledMember)
    2014-09-11 08:47:55.002/13.743 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=n/a): Service Cluster left the cluster
    2014-09-11 08:47:55.015/13.756 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=NameService:TcpAcceptor, member=n/a): Stopped: TcpAcceptor{Name=NameService:TcpAcceptor, State=(SERVICE_STOPPED), ThreadCount=0, Codec=Codec(Format=POF), Serializer=com.tangosol.net.internal.NameServicePofContext, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@49c53f6a, LocalAddress=WrapperSocketAddressProvider{Providers=[WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40102]]}]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}, ListenBacklog=0, BufferPoolIn=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited), BufferPoolOut=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited)}
    2014-09-11 08:47:55.017/13.758 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=Proxy:ExtendProxyServerService:TcpAcceptor, member=n/a): Stopped: TcpAcceptor{Name=Proxy:ExtendProxyServerService:TcpAcceptor, State=(SERVICE_STOPPED), ThreadCount=8, HungThreshold=0, TaskTimeout=0, Codec=Codec(Format=POF), Serializer=com.tangosol.io.DefaultSerializer, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@75ee2516, LocalAddress=WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40150]]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}, ListenBacklog=0, BufferPoolIn=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited), BufferPoolOut=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited)}
    2014-09-11 08:47:55.020/13.761 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Proxy:ExtendProxyServerService, member=n/a): Service ExtendProxyServerService left the cluster
    2014-09-11 08:47:55.023/13.764 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=DistributedCache, member=n/a): Service DistributedCache left the cluster
    2014-09-11 08:47:55.027/13.768 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Invocation:Management, member=n/a): Service Management left the cluster
    2014-09-11 08:47:55.045/13.786 Oracle Coherence GE 12.1.3.0.0 <D5> (thread=Cluster, member=n/a): Service Cluster left the cluster
    2014-09-11 08:47:55.048/13.789 Oracle Coherence GE 12.1.3.0.0 <D6> (thread=NameService:TcpAcceptor, member=n/a): Stopped: TcpAcceptor{Name=NameService:TcpAcceptor, State=(SERVICE_STOPPED), ThreadCount=0, Codec=Codec(Format=POF), Serializer=com.tangosol.net.internal.NameServicePofContext, PingInterval=0, PingTimeout=0, RequestTimeout=0, MaxIncomingMessageSize=0, MaxOutgoingMessageSize=0, SocketProvider=com.oracle.common.internal.net.MultiplexedSocketProvider@75ee2516, LocalAddress=WrapperSocketAddressProvider{Providers=[WrapperSocketAddressProvider{Providers=[[/127.0.0.1:40104]]}]}, SocketOptions{LingerTimeout=0, KeepAliveEnabled=true, TcpDelayEnabled=false}, ListenBacklog=0, BufferPoolIn=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited), BufferPoolOut=BufferPool(BufferSize=2KB, BufferType=DIRECT, Capacity=Unlimited)}
    Sep 11, 2014 8:47:55 AM org.littlegrid.impl.DefaultClusterMemberGroup shutdownAll
    INFO: ___ Group of 3 cluster member(s) shutdown in 2622ms ___
