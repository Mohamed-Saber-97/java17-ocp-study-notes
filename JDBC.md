# Java Database Connectivity

## Introducing the Interfaces of JDBC

* Each database has a different JAR file with the implementation of each database.
* The **JDBC** has 5 interfaces in the package `java.sql` to interact with the implementation on a very high level:
    * `Driver`: Establishes a connection to the database
    * `Connection`: Sends commands to a database
    * `PreparedStatement`: Executes a SQL query
    * `CallableStatement`: Executes commands stored in the database
    * `ResultSet`: Reads the results of a query

```java
import java.sql.*;

public static void main(String[] args) throws SQLException {
    String url = "jdbc:${Database Vendor}:${File Type}:${Database Name}";
    try (Connection conn = DriverManager.getConnection(url);
         PreparedStatement ps = conn.prepareStatement(
                 "SELECT name FROM exhibits");
         ResultSet rs = ps.executeQuery()) {
        while (rs.next())
            System.out.println(rs.getString(1));
    }
}
```

## Connecting to a Database
* `jdbc:mysql://localhost:3306:test`
    * `jdbc`: is the _protocol_ **_(always the same)_**
    * `mysql`: is the _subprotocol_ which is the **_(product/vendor name)_**
    * `//localhost:3306/test`: is the _subname_ which is adatabase-specific connection details
        * `3306`: the port is optional when using the default location
* Always use `DataSource` instead of `DriverManager` as it can pool connections or store the database connection
  information outside the application
* `Connection connection = DataSource.getConnection(DB_URL,DB_USR_NAME,DB_PASSWORD);`

## Working with a PreparedStatement

* `PreparedStatement` interface and `CallableStatement` interface extends the main `Statement` interface
* The main difference between `PreparedStatement` and `Statement` is that the second doesn't take any parameters
  unlike the first. It just executes whatever query you have it.
* It is possible to run SQL directly with `Statement`, you shouldn’t.
  `PreparedStatement` is far superior for the following reasons:
    * **Performance**: `PreparedStatement` are cached so it's better for reuse.
    * **Security**: protected against SQL injection when using a
      `PreparedStatement` correctly
    * **Readability**: it's not nice to deal with `String` concatenation with lots of parameters
* `PreparedStatement ps = connection.prepareStatement("SELECT * FROM students");`
* `var ps = conn.prepareStatement();// DOES NOT COMPILE`
    * `SQL` is not supplied at the time a `PreparedStatement` is requested.
* Modifying Data with `executeUpdate()` used with `SQL` statements that
  begin with `DELETE`, `INSERT`, or `UPDATE` and returns number of affected rows `int result = ps.executeUpdate();`.
* Reading Data with `executeQuery()` used with `SQL` statement that begins with `SELECT` returns
  `ResultSet` `ResultSet rs = ps.executeQuery()`.
* Processing Data with `execute()` used with either query or update returns  `boolean` so that we know whether there is
  a `ResultSet` `boolean isResultSet = ps.execute();`

```java
import java.sql.*;

public static void main(String[] args) throws SQLException {
    boolean isResultSet = ps.execute();
    if (isResultSet) {
        try (ResultSet rs = ps.getResultSet()) {
            System.out.println("ran a query");
        }
    } else {
        int result = ps.getUpdateCount();
        System.out.println("ran an update");
    }
}
```

### SQL runnable by the execute method

|        Method        | DELETE  | INSERT  | SELECT  | UPDATE  |
|:--------------------:|:-------:|:-------:|:-------:|:-------:|
|    `ps.execute()`    | **YES** | **YES** | **YES** | **YES** |
| `ps.executeQuery()`  | **NO**  | **NO**  | **YES** | **NO**  |
| `ps.executeUpdate()` | **YES** | **YES** | **NO**  | **YES** |

### Return types of execute methods

|        Method        | Return type | What is returned for `SELECT` | What is returned for `DELETE/INSERT/UPDATE` |
|:--------------------:|:-----------:|:-----------------------------:|:-------------------------------------------:|
|    `ps.execute()`    |  `boolean`  |            `true`             |                   `false`                   |
| `ps.executeQuery()`  | `ResultSet` |   Rows and columns returned   |                     N/A                     |
| `ps.executeUpdate()` |    `int`    |              N/A              |           Number of rows affected           |

* `?` can be used as _bind variable_ as placeholder to specify values at runtime.

```java

import java.sql.*;

public static void main(String[] args) throws SQLException {
    String sql = "INSERT INTO names VALUES(?, ?, ?)";
    try (PreparedStatement ps = conn.prepareStatement(sql)) {
        //Or use setObject for all of them
        ps.setInt(1, 20);
        ps.setString(3, 1);
        ps.setInt(2, "Hello");
        ps.executeUpdate();
//It can remember the parts we used before so we don't have to re-write them again
        ps.setInt(1, 21);
        ps.setString(3, "Elias");
        ps.executeUpdate();
    }
}
```

### PreparedStatement methods

|   Method   | Parameter type | Example database type |
|:----------:|:--------------:|:---------------------:|
| setBoolean |    boolean     |        BOOLEAN        |
| setDouble  |     double     |        DOUBLE         |
|   setInt   |      int       |        INTEGER        |
|  setLong   |      long      |        BIGINT         |
|  setNull   |      int       |       Any type        |
| setObject  |     Object     |       Any type        |
| setString  |     String     |     CHAR,VARCHAR      |

* `setNull()` method takes an int parameter representing the column type in the database.
* `setObject()` method works with any Java type, and it will give you runtime error instead of compile-time error

## Getting Data from a ResultSet
* A `ResultSet` has a cursor, which points to the current location in the data which is **_before_** the first row 
  in the `ResultSet`.
* 
  
   