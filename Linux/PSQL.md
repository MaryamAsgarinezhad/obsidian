The data in a database is typically stored on disk, either locally on the same machine where the database software is running or on a remote server. The exact location and format of the data storage depend on the database management system (DBMS) being used.

For example, in a relational database management system (RDBMS) like PostgreSQL or MySQL, data is stored in tables on disk, with each table containing rows of data organized into columns. The actual storage mechanism used by the DBMS can vary, but it usually involves storing data in files on the file system.

In a NoSQL database like MongoDB or Cassandra, data is also stored on disk, but the storage format and organization are different from traditional RDBMSs, as NoSQL databases often use document-oriented or key-value storage models.

**Connecting to a postgres server using docker:**
![[Pasted image 20240426172021.png]]

- User, Password, and Database Name must be the same in the two services.
  The "DATABASE_PORT" must be the exposed port from the postgres image.
- Must do portforwarding for the postgres service in order to **connect to it from localhost. The expose process is already done in the postgres image on port 5432.**
- DATABASE_HOST=db : This indicates that the pg server is the db container.

- If you don't specify the `DATABASE_URL` environment variable in the `docker-compose.yml` file, your application may not be able to connect to the PostgreSQL database. The `DATABASE_URL` variable is a convenient way to provide all the necessary connection information (username, password, host, port, and database name) in a single variable.

- Without the `DATABASE_URL` variable, you would need to specify each connection parameter separately (e.g., `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_HOST`, `DATABASE_PORT`, `DATABASE_NAME`). While this is possible, it can be more cumbersome and error-prone, especially when managing multiple environment variables for different services or configurations.


After pulling postgres docker image, install the psql to connect to the postgres server as a client:

```shell
sudo apt update
sudo apt install postgres-client
```
Then open the psql shell to run sql commands. With `psql`, you can connect to a PostgreSQL database server and perform operations such as querying data, modifying table structures, importing and exporting data, and managing users and permissions.

```shell

```
