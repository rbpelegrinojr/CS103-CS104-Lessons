# Week 35: CRUD Application II / Final Project

## Main Topic
`UPDATE`, `DELETE`, search, and an integrated Java Swing + SQLite CRUD
application.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Update an existing database record using `PreparedStatement` and an
   `UPDATE` statement.
2. Delete an existing database record using `PreparedStatement` and a
   `DELETE` statement.
3. Search for records matching a partial keyword using a parameterized
   `LIKE` clause.
4. Select a row in a `JTable` and load its values back into the form for
   editing.
5. Combine `INSERT`, `SELECT`, `UPDATE`, `DELETE`, and search into a single,
   complete CRUD application ready to serve as the final project foundation.

## Prerequisite Knowledge
Students must already have a working insert-and-display application from
Week 34, including `createTableIfNeeded()`, a validated form, a
`PreparedStatement`-based insert method, and a `refreshTable()` method
backed by `DefaultTableModel`. No prior knowledge of `UPDATE`, `DELETE`, or
row-selection handling is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Week 34's insert-and-display application |
| 2 | 20 min | Selecting a table row and loading it back into the form |
| 3 | 25 min | `UPDATE` statements with `PreparedStatement` |
| 4 | 20 min | `DELETE` statements with confirmation dialogs |
| 5 | 20 min | Searching records using a parameterized `LIKE` clause |
| 6 | 25 min | Live coding: completing the full CRUD student registration application |

## Detailed Lesson Discussion

Week 34 produced an application that could create and read records — the
"C" and "R" of CRUD. This week completes the remaining two operations,
Update and Delete, and adds search functionality, turning last week's
application into a fully functional CRUD system: Create, Read, Update, and
Delete.

The first new piece needed is a way to select an existing record from the
table and load its data back into the form so it can be edited. A `JTable`
reports which row is currently selected through
`studentTable.getSelectedRow()`, which returns the row's index within the
*visible* table (starting at 0), or `-1` if nothing is currently selected.
This index can then be used to read that row's values directly out of the
`DefaultTableModel` using `tableModel.getValueAt(selectedRow, columnIndex)`,
again with columns numbered from 0. A `ListSelectionListener`, registered
using `studentTable.getSelectionModel().addListSelectionListener(...)`, is
the standard way to detect when the user clicks a different row, although
for this course's needs, it is equally acceptable and simpler to read the
selected row only at the moment an Update or Delete button is clicked,
avoiding an additional listener type. When a record is loaded into the form
for editing, the record's database `id` must also be remembered, typically
in a private instance variable such as `selectedStudentId`, since the `id`
is what identifies exactly which row to modify or remove in the database,
even though the `id` column itself is not something the user directly
types into a text field.

An `UPDATE` statement modifies an existing row's column values without
changing its `id`, using the same parameter-placeholder style already
familiar from `INSERT`:

```sql
UPDATE students SET name = ?, year_level = ? WHERE id = ?;
```

In Java, this becomes a `PreparedStatement` with three parameters set in
order — the new name, the new year level, and finally the `id` of the
record being changed, which is why the `WHERE id = ?` clause is essential;
without it, an `UPDATE` statement would apply to *every* row in the table
rather than just the one intended. Executing an `UPDATE` uses
`executeUpdate()`, exactly like `INSERT`, and the returned `int` reports how
many rows were actually affected, which can be used to confirm the update
found a matching row rather than silently doing nothing.

A `DELETE` statement removes an entire row and follows the same
`WHERE`-clause safety principle:

```sql
DELETE FROM students WHERE id = ?;
```

Because deleting a record is destructive and cannot be undone within the
application, it is standard practice to confirm the action with the user
first using `JOptionPane.showConfirmDialog(...)`, as learned in Week 29,
before actually executing the `DELETE` statement. Skipping this confirmation
step risks a user accidentally removing a record with a single misplaced
click, which is a poor and risky user experience for any real application.

Searching records by a partial keyword uses SQL's `LIKE` operator combined
with the wildcard character `%`, which matches any sequence of zero or more
characters. A query such as
`SELECT id, name, year_level FROM students WHERE name LIKE ?` combined with
setting the parameter to `"%" + keyword + "%"` will match any student whose
name *contains* the given keyword anywhere within it, not only names that
begin with it. Building the wildcard pattern this way, by concatenating `%`
onto a value that is still passed in as a single `PreparedStatement`
parameter, keeps the search safely parameterized; the keyword itself is
never inserted directly into the SQL text, only into the *value* passed to
`setString(...)`, preserving all of the safety benefits of
`PreparedStatement` discussed in Week 33 even while building a dynamic
search pattern.

With select-to-edit, update, delete, and search all in place, alongside the
insert and refresh functionality from Week 34, the application now supports
every operation required of a genuine CRUD system, and represents the
complete technical foundation that the final project, due at the end of
Week 36, will build upon. From this point forward, extending the
application further mostly involves adding more fields, more tables, or
refining its visual design, rather than learning fundamentally new
techniques.

## Important Terminology

Key terms: **UPDATE**, **DELETE**, **WHERE clause safety**,
**getSelectedRow()**, **getValueAt()**, **LIKE operator**, **wildcard (%)**,
and **CRUD (Create, Read, Update, Delete)**.

## Complete Java Programming Example

This example extends the `StudentRegistrationApp` built in Week 34 with
edit, delete, and search functionality, forming the complete CRUD
application.

```java
package week35.gui;

import java.awt.BorderLayout;
import java.awt.GridLayout;
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

/**
 * A complete Swing + SQLite CRUD application for managing student
 * records: create, read, update, delete, and keyword search.
 */
public class StudentCrudApp extends JFrame {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    private final JTextField nameField;
    private final JTextField yearLevelField;
    private final JTextField searchField;
    private final DefaultTableModel tableModel;
    private final JTable studentTable;

    private Integer selectedStudentId = null;   // null means "no record selected"

    public StudentCrudApp() {
        super("Student CRUD Application");
        setSize(600, 480);
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

        JButton saveButton = new JButton("Save (Add or Update)");
        JButton clearButton = new JButton("Clear Form");
        JPanel buttonRow = new JPanel();
        buttonRow.add(saveButton);
        buttonRow.add(clearButton);

        JPanel northPanel = new JPanel(new BorderLayout());
        northPanel.add(formPanel, BorderLayout.CENTER);
        northPanel.add(buttonRow, BorderLayout.SOUTH);
        add(northPanel, BorderLayout.NORTH);

        tableModel = new DefaultTableModel(
                new String[] {"ID", "Name", "Year Level"}, 0) {
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;   // table is for display and selection only
            }
        };
        studentTable = new JTable(tableModel);
        add(new JScrollPane(studentTable), BorderLayout.CENTER);

        JPanel southPanel = new JPanel();
        searchField = new JTextField(15);
        JButton searchButton = new JButton("Search");
        JButton showAllButton = new JButton("Show All");
        JButton deleteButton = new JButton("Delete Selected");
        southPanel.add(new JLabel("Search Name:"));
        southPanel.add(searchField);
        southPanel.add(searchButton);
        southPanel.add(showAllButton);
        southPanel.add(deleteButton);
        add(southPanel, BorderLayout.SOUTH);

        saveButton.addActionListener(e -> saveStudent());
        clearButton.addActionListener(e -> clearForm());
        searchButton.addActionListener(e -> searchStudents());
        showAllButton.addActionListener(e -> refreshTable());
        deleteButton.addActionListener(e -> deleteSelectedStudent());
        studentTable.getSelectionModel().addListSelectionListener(e -> {
            if (!e.getValueIsAdjusting()) {
                loadSelectedRowIntoForm();
            }
        });

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
                    "Could not prepare the database: " + e.getMessage());
        }
    }

    private void loadSelectedRowIntoForm() {
        int row = studentTable.getSelectedRow();
        if (row == -1) {
            return;
        }
        selectedStudentId = (Integer) tableModel.getValueAt(row, 0);
        nameField.setText((String) tableModel.getValueAt(row, 1));
        yearLevelField.setText((String) tableModel.getValueAt(row, 2));
    }

    private void clearForm() {
        selectedStudentId = null;
        nameField.setText("");
        yearLevelField.setText("");
        studentTable.clearSelection();
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

        if (selectedStudentId == null) {
            insertStudent(name, yearLevel);
        } else {
            updateStudent(selectedStudentId, name, yearLevel);
        }
    }

    private void insertStudent(String name, String yearLevel) {
        String sql = "INSERT INTO students (name, year_level) VALUES (?, ?)";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, name);
            statement.setString(2, yearLevel);
            statement.executeUpdate();
            clearForm();
            refreshTable();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not add the student: " + e.getMessage());
        }
    }

    private void updateStudent(int id, String name, String yearLevel) {
        String sql = "UPDATE students SET name = ?, year_level = ? WHERE id = ?";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, name);
            statement.setString(2, yearLevel);
            statement.setInt(3, id);
            int rowsAffected = statement.executeUpdate();

            if (rowsAffected == 0) {
                JOptionPane.showMessageDialog(this,
                        "No matching record was found to update.");
            } else {
                clearForm();
                refreshTable();
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not update the student: " + e.getMessage());
        }
    }

    private void deleteSelectedStudent() {
        if (selectedStudentId == null) {
            JOptionPane.showMessageDialog(this,
                    "Please select a student in the table first.");
            return;
        }

        int confirm = JOptionPane.showConfirmDialog(this,
                "Delete the selected student? This cannot be undone.",
                "Confirm Delete", JOptionPane.YES_NO_OPTION);
        if (confirm != JOptionPane.YES_OPTION) {
            return;
        }

        String sql = "DELETE FROM students WHERE id = ?";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setInt(1, selectedStudentId);
            statement.executeUpdate();
            clearForm();
            refreshTable();
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Could not delete the student: " + e.getMessage());
        }
    }

    private void searchStudents() {
        String keyword = searchField.getText().trim();
        tableModel.setRowCount(0);

        String sql = "SELECT id, name, year_level FROM students "
                + "WHERE name LIKE ? ORDER BY id";
        try (Connection connection = DriverManager.getConnection(DB_URL);
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setString(1, "%" + keyword + "%");
            try (ResultSet resultSet = statement.executeQuery()) {
                boolean found = false;
                while (resultSet.next()) {
                    found = true;
                    tableModel.addRow(new Object[] {
                        resultSet.getInt("id"),
                        resultSet.getString("name"),
                        resultSet.getString("year_level")
                    });
                }
                if (!found) {
                    JOptionPane.showMessageDialog(this,
                            "No students matched that search.");
                }
            }
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this,
                    "Search error: " + e.getMessage());
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
                    "Could not load students: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        new StudentCrudApp();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week35-StudentCrud`.
2. Add the SQLite JDBC driver `.jar` to the Build Path.
3. Create a `database` folder at the project root (or reuse the one from
   Week 34's project if continuing the same project).
4. Create a package named `week35.gui`.
5. Create the class `StudentCrudApp` and paste in the complete example.
6. Save and resolve imports with **Ctrl+Shift+O**.
7. Run the class using **Run As > Java Application**.
8. Add two or three students using the form and Save button, confirming
   they appear in the table.
9. Click a row in the table and confirm the name and year level fields
   populate automatically with that row's data.
10. Change the year level in the form and click **Save (Add or Update)**;
    confirm the table updates that same row rather than adding a new one.
11. Click **Clear Form**, confirm both fields empty and the table
    selection clears, then click **Save** again with new data and confirm
    it adds a brand-new row instead of updating anything.
12. Select a row and click **Delete Selected**; confirm a confirmation
    dialog appears, and clicking **Yes** removes that row from the table.
13. Type part of a student's name into the search field and click
    **Search**; confirm only matching students appear.
14. Click **Show All** and confirm every remaining student reappears.
15. As a deliberate test, click **Save (Add or Update)** with a row
    currently selected but after manually clearing `selectedStudentId` in
    the debugger or by clicking **Clear Form** first — confirm this
    correctly performs an insert rather than an update, verifying the
    `selectedStudentId == null` branching logic.

## Guided Worked Example

**Problem description:** Demonstrate the update and delete logic in
isolation using a simplified "Note" table with just an `id` and a `text`
column, to make the WHERE-clause safety concept especially clear with a
minimal example.

```java
package week35.gui;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;

public class NoteUpdateDeleteDemo {

    private static final String DB_URL = "jdbc:sqlite:database/app.db";

    public static void main(String[] args) {
        setup();
        updateNote(1, "Updated note text");
        deleteNote(2);
    }

    private static void setup() {
        try (Connection c = DriverManager.getConnection(DB_URL);
             Statement s = c.createStatement()) {
            s.execute("CREATE TABLE IF NOT EXISTS notes ("
                    + "id INTEGER PRIMARY KEY AUTOINCREMENT, text TEXT NOT NULL)");
        } catch (SQLException e) {
            System.out.println("Setup error: " + e.getMessage());
        }
    }

    private static void updateNote(int id, String newText) {
        String sql = "UPDATE notes SET text = ? WHERE id = ?";
        try (Connection c = DriverManager.getConnection(DB_URL);
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setString(1, newText);
            ps.setInt(2, id);
            int rows = ps.executeUpdate();
            System.out.println(rows + " row(s) updated.");
        } catch (SQLException e) {
            System.out.println("Update error: " + e.getMessage());
        }
    }

    private static void deleteNote(int id) {
        String sql = "DELETE FROM notes WHERE id = ?";
        try (Connection c = DriverManager.getConnection(DB_URL);
             PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setInt(1, id);
            int rows = ps.executeUpdate();
            System.out.println(rows + " row(s) deleted.");
        } catch (SQLException e) {
            System.out.println("Delete error: " + e.getMessage());
        }
    }
}
```

**Walkthrough:** Both `updateNote` and `deleteNote` always include a
`WHERE id = ?` clause with the `id` supplied as a parameter, ensuring each
operation affects exactly one specific row rather than the entire table.
The printed row count confirms whether a matching record was actually
found; a result of `0` would indicate the given `id` did not exist.

**Expected behavior:** If a note with `id = 1` exists, running this program
updates its text and prints "1 row(s) updated."; if a note with `id = 2`
exists, it is removed and the program prints "1 row(s) deleted.".

**Test data:** Running this program with an `id` that does not exist in the
table (such as `999`) should print "0 row(s) updated." or "0 row(s)
deleted.", demonstrating that `executeUpdate()`'s return value is a
reliable way to detect a missing record.

**Likely errors:** Omitting the `WHERE` clause entirely from either
statement would update or delete every row in the table, which is one of
the most serious mistakes possible in database programming.

## Code Walkthrough

`StudentCrudApp` introduces the `selectedStudentId` field, initialized to
`null`, which acts as the switch between insert and update behavior:
`saveStudent()` checks whether it is `null` and calls either
`insertStudent(...)` or `updateStudent(...)` accordingly, meaning the same
Save button serves both purposes depending on whether a row is currently
selected. `loadSelectedRowIntoForm()`, triggered by a
`ListSelectionListener` on the table, reads the selected row's `id`, name,
and year level directly out of the `DefaultTableModel` using
`getValueAt(row, column)` and copies them into both the form fields and
`selectedStudentId`. Overriding `isCellEditable(...)` to always return
`false` on the table model prevents the user from double-clicking directly
into a table cell and typing over it, which would create a confusing
mismatch between the table's displayed data and the actual database
contents; all editing in this application is intentionally routed through
the form instead. `deleteSelectedStudent()` requires a confirmed selection
and a confirmed dialog response before executing the `DELETE` statement,
providing two separate safeguards against an accidental, irreversible data
loss.

## Expected GUI or Program Behavior

Running `StudentCrudApp` allows a user to add new students, click any row to
load it into the form for editing, save changes back to that same row,
delete a selected row after confirming, and search for students by partial
name match, with a "Show All" option to clear the search filter. Every
operation immediately reflects in the visible table, and all changes persist
correctly in `database/app.db` across application restarts.

## Laboratory Session (3 Hours)

### Laboratory Title
Completing the Course Catalog CRUD Application and Preparing the Final Project

### Objectives
1. Extend the Week 34 course catalog application with update, delete, and
   search functionality.
2. Correctly implement row selection to populate the form for editing.
3. Correctly implement confirmation before destructive delete operations.
4. Produce a fully working CRUD application ready to be extended into the
   Week 36 final project.

### Required Software and Materials
Eclipse IDE with a configured JDK and the SQLite JDBC driver `.jar` added to
the Build Path.

### Required Java Concepts
Everything from Weeks 23–34, combined with this week's `UPDATE`, `DELETE`,
and `LIKE`-based search material.

### Development Requirements
Extend the Week 34 `CourseCatalogApp` (or rebuild it if necessary) inside a
new project `Week35-Lab`, package `week35.lab`, as a class
`CourseCatalogCrudApp`, adding: row selection that loads a course's data
into the form; a Save button that inserts a new course when no row is
selected and updates the selected course when one is selected; a Delete
button that removes the selected course after a confirmation dialog; and a
search field and button that filters courses by a partial course title
match using `LIKE`.

### Detailed Laboratory Procedure

1. Create the project, add the SQLite JDBC driver to the Build Path, and
   create the `database` folder.
2. Recreate the Week 34 course form, table, and insert/refresh logic inside
   `CourseCatalogCrudApp`.
3. Add a `selectedCourseId` field, initialized to `null`.
4. Add a `ListSelectionListener` to the table that loads the selected row's
   `id`, course code, course title, and units into the form and into
   `selectedCourseId`.
5. Modify the Save button's handler to check `selectedCourseId`: if `null`,
   insert a new course; otherwise, update the course with that `id`.
6. Add a Delete button that requires a selected row, shows a confirmation
   dialog, and deletes the course with a `WHERE id = ?` clause if confirmed.
7. Add a search field and Search button that filters courses using
   `course_title LIKE ?` with the keyword wrapped in `%` wildcards, and a
   "Show All" button that reloads every course.
8. Add a Clear Form button that resets `selectedCourseId` to `null` and
   empties all form fields.
9. Run the application and test the full cycle: add several courses, edit
   one by selecting it and changing its units, delete another after
   confirming, and search for a partial course title.
10. Restart the application and confirm all changes persisted correctly.

### Required Features
- Selecting a table row must populate the form with that course's data.
- Saving with no row selected must insert a new course; saving with a row
  selected must update that specific course.
- Deleting must require a confirmation dialog and must only remove the
  selected course.
- Searching must correctly filter by partial course title match; "Show All"
  must correctly restore the full list.

### Testing Procedure
Test the complete CRUD cycle in sequence: create several records, read them
in the table, update one, delete another, and search for a partial match,
confirming correct behavior and correct persistence at every step.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Add a course | Code `IT210`, title `Database Systems`, units `3` | Click Save | New row added |
| Edit a course | Select a row, change units to `4` | Click Save | Same row updates; no duplicate row created |
| Delete a course | Select a row | Click Delete, confirm Yes | Row removed from table |
| Search by partial title | Type `data` | Click Search | Only courses containing "data" (case-insensitive) appear |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Search with empty keyword | Leave search field blank | Click Search | All courses returned, since "%%" matches every title |
| Delete the only remaining course | Delete until one course remains, then delete it | Click Delete, confirm Yes | Table becomes empty; no errors occur |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Delete with nothing selected | No row selected | Click Delete | Message asking user to select a student/course first; no crash |
| Search with no matches | Type an unmatched keyword such as `zzz` | Click Search | "No matches" message; table shows no rows |
| Cancel a delete confirmation | Select a row, click Delete, then click No | N/A | Row remains in the table, unaffected |

### Troubleshooting Guidance
If clicking Save always inserts a new row instead of updating, confirm
`selectedCourseId` is actually being set inside the
`ListSelectionListener`, and that it is checked correctly (`== null` versus
not) inside the save logic. If deleting removes the wrong row or every row,
confirm the `DELETE` statement includes `WHERE id = ?` and that the correct
`id` value is passed as the parameter. If search returns no results even
when a match should exist, confirm the wildcard `%` characters were added
around the keyword in Java code (`"%" + keyword + "%"`), not accidentally
included inside the SQL string itself in a way that would need separate
parameter handling. If the form does not clear after a delete or update,
confirm `clearForm()` (or equivalent) is called at the end of each
successful operation.

### Required Deliverables
1. Eclipse project `Week35-Lab` with the completed `CourseCatalogCrudApp`
   class, implementing full CRUD functionality.
2. Screenshots demonstrating a successful update, a successful delete (with
   its confirmation dialog visible), and a successful search.
3. A short written summary describing how `selectedCourseId` determines
   whether Save performs an insert or an update.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Row selection correctly loads data into the form | 15 |
| Save correctly inserts when nothing is selected | 10 |
| Save correctly updates the selected record | 20 |
| Delete correctly removes only the selected record, with confirmation | 20 |
| Search correctly filters using a parameterized LIKE clause | 20 |
| Submission completeness | 15 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is an Update that behaves like an Insert
(or vice versa); this always traces back to how `selectedCourseId` is
managed, so confirm it is set when a row is clicked and reset to `null`
after every insert and after clicking Clear Form. A `DELETE` or `UPDATE`
statement that seems to affect the wrong row, or every row, almost always
means the `WHERE id = ?` clause was omitted or the wrong variable was passed
as the parameter. If search never returns any results even for data known
to exist, confirm the wildcard characters were placed around the Java
`String` value passed to `setString(...)`, not typed directly into the SQL
text itself, since a literal `%` character inside the SQL string without a
parameter would not adapt to the user's typed keyword. If clicking a table
row appears to do nothing, confirm the `ListSelectionListener` was actually
registered on `studentTable.getSelectionModel()`, not on the table object
directly, since `JTable` does not implement selection listening itself.

## Review and Practice Questions

1. Why must both `UPDATE` and `DELETE` statements always include a `WHERE`
   clause identifying a specific record?
2. Explain how `selectedStudentId` (or `selectedCourseId`) determines
   whether clicking Save performs an insert or an update.
3. Why does the application ask for confirmation before executing a
   `DELETE` statement but not before an `INSERT` or `UPDATE`?
4. Rewrite the search logic in the main example so that it searches by year
   level instead of by name.
5. What does `executeUpdate()`'s return value tell you, and how could it be
   used to detect that an `UPDATE` or `DELETE` did not match any existing
   record?
6. A classmate's search feature returns zero results no matter what keyword
   is typed, even for names known to exist. What is the most likely mistake
   in their `LIKE` query or parameter value?

## Lesson Summary

This week completed the CRUD application begun in Week 34 by adding update,
delete, and keyword search functionality, along with row selection that
loads an existing record back into the form for editing. Combined with
Week 34's insert-and-display features, the application built this week now
supports every core database operation — Create, Read, Update, and Delete —
needed for a genuine desktop database application, and stands ready to be
extended, polished, and presented as the final project in Week 36.

## Final Project Development Guidance

Students should treat this week's completed CRUD application as the working
foundation for the final project due in Week 36. Between this week's
laboratory and the final submission, students are expected to finalize a
single cohesive Java Swing + SQLite application of their own design (using a
fictional data domain of their choice, such as inventory, library books, or
contact records) that includes every feature demonstrated this week:
validated create and update operations sharing a single form, a live table
display, delete with confirmation, and keyword search, all connected to a
SQLite database stored at `database/app.db` within the submitted Eclipse
project. Students should use the remaining time before Week 36 to confirm
their project compiles cleanly from a fresh Eclipse import, that the
database folder and file are included or correctly regenerated on first run,
and that every required feature has been personally tested using normal,
boundary, and invalid input, exactly as practiced in this week's laboratory.
