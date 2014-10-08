.. index::
   single: Fast-start join timeout; Faster

.. _fast-start-join-timeout:

Fast-start join timeout
=======================

What?
-----
The fast-start join timeout feature enables Coherence to establish a new cluster more quickly.  By default
in ``dev`` mode, Coherence will wait 3 seconds before establishing a new cluster (if no existing cluster
is up and running).  The ``prod`` mode setting by default will wait 30 seconds.


Why?
----
Getting the cluster up and running faster makes iterative development easier, helping facilitate
**Test Driven Development (TDD)**.  This option can help establish a cluster *ready for running tests*
against in approximately 3 seconds (depending upon your machine) - remember this is a running cluster,
Coherence is pretty quick starting when you reduce its join-timeout via the fast-start option.

.. note:: Newer versions of Coherence can use a small time of typically something 100ms, whilst earlier versions of Coherence can only accept a minimum fast-start time of 1000ms.


How? (when you don't have your own Coherence override file)
-----------------------------------------------------------
If you don't have a Coherence override file (typically called something like ``tangosol-coherence-override.xml``),
then you can simply use the builder API or one of the other mechanisms for configuring littlegrid,
see :ref:`builder-to-value-mapping`

.. code-block:: java

    memberGroup = ClusterMemberGroupUtils.newBuilder()
            .setStorageEnabledCount(1)
            .setCacheConfiguration("simple-cache-config.xml")
            .setFastStartJoinTimeoutMilliseconds(100) // Note: older versions of Coherence need 1000
            .buildAndConfigureForStorageDisabledClient();


Without specifying your own Coherence override file, then littlegrid will use its own in-built override file
called ``littlegrid-fast-start-coherence-override.xml``, it looks like the below:

.. code-block:: xml

    <coherence>
        <cluster-config>
            <multicast-listener>
                <join-timeout-milliseconds system-property="littlegrid.join.timeout.milliseconds">1500</join-timeout-milliseconds>
            </multicast-listener>
        </cluster-config>
    </coherence>


When littlegrid uses its own override file and when the fast-start option has been configured (via the Builder or
configuration), then you should see the below warning - this is logged to let you know that littlegrid thinks it is
controlling the time that Coherence waits before starting the cluster.


.. code-block:: bash

    WARNING: Fast-start join timeout specified.  Note: the fast-start Coherence override file will now be configured to be used



How? (if you have your own Coherence override file)
---------------------------------------------------
If you're using your own Coherence override file, then in order for the littlegrid fast-start option to work,
you will need to include the ``join-timeout-milliseconds`` XML element within your own override file - additionally
if you'd like to use your own system property name (e.g. jupiter.join.timeout.milliseconds), then please refer to
:ref:`builder-to-system-property-mapping`.

.. code-block:: xml

    <coherence>
        <cluster-config>
            <multicast-listener>
                <join-timeout-milliseconds system-property="jupiter.join.timeout.milliseconds">1500</join-timeout-milliseconds>
            </multicast-listener>
        </cluster-config>
    </coherence>

