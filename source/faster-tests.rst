.. index::
   single: Faster; Fast-start option
   single: Faster; Reusable cluster member group

.. _faster-tests:

Making tests faster
===================

Coherence is pretty fast at starting up and shutting down when you compare it to other software like a
database or middleware, such as an application server - however, whilst it is fast and littlegrid helps
start up an entire small cluster quickly, it gets a bit tedious if you want to
**practice Test Driven Development (TDD)** or have lots of automated integration tests that you run
locally or on your :ref:`continuous-integration` server.

What?
-----
Ways of making your tests start faster, how to avoid the repeated start-up and shutdown cluster time
when running lots of test classes or a suite of tests and how to run tests in parallel.

Why?
----
Faster test start-up and faster execution of all your tests makes practicing TDD and CI easier and
nicer - besides, no-one likes waiting if there is a faster way!

How?
----
littlegrid has a number of ways of making things faster, these are:

* A way of establishing your cluster faster, shaving a few seconds off the initial cluster start-up - this is called the fast-start join timeout :ref:`fast-start-join-timeout`.

* A way of reusing an established cluster across many test classes or a suite of tests :ref:`reusable-member-group`.

* A way of running tests in parallel :ref:`parallel-tests`.
