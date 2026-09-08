# Week 33: Java Swing + SQLite Connection

## Main Topic
SQLite JDBC driver, `Connection`, `DriverManager`, `PreparedStatement`,
`ResultSet`, and Java-to-SQLite connection.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Open a connection from a Java application to a local SQLite database
   file using `DriverManager`.
2. Explain the role of a JDBC connection URL for SQLite.
3. Use `PreparedStatement` to safely execute SQL statements containing
   parameters.
4. Use `ResultSet` to read data returned by a `SELECT` query.
5. Correctly close JDBC resources using try-with-resources.
6. Combine a Swing form with a JDBC connection to insert and display a
   single record.

## Prerequisite Knowledge
Students must already understand basic SQL statements and have successfully
configured the SQLite JDBC driver in their Eclipse Build Path, as taught in
Week 32. Students must also be comfortable with try-with-resources from Week
17 and Swing forms with validated input from Week 31. No prior experience
actually writing JDBC code is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Week 32: SQLite, SQL basics, and the JDBC driver |
| 2 | 20 min | `DriverManager` and the SQLite connection URL |
| 3 | 25 min | `PreparedStatement`: why it is safer than building SQL strings manually |
| 4 | 20 min | `ResultSet`: reading rows returned by a query |
| 5 | 20 min | try-with-resources for JDBC objects |
| 6 | 25 min | Live coding: connecting a Swing form to SQLite for the first time |

## Detailed Lesson Discussion

Week 32 explained what SQLite, SQL, and JDBC are, and got the required
driver installed in Eclipse. This week writes the actual Java code that uses
that driver to open a real connection to a database file and run SQL
statements from inside a running Java program.

The starting point for any JDBC code is the `Connection` interface, which
represents an open, active link between the Java program and a specific
database. A `Connection` object is obtained using the static method
`DriverManager.getConnection(url)`, where `url` is a specially formatted
string that tells JDBC which database system to use and where to find the
actual database. For SQLite, this URL always begins with `jdbc:sqlite:`,
followed immediately by the path to the database file, such as
`jdbc:sqlite:database/app.db`. This relative path assumes the working
directory when the program runs is the project's root folder, which is the
default behavior when running a class through Eclipse's **Run As > Java
Application**. If the file specified in the URL does not already exist,
SQLite automatically creates a new, empty database file at that location the
first time a connection is opened, which is a convenient behavior but also
means a typo in the path can silently create an unwanted extra file rather
than immediately producing an error.

Once a `Connection` is open, SQL statements are sent to the database using a
`Statement` or, preferably in this course, a `PreparedStatement`. A
`PreparedStatement` is created from an open connection using
`connection.prepareStatement(sql)`, where `sql` is a SQL string containing
question mark placeholders, called **parameters**, anywhere a value needs to
be inserted, such as `INSERT INTO students (name, year_level) VALUES (?,
?)`. Each placeholder is then filled in using a `setXxx` method, matching the
parameter's position, starting at 1 rather than 0: `statement.setString(1,
name)` and `statement.setString(2, yearLevel)`, for example. This
parameterized approach is strongly preferred over building a SQL string
directly by concatenating user input, such as writing
`"INSERT INTO students VALUES ('" + name + "')"`, for two important reasons.
First, it avoids a category of bugs and security vulnerabilities known as
**SQL injection**, where specially crafted user input, such as a name
containing a stray quotation mark, could corrupt the intended SQL statement
or allow unintended commands to run against the database; a
`PreparedStatement` always treats parameter values strictly as data, never
as part of the SQL command itself, regardless of what characters they
contain. Second, a `PreparedStatement` avoids tricky and error-prone manual
string-building code, such as remembering to wrap text values in quotes but
not numeric values, which becomes especially cumbersome once a statement has
several parameters.

A `PreparedStatement` that changes data, such as an `INSERT`, is executed
using `executeUpdate()`, which returns an `int` representing the number of
rows affected by the statement. A `PreparedStatement` that retrieves data,
such as a `SELECT`, is executed using `executeQuery()`, which returns a
`ResultSet`. A `ResultSet` represents the rows returned by a query, and it
works somewhat like a cursor that starts positioned just before the first
row; calling `resultSet.next()` moves the cursor forward one row at a time
and returns `true` if a row was found or `false` once there are no more rows
left, which is why reading a `ResultSet` is almost always written as a
`while (resultSet.next()) { ... }` loop. Inside that loop, individual column
values are read using methods such as `resultSet.getString("name")` or
`resultSet.getInt("id")`, referring to columns either by name, as shown
here, or by their numeric position starting at 1.

Every JDBC object introduced this week — `Connection`, `PreparedStatement`,
and `ResultSet` — represents a real external resource that must eventually be
closed to release memory and file handles back to the operating system,
exactly the same underlying idea as closing a file stream in Week 16.
Because all three of these interfaces implement `AutoCloseable`, they can be
declared inside the parentheses of a try-with-resources statement, learned
in Week 17, which guarantees they are closed automatically once the block
finishes, whether it finishes normally or because an exception was thrown.
Using try-with-resources for JDBC code, rather than manually calling
`close()` inside a `finally` block, keeps database code shorter and removes
an entire category of bugs caused by forgetting to close a connection,
which can otherwise cause a SQLite database file to remain locked and
inaccessible to future connection attempts.

## Important Terminology

Key terms: **Connection**, **DriverManager**, **JDBC URL**,
**PreparedStatement**, **parameter/placeholder**, **SQL injection**,
**executeUpdate()**, **executeQuery()**, **ResultSet**, and
**try-with-resources**.

## Complete Java Programming Example

```java
package week33.db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

/**
 * Demonstrates opening a SQLite connection, creating a table if it does
 * not already exist, inserting one record with a PreparedStatement, and
 * reading all records back using a ResultSet.
 */
public class ContactDatabaseDemo {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    public static void main(String[] args) {
        createTableIfNeeded();
        insertContact("Maya Lopez", "maya.lopez@example.edu");
        printAllContacts();
    }

    private static void createTableIfNeeded() {
        String sql = "CREATE TABLE IF NOT EXISTS contacts ("
                + "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                + "name TEXT NOT NULL, "
                + "email TEXT NOT NULL)";

        try (Connection connection = DriverManager.getConnection(DB_URL);
             Statement statement = connection.createStatement()) {
            statement.execute(sql);
            System.out.println("Table ready.");
        } catch (SQLException e) {
            System.out.println("Error creating table: " + e.getMessage());
        }
    }

    private static void insertContact(String name, String email) {
        String sql = "INSERT INTO contacts (name, email) VALUES (?, ?)";

        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, name);
            statement.setString(2, email);
            int rowsInserted = statement.executeUpdate();
            System.out.println(rowsInserted + " row(s) inserted.");
        } catch (SQLException e) {
            System.out.println("Error inserting contact: " + e.getMessage());
        }
    }

    private static void printAllContacts() {
        String sql = "SELECT id, name, email FROM contacts";

        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql);
             ResultSet resultSet = statement.executeQuery()) {

            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                String email = resultSet.getString("email");
                System.out.println(id + ": " + name + " (" + email + ")");
            }
        } catch (SQLException e) {
            System.out.println("Error reading contacts: " + e.getMessage());
        }
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week33-DatabaseConnection`.
2. Right-click the project and add the SQLite JDBC driver `.jar` to the
   Build Path exactly as practiced in Week 32
   (**Build Path > Configure Build Path > Libraries > Add External JARs**).
3. Create a folder named `database` at the project's root level.
4. Create a package named `week33.db`.
5. Create the class `ContactDatabaseDemo` and paste in the complete example.
6. Save the file and resolve imports with **Ctrl+Shift+O**, confirming the
   `java.sql` package classes resolve correctly.
7. Run the class using **Run As > Java Application**.
8. Observe the Console output: "Table ready.", "1 row(s) inserted.", and a
   single printed contact line.
9. Refresh the project in the Package Explorer (right-click the project,
   **Refresh**, or press **F5**) and confirm a new file `app.db` now exists
   inside the `database` folder.
10. Run the program a second time without changing anything, and observe
    that the Console now shows two printed contact lines, since each run
    inserts another row into the same persistent database file.
11. Intentionally misspell the database URL, such as changing
    `"jdbc:sqlite:database/app.db"` to `"jdbc:sqlite:Database/app.db"`
    (capital D), run the program again, and observe in the Package Explorer
    that a second, unwanted database file was created in a different folder,
    demonstrating why exact path spelling matters. Correct the URL
    afterward and delete the accidental extra file and folder.

## Guided Worked Example

**Problem description:** Write a small standalone program that creates a
"tasks" table if needed, inserts a single fictional task using a
`PreparedStatement`, and then queries and prints only tasks that are marked
incomplete, demonstrating a `WHERE` clause combined with a parameter.

```java
package week33.db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TaskQueryDemo {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    public static void main(String[] args) {
        try (Connection connection = DriverManager.getConnection(DB_URL);
             Statement statement = connection.createStatement()) {
            statement.execute("CREATE TABLE IF NOT EXISTS tasks ("
                    + "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                    + "description TEXT NOT NULL, "
                    + "completed INTEGER NOT NULL)");
        } catch (SQLException e) {
            System.out.println("Error creating table: " + e.getMessage());
            return;
        }

        String insertSql = "INSERT INTO tasks (description, completed) VALUES (?, ?)";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(insertSql)) {
            statement.setString(1, "Submit Week 33 lab report");
            statement.setInt(2, 0);
            statement.executeUpdate();
        } catch (SQLException e) {
            System.out.println("Error inserting task: " + e.getMessage());
            return;
        }

        String selectSql = "SELECT id, description FROM tasks WHERE completed = ?";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(selectSql)) {
            statement.setInt(1, 0);
            try (ResultSet resultSet = statement.executeQuery()) {
                System.out.println("Incomplete tasks:");
                while (resultSet.next()) {
                    System.out.println("- [" + resultSet.getInt("id") + "] "
                            + resultSet.getString("description"));
                }
            }
        } catch (SQLException e) {
            System.out.println("Error reading tasks: " + e.getMessage());
        }
    }
}
```

**Walkthrough:** The final query uses `WHERE completed = ?` with the
parameter set to `0` using `statement.setInt(1, 0)`, demonstrating that
`PreparedStatement` parameters work the same way for filtering conditions in
a `SELECT` as they do for values in an `INSERT`. The `ResultSet` is opened
inside a nested try-with-resources, since it depends on the
`PreparedStatement` remaining open while it is being read.

**Expected behavior:** Running this program repeatedly adds a new
incomplete task each time and prints every currently incomplete task found
in the table.

**Test data:** Manually running an `UPDATE tasks SET completed = 1 WHERE id
= 1;` statement in a SQL tool between runs (a preview of Week 35's material)
would cause that specific task to stop appearing in the printed list.

**Likely errors:** Forgetting to set a value for a parameter before calling
`executeQuery()` or `executeUpdate()` throws an `SQLException` indicating a
parameter was not supplied.

## Code Walkthrough

`ContactDatabaseDemo` separates its logic into three focused methods, each
opening and closing its own `Connection` using try-with-resources rather
than sharing one connection across the whole program; this is a reasonable,
simple approach for a small beginner program, even though larger
applications often reuse a single connection more efficiently. Each method
follows the same overall pattern: build the SQL string (with `?` parameter
placeholders where needed), open a connection, prepare the statement, supply
any parameters, execute it, and process the result if there is one, all
within a `try` block that also declares the JDBC resources so they are
closed automatically, with any `SQLException` caught and reported instead of
crashing the whole program. The `CREATE TABLE IF NOT EXISTS` statement is a
small but important SQL detail: it prevents an error from being thrown if the
table was already created during a previous run of the program, which is
essential since this program, like most database applications, may be run
many times against the same persistent database file.

## Expected GUI or Program Behavior

This week's programs are console-based, focusing entirely on the JDBC
mechanics rather than a graphical interface, since Week 34 is where these
techniques are first connected to a Swing form for data entry and display.
Running `ContactDatabaseDemo` repeatedly against the same `database/app.db`
file accumulates one additional contact row with each run, all of which are
printed to the Console every time the program runs.

## Laboratory Session (3 Hours)

### Laboratory Title
Connecting Java to SQLite: Table Setup, Insert, and Query

### Objectives
1. Correctly configure a SQLite connection URL pointing to
   `database/app.db`.
2. Use `Statement` to create a table if it does not already exist.
3. Use `PreparedStatement` with parameters to insert data safely.
4. Use `ResultSet` to read and display query results.
5. Correctly manage JDBC resources using try-with-resources.

### Required Software and Materials
Eclipse IDE with a configured JDK and the SQLite JDBC driver `.jar` added to
the Build Path.

### Required Java Concepts
try-with-resources and exception handling (Week 17), combined with this
week's `Connection`, `PreparedStatement`, and `ResultSet` material.

### Development Requirements
Create an Eclipse project `Week33-Lab` with package `week33.lab` containing
a class `InventoryDatabaseSetup` that creates a `products` table (columns:
`id` primary key autoincrement, `name` TEXT, `quantity` INTEGER, `price`
REAL) if it does not already exist, inserts at least three fictional product
records using `PreparedStatement`, and then queries and prints all products
with a `quantity` greater than zero, using a parameterized `WHERE` clause.

### Detailed Laboratory Procedure

1. Create the project `Week33-Lab`, add the SQLite JDBC driver to the Build
   Path, and create a `database` folder at the project root.
2. Create the package `week33.lab` and the class `InventoryDatabaseSetup`.
3. Write a method that creates the `products` table if it does not already
   exist, using `CREATE TABLE IF NOT EXISTS`.
4. Write a method that inserts a single product record using a
   `PreparedStatement` with two `?` parameters, called at least three times
   with different fictional product data (for example, "Notebook", "Stapler",
   "Highlighter").
5. Write a method that queries all products with `quantity > ?`, passing `0`
   as the parameter, and prints each matching product's name, quantity, and
   price using a `ResultSet` loop.
6. Call all three methods, in order, from `main`.
7. Run the program and confirm the Console shows a successful table
   creation message, three successful insert messages, and a printed list of
   all three products (since all were inserted with a positive quantity).
8. Refresh the project and confirm `database/app.db` now exists.
9. Manually change one product's quantity to `0` in the insert data, rerun
   the program (after first deleting `app.db` to start fresh, since the
   table persists between runs), and confirm that product no longer appears
   in the printed list.

### Required Features
- The `products` table must be created only if it does not already exist.
- All inserts must use `PreparedStatement` with parameters, never string
  concatenation of values into the SQL text.
- The final query must use a parameterized `WHERE` clause, not a hard-coded
  value written directly into the SQL string.
- All JDBC resources must be managed using try-with-resources.

### Testing Procedure
Run the program from a completely fresh state (no existing `app.db` file),
confirm correct table creation, confirm all inserts succeed, and confirm the
query returns exactly the expected filtered set of products.

### Normal Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Fresh run, no existing app.db | Delete database/app.db, then run | Table created, three products inserted, all three printed |
| Second run against existing database | Run again without deleting app.db | Table creation step succeeds silently (IF NOT EXISTS); three more rows inserted; six products now printed |

### Boundary Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Product with quantity exactly 0 | Insert a product with quantity 0 | Not included in the "quantity > 0" results |
| Product with quantity exactly 1 | Insert a product with quantity 1 | Included in the results, confirming the boundary is handled correctly |

### Invalid Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Missing database folder | Delete the `database` folder before running | SQLite may fail to create the file depending on the OS; an SQLException should be caught and reported, not crash the program |
| Malformed SQL (deliberately introduced typo) | Temporarily misspell a column name in the CREATE TABLE statement | SQLException caught and printed with a clear message; program does not crash |

### Troubleshooting Guidance
If the program reports "No suitable driver found," confirm the SQLite JDBC
`.jar` is present under the project's Build Path libraries. If `database/app.db`
never appears, confirm the `database` folder exists at the project root and
that the working directory Eclipse uses when running matches the project
root (this is the default and rarely needs adjustment). If an insert appears
to succeed but no rows show up later, confirm that the same database file
path is being used consistently across all methods; a typo in one method's
URL string will silently create or use a different file. A
`SQLException: parameter index out of range` error means a `?` placeholder
was never given a value with the matching `setXxx` call before the statement
was executed.

### Required Deliverables
1. Eclipse project `Week33-Lab` with the completed `InventoryDatabaseSetup`
   class.
2. A screenshot of the Console output showing successful table creation,
   inserts, and the filtered query results.
3. A short written explanation of why `PreparedStatement` is used instead of
   directly building SQL strings with concatenation.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Table created correctly using CREATE TABLE IF NOT EXISTS | 15 |
| All inserts use PreparedStatement with parameters correctly | 25 |
| Parameterized SELECT with WHERE clause correctly implemented | 25 |
| ResultSet correctly read and all fields displayed accurately | 20 |
| try-with-resources used correctly for all JDBC objects | 10 |
| Submission completeness | 5 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a `SQLException` complaining about no
suitable driver, which almost always traces back to the SQLite JDBC `.jar`
not being present on this specific project's Build Path, since Build Path
settings are configured per project rather than globally across the entire
Eclipse workspace. Another frequent mistake is forgetting that `?`
placeholders are numbered starting at 1, not 0, which produces a confusing
"parameter index out of range" error if a student tries `setString(0, ...)`.
Students should also watch for `ResultSet` column name typos, such as
requesting `resultSet.getString("Name")` when the column was actually
created as `name`, since SQLite column names are generally treated in a
case-insensitive way, but a genuinely misspelled column name still produces
an `SQLException`. If a database file appears in an unexpected folder,
recheck the exact spelling and capitalization of the JDBC URL string.

## Review and Practice Questions

1. What does the JDBC URL `jdbc:sqlite:database/app.db` tell Java, piece by
   piece?
2. Why is `PreparedStatement` preferred over building SQL strings through
   direct concatenation of user input?
3. Explain what `resultSet.next()` does and why reading a `ResultSet` is
   typically written as a `while` loop.
4. Rewrite the "Task Query Demo" worked example so it instead queries and
   prints only *completed* tasks (`completed = 1`).
5. Why does `CREATE TABLE IF NOT EXISTS` matter for a program that might be
   run many times against the same database file?
6. A classmate's insert statement throws
   "SQLException: parameter index out of range: 2". What is the most likely
   mistake in their code?

## Lesson Summary

This week turned last week's database concepts into working Java code: a
`Connection` opened through `DriverManager` using a SQLite-specific JDBC
URL, `PreparedStatement` for safely inserting and querying parameterized
data, `ResultSet` for reading rows back into the program, and
try-with-resources for reliably closing every JDBC object. These four tools
form the technical foundation for the CRUD application built over the next
two weeks. Week 34 connects this JDBC code directly to a Swing form,
implementing a real data-entry screen backed by `INSERT` and `SELECT`
statements and displaying results in a `JTable`.
