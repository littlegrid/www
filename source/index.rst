.. title:: Home

#########################################################
littlegrid - helping developers test their Coherence code
#########################################################

.. image:: _static/littlegrid-logo-black.png
    :scale: 15%

littlegrid is a **small** and **simple** open source framework for Oracle Coherence that
lets you run multiple Coherence cluster members in a **single JVM**.

Testing in-process (using single JVM) is a **highly effective** way of developing your
Coherence related code because the cluster is **quick to start** and you can **debug locally**.

Development on littlegrid has been quiet over the last few years, but it's getting a refresh
and reboot to help Developer now adopting Coherence CE.

With just a few lines of littlegrid code you can quickly try out ideas in your **IDE**
or easily automate your Coherence tests with your favourite test framework (e.g. **JUnit**)
for running on your **Continuous Integration** (CI) build servers.

**Coherence + littlegrid = develop faster :-)**

Quick code snippet of two cache servers (storage-enabled servers) and then joining the
cluster as a storage-disabled client.

.. code-block:: java

    ClusterMemberGroup memberGroup = ClusterMemberGroupUtils.newBuilder()
        .setStorageEnabledCount(2)
        .buildAndConfigureForStorageDisabledClient(); // this bit is littlegrid

    NamedCache cache = CacheFactory.getCache("test");


Contents
--------

.. toctree::
    :maxdepth: 2

    list-of-examples
    documentation-updates
    introduction
    features
    who-is-using
    credits
    getting-littlegrid
    getting-started
    using-extend-proxy
    faster-tests
    fast-start-join-timeout
    no-coding-needed
    mini-cluster-for-extend-clients
    failover-testing
    multiple-autonomous-clusters
    maven-best-practice
    continuous-integration
    builder-to-value-mapping
    builder-to-system-property-mapping
    reusable-member-group
    parallel-tests
    sample-exception-report
    javadoc
    test-coverage
    faq
    support
    license
    release-notes
    management-repl
