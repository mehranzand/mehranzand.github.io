---
layout: post
title:  "Transactional Replication in Sql Server"
subtitle: "Performance Tips"
date: 2021-08-25 00:00:00 +000
categories: database
tags: sqlserver replication performance tips logreader agent
comments: true
author: "Mehran Zand"
meta: "sqlserver replication performance transactional logreader"
---
As we know by now the SQL Server offers us [several different] ways to replicate data, transactional replication great for highly transactional data, low latency and can be implemented at the object level of the database. 

Performance tuning SQL Server transactional replication is one area where most of us will agree that we have less working knowledge .This is because of its complex nature. There are more than one server involved with many agent jobs and metadata tables that makes it complex.

In this blog post, I am going to discuss about a few important `Log Reader Agent` profile settings (in Transactional Replication) that we can dare to change but with with care. This will definitely increase the replication throughput to a great extent. It is an experience that I want to share with you in this blog post.

### Log Reader Agent Tweaks

**LogScanThreshold** The default value is 500,000 which mean that the `Log Reader` will scan these many records in the transaction log that are marked for replication. `Log Reader Agent` does this every 5 seconds (by default polling interval is set to 5 seconds). So even if the rate of records marked for replication is 100000/sec, this setting should be fine. Sometimes you might notice that the `Log Reader` will scan through 1,000,000 records even if the default is set to 500,000. This is because of the overlapping of the time it takes to scan and the next scan time. I would personally feel that no changes should be needed. If you increase it, it will have a direct impact in the delivery of the transactions to the distribution database. `Log Reader` will have to spend more time in scanning the log in one go. So in my view this setting is already fine tuned. I have also seen that on a fast system it takes the `Log Reader` around 5-7 seconds to scan this many records which is a considerable time.

**QueryTimeOut** How long `Agent Log Reader` waits with 0 data movement and 0 ping back from SQL Server engine. Used to monitor timeout of new data from SQL Server publisher and timeouts while writing to the distribution database. Under normal data flow change should not be needed. If you hit a timeout, investigate, don’t just increase and wait longer without first investigating the *why* `Agent Log Reader` had to wait. Most likely a blocking and\or storage performance problems preventing data movement.

**MaxCmdsInTran** I would recommend first changing your application to perform data changes in smaller batches, however, if you need `Log Reader` to breakup large transactions into smaller batches, adjust this setting high enough to prevent flood of tiny transactions. For example, if you `Log Reader` is moving data around 5,000 commands per second and your batches are touching 5,000,000 rows try setting at 5,000 (the commands per second rate). At this setting the `Log Reader` will commit a batch of 5,000 commands every 1 second for next 20 minutes. If you didn’t break up the transaction, `Log Reader` will report “no response in the last 10 minutes” messages during the 20-minute large transaction transfer leading you to incorrectly believe the `Log Reader` is *hung* and restart `Log Reader`.
If you’re running fast drives perhaps your `Log Reader` is running at 20,000 or even 50,000 commands per second, then bump up the value even higher archiving about 1 batch per second.

**ReadBatchSize** This is the number of transactions to be taken in one scoop (once the `Log Reader` finds them during the scanning). The default is 500 which means that not more than 500 transactions will be taken per cycle (i.e. one batch). It can take a bit less then that though. For fast OLTP systems where there are multiple small increments happening all the time, this value might not be sufficient. Lets say each transaction has around 7-10 commands in it. With this rate only around 3500-5000 commands will be delivered in one batch.
For systems processing +5,000 transaction per second, the default batch size 500 is committing batch every 1/10th second. To reduce overhead associated with “commit” try increasing **ReadBatchSize** to 5,000, then monitor `Log Reader` performance to achieve balance.

**ReadBatchThreshold** This is same as **ReadBatchSize** parameter with a difference that this value is for the number of commands being read from the transaction log (per transaction) in other words **ReadBatchSize** is transactions cap and **ReadBatchThreshold** is the commands cap. The default is 0.
I would always recommend making modifications here first rather than in the **ReadbatchSize** parameter. This is a less impactful change and hence will do less harm in case you go wrong. Increase this value by a 100 and note down the impact. Normally, I start off with 500 and then increase it by 100.


### In conclusion
I think to repeat that do small increments and notice the impact rather than jumping to a bigger number straight away. All the changes to the profile parameters need the agents to be restarted.

[several different]: https://docs.microsoft.com/en-us/sql/relational-databases/replication/types-of-replication?view=sql-server-ver15
