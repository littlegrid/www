.. index::
   single: Maven; Best practice
   single: Maven; Dependency
   single: Maven; Surefire plugin
   single: Surefire; Memory configuration

.. _maven-best-practice:

Maven best practice
===================

littlegrid is available from Maven central, simply add the following dependency
to your POM file - littlegrid should be set to **test** scope.

.. note:: littlegrid doesn't have any dependencies on other frameworks, it simply requires a version of Coherence to be available in your project.

.. code-block:: xml

    <dependency>
        <groupId>org.littlegrid</groupId>
        <artifactId>littlegrid</artifactId>
        <version>2.15.2</version>
        <scope>test</scope>
    </dependency>


littlegrid is used on a lot of Maven projects and so the below is a quick list of best practice to help you get going easily and quickly


1. Ensure littlegrid is set to *test* scope.

2. Create a separate Maven module for the functional/integration tests (basically those using littlegrid to start a Coherence cluster).  This module will typically have your tests for things like: entry processors, cache stores, triggers, backing map access etc.

    Typical names for this module might be something like: ``my-project-datagrid-cluster-integration-tests``

3. Configure Surefire to allocate more memory to your functional/integration tests and allow for class-reloading (littlegrid loads classes into separate class-loaders for each Coherence cluster members).

.. note:: this Surefire configuration is only required for your littlegrid functional/integration test module(s).

.. code-block:: xml

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${maven-surefire-plugin.version}</version>
                <configuration>
                    <!--
                        Set the forkMode to 'once' if you have many tests and you intend
                        to use the reusable cluster member group functionality that
                        littlegrid can provide.
                    -->
                    <forkMode>once</forkMode>

                    <!--
                        Set the forkMode to 'always' if you're just getting started -
                        after you've got littlegrid embedded into your build, then look
                        to change this to 'once' and also use the littlegrid
                        reusable cluster member group functionality.
                    -->
                    <!-- <forkMode>always</forkMode> -->
                    <argLine>-Xmx768m -Xms768m -XX:MaxPermSize=384m</argLine>
                </configuration>
            </plugin>

            ...
        </plugins>
    </build>

4. Use a common way to start and shutdown littlegrid, typically littlegrid and your Coherence cluster controlled by littlegrid are started within something like JUnit's ``@BeforeClass`` and typically shutdown in an ``@AfterClass``

.. note:: Often a base class is used which performs the starting up and shutting down of littlegrid, typical examples might be a base class for storage-disabled client tests using one or two storage-enabled cache servers.  Other examples are an Extend client, connecting to an Extend proxy and one or more cache servers.

5. Most projects can utilise littlegrid's reusable cluster member group - this basically tells littlegrid to try and use the same established cluster for all your tests, this :ref:`reusable-member-group` avoids the delay of starting up and shutting down the Coherence cluster members for each test class

6. If your project also uses Extend, then it is recommended that a separate Maven module is introduced just for the Extend tests.

    Typical names for this module might be something like: ``my-project-datagrid-remote-client-integration-tests``

Again, configure Surefire for this module to use specific memory settings (see above) and also
to use the reusable cluster member group.

So, here's how the project might look:

``my-project-datagrid-cluster``

    Actual code for entry processors, cache stores etc.  Any standard unit tests (i.e. not integration tests).


``my-project-datagrid-cluster-integration-tests``

    littlegrid integration tests for entry processors, cache stores etc. typically as storage-disabled client and using one or more storage-enabled members.


``my-project-datagrid-remote-client``

    Actual code for Extend remote client, perhaps Spring configuration/wrapper around Coherence etc.


``my-project-datagrid-remote-client-integration-tests``

    littlegrid integration tests connecting as an Extend client with one or more storage-enabled members and one or more Extend proxies.
