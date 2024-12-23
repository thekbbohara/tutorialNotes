# Mysql 101 for mongoose developer
## INTRODUCTION
- I dont care.
- Mysql is a relational database management systems (RDBMS). It is an open-source, multi-user, multi-threaded database system that allows for storing and managing structured data in tables. It uses the **Structured Query Language (SQL)** to manage and manipulate data.
## Key Features of MySQL:
- **Open Source**
- **Cross-Platform**
- **Relational Database**: MySQL is based on a relational database model, which stores data in tables (also known as relations).
- **High Performance**: It is optimized for speed and can handle a large amount of data efficiently.
- **ACID Compliant**: MySQL supports the ACID (Atomicity, Consistency, Isolation, Durability) properties, ensuring that database transactions are processed reliably.
	 - _Atomicity ensures that a transaction is treated as a single, indivisible unit. Either all of the operations within a transaction are completed successfully, or none of them are applied. In other words, a transaction is atomic: it is "all or nothing."_
	 - _Consistency ensures that a transaction takes the database from one valid state to another valid state. After a transaction, all data must be in a consistent state, adhering to all defined rules, constraints, and relationships._
	 - _Isolation ensures that transactions are executed in isolation from one another, even if they occur concurrently. Each transaction should be executed as if it is the only transaction being processed, preventing interference from other transactions._
	 - _Durability ensures that once a transaction is committed, it is permanent, even in the case of system failures like power outages or crashes. The changes made by the transaction are saved to disk and will survive any subsequent failures._
- **Multi-User Access**: MySQL allows multiple users to access the database simultaneously without affecting performance.
## SQL-Keywords
### CREATE
1. **CREATE DATABASE**
    - The `CREATE DATABASE`  command is used to create a new database. In Mongoose, you don't need to explicitly create a database; it is automatically created when you connect to the database.
    ```js
    // DB is created if it doesn't exist
    mongoose.connect('mongodb://localhost/my_database');
    ```

    ```sql
    CREATE DATABASE my_database;
    ```

2. **USE DATABASE**
    - The `USE DB_NAME` is used to select the database to use. In Mongoose, this is handled by the connection string.
    ```js
    mongoose.connect('mongodb://localhost/my_database');
    ```

    ```sql
    USE my_database;
    ```

3. **CREATE TABLE**
    - The `CREATE TABLE` command is used to create a new table in the database. In Mongoose, this is similar to creating a **new collection**.
    ```js
    mongoose.model('User', UserSchema);
    ```

    ```sql
    CREATE TABLE Users (
        id INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(100) NOT NULL,
        email VARCHAR(100) NOT NULL UNIQUE
    );
    ```

4. **CREATE INDEX**
    - The `CREATE INDEX` command is used to create an index on a table to improve query performance. In MongoDB, this is the same.
    ```js
    UserSchema.index({ email: 1 }); // Unnamed Index
    UserSchema.index({ email: 1 }, { name: 'idx_email' }); // Named Index
    ```

    ```sql
    -- Syntax: `CREATE INDEX index_name ON table_name (column_name);`
    CREATE INDEX idx_email ON Users (email); -- Named Index
    CREATE INDEX ON Users (email); -- Unnamed Index
    ```
### DESCRIBE
- Used in SQL to view the structure of a table (its columns, data types, constraints, etc.). **Mongoose Example**: In MongoDB, there isn’t a direct equivalent to `DESCRIBE`. However, you can inspect a schema programmatically.
```js
console.log(UserSchema.paths);
// Outputs details about the schema fields and types
```

```sql
DESCRIBE Users;
```
### INSERT
- The `INSERT INTO` command is used to insert new rows in a table. In mongoose you would insert a new document into a collection/(Model).
```js
	// In mongoose its equivalent to .save() or .create();
	const newUser = new User({ name: 'John Doe', email: 'john@example.com' });
	newUser.save()
```

```sql
	INSERT INTO Users (name, email)
	VALUES ('John Doe', 'john@example.com');
```

### SELECT
- The `SELECT` statement in SQL is used to retrieve data from a database. In Mongoose, this is equivalent to using the `.find()` method to query a collection.
```js
	const users = await User.find(); // Fetches all users
	const { name, email } = await User.findById(1); // Fetches user with id = 1
```

```sql
	SELECT * FROM Users; -- all users
	SELECT name, email FROM Users WHERE id = 1; -- user of id 1
```

### UPDATE
- The `UPDATE` statement is used to modify the existing records in a table. In mongoose you use find and update or .update()
```js
	// update all user of name kb
	const query = {name:"kb"};
	USER.update(query,{name:"thekbbohara"})
```

```sql
	-- update all user of name kb
	UPDATE Users
	SET name="thekbbohara", email="thekbbohara@gmail.com"
	WHERE name="kb";
```

### DELETE
- The `DELETE` statement is used to delete existing records in a table. In mongoose we'd use deleteOne, deleteMany or find and delete.
```js
	User.deleteOne({_id:1})
	// All users whose name is notKb will be deleted.
	User.deleteMany({name:"notKb"})
```

```sql
	DELETE FROM Users WHERE id=1;
	DELETE FROM Users Where name="notKb"
	-- All users whose name is notKb will be deleted.
```

### ALTER
- The `ALTER TABLE` statement in SQL is used to modify the structure of an existing table (add column, drop column and modify column).
  In Mongoose, the equivalent operation would be modifying the schema to include the new field and then handling updates to existing documents if necessary.
```js
  // Update the UserSchema to add the 'age' field
  const UserSchema = new mongoose.Schema({
    name: String,
    email: String,
    age: Number, // New field
  });
```

```sql
	-- Adds an 'age' column to the Users table
	ALTER TABLE Users ADD age INT;
	
	-- Delete 'Email' column from Users table
	ALTER TABLE Users DROP COLUMN email;
	
	-- Makes 'id' column unsigned and auto-incrementing
	ALTER TABLE Users MODIFY COLUMN id INT UNSIGNED AUTO_INCREMENT;
```

### JOIN
- A `JOIN` clause is used to combine rows from two or more tables, based on a related column between them. In MongoDB, **joins** are not natively supported like in relational databases. Instead, you typically use **aggregation pipelines** like `$lookup` for similar functionality.
```js
	// MongoDB Example: Using $lookup for a JOIN-like operation
	db.orders.aggregate([
	  {
	    $lookup: {
	      from: "users",         // Target collection to join with
	      localField: "user_id", // Field in 'orders'
	      foreignField: "_id",   // Field in 'users'
	      as: "userDetails"      // Alias for joined data
	    }
	  }
	]);
```

- #### INNER JOIN
	The `INNER JOIN` keyword selects records that have matching values in both tables.
```sql
-- SQL Example: INNER JOIN
SELECT Orders.order_id, Users.name
FROM Orders
INNER JOIN Users
ON Orders.user_id = Users.id;
```
- #### LEFT JOIN
	The `LEFT JOIN` keyword returns all records from the left table (table1), and the matching records (if any) from the right table (table2).
```sql
-- SQL Example: INNER JOIN
SELECT Orders.order_id, Users.name
FROM Orders
LEFT JOIN Users
ON Orders.user_id = Users.id;
```
- #### RIGHT JOIN
	The `RIGHT JOIN` keyword returns all records from the right table (table2), and the matching records (if any) from the left table (table1).
```sql
-- SQL Example: INNER JOIN
SELECT Orders.order_id, Users.name
FROM Orders
RIGHT JOIN Users
ON Orders.user_id = Users.id;
```
- #### CROSS JOIN
	The `CROSS JOIN` keyword returns all records from both tables (table1 and table2).
```sql
-- SQL Example: CROSS JOIN
SELECT Orders.order_id, Users.name
FROM Orders
CROSS JOIN Users
```

## DATATYPES
- In MySQL there are three main data types: string, numeric, and date and time. But in MongoDB, there are a variety of data types, but they differ from those in MySQL. MongoDB uses BSON (Binary JSON) to store data, which supports a rich set of data types. Here's a comparison of common data types in MySQL and MongoDB:

### **String Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`CHAR`, `VARCHAR`|`String`|Both store textual data. MongoDB's `String` is analogous to `VARCHAR`.|
|`TEXT`, `TINYTEXT`, etc.|`String`|No separate `TEXT` type in MongoDB; all textual data is stored as `String`.|
### **Numeric Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`INT`, `SMALLINT`, etc.|`NumberInt`|Represents 32-bit integers.|
|`BIGINT`|`NumberLong`|Represents 64-bit integers.|
|`FLOAT`, `DOUBLE`|`NumberDouble`|Represents floating-point numbers.|
|`DECIMAL`, `NUMERIC`|`String` or custom|MongoDB doesn't have an exact equivalent; use `String` for precision.|
### **Date and Time Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`DATE`|`Date`|Both store date-only values.|
|`DATETIME`, `TIMESTAMP`|`Date`|MongoDB stores both date and time as a `Date` object.|
|`TIME`|`String` or custom|MongoDB does not have a direct `TIME` type; store as `String` if needed.|
|`YEAR`|`String` or `Int`|Represented using `String` or `NumberInt`.|
### **Boolean Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`BOOLEAN`, `TINYINT(1)`|`Boolean`|Both store true/false values.|
### **Binary Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`BLOB`, `TINYBLOB`, etc.|`BinData`|MongoDB's `BinData` is used for storing binary data like files.|
### **JSON/Array Data Types**

|**MySQL**|**MongoDB (BSON)**|**Notes**|
|---|---|---|
|`JSON`|`Object`|MongoDB natively stores JSON-like documents as `Object`.|
|N/A|`Array`|MongoDB has a native `Array` type for storing lists of values.|
### **Other Data Types**

| **MySQL** | **MongoDB (BSON)** | **Notes**                                                           |
| --------- | ------------------ | ------------------------------------------------------------------- |
| `ENUM`    | `String` or custom | Use a `String` field with validation for enumerated values.         |
| `SET`     | `Array`            | Use an `Array` to represent sets of values.                         |
| N/A       | `ObjectId`         | Unique identifier type in MongoDB, typically used as a primary key. |
| N/A       | `Decimal128`       | Used for high-precision decimal numbers in MongoDB.                 |

 [ALL SQL DATATYPES](https://www.w3schools.com/sql/sql_datatypes.


## PRIMARY KEY
- Ensures that each row in a table has a unique identifier.
```js
   const UserSchema = new mongoose.Schema({
     _id: { type: mongoose.Schema.Types.ObjectId, auto: true }, // Auto-generated unique ID (default primary key in MongoDB)
     name: { type: String, required: true }
   });
   const User = mongoose.model('User', UserSchema);
```

```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     name VARCHAR(100) NOT NULL
   );
```
## FOREIGN KEY
- Ensures a column's values correspond to values in another table.
```js
const PostSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String },
  userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User' } // Reference to the User model
});

const User = mongoose.model('User', UserSchema);
const Post = mongoose.model('Post', PostSchema);
```

```sql
   CREATE TABLE Posts (
     id INT AUTO_INCREMENT PRIMARY KEY,
     title VARCHAR(100) NOT NULL,
     content TEXT,
     user_id INT,
     FOREIGN KEY (user_id) REFERENCES Users(id)
   );
```
## Data Integrity and Constraints

1. **NOT NULL**:
   Ensures that a column cannot have `NULL` values.
   ```js
   const UserSchema = new mongoose.Schema({
     name: { type: String, required: true } // Ensures name cannot be null
   });
   const User = mongoose.model('User', UserSchema);
   ```

   ```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     name VARCHAR(100) NOT NULL
   );
   ```

2. **UNIQUE**:
   Ensures that all values in a column are unique.
   ```js
   const UserSchema = new mongoose.Schema({
     email: { type: String, unique: true } // Ensures email values are unique
   });
   const User = mongoose.model('User', UserSchema);
   ```

   ```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     email VARCHAR(100) UNIQUE
   );
   ```

3. **DEFAULT**:
   Assigns a default value to a column if no value is provided.
   ```js
   const UserSchema = new mongoose.Schema({
     status: { type: String, default: 'active' } // Default value for status
   });
   const User = mongoose.model('User', UserSchema);
   ```

   ```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     status VARCHAR(20) DEFAULT 'active'
   );
   ```

4. **CHECK** (MySQL 8.0+):
   Ensures that the values in a column satisfy a given condition.
   ```js
   const UserSchema = new mongoose.Schema({
     age: { type: Number, min: 18 } // Ensures age is >= 18
   });
   const User = mongoose.model('User', UserSchema);
   ```

   ```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     age INT CHECK (age >= 18)
   );
   ```

5. **AUTO_INCREMENT**:
   Automatically generates a unique value for a column, often used for primary keys.
   ```js
   const UserSchema = new mongoose.Schema({
     _id: { type: mongoose.Schema.Types.ObjectId, auto: true }, // Auto-generated unique ID
     name: String
   });
   const User = mongoose.model('User', UserSchema);
   ```

   ```sql
   CREATE TABLE Users (
     id INT AUTO_INCREMENT PRIMARY KEY,
     name VARCHAR(100)
   );
   ```
---
