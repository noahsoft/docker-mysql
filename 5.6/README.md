docker-mysql-5.6
================

Run an out-of-the-box mysql server.

Import script included

I highly advise to use in conjunction with the data-container:

- It has all the advantages of the philosophy of One container for One job.

- plus the data-container has Volumes to allow acces to config and data dirs (better/safer than having Volumes on the MySQL server).

Usage
-----

The first time that you run the container, the server will generate a random password for the user `admin`. You can see the password in the docker logs:

    docker logs <CONTAINER_ID>

You can set MYSQL_USER and MYSQL_PASS env variables to set user and/or password :

    docker run -d -e MYSQL_USER="myuser" -e MYSQL_PASS="mypass" sabbasth/mysql:<tag>

Remember that root user is only accessible from localhost.

Mounting with the data container
--------------------

Start a data container (See [sabbasth/mysql:data-container](https://github.com/Sabbasth/docker-mysql/tree/master/data-container))

Run the server:

    docker run -d --volumes-from <data container name> sabbasth/mysql:<tag>

Export/Import database data
---------------------------

Dump your database structure:

    mysqldump -u<user> -p --opt -d -B <database(s) name> > /tmp/db_schema.sql

Dump your datas:

    mysqldump -u<user> -p --quick --single-transaction -t -n -B <database(s) name> > /tmp/db_data.sql

Start a new database and init with the SQL file:

    docker run -d -v /path/in/host:/var/lib/mysql -e STARTUP_SQL="/tmp/<dump.sql>" sabbasth/mysql:<tag>

Replication - Master/Slave
-------------------------
Set environment variable `REPLICATION_MASTER` or `REPLICATION_SLAVE` to `true`.
On master, specify `REPLICATION_USER` and `REPLICATION_PASS` for the account to perform replication, the default value is `replica:replica`.*Always change this user/password combination as it is very insecure !*
The slave must have its master's link alias named `mysql`.

Examples:

- Master MySQL

    ```docker run -d -e REPLICATION_MASTER=true -e REPLICATION_USER=repuser -e REPLICATION_PASS=reppass -p 3306:3306 --name mysql tutum/mysql```

- Example on Slave MySQL:

    ```docker run -d -e REPLICATION_SLAVE=true -p 3307:3306 --link mysql:mysql tutum/mysql```

Now, you can access port `3306` and `3307` for the master/slave mysql
