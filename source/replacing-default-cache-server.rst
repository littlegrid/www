.. index::
   single: DefaultCacheServer; Replacing with your own class
   single: Spring; Bootstrap application context before Coherence
   single: ReflectionDelegatingClusterMember; Example

.. _replacing-default-cache-server:

Replacing DefaultCacheServer
============================

What?
-----
Coherence provides a class called the DefaultCacheServer (DCS), the DCS is a great way to start a Coherence
cluster member such as storage-enabled member (cache server), an Extend proxy server or JMX monitor node.  Once
started, the cluster member will be active and perform its designated role until shutdown.

..however, sometimes you might need to use your own cluster member starter class.


Why?
----
The DefaultCacheServer is fine for many projects - and this is what littlegrid uses out of the box, however
there are also many projects that need to perform some *sort of initialisation* of a cluster member before
the cluster member starts up, examples might be if the Spring application context should be bootstrapped first
before Coherence starts up.


How? (if you already have your own replacement class to DCS)
------------------------------------------------------------
littlegrid provides a ) feature called the 'Reflection Delegating Cluster Member', this enables you to use
your own DCS replacement within littlegrid typically with little or no changes to your DCS replacement class.

So, let's look at how to use the reflection delegating feature:




How? (if fine-grained control is just required within tests)
------------------------------------------------------------
