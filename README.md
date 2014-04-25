Accismus
========

A [Percolator][2] prototype  for [Accumulo][1].  This prototype relies on the
Accumulo 1.6.0-SNAPSHOT which has [ACCUMULO-1000][3] and [ACCUMULO-112][5].
[ACCUMULO-1000][3] makes cross row transactions possible and  [ACCUMULO-112][5]
makes it possible to effeciently find notifications.  Theoretically this
prototype is to a point where it could run in a distributed manner.  But this
has not been tested.  The pieces are in place, CAS is done on the tablet server
and the Oracle is a service.  

There is a lot that needs to be done.  If you are interested in contributing
send me an email or check out the issues.

Building Accumulo 1.6.0 with CAS
--------------------------------

Before you can build Accismus, you will need to build Accumulo with CAS.  This
can be accomplished with the following steps.

```
git clone http://git-wip-us.apache.org/repos/asf/accumulo.git
cd accumulo
git checkout 1.6.0-SNAPSHOT
mvn clean compile install -DskipTests
```

Running these commands will leave 1.6.0-SNAPSHOT jars in your local maven repo.
You may want to delete these later.  The skipTest option was suggested because 
the integration test are currently in a state of flux in 1.6.0-SNAPSHOT.


Building Accismus
-----------------

After building Accumulo with CAS, you can build Accismus with the following steps.

```
git clone https://github.com/keith-turner/Accismus.git
cd Accismus
mvn package
```

Experimenting with Accismus
---------------------------

An easy way to experiment with Accismus is to edit a unit test and run it.  The
[WorkerTest][6] is probably a good place to start because it uses an Observer to
build a secondary index.  This can be done using the following commands.  

```
vim src/test/java/accismus/impl/WorkerTestIT.java
mvn verify -Dit.test=WorkerTestIT
```

Running Accismus
----------------

The following instructions command show a rough outline of how to get Accismus
installed and running on a single node.

You will need observers to run.  You can use the [jinex][7] examples observers.
[Jinex][7] is a probably a bit too complicated for a starting example, will
create something simpler soon.

```
OJAR=<location of observer jar>
OPT=/opt
mvn package assembly:assembly
tar -C $OPT -xvzf target/accismus-0.0.1-SNAPSHOT-bin.tar.gz
cd $OPT/accismus-0.0.1-SNAPSHOT
cp $OJAR lib/observers
cd conf
cp examples/* .
vim accismus-env.sh
vim initialization.properties
vim accismus.properties
cd ..
./bin/initialize.sh
./bin/oracle.sh start
./bin/worker.sh start
```

When finished, run the following commands to stop the oracle and worker.

```
./bin/worker.sh stop
./bin/oracle.sh stop
```

[1]: http://accumulo.apache.org
[2]: http://research.google.com/pubs/pub36726.html
[3]: https://issues.apache.org/jira/browse/ACCUMULO-1000
[5]: https://issues.apache.org/jira/browse/ACCUMULO-112
[6]: src/test/java/accismus/impl/WorkerTestIT.java
[7]: https://github.com/keith-turner/jinex

