# Week 34: CRUD Application I

## Main Topic
`INSERT` and `SELECT`, a registration/data-entry form, and displaying SQLite
records using `JTable`.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Connect a Swing data-entry form to a SQLite database using JDBC.
2. Insert validated form data into a database table using
   `PreparedStatement`.
3. Query all records from a table and load them into a `JTable` using
   `DefaultTableModel`.
4. Refresh a `JTable`'s contents after a new record is inserted.
5. Combine everything learned since Week 23 — components, layouts, events,
   validation, and JDBC — into a single working application.

## Prerequisite Knowledge
Students must already be comfortable validating form input (Week 31),
opening a SQLite connection, and using `PreparedStatement` and `ResultSet`
(Week 33). Students must also recall `JTable` from Week 30. No prior
knowledge of `DefaultTableModel` or connecting live database data to a
`JTable` is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Week 33's JDBC mechanics |
| 2 | 20 min | Designing the registration form and its backing table |
| 3 | 25 min | Wiring the Save button to a validated INSERT statement |
| 4 | 20 min | `DefaultTableModel`: loading SELECT results into a JTable |
| 5 | 20 min | Refreshing the table after every insert |
| 6 | 25 min | Live coding: the complete registration + table display application |

## Detailed Lesson Discussion

Every piece needed to build a real data-entry application has now been
introduced separately: Swing components and layouts from Weeks 23–27, event
handling from Week 26, validation from Week 31, and JDBC operations from
Weeks 32–33. This week combines all of them into a single, complete
application: a form that saves records into SQLite, and a table that
displays every record currently stored in the database.

The application built this week centers on a `students` table, containing an
`id` (primary key, autoincrement), a `name` (TEXT), and a `year_level`
(TEXT). The Swing form collects a name and a year level, validates that
neither is blank, and, once valid, inserts a new row using a
`PreparedStatement`, following exactly the pattern practiced in Week 33.
What is new this week is connecting that insert operation to a live,
on-screen `JTable` that immediately reflects the newly added record, without
requiring the application to be restarted.

Rather than constructing a `JTable` directly from a fixed `Object[][]` array,
as done in Week 30, a `JTable` that needs to be refreshed while the program
is running is built using a `DefaultTableModel`, a class specifically
designed to hold a table's data separately from the visual `JTable`
component itself, and to notify the table automatically whenever that data
changes. A `DefaultTableModel` is created with a set of column names and
starts with zero rows: `new DefaultTableModel(new String[] {"ID", "Name",
"Year Level"}, 0)`. This model object is then passed into the `JTable`
constructor: `new JTable(tableModel)`. Rows are added to the model, not
directly to the table, using `tableModel.addRow(new Object[] {id, name,
yearLevel})`, and the `JTable` automatically redraws itself to show the new
row as soon as `addRow` is called, because the table is listening to changes
happening inside its model.

Loading the table with existing data from the database follows a two-step
pattern: first, clear the model of any previously loaded rows using
`tableModel.setRowCount(0)`, which removes every existing row without
needing to rebuild the model object itself; second, run a `SELECT * FROM
students` query, and for every row returned by the `ResultSet`, call
`tableModel.addRow(...)` with that row's values. This "clear, then reload"
pattern is what makes it possible to refresh the table's contents at any
point while the program runs — most importantly, immediately after a new
record has been successfully inserted, so the user sees their new record
appear in the table without needing to close and reopen the application. A
private method such as `refreshTable()` is a natural place to hold this
logic, since it is called both once when the window first opens and again
every time a new record is successfully saved.

Structuring the whole application well matters as much this week as the
individual pieces do. A clean design separates the form section, typically
built with a `GridLayout` inside a `JPanel` as practiced in Week 27, from the
table section, wrapped in a `JScrollPane` as practiced in Week 30, combined
using `BorderLayout` on the frame itself — the form in the `NORTH` region and
the table in the `CENTER` region, for example. Keeping the database logic in
its own clearly named private methods, separate from the Swing construction
code in the constructor, also keeps the class readable as it grows; this
organizational pattern — a `saveRecord()` method and a `refreshTable()`
method, each opening and closing its own connection — will be extended
directly in Week 35 to add update, delete, and search operations to this
same application.

## Important Terminology

Key terms: **DefaultTableModel**, **addRow()**, **setRowCount(0)**,
**refreshTable pattern**, and **form-to-database-to-table pipeline**.

## Complete Java Programming Example

```java
package week34.gui;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.JTextField;
import javax.swing.table.DefaultTableModel;
import java.awt.BorderLayout;
import java.awt.GridLayout;

/**
 * A student registration form that inserts records into a SQLite database
 * and displays every stored record in a JTable, refreshed after each save.
 */
public class StudentRegistrationApp extends JFrame {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    private final JTextField nameField;
    private final JTextField yearLevelField;
    private final DefaultTableModel tableModel;
    private final JTable studentTable;

    public StudentRegistrationApp() {
        super("Student Registration");
        setSize(500, 420);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10));

        createTableIfNeeded();

        JPanel formPanel = new JPanel(new GridLayout(3, 2, 8, 8));
        formPanel.add(new JLabel("Full Name:"));
        nameField = new JTextField();
        formPanel.add(nameField);

        formPanel.add(new JLabel("Year Level:"));
        yearLevelField = new JTextField();
        formPanel.add(yearLevelField);

        JButton saveButton = new JButton("Save Student");
        formPanel.add(new JLabel());   // empty spacer cell
        formPanel.add(saveButton);

        add(formPanel, BorderLayout.NORTH);

        tableModel = new DefaultTableModel(
                new String[] {"ID", "Name", "Year Level"}, 0);
        studentTable = new JTable(tableModel);
        add(new JScrollPane(studentTable), BorderLayout.CENTER);

        saveButton.addActionListener(e -> saveStudent());

        refreshTable();

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void createTableIfNeeded() {
        String sql = "CREATE TABLE IF NOT EXISTS students ("
                + "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                + "name TEXT NOT NULL, "
                + "year_level TEXT NOT NULL)";

        try (Connection connection = DriverManager.getConnection(DB_URL);
             Statement statement = connection.createStatement()) {
            statement.execute(sql);
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not prepare the database: " + e.getMessage(),
                    "Database Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void saveStudent() {
        String name = nameField.getText().trim();
        String yearLevel = yearLevelField.getText().trim();

        if (name.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Full name is required.");
            return;
        }
        if (yearLevel.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Year level is required.");
            return;
        }

        String sql = "INSERT INTO students (name, year_level) VALUES (?, ?)";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, name);
            statement.setString(2, yearLevel);
            statement.executeUpdate();

            nameField.setText("");
            yearLevelField.setText("");
            refreshTable();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not save the student: " + e.getMessage(),
                    "Database Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void refreshTable() {
        tableModel.setRowCount(0);

        String sql = "SELECT id, name, year_level FROM students ORDER BY id";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql);
             ResultSet resultSet = statement.executeQuery()) {

            while (resultSet.next()) {
                tableModel.addRow(new Object[] {
                    resultSet.getInt("id"),
                    resultSet.getString("name"),
                    resultSet.getString("year_level")
                });
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not load students: " + e.getMessage(),
                    "Database Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    public static void main(String[] args) {
        new StudentRegistrationApp();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week34-StudentRegistration`.
2. Add the SQLite JDBC driver `.jar` to the Build Path exactly as in Weeks
   32–33.
3. Create a `database` folder at the project root.
4. Create a package named `week34.gui`.
5. Create the class `StudentRegistrationApp` and paste in the complete
   example.
6. Save the file and resolve imports with **Ctrl+Shift+O**.
7. Run the class using **Run As > Java Application**.
8. Confirm the window opens with an empty form at the top and an empty
   table (with three column headers) below it.
9. Enter a name and year level, click **Save Student**, and confirm the new
   row immediately appears in the table below, and that both text fields
   clear automatically.
10. Repeat step 9 two more times with different sample data, confirming each
    new row appears and the table now shows three rows in total.
11. Leave the name field blank and click **Save Student**; confirm the
    "Full name is required" dialog appears and no new row is added.
12. Close the application entirely and run it again; confirm the table
    still shows all three previously saved records, proving the data was
    truly persisted to `database/app.db` rather than only kept in memory.

## Guided Worked Example

**Problem description:** Build a smaller "Book Log" application containing
only a title field, an author field, an Add button, and a table showing all
logged books, to reinforce the exact same insert-then-refresh pattern with
different data.

```java
package week34.gui;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.JTextField;
import javax.swing.table.DefaultTableModel;

public class BookLogApp extends JFrame {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    private final JTextField titleField;
    private final JTextField authorField;
    private final DefaultTableModel tableModel;

    public BookLogApp() {
        super("Book Log");
        setSize(450, 380);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        createTableIfNeeded();

        JPanel formPanel = new JPanel();
        formPanel.add(new JLabel("Title:"));
        titleField = new JTextField(15);
        formPanel.add(titleField);
        formPanel.add(new JLabel("Author:"));
        authorField = new JTextField(15);
        formPanel.add(authorField);
        JButton addButton = new JButton("Add Book");
        formPanel.add(addButton);
        add(formPanel, java.awt.BorderLayout.NORTH);

        tableModel = new DefaultTableModel(new String[] {"ID", "Title", "Author"}, 0);
        JTable bookTable = new JTable(tableModel);
        add(new JScrollPane(bookTable), java.awt.BorderLayout.CENTER);

        addButton.addActionListener(e -> addBook());
        refreshTable();

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void createTableIfNeeded() {
        try (Connection c = DriverManager.getConnection(DB_URL);
             Statement s = c.createStatement()) {
            s.execute("CREATE TABLE IF NOT EXISTS books ("
                    + "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                    + "title TEXT NOT NULL, author TEXT NOT NULL)");
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Setup error: " + e.getMessage());
        }
    }

    private void addBook() {
        String title = titleField.getText().trim();
        String author = authorField.getText().trim();
        if (title.isEmpty() || author.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Title and author are both required.");
            return;
        }

        String sql = "INSERT INTO books (title, author) VALUES (?, ?)";
        try (Connection c = DriverManager.getConnection(DB_URL);
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setString(1, title);
            ps.setString(2, author);
            ps.executeUpdate();
            titleField.setText("");
            authorField.setText("");
            refreshTable();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Insert error: " + e.getMessage());
        }
    }

    private void refreshTable() {
        tableModel.setRowCount(0);
        String sql = "SELECT id, title, author FROM books ORDER BY id";
        try (Connection c = DriverManager.getConnection(DB_URL);
             PreparedStatement ps = c.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {
            while (rs.next()) {
                tableModel.addRow(new Object[] {
                    rs.getInt("id"), rs.getString("title"), rs.getString("author")
                });
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Load error: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        new BookLogApp();
    }
}
```

**Walkthrough:** This example mirrors `StudentRegistrationApp` exactly in
structure — setup, add, refresh — with different table and column names,
demonstrating that the pattern generalizes to any similar record-keeping
application rather than being specific to student data.

**Expected behavior:** Adding several books shows them all listed in the
table, most recently added at the bottom, ordered by `id`.

**Test data:** Adding a book titled `Dune` by `Frank Herbert` followed by
`1984` by `George Orwell` should show both rows with sequential `id` values.

**Likely errors:** If the table never updates after clicking Add, confirm
`refreshTable()` is actually being called at the end of `addBook()`, after
the insert succeeds.

## Code Walkthrough

`StudentRegistrationApp` calls `createTableIfNeeded()` once, early in the
constructor, ensuring the `students` table always exists before any other
database operation is attempted. The form is built using `GridLayout(3, 2, 8,
8)`, matching the technique from Week 27, with an empty spacer label used to
keep the Save button aligned in the second column rather than the first.
`saveStudent()` follows the exact validation-then-insert pattern from Week
31 combined with Week 33's JDBC pattern: check for blank fields first, then
attempt the parameterized insert inside try-with-resources, and only clear
the fields and refresh the table if the insert actually succeeds.
`refreshTable()` always begins with `tableModel.setRowCount(0)`, which is
essential — without it, every call to `refreshTable()` would keep appending
duplicate rows on top of whatever was already displayed, rather than
showing a clean, accurate reflection of the current database contents.

## Expected GUI or Program Behavior

Running `StudentRegistrationApp` shows an empty form above an empty table.
Entering valid data and clicking Save Student clears the form, and the new
student immediately appears as a new row in the table with an
automatically assigned `id`. Restarting the application shows all
previously saved students still present, since the data lives in
`database/app.db` rather than only in the program's memory.

## Laboratory Session (3 Hours)

### Laboratory Title
Building a Complete Course Catalog Registration Application

### Objectives
1. Build a complete Swing + SQLite application combining a validated
   data-entry form with a live, refreshing `JTable`.
2. Correctly implement the create-table, insert, and refresh-table
   operations using JDBC.
3. Confirm that data persists correctly across separate program runs.

### Required Software and Materials
Eclipse IDE with a configured JDK and the SQLite JDBC driver `.jar` added to
the Build Path.

### Required Java Concepts
Everything from Weeks 23–33: components, layouts, event handling,
validation, and JDBC connections, statements, and result sets.

### Development Requirements
Create an Eclipse project `Week34-Lab` with package `week34.lab` containing
a class `CourseCatalogApp` that manages a `courses` table (columns: `id`
primary key autoincrement, `course_code` TEXT, `course_title` TEXT, `units`
INTEGER), with a form collecting course code, course title, and units, an
"Add Course" button that validates all three fields (course code and title
non-empty, units a valid whole number between 1 and 6), and a `JTable`
displaying every course currently in the database, refreshed after every
successful insert.

### Detailed Laboratory Procedure

1. Create the project, add the SQLite JDBC driver to the Build Path, and
   create the `database` folder at the project root.
2. Create the package `week34.lab` and the class `CourseCatalogApp`.
3. Write `createTableIfNeeded()`, creating the `courses` table with the four
   columns described above.
4. Build the form using a `GridLayout`, with fields for course code, course
   title, and units, and an "Add Course" button.
5. Build the table using a `DefaultTableModel` with columns "ID", "Course
   Code", "Course Title", and "Units", wrapped in a `JScrollPane`.
6. Write the button's event handler: validate course code and title are not
   blank; validate units can be parsed as an integer using `try-catch`;
   validate units is between 1 and 6; if all checks pass, insert the record
   using a `PreparedStatement` and refresh the table.
7. Write `refreshTable()` following the clear-then-reload pattern shown in
   lecture.
8. Call `createTableIfNeeded()` and `refreshTable()` appropriately when the
   window is constructed.
9. Run the application and add at least four different fictional courses,
   confirming each appears correctly in the table.
10. Test each invalid case listed below, confirming the correct message
    appears and no invalid row is added to the table.
11. Close and restart the application, confirming all four courses are
    still present.

### Required Features
- The courses table must be created automatically if it does not already
  exist.
- All three fields must be validated before any insert is attempted.
- The table must refresh automatically and immediately after every
  successful insert.
- Data must persist correctly across application restarts.

### Testing Procedure
Test the application by adding several valid courses, attempting several
invalid entries, and restarting the application to confirm persistence.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Valid course | Code `CS101`, title `Intro to Programming`, units `3` | Click Add Course | New row appears in table; fields clear |
| Multiple valid courses | Add four different courses in sequence | Click Add Course each time | All four appear, in order, with sequential IDs |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Minimum valid units | Units `1` | Click Add Course | Accepted |
| Maximum valid units | Units `6` | Click Add Course | Accepted |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Empty course code | Leave code blank | Click Add Course | Validation message; no row added |
| Non-numeric units | Units `three` | Click Add Course | Validation message; no row added |
| Units out of range | Units `9` | Click Add Course | Validation message; no row added |
| Restart and reload | Close and reopen the app after adding courses | N/A | All previously added courses still appear in the table |

### Troubleshooting Guidance
If the table never shows any rows even after a successful-looking insert,
confirm `refreshTable()` is called immediately after `executeUpdate()`
succeeds, and confirm `setRowCount(0)` is the first line inside
`refreshTable()`. If duplicate-looking rows appear after several inserts,
this typically means `setRowCount(0)` was accidentally omitted. If the
application throws an `SQLException` on startup, confirm the `database`
folder actually exists at the project root, since some operating systems do
not automatically create missing parent folders for a SQLite file path. If
inserted data does not persist after restarting the application, confirm
the JDBC URL string is identical, character for character, in every method
that opens a connection.

### Required Deliverables
1. Eclipse project `Week34-Lab` with the completed `CourseCatalogApp` class.
2. A screenshot showing at least four courses successfully added to the
   table.
3. A short written explanation of what would happen if
   `tableModel.setRowCount(0)` were removed from `refreshTable()`.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Table created correctly with all required columns | 10 |
| Form validation for all three fields (blank, numeric, range) | 25 |
| PreparedStatement insert correctly implemented | 20 |
| JTable correctly refreshes after every successful insert | 20 |
| Data persists correctly across application restarts | 15 |
| Submission completeness | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a table that appears empty even after
a successful insert; this is almost always because `refreshTable()` was
never called after `executeUpdate()`, or because a separate, incorrect
database URL is accidentally used somewhere in the class. Another frequent
issue is duplicate rows appearing after repeated saves, which happens when
`tableModel.setRowCount(0)` is missing from the beginning of
`refreshTable()`. If the whole application throws an exception on startup
before the window even appears, check that `createTableIfNeeded()` is
actually being called, and that the `database` folder exists so SQLite has
somewhere valid to create `app.db`. Students should also re-confirm that
their event handler validates all fields *before* attempting the insert, in
the same fail-fast style practiced in Week 31, so that an invalid course
never reaches the database at all.

## Review and Practice Questions

1. Why is data stored in a `DefaultTableModel` rather than added directly to
   a `JTable`?
2. Explain exactly what happens, step by step, from the moment the user
   clicks "Save Student" to the moment the new row appears in the table.
3. Why must `tableModel.setRowCount(0)` be called at the start of
   `refreshTable()`?
4. Rewrite the "Book Log" worked example so it also validates that the
   title field is no longer than 100 characters, displaying an appropriate
   message if it is.
5. Why does closing and reopening `StudentRegistrationApp` still show all
   previously saved students?
6. A classmate's Save button adds a database row correctly (confirmed using
   a SQL tool) but the table on screen never updates. What is the most
   likely missing line of code?

## Lesson Summary

This week combined every skill built since Week 23 into a complete,
functioning application: a validated Swing form that inserts records into a
SQLite database using `PreparedStatement`, and a `JTable`, backed by a
`DefaultTableModel`, that displays and refreshes to show every record
currently stored. This insert-and-display pattern is the foundation of the
final CRUD application. Week 35 extends this exact application with update,
delete, and search functionality, completing the full CRUD (Create, Read,
Update, Delete) feature set required for the final project.
