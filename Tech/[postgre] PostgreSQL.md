# PostgreSQL

- ORDBMS (Object-Relational Database Management System)
- Can create custom data type
- Table inheritance
- Types: array, JSON, hstore (key-value)
- Cast
- Follow the **ACID** properties: [More](https://www.youtube.com/watch?v=pomxJOFVcQs), [More](https://www.youtube.com/watch?v=4EajrPgJAk0), [More](https://www.youtube.com/watch?v=URwmzTeuHdk)

  - **Atomic**: all-or-nothing completed
  - **Consistent**: ensure data must be valid and follow the rules, if not -> rollback
  - **Isolate**: none of transaction effect the others, isolated with each other, use **locking mechanism**:

    Example: Transaction 1 is updating a specific row. At the same time, transaction 2 can't get the value of that row (except other rows) because transaction 1 haven't commited or rollbacked yet.

    ```sql
    /* transaction 1 */
    begin;
    update A set a = 'new value'
    where id = 'key1'
    select * from A where id = 'key1' /* return new value */

    /* transaction 2 */
    select * from A where id = 'key1' /* blocking and waiting data */

    /* other transaction */
    select * from A where id = 'another key' /* work */
    ```

  - **Durable**: make sure data stored permanently after commit

- Support Full-text search
- Function Overloading

  **More: Function vs Store proceduce**

  - Stored procedures can modify database objects, and need not return results.
  - Functions can only have input parameters, whereas procedures can have either input or output parameters.
  - Functions can be called from procedures, but procedures cannot be called from functions.
  - Exceptions can be handled in try-catch blocks within procedures, but try-catch blocks cannot be used within functions.
  - Procedures cannot be used in SELECT statements, but functions can be embedded in SELECT statements.

- [Multiversion Concurrency Control](https://www.youtube.com/watch?v=iM71d2krbS4)
- OLTP
- Continuity
- Performance

[More](https://opencloudconnect.org/2023/06/01/why-postgresql-unveiling-the-benefits-of-this-robust-database/)

## CLI

- **Docker**

`docker run -d --name postgresDB -p 5432:5432 -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=demodb postgres:16.1`

`docker exec -it postgresDB psql -d demodb -U admin`

- **psql**

`\l`: list database

`\c`: connect to db

`\dt`: list database tables

`\q`: quit psql

`\conninfo`: display current connection info
