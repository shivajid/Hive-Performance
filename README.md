Hive-Performance
================

Your recommendations are on mark.  I had started working on a list as well
Use the latest version of Hive
Use ORC as the storage file format
Use Tez as the execution engine 
Using Ambari set hive.execution.engine=tez
Enable vectorizaton and cost based optimization
Using Ambari set hive.vectorized.execution.enabled=true
Using Ambari set hive.stats.autogather=true
Partition  your data
Partition along natural query boundary – Example Date
Do not go overboard with partitions.  1000 partitions are better than 10,000
Limit data movement between BI and HDP.  
Pre-Compute aggregates where appropriate/feasible
As an example roll up per second observations to per-minute observations.  Keep all source data to enable re-compute as needed.
Enable Tez and Tez session pooling in HS2
This requires pre-emption turned on and queues setup in the capacity scheduler. Follow this guide for details. What I've been doing is setup a "hiveserver" queue containing 4 "hive1" through "hive4” queues
Turn on YARN pre-emption
Turn on ORC as the default file format. This will ensure that any CTAS or CREATE TABLE are stored in ORC
HIVE: hive.default.fileformat=ORC
Note that this will break DDL statements that made the assumption that TEXT was the default. Any table that wants to define a text table will have to issue STORED AS TEXTFILE in the DDL
Note last suggestion above implies that tables backed by text files will need to explicitly specify "stored as textfile”.    Below is list of settings for best experience with hive.

Let’s catch next week/after Thanksgiving to follow on this.

Thanks
Ajay 

All settings
Copy paste the following into Ambari. You will need to overwrite the existing setting if it exists or create a custom setting otherwise.

Yarn settings

yarn.resourcemanager.scheduler.monitor.enable=true

yarn.resourcemanager.scheduler.monitor.policies=org.apache.hadoop.yarn.server.resourcemanager.monitor.capacity.ProportionalCapacityPreemptionPolicy

yarn.resourcemanager.monitor.capacity.preemption.monitoring_interval=1000

yarn.resourcemanager.monitor.capacity.preemption.max_wait_before_kill=5000

yarn.resourcemanager.monitor.capacity.preemption.total_preemption_per_round=0.4

Yarn Capacity Scheduler settings

yarn.scheduler.capacity.root.default.capacity=50

yarn.scheduler.capacity.root.hiveserver.capacity=50

yarn.scheduler.capacity.root.hiveserver.hive1.capacity=50

yarn.scheduler.capacity.root.hiveserver.hive1.user-limit-factor=4

yarn.scheduler.capacity.root.hiveserver.hive2.capacity=50

yarn.scheduler.capacity.root.hiveserver.hive2.user-limit-factor=4

yarn.scheduler.capacity.root.hiveserver.queues=hive1,hive2

yarn.scheduler.capacity.root.queues=default,hiveserver

Tez settings

tez.am.container.reuse.enabled=true

tez.am.container.session.delay-allocation-millis=900000

tez.session.am.dag.submit.timeout.secs=900

Hive settings

hive.default.fileformat=orc

hive.execution.engine=tez

hive.server2.tez.initialize.default.sessions=true

hive.server2.tez.default.queues=hive1,hive2

hive.server2.tez.sessions.per.default.queue=1

hive.server2.enable.doAs=false

hive.vectorized.groupby.maxentries=10240

hive.vectorized.groupby.flush.percent=0.1

hive.support.concurrency=true

hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager

hive.compactor.initiator.on=true

hive.compactor.worker.threads=2

hive.enforce.bucketing=true

hive.exec.dynamic.partition.mode=nonstrict


