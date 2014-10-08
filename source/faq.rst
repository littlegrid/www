.. index::
   single: Perm-space problems

.. _faq:

FAQ
===

Got a question or having a problem that isn't answered below, then please feel free to
contact me: ``help at littlegrid dot org``

* Q) How does littlegrid work?

    * A) littlegrid loads each Coherence cluster member into its own 'child-first' class-loader using the settings that littlegrid has defined for cluster ports and names, this ensures that you don't have any accidental clustering.

.. note:: littlegrid does rely on using Coherence system properties to control certain aspects of Coherence's behaviour, such as the Extend proxy address and port, along with others.


* Q) How many cluster members can I run in a single JVM?

    * A) Quite a few!  On an average laptop/desktop, it is perfectly easy to run 30 or more cluster members in a single JVM by setting the memory settings appropriately.


* Q) I can't seem to start my cluster with littlegrid.

    * A) In the event a problem starting up your cluster, then littlegrid will produce an 'exception report' :ref:`sample-exception-report` which details the class path, system properties and other useful information to help diagnose the problem.  If you're really stuck, then just email me the exception report and I'll take a look :-)


* Q) I'm getting perm-space problems.

    * A) This is because each Coherence cluster member gets loaded into their own class-loader, this causes a lot of classes to loaded, with the Sun JVM, these classes reside in the perm-space.  To accommodate the extra classes, simply include the following as it will be fine for most cases:

        ``-XX:MaxPermSize=256m``


* Q) I'm using Ant and it looks like it doesn't load some littlegrid classes - I'm getting ClassNotFoundExceptions.

    * A) When using Ant, please set JUnit to fork:

        .. code-block:: xml

            <junit fork="yes">
                ...
            </junit>


* Q) I'm using Maven and getting perm-space problems.

    * A) With Maven, you need to configure the Surefire plugin, take a look at the :ref:`maven-best-practice`


* Q) I'm seeing java.lang.SecurityException: Prohibited package name: java.lang- why is that?

    * A) Please upgrade to version 2.12 (or later) - older versions of littlegrid used your JAVA_HOME environment variable to work out which core Java JAR files (such as rt.jar) should be excluded from its child-first class loader - with 2.12 the requirement for JAVA_HOME to match your IDE is no longer required.


* Q) I'm seeing an exception message with Error instantiating Filter with name: gzip - why is that?

    * A) When used with littlegrid, Coherence 3.7.0.x onwards gives this exception if none of your cache services are marked as autostart.  As of version 2.13, littlegrid attempts to identify this exception and report it as requiring a service to be auto-started.

        ``<autostart>true</autostart>``
