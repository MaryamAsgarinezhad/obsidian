After pulling postgres docker imagem install the psql to connect to the postgres server as a client:

```shell
sudo apt update
sudo apt install postgres-client
```
Then open the psql shell to run sql commands. With `psql`, you can connect to a PostgreSQL database server and perform operations such as querying data, modifying table structures, importing and exporting data, and managing users and permissions.

```shell

```
The `psychopg` package, often referred to as psycopg2, is a PostgreSQL adapter for the Python programming language. It allows Python code to interact with PostgreSQL databases, enabling tasks such as querying data, executing SQL commands, and managing database connections.
  
  ```pyhton
  connection = psycopg2.connect(user=os.getenv('DATABASE_USER'),  
                              password=os.getenv('DATABASE_PASSWORD'),  
                              host=os.getenv('DATABASE_HOST'),  
                              port=os.getenv('DATABASE_PORT'),  
                              database=os.getenv('DATABASE_NAME'))  
cursor = connection.cursor()  
cursor.execute(  
    """  
    CREATE TABLE IF NOT EXISTS messages (        id SERIAL PRIMARY KEY,        message TEXT NOT NULL    )    """)  
connection.commit()  
cursor.close()  
connection.close()
```