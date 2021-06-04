# MongodbSync
1. Support mongos: The source supports mongos, and most tools currently only support replica sets;
1. Fault tolerance: Some tools will hang up when the source or destination connection times out. This is likely to cause some problems in the production environment, resulting in incomplete data synchronization. This tool will handle this abnormality;
1. Multithreading: When the source and destination speeds are not synchronized, such as fast reading and slow writing, this problem can be avoided to a large extent by using multithreading;
1. Multiple synchronization modes: full synchronization (one-time copy), incremental synchronization, intelligent synchronization (one-time copy first, then incremental synchronization);
1. Support collection-level synchronization: most tools support db-level synchronization. A major feature of this tool is that it can support a certain db or specify a collection under db;

Through practice in the production environment, this tool has a relatively good performance in remote and local area network synchronization, and has shown excellent stability.


# Features

mongodb A synchronization tool of, which can synchronize data from one data source to other mongodb, and supports:
1. mongos -> (mongos, mongod)
1. mongod -> (mongos, mongod)

If the source is mongos, the situation is more complicated, and all the copy information needs to be taken out from mongos and synchronized to mongod;
It should be noted that both the source and destination mongo need to use the admin account to obtain all permissions;
The supported oplog format is: "ts": Timestamp(1372320938000, 1) The current 2.6.4 version is this format;

# Configuration introduction
```
    sync-info
        mode: the value of incr means incremental synchronization; all means full synchronization, which will copy the source data to the target database; smart means smart synchronization, which will first copy the full amount and then perform the incremental；
        record_interval: Read a certain amount of data and update optime;
        record_time_interval: After reading for a certain period of time, optime will be updated；
        opt_file: optime is recorded in this file, all mode updates it after copying, incr, smart mode updates it from time to time, if the file does not exist, it will be synchronized from 1 hour ago by default；
        all_dbs: True means to synchronize all databases, excluding admin, config, and local, otherwise synchronize dbs；
        dbs: The database collection that needs to be synchronized, excluding admin, config, and local. If you want to synchronize a collection under a certain db, use the db.collection form；
        queue_num: The maximum number of queues, this parameter is not used temporarily, the code is fixed at 20000；
        threads: The number of threads, because the source and destination network conditions may be different, there can be multiple connections to write the destination address;

    mongo-src
        addr: The source address can be a replica set or mongos. If it is a mongos, the system will automatically connect to the corresponding replica set；
        user: Administrator account
        pwd: Administrator password

    mongo-dest
        addr: The destination address can be a replica set or mongos；
        user: Administrator account
        pwd: Administrator password
```

# Key description
1. When there is no database and collection for the purpose: it will be created automatically, including indexes

1. When the source is mongos: the contents of all replica sets will be synchronized;

1. When the source is a replica set: the contents of all replica sets will be synchronized;

1. Add, delete, modify data command: OK;

1. When adding or deleting collection: OK;

1. db level operation: ignore;

1. Synchronization performance: depends on network bandwidth;

1. Fault tolerance: it can handle the source and destination network anomalies, and the system has fault tolerance processing capabilities;



# Operation mode
```
    python src/python/main.py conf/xxx.conf logs/xxx.log [is-debug]
    is-debug: If the value is true, false, or true, the log will print the debug log, otherwise close the debug log
```

# Change Log
    [2014-12.01]: Multi-threading is supported to solve the problem of inconsistent speed between source and destination. When the destination is slow, multi-threading is used to improve performance;
