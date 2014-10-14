Management REPL application
===========================

The management REPL application is a new concept that is under development in littlegrid 2.16-SNAPSHOT and
is therefore subject to change or possible removal.

If you do want to play with the management REPL app, then it can be started using a command similar to below
(changing the UrlPath to match the connection string to your JMX monitor node):

.. code-block:: bash

    java -Dlittlegrid.management.builder.UrlPath="service:jmx:rmi:///jndi/rmi://localhost:50003/jmxrmi" -cp coherence.jar;littlegrid-2.16-SNAPSHOT.jar org.littlegrid.app.ManagementReplApp

Once the console has started you can then invoke the following commands (it is envisaged that the commands
available will be expanded in the future based upon feedback):

* select columns from MBean where column = someValue

.. code-block:: bash

    select min(get('PacketsSent')), max(get('PacketsSent')), sum(get('PacketsSent')) from 'Coherence:type=Node,nodeId=*'


Resulting in (output to be tweaked/improved):

.. code-block:: bash

    {DoubleMin(.get(PacketsSent))=1922.0, DoubleMax(.get(PacketsSent))=99404.0, DoubleSum(.get(PacketsSent))=113064.0}


.. code-block:: bash

    select get('nodeId'), get('service'), get('name'), get('Size') from 'Coherence:type=Cache,service=*,name=*,nodeId=*,tier=back' where get('StoreFailures') > 0L

    select get('Units'), count() from 'Coherence:type=Cache,service=DistributedCache,name=test,nodeId=*,tier=back' group by get('Units')



* create snapshot MySnapshot MBean

.. code-block:: bash

    create snapshot node 'Coherence:type=Node,nodeId=*'


A snapshot is used instead of specifying the MBean:

.. code-block:: bash

    select get('nodeId'), get('PacketsSent') from node


* show snapshots

.. code-block:: bash

    show snapshots


* drop snapshot MySnapshot

.. code-block:: bash

    drop snapshot node
