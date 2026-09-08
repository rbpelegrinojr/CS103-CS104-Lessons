# Week 32: Introduction to Database Programming

## Main Topic
Database concepts, SQLite setup, tables, records, SQL basics, and JDBC
introduction.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Explain what a database is and why applications use one instead of plain
   text files for storing structured data.
2. Explain what SQLite is and why it is a good fit for a beginner desktop
   application.
3. Describe a table's structure in terms of columns, rows, and data types.
4. Write basic SQL statements: `CREATE TABLE`, `INSERT`, and `SELECT`.
5. Explain what JDBC is and how it allows a Java program to communicate with
   a database.
6. Add the SQLite JDBC driver to an Eclipse project's Build Path.

## Prerequisite Knowledge
Students must already be comfortable with file handling from Week 16 and
exception handling from Week 17, since database operations involve similar
resource-management ideas. No prior knowledge of databases, SQL, SQLite, or
JDBC is assumed; all of it is introduced from the beginning this week.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 15 min | Why applications need databases instead of plain text files |
| 2 | 20 min | Introducing SQLite and where its database file lives |
| 3 | 25 min | Tables, columns, rows, and SQL data types |
| 4 | 25 min | Basic SQL: CREATE TABLE, INSERT, SELECT |
| 5 | 20 min | Introducing JDBC and how Java talks to SQLite |
| 6 | 15 min | Eclipse setup: adding the SQLite JDBC driver to the Build Path |

## Detailed Lesson Discussion

Weeks 16 and 20 showed how a Java program can save data to a plain text
file so that it survives after the program closes. That approach works for
very simple needs, but it starts to break down as an application grows: text
files have no built-in way to search efficiently for a specific record,
no way to prevent two pieces of related data from becoming inconsistent with
each other, and no standard way to update or delete just one specific record
without rewriting the entire file. A **database** solves these problems by
organizing data into a structured format specifically designed for storing,
searching, updating, and deleting records efficiently and reliably.

This course uses **SQLite**, a lightweight database engine that stores an
entire database as a single ordinary file on disk, commonly given the `.db`
extension. Unlike larger database systems such as MySQL or PostgreSQL,
SQLite does not require installing and running a separate server program in
the background; the Java application simply opens the `.db` file directly,
the same way it might open a text file, except that the file's internal
format is structured specifically for fast, reliable data storage rather
than being plain readable text. This makes SQLite an excellent fit for a
self-contained desktop application, since the entire database travels with
the project as a single file rather than depending on a separately running
database server.

Inside a SQLite database, data is organized into **tables**. A table is
similar in concept to a spreadsheet: it has a fixed set of **columns**, each
with a name and a data type, and any number of **rows**, where each row
represents one individual record. For example, a `students` table might have
columns named `id`, `name`, and `year_level`, and each row in that table
would represent one specific student. SQLite supports a small set of core
column data types, most commonly `INTEGER` for whole numbers, `TEXT` for
strings, `REAL` for decimal numbers, and `BLOB` for raw binary data, which is
not needed in this course. Every table intended to store individually
identifiable records should include a column that uniquely identifies each
row, commonly named `id`, declared as
`id INTEGER PRIMARY KEY AUTOINCREMENT`, which tells SQLite to automatically
assign a new, unique whole number to that column every time a new row is
inserted, without the program needing to manage that numbering itself.

Communicating with a table is done using **SQL**, or Structured Query
Language, a specialized language designed specifically for describing and
manipulating data inside a database, quite different in style from the
Java code written so far in this course. A new table is created using a
`CREATE TABLE` statement, listing each column's name and type:

```sql
CREATE TABLE students (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    year_level TEXT NOT NULL
);
```

Adding a new row to that table is done using an `INSERT` statement, listing
the columns being filled in and the values to store in them:

```sql
INSERT INTO students (name, year_level) VALUES ('Ana Reyes', 'Sophomore');
```

Notice that `id` is not included in the `INSERT` statement, since
`AUTOINCREMENT` means SQLite assigns that value automatically. Reading data
back out of a table is done using a `SELECT` statement, which can retrieve
every row and every column using `SELECT * FROM students;`, or a more
specific subset, such as `SELECT name FROM students WHERE year_level =
'Sophomore';`, which limits the returned rows to only those matching the
given condition. These three statement types — `CREATE TABLE`, `INSERT`, and
`SELECT` — are the foundation this week focuses on; `UPDATE` and `DELETE`
statements, which modify or remove existing rows, are introduced in Week 35
once a full CRUD application is being built.

None of this SQL knowledge is useful to a Java program on its own unless
Java has some way to actually send these statements to the database and
receive results back. That connection is provided by **JDBC**, or Java
Database Connectivity, a standard part of Java specifically designed for
communicating with databases. JDBC itself is a general-purpose specification
supported by many different database systems; to actually talk to a SQLite
database specifically, a small additional library called the **SQLite JDBC
driver** is required, which translates JDBC's general database operations
into the specific format SQLite understands. This driver does not come
bundled with the standard Java Development Kit, which means it must be
downloaded separately and added to an Eclipse project's **Build Path**
before any JDBC code involving SQLite will compile or run correctly. Week 33
covers the specific Java classes used to open a connection and run SQL
statements from Java code; this week's focus is purely on understanding what
a database is, what SQL looks like, what JDBC is for, and getting the
required driver correctly installed in Eclipse so that next week's code will
actually run.

Beginning this week, every project involving a database in this course
stores its SQLite file at a consistent, predictable location inside the
Eclipse project folder: `database/app.db`, relative to the project's root
directory. Keeping the database file inside the project this way means the
whole project, code and data together, can be shared, copied, or submitted
as a single self-contained folder, which becomes especially important
starting with the final CRUD project in Weeks 34 and 35.

## Important Terminology

Key terms: **database**, **SQLite**, **table**, **column**, **row/record**,
**primary key**, **AUTOINCREMENT**, **SQL**, **CREATE TABLE**, **INSERT**,
**SELECT**, **JDBC**, and **SQLite JDBC driver**.

## Complete Java Programming Example

This week's example focuses on installing the driver and confirming the
connection works, rather than performing full CRUD operations, which begin
in Week 33. The following program only tests that Eclipse can locate and
load the SQLite JDBC driver class correctly.

```java
package week32.db;

/**
 * A minimal check that confirms the SQLite JDBC driver has been correctly
 * added to this project's Build Path. It does not yet open a real
 * database connection; that begins in Week 33.
 */
public class DriverCheck {

    public static void main(String[] args) {
        try {
            Class.forName("org.sqlite.JDBC");
            System.out.println("SQLite JDBC driver found successfully.");
        } catch (ClassNotFoundException e) {
            System.out.println("SQLite JDBC driver NOT found. "
                    + "Check that the .jar file was added to the Build Path.");
        }
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Before creating the project, download the SQLite JDBC driver `.jar` file
   (for example, `sqlite-jdbc-3.45.0.0.jar` or a similar current version)
   from the official Maven Central repository, and save it somewhere easy to
   find, such as a `libs` folder on the Desktop.
2. Open Eclipse and create a new Java project named `Week32-DatabaseIntro`.
3. Create a package named `week32.db`.
4. Create a class named `DriverCheck` and paste in the complete example
   above.
5. Right-click the project in the Package Explorer and choose
   **Build Path > Configure Build Path**.
6. In the Build Path dialog, select the **Libraries** tab.
7. Click **Classpath**, then click **Add External JARs...**.
8. Browse to the downloaded SQLite JDBC `.jar` file, select it, and click
   **Open**.
9. Click **Apply and Close** to save the Build Path change.
10. Save `DriverCheck.java` and run it using **Run As > Java Application**.
11. Observe the result in the Console view: it should print "SQLite JDBC
    driver found successfully."
12. As a deliberate test, remove the `.jar` file from the Build Path
    (repeat steps 5–6, select the entry under Libraries, and click
    **Remove**), run `DriverCheck` again, and confirm it now prints the
    "NOT found" message instead, demonstrating exactly what this error looks
    like before re-adding the `.jar` file to fix it.
13. Right-click the project, choose **New > Folder**, and create a folder
    named `database` at the project's root level, in preparation for storing
    `app.db` starting next week.

## Guided Worked Example

**Problem description:** Practice writing SQL statements on paper (or using
the DB Browser for SQLite tool, if available in the lab) for a small
"library books" table, reinforcing `CREATE TABLE`, `INSERT`, and `SELECT`
syntax before writing any Java code that uses them.

```sql
-- Create a table to store library book records
CREATE TABLE books (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    author TEXT NOT NULL,
    available INTEGER NOT NULL
);

-- Insert two sample records
INSERT INTO books (title, author, available)
VALUES ('The Old Man and the Sea', 'Ernest Hemingway', 1);

INSERT INTO books (title, author, available)
VALUES ('Noli Me Tangere', 'Jose Rizal', 0);

-- Retrieve every record
SELECT * FROM books;

-- Retrieve only the available books
SELECT title, author FROM books WHERE available = 1;
```

**Walkthrough:** The `available` column uses `INTEGER` rather than a true
boolean type, storing `1` for available and `0` for unavailable, because
SQLite does not have a dedicated boolean column type; this is a common and
accepted convention. The final `SELECT` statement demonstrates a `WHERE`
clause, which filters rows to only those matching a specific condition,
exactly the same logical idea as an `if` condition in Java, but expressed in
SQL's own syntax.

**Expected behavior:** Running the first `SELECT` statement returns both
book rows with all four columns; running the second returns only the title
and author of "The Old Man and the Sea", since only that row has
`available = 1`.

**Test data:** Add a third book with `available = 1` and confirm the
filtered `SELECT` statement now returns two rows instead of one.

**Likely errors:** Forgetting the comma between column definitions inside
`CREATE TABLE`, or forgetting to wrap text values in single quotes inside
`INSERT` and `WHERE` clauses, are the two most common SQL syntax mistakes
for beginners.

## Code Walkthrough

`DriverCheck` uses `Class.forName("org.sqlite.JDBC")`, a Java mechanism that
attempts to locate and load a class by its fully qualified name at runtime.
If the SQLite JDBC `.jar` file has been correctly added to the project's
Build Path, Java successfully finds the `org.sqlite.JDBC` class inside that
`.jar` file, and no exception is thrown. If the `.jar` file is missing from
the Build Path, Java cannot find that class anywhere, and throws a
`ClassNotFoundException`, which is caught and reported with a clear message
rather than allowing an unhandled stack trace to appear. This simple check
is a useful first step before writing any real connection code, since it
isolates and confirms the Eclipse configuration is correct independently
from the database logic itself, which begins next week.

## Expected GUI or Program Behavior

This week's program is a console application, not a GUI application, since
the focus is entirely on setting up the SQLite JDBC driver correctly.
Running `DriverCheck` after correctly configuring the Build Path prints a
single success message to the Eclipse Console; running it before the driver
is added, or after it has been removed, prints a clear failure message
instead.

## Laboratory Session (3 Hours)

### Laboratory Title
Setting Up SQLite and Writing Foundational SQL

### Objectives
1. Correctly add the SQLite JDBC driver to an Eclipse project's Build Path.
2. Write correct `CREATE TABLE`, `INSERT`, and `SELECT` statements for a
   given scenario.
3. Explain, in writing, the purpose of a primary key column.
4. Prepare a project folder structure ready for JDBC connection code in
   Week 33.

### Required Software and Materials
Eclipse IDE with a configured JDK, the SQLite JDBC driver `.jar` file, and
(optionally, if available in the lab) the DB Browser for SQLite tool for
visually inspecting SQL statements before writing Java code.

### Required Java Concepts
Basic class structure and the `main` method from Weeks 1–2; no prior
database knowledge is assumed, since this is the first database-focused
laboratory in the course.

### Development Requirements
Create an Eclipse project `Week32-Lab` with package `week32.lab` containing
the `DriverCheck` verification class from the lecture, plus a text file
named `schema.sql` (added to the project root, not compiled as Java code)
containing hand-written SQL statements for a fictional "course_catalog"
scenario: a `courses` table with columns `id` (primary key, autoincrement),
`course_code` (TEXT), `course_title` (TEXT), and `units` (INTEGER), along
with at least four `INSERT` statements populating sample course records, and
at least two different `SELECT` statements demonstrating a full-table query
and a filtered query using a `WHERE` clause.

### Detailed Laboratory Procedure

1. Create the project `Week32-Lab` and package `week32.lab`.
2. Add the SQLite JDBC driver `.jar` file to the Build Path following the
   exact steps from the lecture instructions.
3. Recreate `DriverCheck` in this new project and confirm it prints the
   success message.
4. Create a new file at the project root named `schema.sql` (right-click the
   project, **New > File**, and type the full file name including the
   `.sql` extension).
5. Write a `CREATE TABLE` statement for the `courses` table as described
   above.
6. Write four `INSERT` statements adding sample course records with
   realistic fictional data (for example, course codes like "CS101",
   "MATH201").
7. Write a `SELECT * FROM courses;` statement to retrieve every record.
8. Write a second `SELECT` statement using a `WHERE` clause to filter
   courses with `units` greater than or equal to 3.
9. If DB Browser for SQLite (or a similar tool) is available, create an
   actual `app.db` file, run each statement from `schema.sql` inside the
   tool, and confirm the table and its data appear correctly. If the tool is
   not available, this step may be completed as a written exercise instead,
   confirming the SQL syntax is correct by careful proofreading.
10. Create a `database` folder inside the project root, in preparation for
    storing `app.db` starting in Week 33.

### Required Features
- `DriverCheck` must run successfully and print the success message.
- `schema.sql` must contain a syntactically correct `CREATE TABLE` statement
  including a primary key column.
- `schema.sql` must contain at least four syntactically correct `INSERT`
  statements.
- `schema.sql` must contain at least two `SELECT` statements, one
  unfiltered and one using a `WHERE` clause.

### Testing Procedure
If a SQL tool is available, execute every statement in `schema.sql` in
order and confirm the table is created and populated correctly, and that
both `SELECT` statements return the expected rows. If no tool is available,
review each statement carefully against SQL syntax rules covered this week
and confirm with the instructor.

### Normal Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Run DriverCheck with driver installed | Run As Java Application | "found successfully" message |
| Execute CREATE TABLE statement | Run in SQL tool (if available) | `courses` table created with four columns |
| Execute unfiltered SELECT | Run in SQL tool (if available) | All four sample rows returned |

### Boundary Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Filtered SELECT with a boundary value | `WHERE units >= 3` when a course has exactly 3 units | That course is included in the result |
| Table with the minimum required columns | Four columns only, as specified | Table creates successfully with no extra columns |

### Invalid Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Run DriverCheck with the .jar removed from Build Path | Run As Java Application | "NOT found" message, no unhandled exception |
| Malformed INSERT statement (missing quote around text) | Attempt to run in SQL tool (if available) | SQL tool reports a syntax error, demonstrating the importance of matching quotes |

### Troubleshooting Guidance
If `DriverCheck` reports the driver was not found, first confirm the exact
`.jar` file was added under **Build Path > Libraries**, not simply copied
into the project folder without being registered on the Build Path. If a
`CREATE TABLE` statement fails, check for a missing comma between column
definitions or a missing closing parenthesis. If an `INSERT` statement
fails, check that every `TEXT` value is wrapped in single quotes and that
the number of values listed matches the number of columns listed. Column
name typos, such as writing `cours_code` instead of `course_code`, are also
a frequent source of errors that are easy to overlook when proofreading
quickly.

### Required Deliverables
1. Eclipse project `Week32-Lab` with the working `DriverCheck` class and the
   completed `schema.sql` file.
2. A screenshot of the Console output showing the successful driver check.
3. A short written explanation of what a primary key column is and why the
   `courses` table needs one.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| SQLite JDBC driver correctly added to Build Path; DriverCheck succeeds | 25 |
| CREATE TABLE statement correct and includes a primary key | 20 |
| Four INSERT statements syntactically correct with realistic data | 25 |
| Both SELECT statements correct, including the WHERE clause | 20 |
| Submission completeness and written explanation | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a `ClassNotFoundException` when running
`DriverCheck`, which almost always means the `.jar` file was added to the
wrong place, such as being copied into the project folder without being
registered through **Build Path > Configure Build Path > Libraries**, or
being added to a different project than the one being run. Students should
also watch for common SQL syntax mistakes: missing commas between column
definitions in `CREATE TABLE`, missing single quotes around text values,
and mismatched numbers of columns and values in an `INSERT` statement. It is
useful to remind students that SQL syntax errors are reported by whichever
tool executes the SQL, not by the Eclipse Java compiler, since `schema.sql`
is a plain text file this week and is not compiled as Java code; syntax
mistakes will only be caught when the statements are actually executed.

## Review and Practice Questions

1. What problem does a database solve that a plain text file does not?
2. Why is SQLite particularly well suited to a simple desktop application
   compared to a database system that requires running a separate server?
3. What is the purpose of declaring a column as
   `INTEGER PRIMARY KEY AUTOINCREMENT`?
4. Write a `CREATE TABLE` statement for a fictional "movies" table with
   columns for id, title, genre, and release year.
5. Write an `INSERT` statement adding one sample movie record to the table
   from the previous question.
6. What is JDBC, and why is an additional driver `.jar` file needed
   specifically for SQLite, even though JDBC itself is part of standard
   Java?

## Lesson Summary

This week introduced the foundational database concepts needed before any
Java database code can be written: what a database and a table are, how
SQLite stores an entire database as a single file, how basic SQL statements
create tables and insert and retrieve records, and what JDBC is for. Students
also completed the essential Eclipse setup step of adding the SQLite JDBC
driver to a project's Build Path, a step that must be repeated correctly in
every future project involving SQLite. Week 33 builds directly on this
foundation by writing the actual Java code that opens a connection to a
SQLite database and executes SQL statements from within a Swing application.
