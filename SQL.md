This package provides the functionality required to access and manipulate data stored in any type of relational database. 

The first step to access a database is to create an 'Endpoint', which is a virtual representation of the physical endpoint that you are trying to connect to. For this you need to create an endpoint of SQL client type and provide the necessary connection parameters. That will create a pool of connections to the specified database.

Example:
```
endpoint sql:Client testDB {
   url: mysql://localhost:3306/testdb
   username: "testuser",
   password: "testpwd",
   options: {maximumPoolSize:1}
};
```
**NOTE**: You can find the full list of connection properties in the struct ‘ConnectionProperties’.

**NOTE**: If you are using a MySQL, SQL Server, Oracle, Sybase, PostgreSQL, DB2, HSQLDB, H2, or Derby database, it is recommended to use enpdoints created using the client types specific to them, by using relevent ballerina packages. This package provides a generic type (i.e. sql:Client) that could be used if you wish to connect to a database type not in that list. Having said that, as shown in the above example, this type can be used to connect to those standard databases as well.

Once the endpoint is created, database operations could be executed through that. This package provides support for selecting, inserting, deleting, updating, batch updating, and executing stored procedures.

Following is an example of a SELECT:
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
As shown in the above example, once the required database operation is done, 'close' function can be called to close the connection pool.

Following is an INSERT example to demonstrate the usage of parameters:

```
sql:Parameter name = {sqlType:sql:TYPE_VARCHAR, value:"John Doe", direction:sql:DIRECTION_IN};
sql:Parameter registrationID = {sqlType:sql:TYPE_INTEGER, value:3, direction:sql:DIRECTION_IN};
sql:Parameter salary = {sqlType:sql:TYPE_DOUBLE, value:5000.00, direction:sql:DIRECTION_IN};
sql:Parameter country = {sqlType:sql:TYPE_VARCHAR, value:"UK", direction:sql:DIRECTION_IN};
sql:Parameter[] parameters = [name, registrationID, salary, country];
var insertCountRet = testDB -> update("INSERT INTO Employee (name, registrationID, salary, country)
                                     values (?,?,?,?)", parameters);
_ = testDB -> close();
```
