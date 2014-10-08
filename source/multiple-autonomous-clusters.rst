.. index::
   single: Multiple separate clusters
   single: WKA port; Example using multiple clusters

.. _multiple-autonomous-clusters:

Multiple autonomous clusters
============================

If you haven't already had a look, the :ref:`getting-started` is the best place to start looking at littlegrid.

The below code example show how to run two autonomous clusters within the same JVM using littlegrid.

.. code-block:: java

    import com.tangosol.net.CacheFactory;
    import org.junit.Test;
    import org.littlegrid.ClusterMemberGroup;
    import org.littlegrid.ClusterMemberGroupUtils;

    import static org.junit.Assert.assertEquals;

    public class AutonomousClusterIntegrationTest {
        @Test
        public void twoAutonomousClusters() {
            final int numberOfMembers = 2;
            final int expectedClusterSize = numberOfMembers + 1; // Include this test which will join as a member

            ClusterMemberGroup memberGroupCluster1 = null;
            ClusterMemberGroup memberGroupCluster2 = null;

            try {
                final ClusterMemberGroup.Builder builder = ClusterMemberGroupUtils.newBuilder()
                        .setStorageEnabledCount(numberOfMembers);

                // Build the first cluster (we won't actually be connecting to this one)
                memberGroupCluster1 = builder.buildAndConfigureForNoClient();

                // The second cluster will need to run on a different port to avoid clustering with
                // the first cluster
                final int member2WkaPort = builder.getWkaPort() + 100;

                // Build the second cluster - we will join this cluster through this test by
                // asserting the cluster size
                memberGroupCluster2 = ClusterMemberGroupUtils.newBuilder()
                        .setStorageEnabledCount(numberOfMembers)
                        .setWkaPort(member2WkaPort)
                        .buildAndConfigureForStorageDisabledClient();

                // Check the size of the second cluster
                assertEquals(expectedClusterSize, CacheFactory.ensureCluster().getMemberSet().size());
            } finally {
                ClusterMemberGroupUtils.shutdownCacheFactoryThenClusterMemberGroups(
                        memberGroupCluster1, memberGroupCluster2);
            }
        }
    }
