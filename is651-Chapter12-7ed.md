   
## Chapter 12 - Microservices Revisited

### Introduction

We continue to use this [introduction to microservices](https://www.nginx.com/blog/introduction-to-microservices) from [nginx](https://www.nginx.com/), sections 2-3 (cached pdf [here](../Microservices_Designing_Deploying.pdf)). Many microservices use binary protocols rather than the text-based ones we have studied in this course. See [Beyond REST](https://dzone.com/articles/beyond-rest) for an introduction to these protocols which we will discuss and demo in class.  Distributed databases are a good example of distributed systems in the wild and we will do a short introduction to these and see how they implement the concepts we have discussed in this course.

### Distributed Databases

There has been a recent revolution in databases associated with the rise of distributed systems and especially the web.   Many of these database systems are called post-relational because since the start of market-dominance of relational database management systems (rdbms), they have been virtually the only type of database used.  This recent revolution happened due to the following problems for rdbms:

1. The poor performance of traditional rdbms especially for joins.
2. The [impedance mismatch](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) of the rdbms representation.
3. The failure of traditional rdbms to work in a widely distributed system environment.
4. Microservices should be loosely coupled and this results in a [database-per-service](http://microservices.io/patterns/data/database-per-service.html) architecture.  Previously, relational databases were considered to be a prime candidate for integration, but this results in tight coupling.

It should be noted that rdbms is not going anywhere and remains the most common type of rdbms used for applications.  It is just that now, there are a lot more choices. We see the following types of dbms in use today (listed by their buzzword name):

- OldSQL - the traditional rdbms
- NoSQL - post-relational dbms developed largely for web applications
- Big Data systems - systems for storing massive amounts of data for batch analysis
- NewSQL - newest rdbms that have a new architecture to fix the problems of OldSQL

This chapter appears very short be be aware that much of the content is in external links.  In general, the testable, <em>important</em> links are annotated that way in the text and others (such as all the wikipedia or vendor links) are just for term definition reference and context.

To quote Michael Stonebraker: "Probably the most important thing that has happened in the DBMS landscape is the death of <em>one size fits all</em>. Until the early 2000's the traditional disk-based row-store architecture was omnipresent. In effect, the commercial vendors had a hammer and everything was a nail."  We discuss these changes below.

#### OldSQL

OldSQL includes most of the large rdbms companies today such as Oracle, IBM, and Microsoft.  They remain the most general purpose dbmss and still have the largest installed base.  They are not going anywhere in the near future.  They are most hobbled by the three problems listed above.  Their performance on online transaction processing (OLTP) is good but not very scalable, they can only store relations as tables, and joins do not work in widely distributed systems.  They are typically deployed as large single nodes or as tight LAN clusters and they cost a large amount of money to increase scalability.  They are completely general purpose in that they do OK at all types of dbms tasks and they are very good at ACID which is a very good thing.  They have been used for the last 30 years as the basis for almost all dbms use cases.  For example, even data warehouses were based on rdbms using a [star or snowflake schema](https://en.wikipedia.org/wiki/Star_schema) even though they denormalized the data and did no OLTP.

#### NoSQL

NoSQL grew out of the dissatisfaction of large web companies with OldSQL for widely distributed web applications.  Companies like Facebook, Twitter, Amazon, and Google developed and open-sourced their own dbmss to solve the three problems.  We will use an [external reading to get a brief introduction to NoSQL](https://www.thoughtworks.com/insights/blog/nosql-databases-overview) - this reading is <em>testable</em>.  That same information is in this [video introduction](https://www.youtube.com/watch?v=qI_g07C_Q5I) from Martin Fowler.  NoSQL gets performance and good distributed behaviour by limiting ACID in favor of BASE. Additionally, many NoSQL dbmss do not have standard query languages like SQL and one must write code (java, javascript, etc.) to query them.  Recall from chapter 8 when we studied XQuery, native XML databases were mentioned.  These are a type of NoSQL Document databases.

#### Big Data

Big Data systems like [Hadoop](https://en.wikipedia.org/wiki/Apache_Hadoop) and [Spark](https://en.wikipedia.org/wiki/Apache_Spark) are mostly outside the scope of our discussion here, but we want to know how they fit in to the post-relational environment.  They are batch-oriented systems for data analysis.  The rise of the web and distributed systems in general have massively increased the amount of data that organizations collect and wish to analyze.  This data is in  all types of semi- or un- structured formats.  So these systems really just define a specialized distributed file system ([HDFS](https://en.wikipedia.org/wiki/Apache_Hadoop#HDFS)) such as we have discussed in this course but with the ability to handle much larger files, for example, a file can be bigger that the disk on any node in the distributed system.  Then algorithms like [mapreduce](https://en.wikipedia.org/wiki/MapReduce) are used to process the files for analytic data sets.  These results are often stored in wide-column NoSQL databases (such as HBase) which have largely replaced using rdbms for data warehouses.

#### NewSQL

NoSQL was a reaction to the problems of OldSQL and did solve some problems.  But:

- OLTP remains a very big application area
- SQL is a great query lanaguage

So NewSQL is a bunch of startup dbms companies that retain ACID for OLTP and SQL for queries.  The major ways they do this is by changing the disk-based architecture of OldSQL to in-memory architectures.  They go some way towards making rdbms more distributable, but only so much can be done.  One cannot override the laws of physics so that widely distributed nodes can act like a single node (CAP Theorem).  For example, distributed joins are always going to be slow and difficult.  A good introduction to these issues are in this [video from Micheal Stonebreaker](https://www.usenix.org/conference/lisa11/newsql-vs-nosql-new-oltp), a pioneer of rdbms in general, MIT professor, and the head of one of the NewSQL startups [Voltdb](https://voltdb.com/).

#### SoWhat

This is a currenly active area that is rapidly changing. These are my take-aways from our readings/videos:

- All the models will survive as in Fowler's term polyglot persistence.
- OldSQL will survive as the most general purpose dbms. 
- NoSQL will survive for distributed web applications and documents.
- NewSQL will survive for distributed OLTP.

And these changes will probably happen:
 
- OldSQL companies will lose their grip on OLTP but remain as general purpose dbms and possibly change to NewSQL.
- NoSQL will move towards standard query languages that look a lot like SQL.
- NoSQL and NewSQL will work together for their niche use cases.
- NewSQL will take over performant and distributed OLTP.

A good video that brings a lot of these issues together is [SQL strikes back](http://www.infoq.com/presentations/sql-newsql-hybrid-fp) from InfoQ.  

#### Distributed Database Terms and Concepts

Because database volume has gone up and systems have become distributed, there have been major changes in database technology in recent years.  We will discuss specific database models in chapter 12.  We will need the following terminology for discussions about distributed databases.

<b>Horizontal Scaling</b> - Putting data/applications on many nodes (distributing) to increase performance and fault-tolerance.

<b>Vertical Scaling</b> - Getting a bigger server or cluster to increase performance (not distribution).

<b>Shared nothing</b> - Database organization that provides a method for scalability across independent servers, each with their own CPU, memory and disk.  It is associated with partioning and horizontal scaling.

<b>Shared everything</b> - Database organization provides a method for scalability by upgrading a single server or a tighly grouped cluster of servers that share storage such as the SANs we saw earlier. It is associated with clustering, sharding, and vertical scaling.

<b>Replication</b> - Copying an entire table or database onto multiple servers. Used for improving speed of access to reference records such as master data.  We have already seen this used in directory services and the master/slave pattern, for example.  This is important for improving performance and fault-tolerance. Used for horizontally scaling applications.

<b>Partitioning</b> - Splitting up a large monolithic database into multiple smaller databases based on data cohesion. Example - splitting a large ERP database into modular databases like accounts database, sales database, materials database etc. This improves performance and management by creating smaller databases and improves distribution by allowing databases to be located strategically.  This can be done with complete databases or tables as above or only with some columns of a relational database. This is also known as vertical partitioning but used for horizontally scaling applications. Horizontal partioning splits databases by row on different nodes in a distributed system. This is also called sharding.

<b>Clustering</b> - Using multiple application servers to access the same database on a LAN. SANs would be used for this. Used for vertically scaling applications.  This is primarily for performance and not distribution.

<b>Sharding</b> - Splitting up a large table of data horizontally i.e. row-wise. A table containing 100s of millions of rows may be split into multiple tables containing 1 million rows each. Each of the tables resulting from the split will be placed into a separate database/server. Sharding is done to spread load (distribution) and improve access speed (performance). Facebook/twitter tables fit into this category. This is also known as horizontal partitioning so used for horizontally scaling applications. 

#### Sharding Methods

Sharding is often done automatically based on key. One way to categorize sharding is algorithmic. Algorithmic sharding distributes data by its sharding function.

Consistent hashing is an algorithm for distributing data (sharding) on many nodes in a distributed system.  Hashing is a function that returns a <i>unique fixed length key</i> for any input.  It is common used in indexing, security/integrity, and in consistent hashing for creating keys for both data and nodes.

![Hashing](../651book-6ed/is651-images/500px-DHT_en.svg.png)

Figure 8.19.  Hash Functions

Hash functions are built into programming languages and even operating system shells. Common hash function algorithms are <i>md5</i> and <i>sha1</i> that are typically used for checking integrity.  For example, you can go to the gl command line and use them:

<pre>
=>echo -n asdfasdf | md5sum
6a204bd89f3c8348afd5c77c717a097a  -
=>echo -n asdfasdasdfasdfasdfasdfasdfasdfsadf | md5sum
ca13b7316c0aaed6a9853e93262c011a  -
=>echo -n password | sha1sum | awk '{print $1}'
5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8
=>echo -n password0000000000000000 | sha1sum | awk '{print $1}'
49d2cb2ed6544470361d03db09183770e429f04c
</pre>

Note that the command is md5sum for md5 and sha1sum for sha1. I piped the input string into these commands and you can see that no matter how big the input string is, the output is the same length.  There is a trailing '3spaces+hyphen' in the output for these commands that shows that the string came from the standard input rather than a file. I got rid of it for sha1sum with the awk statement that selects the first field only - one could do the same thing for md5sum. Awk is a line-oriented text processing utility available on the command line.  

Consisent hashing uses a custom hashing function (different from those above) that only outputs fixed keys that fall in a circle - they wrap around evenly so that all keys are randomly distributed around the circle.  So the way consistent hashing works is that all the nodes (servers) in a system are hashed so they are in the circle (maybe their names or ip addresses are the input). Then all incoming data is also hashed so that it is on the circle (maybe the data id is the input).  The rule is that wherever the data key lands, one goes clockwise to the first node and stores it there.  This allows all data to be sharded evenly among all nodes.

![Consistent Hashing](../651book-6ed/is651-images/consistent_hashing_3.PNG)

Figure 8.20. Consistent Hashing

This is actually the way that peer-2-peer systems like bittorrent work. All the data (blocks of the music, videos, programs, etc.) is hashed in this way so all peers can find it using an index on the tracker.  Consistent hashing algorithms always include replication so that all data gets replciated to multiple nodes for fault-tolerance. See the figure below for how a simple strategy for replicas works - in this case there is a second replica, but there can be any number.

![replicas](../651book-6ed/is651-images/consistent-hashing.png)

Figure 8.21. Replicas

A good video about consistent hashing is [here](https://www.youtube.com/watch?v=jznJKL0CrxM).  It goes into some details that are outside the scope of our brief intro and so that extra stuff is not testable.

Other ways to shard include the dynamic and entity methods. Dynamic sharding is done be range of key values.  Dynamic sharding is more resilient to nonuniform distribution of data and allows one to get equal storage on all nodes. Entity sharding is done by a logical group such as a user or location. That way, one can keep all user info in one shard to increase query performance.
As an example, here is a simple Bookstore schema showing how the data can be sharded:

![entity sharding](http://dbshards.com/wp-content/uploads/2013/01/white-paper-database-tables.png)

In the Bookstore example, the Primary Shard Table is the 'customer' entity. This is the table that is used to shard the data. The 'customer' table is the parent of the shard hierarchy, with the 'customer_order' and 'order_item' entities as child tables. The data is sharded by the 'customer.id' attribute, and all related rows in the child tables associated with a given 'customer.id' are sharded as well. The Global Tables are the common lookup tables, which have relatively low activity, and these tables are replicated to all shards to avoid cross-shard joins.

The picture below illustrates the difference between partition and dynamic sharding: partitioned data (A and B below) is divided into independent sets, while replicated data (C below) is copied to multiple locations. Note that the sharding is dynamic by key value.

![partition vs. sharding](../651book-6ed/is651-images/part-repl.png)

Figure 8.22. Partition vs. Sharding

#### Partitions and Consistency

We have seen that the ways to distributed data in a distributed system are sharding and replication. Furthermore, all sharding systems also use replication for fault-tolerance.  So replication is an important concept for distributeing data and is subject to the CAP theorem as we have seen before.  The CP Theorem is an 'impossibility' result that says in the presence of a partition, we can only have a tradeoff between consistency (C) and availability (A) since in a distributed system, we <em>always</em> have the possibility of a partition from node failure or network failure.  Here is a visual representation of the [CAP Theorem](https://i.stack.imgur.com/Hx8h0.png) which we will discuss in class.

How do we detect failures? This depends on how time is handled in a distributed system.  There are three possibilities: a global clock, only local clocks, or no clocks.  Global clocks depend on all nodes in a system being synchronized exactly.  This would depend on some synchronization method such as all nodes checking the time every few seconds with a protocol such as [ntp](https://en.wikipedia.org/wiki/Network_Time_Protocol).  This is usually impractical for data consistency (although useful for other purposes) because the network lag time will result in the clocks drifting to an impractical difference.  So local clock time is <em>usually</em> used for failure detection.  This means that if a request does not return in a set amount of time, it is declared a failure.  Note that this could be because the node failed, the network failed, or it just took too long - there is no way to differentiate.  When there is no clock, distributed systems typically use sequence numbers included in a all requests to allow 'newer' data to be identified.  There are methods for this that are beyond the scope of this discussion such as lamport clocks or [vector clocks](https://en.wikipedia.org/wiki/Vector_clock).  Note that they are called clocks, but do not depend on a real physical clock on the node - they use sequence numbers as a logical clock.  The terms synchronous and asynchronous <em>timing</em> as used to describe systems as to whether they have a clock or not. This is a different use of those terms than we have previously seen in this course where synchronous <em>communication</em> means blocking and asynchronous communication means no blocking.  This can be confusing when reading the literature.

Replication can therefore be implemented ways that allow divergence of data between nodes (<em>eventual consistency</em>) or allow no divergence (<em>strong consistency</em>).  Strong consistency has the ACID properties for transactions that we studied earlier. It uses locking (blocking with synchronous communication) to enforce no divergence.  

We will look at two strong consistency methods:

- 2-Phase Commit (2pc)
- Paxos (and a more modern simpler version called Raft)

We have already studied 2pc earlier when discussing tranactions. 2pc uses a TPM (a static coordinator) to montor distributed tranactions where all nodes are blocked (or locked) until the TPM has verified that all agree.  This is the typical method used in relational databases.  2pc is not partition tolerant.  This is typically not a problem for those use cases since relational database servers mostly run on clusters rather than as truly distributed.

![2pc](../651book-6ed/is651-images/tpc-fault-free-phase-1.png)

Figure 11.2. 2-Phase Commit

Paxos is an algorithm developed by Leslie Lamport in 1989 for consensus and strong consistency in a distributed system where a majority of nodes involved must return and there are dynamic coordinators that can change for each transaction.  Paxos is partition tolerant.  The key difference from 2PC is that unlike 2PC where all nodes need to agree, here only a majority needs to agree.  Raft is a simpler to understand algorithm that is based on and very similar to Paxos.  See this excellent [interactive presentation](http://thesecretlivesofdata.com/raft/) to see how Raft (and Paxos) works on a high level.  Note that it is synchronous communication and blocks so gives strong consistency.  Strong consistency methods entail more latency and so have worse performance than eventual consistency methods but are requied in applications that demand that the data does not diverge for <em>any</em> client.  Examples of such applications would include finanical transactions, gambling applications, etc.

Replication with eventual consistency does not block (asynchronous communication) and so allows data to diverge for some (typically short) time period but eventually does become consistent.  This allows much faster performance due to reduced latency at the price of data divergence.  It is good for applications that do no care if occaisionally clients get slightly different data or it difference must be repaired at some point.  Examples would include most soical media applications and even shopping applications.  For example, no one will care if your 'friends online' number differs slightly in Facebook between clients.  Even in online shopping, Amazon would much rather you completed your shopping cart quickly, even if the price was changing during the transaction because latency costs sales.  We have seen earlier in this course that eventual consistency is used for DNS and Directory Services databases.  Protocols such as [gossip protocols](https://en.wikipedia.org/wiki/Gossip_protocol) can be used for eventual consisency where there is no locking or blocking during the period that nodes are updated.   For example, the consistent hashing algorith for sharding uses such an eventual consistency  method for replicating the shards.

The difference between strong and eventual consistency is commonly charaterised as ACID which se have previouly covered and BASE which stands for Basic Availability, Soft-state, Eventual consistency.   Instead of requiring consistency after every transaction, it is enough for a database to eventually be in a consistent state. In these environments, accessing stale data is acceptable. This leniency makes it easy to cache copies of data throughout multiple nodes, never have to lock access to all those copies for any extensive time (e.g., a transaction operating on data will not lock all copies of that data), and update that data asynchronously (eventually). With a BASE model, extremely high scalability is obtainable through caching (replication), no central point of congestion, and no need for excessive messages to coordinate activity and access to data.  Patterns such as Master/Slave database replication that we have seen earlier can be either strong or eventual.  If the master replicates and locks until all replicates are updates, it is strong.  If it writes immediately to istself before replication and then replicates with no locking, then it is eventual.

![acid vs. base](/class/img/PODC.png)

Figure 11.3. ACID vs. BASE

#### NoSQL MongoDB Case Studies (for the homework)

We will use the cloud provider [codeanywhere](https://codeanywhere.com/) for MongoDB. Cloud providers give us software as service on the web typically for a fee, but also usually offer a free starter service (which we will use). Codeanywhere has a built in editor, web server, and unix command line with a lot of software available for us to use.

Sign up for a free account on codeanywhere at this time and create a php/ubuntu workspace. You can see the basics in their [documentation](http://docs.codeanywhere.com/).

To verify functionality you can run web pages in a browser, edit the files, and use the command line by opening an ssh terminal. For example, make sure you can change the displayed string in the php file and see the result in a browser and that you can see the file from the command line.

You can see that codeanywhere is running php5 from the command line (using php-cli) as:

<pre>
cabox@box-codeanywhere:~/workspace$ php5 -v
PHP 5.5.9-1ubuntu4.20 (cli) (built: Oct  3 2016 13:00:37)
Copyright (c) 1997-2014 The PHP Group                                       
Zend Engine v2.5.0, Copyright (c) 1998-2014 Zend Technologies
    with Zend OPcache v7.0.3, Copyright (c) 1999-2014, by Zend Technologies
</pre>

This will not work on gl because they have not installed the cli (command-line interface) for php. But we can see that it is php5 with the built-in <em>phpinfo()</em> function as in my account [here](http://swe.umbc.edu/~canfield/test/phpinfo.php).

<div class="noteBorder">
<b>Important</b> - Everybody is <em>required</em> to share the workspace in with my email address for `edit`. This gives me the rights to see your account and will allow me restart your app since your free workspace is a virtual machine that goes to sleep when you are not using it. Right click on your top-level project and choose `share project`.
</div>

##### MongoDB

We will get a brief introduction to [MongoDB](https://www.mongodb.org/) by doing a couple of tutorials from the syllabus notes.  This document NoSQL database illustrates many concepts we have seen in our reading: it is schemaless, uses javascript as a query language, sharding, replication, and JSON as a serialization.  Codeanywhere workspaces can have access to mongodb, but you must install it with the following:

1. open a free account on *https://codeanywhere.com/* (no cc required)

2. install a *php/ubuntu* container. you just select it from the list.

3. install mongodb - *https://docs.mongodb.com/v3.2/tutorial/install-mongodb-on-ubuntu/*. Use the 4 steps to install mongodb for *linux/ubuntu*. Use the further steps to start/stop mongodb. Once you have started the db, you can leave it running - no need to stop it after use.

4. restart mongodb - *sudo service mongod restart*

5. use the `share project` menu item (or right-click the top workspace) to invite me with *edit* rights. be sure to share the whole project container with me and not just a file.

Issue the command <em>mongo</em> to start the command-line client and get the '>' prompt where you can issue mongodb commands in javascript.  You can stop the client with <em>control-d</em> or typing *quit()*.  We will be using just a single copy of mongdb in our exercises, but see the docs for a brief introduction to how it is [replicated](https://docs.mongodb.org/manual/core/replication-introduction/) and [sharded](https://docs.mongodb.org/manual/core/sharding-introduction/). Remember that mongodb is created to be a distributed database.  

For homework (deliverables given in the homework instructions), do the introductory tutorial [Getting Started with MongoDB - Part 1](https://code.tutsplus.com/tutorials/getting-started-with-mongodb--net-22879).  Step1 shows you how to install mongodb on your own computer, but we do not need to do that since we already did it.  You can do all the remaining steps as in the tutorial.  Note that javascript is the data definition and query language for mongodb with JSON as a serialization.  Also note that we do not need to define a schema before we create a database and add records. You should [understand some mongo commands](https://docs.mongodb.org/manual/reference/mongo-shell/) to see how to use mongodb commands.

<pre>
cabox@box-codeanywhere:~/workspace$ mongo       
MongoDB shell version: 3.2.11                                                     
connecting to: test                                                                             
Server has startup warnings:                                                                    
2017-02-10T10:24:30.321-0500 I CONTROL  [initandlisten]                         
2017-02-10T10:24:30.321-0500 I CONTROL  [initandlisten] 
** WARNING: You are running in OpenVZ which can cause issues on versions of RHEL older than RHEL6.      
2017-02-10T10:24:30.321-0500 I CONTROL  [initandlisten]                                         
> show databases                                                                                
blog   0.000GB        
local  0.000GB                                                                                  
> use blog                                                                                      
switched to db blog                                                                             
> show collections                                                                                          
posts                                                                                                
> db.posts.find()                                                                               
{ "_id" : ObjectId("5842b63a595fd11c028b4568"), "title" : "cat with a hat", "content" : "once up
on a time a cat with a hat ..." }                                                               
{ "_id" : ObjectId("5871283b595fd103028b4567"), "title" : "cat with a hat", "content" : "once up
on a time a cat with a hat ..." }                                                               
> db.posts.drop()                                                         
true 
>db.posts.find() 
>
>quit()
cabox@box-codeanywhere:~/workspace$ 

</pre>

Listing 12.1.  MongoDB shell commands.

##### PHP with MongoDB

Next we will see how to use mongodb from a php application just as we used mysql in a previous chapter.  The tutorial [Introduction to MongoDB](http://www.sitepoint.com/introduction-to-mongodb/) shows how to create a blog using mongo. If you wish to try this  you  must install the mongo driver using the [pecl](https://en.wikipedia.org/wiki/PEAR#PECL) php package manager so that php can connect to mongodb.  

In the tutorial you will create a static blog that displays using print_r that shows the underlying php data structure for educational purposes.  Obviously, in real life, one would display it differently  Here is an example program using the tutorial.  Make sure you know how it work. Note that one can uncomment the [drop() statement](https://docs.mongodb.org/manual/reference/mongo-shell/) to allow it to run clean every time (or use <em>db.posts.drop()</em> from the mongo command line).

<pre>
&lt;?php
$mongo = new Mongo();
$db = $mongo-&gt;blog;
$collection = $db-&gt;createCollection("posts");

$document = array( // create document
    "title" =&gt; "cat with a hat",
    "content" =&gt; "once upon a time a cat with a hat ...");
    
$document2 = array(
    "title" =&gt; "cat with a hat",
    "content" =&gt; "2once upon a time a cat with a hat2 ...");
    
$insertOpts = array("safe" =&gt; true);
$collection-&gt;insert($document, $insertOpts); // insert to db - no dups
print_r($document);
echo "&lt;br/&gt;&lt;br/&gt;";

$document["author"] = "Shreef"; //add author field
$collection-&gt;save($document);
print_r($document);
echo "&lt;br/&gt;&lt;br/&gt;";

$collection-&gt;insert($document2, $insertOpts);
print_r($document);
echo "&lt;br/&gt;&lt;br/&gt;";

$document2["author"] = "Kip";
$collection-&gt;save($document2);
print_r($document2);
echo "&lt;br/&gt;&lt;br/&gt;";

$collection-&gt;update( // update only record matching
     array("author" =&gt; "Kip"),
     array('$set' =&gt; array("author" =&gt; "Timothy")),
     array("multiple" =&gt; true));
$cursor = $collection-&gt;find(); // display all records
foreach ($cursor as $document) {
    print_r($document);
    echo "&lt;br/&gt;";
}
echo "&lt;br/&gt;&lt;br/&gt;";

$cursor = $collection-&gt;find(array("title" =&gt; "cat with a hat")); // display only records w/certain title
foreach ($cursor as $document) {
    print_r($document);
    echo "&lt;br/&gt;";
}

echo "&lt;br/&gt;&lt;br/&gt;";

$document = $collection-&gt;findOne(array("author" =&gt; "Timothy"));
print_r($document);


//$collection-&gt;drop(); // drop all posts so we can run clean next time 

?&gt;
</pre>

Listing 12.2.  Sample program using the tutorial.

The mongodb looks like this after a run of listing 12.2 from the command-line:

<pre>
> db.posts.find()
{ "_id" : ObjectId("5665cc6f43b52590218b456d"), "title" : "cat with a hat", "content" : "once upon a time a cat with a hat ...", "author" : "Shreef" }
{ "_id" : ObjectId("5665cc6f43b52590218b456e"), "title" : "cat with a hat", "content" : "2once upon a time a cat with a hat2 ...", "author" : "Timothy" }
> 
</pre>

Listing 12.3.  Mongodb after listing 12.2.


### Chapter 12 Exercises

Do the end-of-chapter exercises for each chapter of the book by following the link in the on-line syllabus.
