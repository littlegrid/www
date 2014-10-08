.. index::
   single: Reusable cluster member group; Improve build test time
   single: SimpleKeepAliveClusterMemberGroup; Improve build test time
   single: UsageCountingClusterMemberGroup; Improve build test time
   single: Improve test build time; Reusable cluster member group

.. _reusable-member-group:

Reusable cluster member group
=============================

Typically on a big project it is useful to start a cluster and then reuse it across the various
test classes or suites, littlegrid 2.15 introduced a 'productised' feature to easily keep an
established cluster up and running across test classes (previously there were some workarounds).


SimpleKeepAliveClusterMemberGroup
---------------------------------

The simple keep-alive cluster member group enables the cluster member group (essentially members
within a cluster) to be reused across test classes.  Once created and started, any requests
for the same member group return the original instance and when it is requested to be shutdown,
it simply ignores the request.

The simple keep-alive cluster member group is ideal for a testing classes within a module that
all require the same type of cluster across all the tests.  The member group is effectively cleared
up when the test process ends.

.. code-block:: java

    memberGroup = ClusterMemberGroupUtils.newBuilder()
        .setClusterMemberGroupInstanceClassName(SimpleKeepAliveClusterMemberGroup.class.getName())
        .setStorageEnabledCount(2)
        .buildAndConfigureForStorageDisabledClient();

    NamedCache cache = CacheFactory.getCache("test");

That's it, simple :ref:`builder-to-value-mapping` configure the littlegrid builder
ClusterMemberGroupInstanceClassName with the name of the SimpleKeepAliveClusterMemberGroup.


UsageCountingClusterMemberGroup
-------------------------------

The usage counting cluster member group is typically useful when test classes have been gathered
up into test suites, this technique is typically used on modules where different types of cluster
are required across different test classes within the module.  Many projects won't need the
usage counting member group and the simple keep-alive member group should be the starting point.

More content will be added soon.
