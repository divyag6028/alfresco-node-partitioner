
Alfresco Node Partitioner
================================================

ALF_NODE_PROPERTIES table stores every metadata value for Alfresco objects: contents, folders, users... Commonly, this table is filled with millions of rows, as it stores about 30 properties per node. Using a table partitioning approach improves performance and reduces maintenance operations. This project provides a shell tool to perform table partitioning on living or brand new Alfresco PostgreSQL databases and also a guide for automatic partition generation. 

A simple "Node Id" partitioning pattern is currently provided.

**License**
The plugin is licensed under the [LGPL v3.0](http://www.gnu.org/licenses/lgpl-3.0.html). 

**State**
Current release is 1.0.0

**Compatibility** 
The current version has been developed using Postgresql 9.4.8

**Currently not working**

Available functions
--------------------------------------

Partitioning ALF_NODE_PROPERTIES table

* create-master: master ALF_NODE_PROPERTIES_INTERMEDIATE table is created
* create-partitions: several ALF_NODE_PROPERTIES_NNNN tables are created, one per partition
* create-trigger: insertion trigger `alf_node_properties_insert_trigger` is created 
* fill: data is copied from ALF_NODE_PROPERTIES to ALF_NODE_PROPERTIES_INTERMEDIATE
* analyze: new tables are analyzed
* swap: drop previous ALF_NODE_PROPERTIES and rename ALF_NODE_PROPERTIES_INTERMEDIATE to ALF_NODE_PROPERTIES

Undo partitioning

* unswap: data is copied from ALF_NODE_PROPERTIES partitions to one single table

Support functions

* add-partition: new partition ALF_NODE_PROPERTIES_NNNN is created 
* count-nodes: max number of `node_id` in ALF_NODE_PROPERTIES
* dump: dump database to a folder
* restore: restore database from a folder

Using the script
----------------------

**Syntax**

```
Usage: ./pg_partitioner.sh <command> -db <database> -np <nodes-per-partition> -d <folder-path> -f <dump-file>
	<command>: create-master | create-partitions | create-trigger | fill | analyze | swap
	           unswap | count-nodes | dump | restore
	-db: Alfresco database name
	-np: Number of nodes to be stored on each partition
	-d: Folder to store a dump
	-f: File to restore a dump from
```

**Samples**

Partitioning tables storing 100,000 nodes per partition. 
As `node_id` is used (which is primary on ALF_NODE table), every partition will store at least 30x this number.

```
$ ./pg_partitioner.sh create-master -db alfresco
$ ./pg_partitioner.sh create-partitions -db alfresco -np 100000
$ ./pg_partitioner.sh create-trigger -db alfresco -np 100000
$ ./pg_partitioner.sh fill -db alfresco -np 100000
$ ./pg_partitioner.sh analyze -db alfresco -np 100000
$ ./pg_partitioner.sh swap -db alfresco -np 100000
```

A **cron** script can be created in order to create new partitions `add-partition` once a nodes number limit is reached.

Todo list
----------------------

* Port this work to other database products (MySQL, MariaDB)
* Re-consolidate partitions into a single table for Alfresco upgrading
* Provide dynamic partition insertion based on patterns (local and global properties) and evaluating QName based expressions

Contributors
----------------------

* Raúl Sampedro
