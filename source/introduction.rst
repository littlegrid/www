.. _introduction:

Introduction
============

littlegrid is a **small, simple and pragmatic open source framework** that lets you run an
entire Oracle Coherence cluster in a **single JVM** for performing developer focused integration
tests (often referred to as functional tests).

.. note:: The littlegrid website is regularly visited by many people from over 70 different countries across the world - lots of people return to find out more, are you interested to know more?


If you answer 'YES' to any of the below questions, then littlegrid can help you out:

* Do you want to quickly, easily and effectively test the functionality of your Coherence code in a small cluster?  So, do you want to easily test your entry processor/cache store/backing map listeners/interceptors etc. :ref:`YES - show me now! <getting-started>`

.. warning:: Did you know if you run tests with a single cluster member then you aren't testing properly!

* Do you want to easily and effectively test with an Extend client, Extend proxy server(s) and cache server(s) in literally seconds and have them shutdown all at once when the test stops? :ref:`YES - sounds good! <using-extend-proxy>`

* Do you want to easily automate your integration or functional tests with your favourite test framework (e.g. JUnit) and run all the tests on a Continuous Integration (CI) server? :ref:`YES - very cool! <continuous-integration>`

* Do you want to test what happens if a cluster member fails over or leaves the cluster? :ref:`YES - that could be useful! <failover-testing>`


With littlegrid and just a few lines of code, you can quickly and easily try out
fully-clustered ideas in your favourite IDE or automate your Coherence integration
tests and have them running on your CI (Continuous Integration) server.

littlegrid was developed to facilitate Test Driven Development (TDD) with Coherence
and enable integration/functional tests to be easily and reliably run in Continuous
Integration (CI) environments.

littlegrid has steadily evolved over a period of time, based upon continuous use on complex
projects, along with valuable feedback from in-house Coherence SME
(Subject Matter Experts) and developers who are actively using littlegrid on their
projects.

littlegrid runs and is used actively used across most of the common platforms: Windows,
Mac OSX, Linux and Solaris.

.. note:: Unit, functional/integration tests with littlegrid have been run on CI servers and locally many, many thousands of times without causing any hangs or problems.
