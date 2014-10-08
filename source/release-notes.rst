.. index::
   single: Release notes

.. _release-notes:

Release notes
=============

**2013-07-14 Version 2.15.2**

* #135 Add workaround for 12.1.2.0.0 DefaultCacheServer.shutdown NPE when static start is used


**2013-05-14 Version 2.15.1**

* #126: Simple keep-alive cluster member group should always report current usage count higher than 1 to ensure it doesn't get shutdown


**2013-05-09 Version 2.15**

* #85: Enable integration tests requiring the same type of cluster member group to share the started group, such as via a holder
* #105 Add timeout milliseconds to identifiable exception
* #106: Provide an interactive console for stopping, shutting down and merging cluster members
* #108: Change Coherence GAV co-ordinates to be in-line with 12c
* #109: Create console package and move simple wait console into package
* #114: Add the ability to have a specific cache configuration file for storage enabled members
* #115: Add the ability to have a specific cache configuration file for Extend proxy members
* #116: Add the ability to have a specific cache configuration file for JMX monitor members
* #117: Add the ability to have a specific log level for Extend proxy, storage enabled members and clients (Extend and storage-disabled)
* #118: Combination of Windows and System.getenv in certain environments returns keys that are in uppercase - even through the environment variable is mixed case
* #119: Include ability to sleep until a particular time in the command DSL
* #121: Make logging more compact, i.e. fewer lines
* #122: Tidy up fast-start and builder interaction
* #125: Add ability to specify site, rack and machine within the command DSL shell


**2013-01-28 Version 2.14**

* #100: Expand the concept of a command-line launcher to accept a string which equates to a build enum, for clients launch a console
* #104: Add build method accepting an enum in order allow the cluster member group to be built
* #103: Add WKA and Extend port getters on cluster member group to enable API to be more fluent when dealing with multiple clusters
* #102: Add reminder(s) for people to check the littlegrid web-site for updated information and new versions


**2012-11-08 Version 2.13**

* #99: Add a simple launcher application to enable a littlegrid cluster to be started from the command-line or via .Net or similar
* #91: Add functionality to more easily control specified cluster members via the actual class loader
* #93: Change system environment variable prefix to littlegrid_builder_X and system property prefix to littlegrid.builder.X instead of littlegrid.builder.override.X. Keep original prefix working
* #46: Add support for client override to be set via builder and then applied before client starts
* #92: Add additional convenience methods for setting additional system properties, for instance accepting an integer and boolean as the value
* #98: When no caches have their service marked as auto-start and Coherence 3.7.0.x or later are used then exception occurs: Error instantiating Filter with name: gzip


**2012-09-23 Version 2.12**

* #90: Add classes.jar and ui.jar for core JAR exclusion


**2012-09-20 Version 2.11**

* #65: Add functionality to report possible reasons for a security exception when performing child-first class loading
* #83: Improve the way that littlegrid excludes core Java JAR files from its class-path used by the child-first class loader
* #86: Ensure that resources are loaded into child-first class loader if possible to ensure they are isolated
* #88: When an exception occurs loading a class with the child first class loader, output the name of the class that it was trying to load
* #89: Exclude any classes with "java." from child-first class loading


**2012-08-21 Version 2.10**

* #80: Child first URL class loader's loadClass is susceptible to a race condition in certain use cases


**2012-08-08 Version 2.9**

* #81: If a security exception occurs then throw a meaningful exception to help user to identify and fix the problem
* #80: Child first URL class loader's loadClass is susceptible to a race condition in certain use cases


**2012-05-04 Version 2.8**

* #73: Add warning message when trying to perform a stop or shutdown on a non-existent member
* #74: Add support for setting a system property easily via the builder
* #75: Improve JMX test example
* #76: Add support for site, rack and machine within builder


**2012-04-13 Version 2.7**

* #7: Include simple contains check for JAR exclusion.
* #52: Bake in the littlegrid version number and display on start-up.
* #66: Expose the functionality to allow merging of cluster member groups.
* #67: For Maven builds use surefire.test.class.path instead of java.class.path.
* #68: Add support for overriding properties using environment variables and system properties.
* #70: Add getter for WKA address.
* #71: Change default WKA and Extend port numbers to increase 'gap' between them.


**2012-03-26 Version 2.6.1**

* #51: In the event of an exception on start-up, output the builder keys and values in the exception report.
* #55: Remove logger place holder - just use Java util logging for now.
* #56: Reduce log level when reading default properties files.
* #58: Make the Extend-related invocables used in test pack public for other people to use.
* #59: Change setBuilderProperties to accept multiple properties files and multiple names of properties file.
* #60: Remove explicit WKA port numbers from builder setting test.
* #62: Add concept of post cluster member group start support.
* #63: Add builder for storage-enabled member.
* #64: Change invocation of doAfterStart of cluster member group, so that is occurs after the 'client' environment has been configured.


**2012-02-28 Version 2.5.1**

* #4: Enable mapping of Builder bean setter names to different system properties.
* #44: Implement basic (sequential) shutdown for multiple members.
* #45: Implement basic (sequential) stop for multiple members.
* #47: Change default behaviour so that fast-start is disabled by default.
* #48: Add support for custom-configured to specify their own cache configuration within a cluster member group.
* #49: Remove the defaulting to 1 storage-enabled member if no specific member types are set - i.e. make it more explicit and require to be set.
* #50: Add specific build methods so that it is clearer to a user what the 'environment' is being set as ready for their tests. Examples being storage-disabled client or Extend client.


**2012-02-19 Version 2.4.8**

* #40: Make project available via Nexus OSS


**2011-02-06 Version 2.2.3**

* #1: Enable overriding builder properties using environment variable and properties file.
* #3: Add mapping from Builder bean setter names to Coherence system properties.
* #5: Add JMX support.
* #6: Tidy up builder.
* #8: Look at why Coherence 3.5 reflection updater doesn't work with integers (and so need to use strings).
* #9: Add checks for incoming parameters for DefaultLocalProcessClusterMemberGroup constructor.
* #11: Add support for more than one than one Extend proxy in a cluster member group containing dedicated Extend proxies.
* #12: Add support for more than one than one Extend proxy in a cluster member group containing storage enabled Extend proxies.
* #13: Fix startAndShutdownWithKnownRequiredJarBeingExcluded test so that it doesn't use multicast (it does so because member group is shutdown and resets the system properties).
* #15: Improve builder setBuilderProperties to take a string for a properties filename, this will avoid the API user having to worry about getResourceAsStream etc.
* #16: Add getExtendPort method to builder to allow this to be used as the starting port for people to offset from it.
* #18: Move code over to common resource loading for properties be it from the filesystem or class path.
* #19: Add license to all source code.
* #21: Change system property for LITTLEGRID_COHERENCE_OVERRIDE to use dots instead of hyphens.
* #23: Add tests for explicit setting of override file.
* #24: Reduce the size of the 'public' API - ensure minimum and primarily interfaces.
* #26: On builder, provide method to add system properties.
* #27: Add test for Extend proxy stop.
* #28: DefaultClusterMemberGroupBuilder.setClusterName() unimplemented.
* #29: Reflection based cluster member with fallback to default behaviour.
* #32: Before starting to build the cluster group output exactly what the final group will contain.
* #33: Add 'fast-start' override file that reduces delay time before creating cluster.
* #34: Add build incident reporter.
* #35: Add support for the properties utils to log at a particular level.
* #36: Add builder style support to reflection delegating cluster member.
* #37: Improve JavaDoc for usage of stop and sleep.
* #38: JMX monitor member doesn't start-up when an Extend proxy is also used - system property not cleared causing problem.
* #41: Reset system properties after cluster is up.


**2011-11-13 Version 2.0.2**

**2011-11-02 Version 1.13**

**2011-08-03 Version 1.12**

**2011-07-27 Version 1.11**

**2011-06-28 Version 1.0.6**

**2011-05-30 Version 1.0.5**

**2011-05-25 Version 1.0.4**

**2011-04-06 Version 1.0.3**

**2011-02-22 Version 1.0.1**

**2011-01-24 Version 1.0**

**2010-10 Version 0.1**
* Development of tumbleweed started - this was the original name for littlegrid.
