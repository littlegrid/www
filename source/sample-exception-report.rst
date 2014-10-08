.. index::
   single: Exception report; Sample output
   single: Trouble shooting; Using the exception report
   single: Support; Sending the exception report

.. _sample-exception-report:

Sample exception report
=======================


The below is a sample exception report that is produced when littlegrid can't start your
cluster correctly (in this example, the Coherence JAR wasn't on the class path), in addition
to the exception report, littlegrid has attempted to diagnose what it thinks the problem is:

``Possible exception reason: CHECK_CHILD_FIRST_CLASS_PATH_IN_USE There were 2 member ids [1, 1] - however only these were unique member ids [1].  Ensure that the Coherence JAR is on your test class path``


.. code-block:: bash

    SEVERE: Failed to start cluster member group - please check the exception report to aid diagnosis
    Build exception
    **********************************************************************************************
    If you would like help, then please email this entire exception report to: ``help at littlegrid dot org``

    Exception occurred, trouble-shooting information below:
    Name.....................: littlegrid
    Version number...........: 2.13
    Build date...............: Thu Nov 08 21:28:36 GMT 2012
    Current time.............: Mon Nov 12 07:56:20 GMT 2012
    ----------
    Possible exception reason: CHECK_CHILD_FIRST_CLASS_PATH_IN_USE There were 2 member ids [1, 1] - however only these were unique member ids [1].  Ensure that the Coherence JAR is on your test class path
    ----------
    Java home................: /opt/java/jdk1.7.0_04/jre
    ----------
    Class path...............: /opt/intellij/idea-IU-117.117/lib/idea_rt.jar:/opt/intellij/idea-IU-117.117/plugins/junit/lib/junit-rt.jar:/opt/java/jdk1.7.0_04/jre/lib/deploy.jar:/opt/java/jdk1.7.0_04/jre/lib/javaws.jar:/opt/java/jdk1.7.0_04/jre/lib/management-agent.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/lib/plugin.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunjce_provider.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunpkcs11.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/zipfs.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/dnsns.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/localedata.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunec.jar:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes:/home/jhall/maven/repository/junit/junit/4.8.2/junit-4.8.2.jar:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar
    ----------
    Class path in use........: [file:/home/jhall/maven/repository/junit/junit/4.8.2/junit-4.8.2.jar]
    ----------
    Current system properties: {java.runtime.name=Java(TM) SE Runtime Environment, sun.boot.library.path=/opt/java/jdk1.7.0_04/jre/lib/amd64, java.vm.version=23.0-b21, java.vm.vendor=Oracle Corporation, java.vendor.url=http://java.oracle.com/, path.separator=:, java.vm.name=Java HotSpot(TM) 64-Bit Server VM, file.encoding.pkg=sun.io, idea.launcher.port=7532, user.country=GB, sun.java.launcher=SUN_STANDARD, sun.os.patch.level=unknown, java.vm.specification.name=Java Virtual Machine Specification, user.dir=/home/jhall/work/littlegrid/littlegrid-coherence-testsupport, java.runtime.version=1.7.0_04-b20, java.awt.graphicsenv=sun.awt.X11GraphicsEnvironment, java.endorsed.dirs=/opt/java/jdk1.7.0_04/jre/lib/endorsed, os.arch=amd64, java.io.tmpdir=/tmp, line.separator=
    Nov 12, 2012 7:56:20 AM org.littlegrid.impl.DefaultClusterMemberGroup startClusterMembers
    , java.vm.specification.vendor=Oracle Corporation, os.name=Linux, sun.jnu.encoding=UTF-8, java.library.path=/opt/java/jdk1.7.0_04/jre/lib/amd64/server:/opt/java/jdk1.7.0_04/jre/lib/amd64:/opt/java/jdk1.7.0_04/jre/../lib/amd64:/opt/java/jdk1.6.0_24/jre/lib/amd64/server:/opt/java/jdk1.6.0_24/jre/lib/amd64:/opt/java/jdk1.6.0_24/jre/../lib/amd64:/opt/intellij/idea-IU-117.117/bin::/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib, java.specification.name=Java Platform API Specification, java.class.version=51.0, sun.management.compiler=HotSpot 64-Bit Tiered Compilers, os.version=3.0.0-26-generic, user.home=/home/jhall, user.timezone=Europe/London, java.awt.printerjob=sun.print.PSPrinterJob, file.encoding=UTF-8, idea.launcher.bin.path=/opt/intellij/idea-IU-117.117/bin, java.specification.version=1.7, user.name=jhall, java.class.path=/opt/intellij/idea-IU-117.117/lib/idea_rt.jar:/opt/intellij/idea-IU-117.117/plugins/junit/lib/junit-rt.jar:/opt/java/jdk1.7.0_04/jre/lib/deploy.jar:/opt/java/jdk1.7.0_04/jre/lib/javaws.jar:/opt/java/jdk1.7.0_04/jre/lib/management-agent.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/lib/plugin.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunjce_provider.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunpkcs11.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/zipfs.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/dnsns.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/localedata.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunec.jar:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes:/home/jhall/maven/repository/junit/junit/4.8.2/junit-4.8.2.jar:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar, java.vm.specification.version=1.7, sun.arch.data.model=64, java.home=/opt/java/jdk1.7.0_04/jre, sun.java.command=com.intellij.rt.execution.application.AppMain com.intellij.rt.execution.junit.JUnitStarter -ideVersion5 org.littlegrid.features.jar_exclusion.JarExclusionIntegrationTest,startAndShutdownWithCoherenceJarBeingExcluded, java.specification.vendor=Oracle Corporation, user.language=en, awt.toolkit=sun.awt.X11.XToolkit, java.vm.info=mixed mode, java.version=1.7.0_04, java.ext.dirs=/opt/java/jdk1.7.0_04/jre/lib/ext:/usr/java/packages/lib/ext, sun.boot.class.path=/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/sunrsasign.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/classes, java.vendor=Oracle Corporation, file.separator=/, java.vendor.url.bug=http://bugreport.sun.com/bugreport/, sun.cpu.endian=little, sun.io.unicode.encoding=UnicodeLittle, sun.desktop=gnome, sun.cpu.isalist=}
    ----------
    System properties before.: {java.runtime.name=Java(TM) SE Runtime Environment, sun.boot.library.path=/opt/java/jdk1.7.0_04/jre/lib/amd64, java.vm.version=23.0-b21, java.vm.vendor=Oracle Corporation, java.vendor.url=http://java.oracle.com/, path.separator=:, java.vm.name=Java HotSpot(TM) 64-Bit Server VM, file.encoding.pkg=sun.io, idea.launcher.port=7532, user.country=GB, sun.java.launcher=SUN_STANDARD, sun.os.patch.level=unknown, java.vm.specification.name=Java Virtual Machine Specification, user.dir=/home/jhall/work/littlegrid/littlegrid-coherence-testsupport, java.runtime.version=1.7.0_04-b20, java.awt.graphicsenv=sun.awt.X11GraphicsEnvironment, java.endorsed.dirs=/opt/java/jdk1.7.0_04/jre/lib/endorsed, os.arch=amd64, java.io.tmpdir=/tmp, line.separator=
    , java.vm.specification.vendor=Oracle Corporation, os.name=Linux, sun.jnu.encoding=UTF-8, java.library.path=/opt/java/jdk1.7.0_04/jre/lib/amd64/server:/opt/java/jdk1.7.0_04/jre/lib/amd64:/opt/java/jdk1.7.0_04/jre/../lib/amd64:/opt/java/jdk1.6.0_24/jre/lib/amd64/server:/opt/java/jdk1.6.0_24/jre/lib/amd64:/opt/java/jdk1.6.0_24/jre/../lib/amd64:/opt/intellij/idea-IU-117.117/bin::/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib, java.specification.name=Java Platform API Specification, java.class.version=51.0, sun.management.compiler=HotSpot 64-Bit Tiered Compilers, os.version=3.0.0-26-generic, user.home=/home/jhall, user.timezone=Europe/London, java.awt.printerjob=sun.print.PSPrinterJob, file.encoding=UTF-8, idea.launcher.bin.path=/opt/intellij/idea-IU-117.117/bin, java.specification.version=1.7, user.name=jhall, java.class.path=/opt/intellij/idea-IU-117.117/lib/idea_rt.jar:/opt/intellij/idea-IU-117.117/plugins/junit/lib/junit-rt.jar:/opt/java/jdk1.7.0_04/jre/lib/deploy.jar:/opt/java/jdk1.7.0_04/jre/lib/javaws.jar:/opt/java/jdk1.7.0_04/jre/lib/management-agent.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/lib/plugin.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunjce_provider.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunpkcs11.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/zipfs.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/dnsns.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/localedata.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunec.jar:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes:/home/jhall/maven/repository/junit/junit/4.8.2/junit-4.8.2.jar:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar, java.vm.specification.version=1.7, sun.arch.data.model=64, java.home=/opt/java/jdk1.7.0_04/jre, sun.java.command=com.intellij.rt.execution.application.AppMain com.intellij.rt.execution.junit.JUnitStarter -ideVersion5 org.littlegrid.features.jar_exclusion.JarExclusionIntegrationTest,startAndShutdownWithCoherenceJarBeingExcluded, java.specification.vendor=Oracle Corporation, user.language=en, awt.toolkit=sun.awt.X11.XToolkit, java.vm.info=mixed mode, java.version=1.7.0_04, java.ext.dirs=/opt/java/jdk1.7.0_04/jre/lib/ext:/usr/java/packages/lib/ext, sun.boot.class.path=/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/sunrsasign.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/classes, java.vendor=Oracle Corporation, file.separator=/, java.vendor.url.bug=http://bugreport.sun.com/bugreport/, sun.cpu.endian=little, sun.io.unicode.encoding=UnicodeLittle, sun.desktop=gnome, sun.cpu.isalist=}
    ----------
    System properties applied: {tangosol.coherence.wka=127.0.0.1, tangosol.coherence.site=DefaultSite, tangosol.coherence.cluster=littlegridCluster, tangosol.coherence.management=none, tangosol.coherence.override=littlegrid/littlegrid-fast-start-coherence-override.xml, com.sun.management.jmxremote=false, tangosol.coherence.log=stdout, tangosol.coherence.ttl=0, tangosol.coherence.log.level=3, littlegrid.join.timeout.milliseconds=1000, tangosol.coherence.distributed.localstorage=true, tangosol.coherence.role=DedicatedStorageEnabledMember, tangosol.coherence.management.remote=true, tangosol.coherence.machine=DefaultMachine, tangosol.coherence.cacheconfig=coherence/littlegrid-test-cache-config.xml, tangosol.coherence.rack=DefaultRack, tangosol.coherence.localport=22000, tangosol.coherence.extend.address=127.0.0.1, tangosol.coherence.tcmp.enabled=true, tangosol.coherence.localhost=127.0.0.1, tangosol.coherence.wka.port=22000}
    ----------
    System properties applied: sorted to help identification
        com.sun.management.jmxremote = false
        littlegrid.join.timeout.milliseconds = 1000
        tangosol.coherence.cacheconfig = coherence/littlegrid-test-cache-config.xml
        tangosol.coherence.cluster = littlegridCluster
        tangosol.coherence.distributed.localstorage = true
        tangosol.coherence.extend.address = 127.0.0.1
        tangosol.coherence.localhost = 127.0.0.1
        tangosol.coherence.localport = 22000
        tangosol.coherence.log = stdout
        tangosol.coherence.log.level = 3
        tangosol.coherence.machine = DefaultMachine
        tangosol.coherence.management = none
        tangosol.coherence.management.remote = true
        tangosol.coherence.override = littlegrid/littlegrid-fast-start-coherence-override.xml
        tangosol.coherence.rack = DefaultRack
        tangosol.coherence.role = DedicatedStorageEnabledMember
        tangosol.coherence.site = DefaultSite
        tangosol.coherence.tcmp.enabled = true
        tangosol.coherence.ttl = 0
        tangosol.coherence.wka = 127.0.0.1
        tangosol.coherence.wka.port = 22000
    ----------
    Number of members........: 2
    ----------
    Cluster member class name: org.littlegrid.impl.DefaultClusterMember
    ----------
    Number of thread in pool.: 4
    ----------
    Memory...................: max memory: 1733MB, current: 116MB, free memory: 88MB
    ----------
    Network..................: hostname: localhost.localdomain, address: 127.0.0.1
    ----------
    System properties current: all current system properties - sorted to help identification
        awt.toolkit = sun.awt.X11.XToolkit
        file.encoding = UTF-8
        file.encoding.pkg = sun.io
        file.separator = /
        idea.launcher.bin.path = /opt/intellij/idea-IU-117.117/bin
        idea.launcher.port = 7532
        java.awt.graphicsenv = sun.awt.X11GraphicsEnvironment
        java.awt.printerjob = sun.print.PSPrinterJob
        java.class.path = /opt/intellij/idea-IU-117.117/lib/idea_rt.jar:/opt/intellij/idea-IU-117.117/plugins/junit/lib/junit-rt.jar:/opt/java/jdk1.7.0_04/jre/lib/deploy.jar:/opt/java/jdk1.7.0_04/jre/lib/javaws.jar:/opt/java/jdk1.7.0_04/jre/lib/management-agent.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/lib/plugin.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunjce_provider.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunpkcs11.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/zipfs.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/dnsns.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/localedata.jar:/opt/java/jdk1.7.0_04/jre/lib/ext/sunec.jar:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/test-classes:/home/jhall/work/littlegrid/littlegrid-coherence-testsupport/target/classes:/home/jhall/maven/repository/junit/junit/4.8.2/junit-4.8.2.jar:/home/jhall/maven/repository/com/oracle/coherence/3.7.1.0/coherence-3.7.1.0.jar
        java.class.version = 51.0
        java.endorsed.dirs = /opt/java/jdk1.7.0_04/jre/lib/endorsed
        java.ext.dirs = /opt/java/jdk1.7.0_04/jre/lib/ext:/usr/java/packages/lib/ext
        java.home = /opt/java/jdk1.7.0_04/jre
        java.io.tmpdir = /tmp
        java.library.path = /opt/java/jdk1.7.0_04/jre/lib/amd64/server:/opt/java/jdk1.7.0_04/jre/lib/amd64:/opt/java/jdk1.7.0_04/jre/../lib/amd64:/opt/java/jdk1.6.0_24/jre/lib/amd64/server:/opt/java/jdk1.6.0_24/jre/lib/amd64:/opt/java/jdk1.6.0_24/jre/../lib/amd64:/opt/intellij/idea-IU-117.117/bin::/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
        java.runtime.name = Java(TM) SE Runtime Environment
        java.runtime.version = 1.7.0_04-b20
        java.specification.name = Java Platform API Specification
        java.specification.vendor = Oracle Corporation
        java.specification.version = 1.7
        java.vendor = Oracle Corporation
        java.vendor.url = http://java.oracle.com/
        java.vendor.url.bug = http://bugreport.sun.com/bugreport/
        java.version = 1.7.0_04
        java.vm.info = mixed mode
        java.vm.name = Java HotSpot(TM) 64-Bit Server VM
        java.vm.specification.name = Java Virtual Machine Specification
        java.vm.specification.vendor = Oracle Corporation
        java.vm.specification.version = 1.7
        java.vm.vendor = Oracle Corporation
        java.vm.version = 23.0-b21
        line.separator =

        os.arch = amd64
        os.name = Linux
        os.version = 3.0.0-26-generic
        path.separator = :
        sun.arch.data.model = 64
        sun.boot.class.path = /opt/java/jdk1.7.0_04/jre/lib/resources.jar:/opt/java/jdk1.7.0_04/jre/lib/rt.jar:/opt/java/jdk1.7.0_04/jre/lib/sunrsasign.jar:/opt/java/jdk1.7.0_04/jre/lib/jsse.jar:/opt/java/jdk1.7.0_04/jre/lib/jce.jar:/opt/java/jdk1.7.0_04/jre/lib/charsets.jar:/opt/java/jdk1.7.0_04/jre/lib/jfr.jar:/opt/java/jdk1.7.0_04/jre/classes
        sun.boot.library.path = /opt/java/jdk1.7.0_04/jre/lib/amd64
        sun.cpu.endian = little
        sun.cpu.isalist =
        sun.desktop = gnome
        sun.io.unicode.encoding = UnicodeLittle
        sun.java.command = com.intellij.rt.execution.application.AppMain com.intellij.rt.execution.junit.JUnitStarter -ideVersion5 org.littlegrid.features.jar_exclusion.JarExclusionIntegrationTest,startAndShutdownWithCoherenceJarBeingExcluded
        sun.java.launcher = SUN_STANDARD
        sun.jnu.encoding = UTF-8
        sun.management.compiler = HotSpot 64-Bit Tiered Compilers
        sun.os.patch.level = unknown
        user.country = GB
        user.dir = /home/jhall/work/littlegrid/littlegrid-coherence-testsupport
        user.home = /home/jhall
        user.language = en
        user.name = jhall
        user.timezone = Europe/London
    ----------
    Builder keys and values - sorted to help identification
        CacheConfiguration = coherence/littlegrid-test-cache-config.xml
        CallbackHandlerInstanceClassName = org.littlegrid.impl.DefaultCallbackHandler
        ClientCacheConfiguration =
        ClientOverrideConfiguration =
        ClusterMemberInstanceClassName = org.littlegrid.impl.DefaultClusterMember
        ClusterName = littlegridCluster
        CoreJarsToExcludeFromClassPath = alt-rt.jar,charsets.jar,deploy.jar,javaws.jar,jce.jar,jfr.jar,jsse.jar,management-agent.jar,plugin.jar,resources.jar,rt.jar,dnsns.jar,localedata.jar,sunec.jar,sunjce_provider.jar,sunpkcs11.jar,zipfs.jar,classes.jar,ui.jar
        CustomConfiguredCacheConfiguration =
        CustomConfiguredClusterMemberInstanceClassName = org.littlegrid.impl.DefaultClusterMember
        CustomConfiguredCount = 0
        CustomConfiguredRoleName = CustomConfiguredMember
        ExceptionReporterInstanceClassName = org.littlegrid.impl.DefaultBuildExceptionReporter
        ExtendClientRoleName = ExtendClient
        ExtendPort = 23000
        ExtendProxyCount = 0
        ExtendProxyRoleName = DedicatedExtendProxyMember
        FastStartJoinTimeoutMilliseconds = 1000
        JarsToExcludeFromClassPath = ,, , , coherence ,,
        JmxMonitorCount = 0
        JmxMonitorRoleName = DedicatedJmxMonitorMember
        LogDestination = stdout
        LogLevel = 3
        MachineName = DefaultMachine
        NumberOfThreadsInStartUpPool = 4
        OverrideConfiguration = littlegrid/littlegrid-fast-start-coherence-override.xml
        RackName = DefaultRack
        SiteName = DefaultSite
        StorageDisabledClientRoleName = StorageDisabledClient
        StorageEnabledCount = 2
        StorageEnabledExtendProxyCount = 0
        StorageEnabledExtendProxyRoleName = StorageEnabledExtendProxyMember
        StorageEnabledRoleName = DedicatedStorageEnabledMember
        SuggestedSleepAfterStopDuration35x = 120
        SuggestedSleepAfterStopDuration36x = 3
        SuggestedSleepAfterStopDurationDefault = 3
        Ttl = 0
        WkaAddress = 127.0.0.1
        WkaPort = 22000
    ----------
    Builder key to system property name mapping - sorted to help identification
        CacheConfiguration = tangosol.coherence.cacheconfig
        ClientCacheConfiguration = tangosol.coherence.cacheconfig
        ClientOverrideConfiguration = tangosol.coherence.override
        ClusterName = tangosol.coherence.cluster
        CoherenceManagement = tangosol.coherence.management
        CoherenceManagementRemote = tangosol.coherence.management.remote
        CustomConfiguredCacheConfiguration = tangosol.coherence.cacheconfig
        CustomConfiguredRoleName = tangosol.coherence.role
        DistributedLocalStorage = tangosol.coherence.distributed.localstorage
        ExtendAddress = tangosol.coherence.extend.address
        ExtendClientRoleName = tangosol.coherence.role
        ExtendEnabled = tangosol.coherence.extend.enabled
        ExtendPort = tangosol.coherence.extend.port
        ExtendProxyRoleName = tangosol.coherence.role
        FastStartJoinTimeoutMilliseconds = littlegrid.join.timeout.milliseconds
        JmxMonitorRoleName = tangosol.coherence.role
        LocalAddress = tangosol.coherence.localhost
        LocalPort = tangosol.coherence.localport
        LogDestination = tangosol.coherence.log
        LogLevel = tangosol.coherence.log.level
        MachineName = tangosol.coherence.machine
        ManagementJmxRemote = com.sun.management.jmxremote
        OverrideConfiguration = tangosol.coherence.override
        RackName = tangosol.coherence.rack
        SiteName = tangosol.coherence.site
        StorageDisabledClientRoleName = tangosol.coherence.role
        StorageEnabledExtendProxyRoleName = tangosol.coherence.role
        StorageEnabledRoleName = tangosol.coherence.role
        TcmpEnabled = tangosol.coherence.tcmp.enabled
        Ttl = tangosol.coherence.ttl
        WkaAddress = tangosol.coherence.wka
        WkaPort = tangosol.coherence.wka.port
    ----------
    Full exception...........: detailed below
    CHECK_CHILD_FIRST_CLASS_PATH_IN_USE There were 2 member ids [1, 1] - however only these were unique member ids [1].  Ensure that the Coherence JAR is on your test class path
        at org.littlegrid.impl.DefaultClusterMemberGroup.ensureMemberIdsAreUnique(DefaultClusterMemberGroup.java:257)
        at org.littlegrid.impl.DefaultClusterMemberGroup.startClusterMembers(DefaultClusterMemberGroup.java:232)
        at org.littlegrid.impl.DefaultClusterMemberGroupBuilder.buildStorageEnabledMembers(DefaultClusterMemberGroupBuilder.java:574)
        at org.littlegrid.impl.DefaultClusterMemberGroupBuilder.buildClusterMembers(DefaultClusterMemberGroupBuilder.java:399)
        at org.littlegrid.impl.DefaultClusterMemberGroupBuilder.build(DefaultClusterMemberGroupBuilder.java:297)
        at org.littlegrid.impl.DefaultClusterMemberGroupBuilder.buildAndConfigureForStorageDisabledClient(DefaultClusterMemberGroupBuilder.java:320)
        at org.littlegrid.features.jar_exclusion.JarExclusionIntegrationTest.startAndShutdownWithCoherenceJarBeingExcluded(JarExclusionIntegrationTest.java:66)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:601)
        at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)
        at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)
        at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)
        at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:20)
        at org.junit.internal.runners.statements.ExpectException.evaluate(ExpectException.java:21)
        at org.junit.internal.runners.statements.RunAfters.evaluate(RunAfters.java:31)
        at org.junit.runners.BlockJUnit4ClassRunner.runNotIgnored(BlockJUnit4ClassRunner.java:79)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:71)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:49)
        at org.junit.runners.ParentRunner$3.run(ParentRunner.java:193)
        at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:52)
        at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:191)
        at org.junit.runners.ParentRunner.access$000(ParentRunner.java:42)
        at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:184)
        at org.junit.runners.ParentRunner.run(ParentRunner.java:236)
        at org.junit.runner.JUnitCore.run(JUnitCore.java:157)
        at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:76)
        at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:195)
        at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:63)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:601)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:120)
    ----------
    Possible exception reason: CHECK_CHILD_FIRST_CLASS_PATH_IN_USE There were 2 member ids [1, 1] - however only these were unique member ids [1].  Ensure that the Coherence JAR is on your test class path
