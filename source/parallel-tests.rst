.. index::
   single: Parallel tests; Faster

.. _parallel-tests:

Parallel tests
==============

The below shows a snippet of a POM, a proper description will be added in the future.  For 
now though, the below demonstrates how to configure Surefire and control the number of threads
and port numbers for those comfortable with Maven.

What?
-----


Why?
----

How?
----

.. code:: xml

   <build>
       <plugins>
           <plugin>
               <groupId>org.apache.maven.plugins</groupId>
               <artifactId>maven-surefire-plugin</artifactId>
               <version>${maven-surefire-plugin.version}</version>
               <configuration>
                   <forkCount>${fork.count}</forkCount>
                   <reuseForks>false</reuseForks>
                   <argLine>-Xmx768m -Xms768m -XX:MaxPermSize=384m</argLine>
                   <systemPropertyVariables>
                       <littlegrid.builder.WkaPort>${starting.thousand.port.number}${surefire.forkNumber}00
                       </littlegrid.builder.WkaPort>
                       <littlegrid.builder.ExtendPort>
                           ${starting.thousand.port.number}${surefire.forkNumber}50
                       </littlegrid.builder.ExtendPort>
                       <littlegrid.builder.ClusterName>littlegridCluster-${surefire.forkNumber}
                       </littlegrid.builder.ClusterName>
                   </systemPropertyVariables>
               </configuration>
           </plugin>
   ...
   
   
   
   <properties>
     <starting.thousand.port.number>40</starting.thousand.port.number>
     <fork.count>4</fork.count>
   
