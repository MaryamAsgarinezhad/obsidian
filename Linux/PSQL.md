The data in a database is typically stored on disk, either locally on the same machine where the database software is running or on a remote server. The exact location and format of the data storage depend on the database management system (DBMS) being used.

For example, in a relational database management system (RDBMS) like PostgreSQL or MySQL, data is stored in tables on disk, with each table containing rows of data organized into columns. The actual storage mechanism used by the DBMS can vary, but it usually involves storing data in files on the file system.

In a NoSQL database like MongoDB or Cassandra, data is also stored on disk, but the storage format and organization are different from traditional RDBMSs, as NoSQL databases often use document-oriented or key-value storage models.

**Connecting to a postgres server using docker:**
![[Pasted image 20240426172021.png]]

- User, Password, and Database Name must be the same in the two services. This user-pass is for the "Database Name" database.
  
  You can have multiple databases in a single PostgreSQL server. Each database is independent and isolated from the others, allowing you to manage them separately. This can be useful for organizing different sets of data, separating development and production environments, or providing multi-tenant applications.
  
  The "DATABASE_PORT" must be the exposed port from the postgres image.
- Must do portforwarding for the postgres service in order to **connect to it from localhost. The expose process is already done in the postgres image on port 5432.**
- DATABASE_HOST=db : This indicates that the pg server is the db container.

- `DATABASE_URL`: Specifies the URL used to connect to the PostgreSQL database. It includes the database type (`postgresql`), username (`postgres`), password (`mysecretpassword`), host (`postgres`), port (`5432`), and database name (`postgres`).

- Without the `DATABASE_URL` variable, you would need to specify each connection parameter separately (e.g., `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_HOST`, `DATABASE_PORT`, `DATABASE_NAME`). While this is possible, it can be more cumbersome and error-prone, especially when managing multiple environment variables for different services or configurations.


After pulling postgres docker image, install the psql to connect to the postgres server as a client:

```shell
sudo apt update
sudo apt install postgres-client
```
Then open the psql shell to run sql commands. With `psql`, you can connect to a PostgreSQL database server and perform operations such as querying data, modifying table structures, importing and exporting data, and managing users and permissions.

Tip: Server application connects to the postgres server by the docker-compos service and postgres ip:port. **If you want to connect to postgres locally after running the docker-compose file locally on your environment, first Exec it to enter the container and then "psql -U username" to use postgres client to run commands.**

![[Pasted image 20240426174307.png]]

```shell
sudo -su postgres psql
```
This command connects to the PostgreSQL **database**(not server) using the postgres user. Replace postgres with your **database username** if it's different.

```shell
\dt
```

This command will display a list of tables in the current database along with their schema and other details.

If you want to see the details of a specific table, you can use the \d command followed by the table name. For example, to see the details of a table named my_table, you would use:

```shell
\d my_table
```

To exit the psql shell and return to the regular command line, you can type:
```shell
\q
```

You can see all databases  and users created by these teo command: 
```shell
\l
\du
```

Tip:
```shell
  CREATE USER myuser WITH PASSWORD 'mysecretpassword';
  ALTER ROLE myuser CREATEDB;
  CREATE DATABASE myDatabase;
  \c myDatabase   (connects to your database)
```

-------------------------

Tip:

![[Pasted image 20240503181023.png]]--------------------------------------------------

Tip: Every linux application has a default user, by which you can connect to the app's shell. 

Postgres's default user is called "postgres", and has access to every database.
If you want to create a new user in the posdtgres shell that has access to databases, you need to grant that access.

```postgres
GRANT CONNECT ON DATABASE cdn_dev_cdn TO cdn_dev;
GRANT USAGE ON SCHEMA public TO cdn_dev;

CONNECT privilege to allow the user to connect to the database and the USAGE privilege to allow the user to use objects in the database.
```



