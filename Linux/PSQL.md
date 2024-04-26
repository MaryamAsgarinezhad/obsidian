The data in a database is typically stored on disk, either locally on the same machine where the database software is running or on a remote server. The exact location and format of the data storage depend on the database management system (DBMS) being used.

For example, in a relational database management system (RDBMS) like PostgreSQL or MySQL, data is stored in tables on disk, with each table containing rows of data organized into columns. The actual storage mechanism used by the DBMS can vary, but it usually involves storing data in files on the file system.

In a NoSQL database like MongoDB or Cassandra, data is also stored on disk, but the storage format and organization are different from traditional RDBMSs, as NoSQL databases often use document-oriented or key-value storage models.

**Connecting to a postgres server using docker:**

```
```
After pulling postgres docker imagem install the psql to connect to the postgres server as a client:

```shell
sudo apt update
sudo apt install postgres-client
```
Then open the psql shell to run sql commands. With `psql`, you can connect to a PostgreSQL database server and perform operations such as querying data, modifying table structures, importing and exporting data, and managing users and permissions.

```shell

```
