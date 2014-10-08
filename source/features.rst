.. _features:

Features
========

Here's a brief overview of some of littlegrid's features.

* Start, stop and shutdown cluster members.
* Runs in a single JVM.
* Easy way to automate Coherence integration tests.
* Simple, compact and fluent API.
* Test framework agnostic (JUnit/TestNG) – can even be used in standalone program.
* No third party dependencies - just Coherence is required (3.5 - 12.1.3 are supported).
* Run using an IDE, Maven, Ant, Gradle etc. – Continuous Integration (CI) friendly.
* Open source (BSD).
* Fast-start option - helps start the cluster even faster.
* Re-use an existing cluster - helps tests run faster by reducing the number of cluster start-ups and shutdowns.
* Run tests in parallel using multiple forked clusters using a parallel test runner like Surefire.
* Override configuration using system properties or environment variables - useful if you need to precisely control which ports are used for different build plans on your CI (Continuous Integration) server.
* Access the child-first class loader containing the Coherence cluster member - this enables you to control or load classes and objects directly within any of the class loaders running Coherence members.
* Control the class-path used by the Coherence cluster members or easily exclude JARs from the default class-path.
* Exception reporter - produces a report-style output in the event of the cluster not starting, this helps with trouble-shooting the problem.
* Easily replace littlegrid's default cluster member if you need to provide hooks for your own life-cycle methods (before or after the cluster member starts and before or after it shuts down).
* Easily substitute your own system property names if you aren't using typical Coherence system property names.
