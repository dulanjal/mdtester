This package provides the functionality required to access and manipulate data stored in any type of relational database. 

If you are using a database of types: MySQL, SQL Server, Oracle, Sybase, PostgreSQL, DB2, HSQLDB, H2, or Derby, it is recommended to use the Ballerina types specific to them. This package provides a generic type that could be used if you wish to connect to a database type not in that list.

The first step to access the database is to create an 'Endpoint', which is a virtual representation of the physical endpoint that you are trying to connect to. For this you need to create an endpoint of SQL client type and provide the necessary connection parameters. That will create a pool of connections to the specified database.

Example:
```
endpoint sql:Client testDB {
   url: mysql://localhost:3306/testdb
   username: "testuser",
   password: "testpwd",
   options: {maximumPoolSize:1}
};
```
NOTE: You can find the full list of connection properties in the struct ‘ConnectionProperties’.

Once the endpoint is created, database operations could be executed through that. Following is an example of a SELECT:
```
var output = testDB -> select("SELECT name FROM Employee WHERE id = 1", null, null);
match output {
   table dt => {
      while (dt.hasNext()) {
         var rs = <Employee>dt.getNext();
         match rs {
            Employee emp => testFunction(emp.name);
            error => return;
            }
         }
      }
      sql:SQLConnectorError => return;
}
var closeStatus = testDB -> close();
```
This package provides support for selecting, inserting, deleting, updating, batch updating, and executing stored procedures. In addition to that there is a ‘Close’ action that will close the connection pool when executed.

