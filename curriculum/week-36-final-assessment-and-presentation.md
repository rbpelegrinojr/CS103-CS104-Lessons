# Week 36: Final Examination and Final Project Presentation — Assessment Guide

## Week Number and Assessment Title
Week 36 — Final Assessment and Project Presentation Guide

## Final Assessment Coverage
This final assessment covers all material from Weeks 23 through 35: Swing
components and layouts, event handling, multi-window navigation and
dialogs, menus, tables, form validation, and the complete Swing + SQLite
CRUD application built across Weeks 32–35. No new technical concepts are
introduced this week; the focus is on demonstrating mastery of everything
already taught and presenting the final project.

## Final Practical Examination Guidelines
The final practical examination is completed individually in Eclipse and
consists of two parts: a short practical programming task (parallel in
format to the Week 28 midterm, but drawing on the full second-semester
syllabus, including database operations) worth 40 points, and the final
project demonstration and presentation worth 60 points. Students may not
communicate with classmates or use internet/AI tools during the practical
examination portion. The final project itself, unlike the midterm's
in-class task, is prepared in advance, as described in Week 35's Final
Project Development Guidance.

## Final Swing + SQLite Project Requirements
Every student's final project must be a single, cohesive Java Swing desktop
application connected to a SQLite database, built entirely in Eclipse,
managing one clearly defined fictional data domain of the student's choosing
(examples include, but are not limited to: library book records, inventory
items, contact records, student club members, or task/to-do items).

## Required Project Features
1. A main application window built with an appropriate combination of
   layout managers (no `setLayout(null)` in the final submission).
2. At least one data-entry form using appropriate Swing components (text
   fields, and at least one selection control such as a combo box, radio
   buttons, or a checkbox).
3. A `JTable`, wrapped in a `JScrollPane`, displaying all records currently
   stored in the database, refreshed automatically after every change.
4. Full CRUD functionality: Create, Read, Update, and Delete, all connected
   to the SQLite database.
5. A working keyword search feature filtering the displayed records.
6. At least one use of `JOptionPane` for confirmation or feedback messages.

## Required Database Features
The application must use a SQLite database stored at `database/app.db`
relative to the project root, automatically created (using
`CREATE TABLE IF NOT EXISTS`) the first time the application runs if it does
not already exist, with at least one table containing an `id` primary key
column, and at least three additional data columns of realistic and
appropriate types.

## Required Validation and Exception Handling
Every required text field must be validated as non-empty before being saved.
Every numeric field must be validated using `try-catch` around parsing and
an appropriate range check afterward, following the pattern established in
Week 31. Every database operation must be wrapped in an appropriate
`try-catch` for `SQLException`, displaying a clear message to the user
rather than allowing the application to crash or show a raw stack trace.

## Required CRUD Functionality
- **Create:** A validated form that inserts a new record using a
  parameterized `PreparedStatement`.
- **Read:** A `JTable` that loads and displays every record from the
  database, refreshed after every change.
- **Update:** Selecting an existing record loads its data into the form,
  and saving with a record selected updates that specific row using a
  parameterized `UPDATE ... WHERE id = ?` statement.
- **Delete:** Selecting a record and confirming through a `JOptionPane`
  dialog removes that specific row using a parameterized
  `DELETE ... WHERE id = ?` statement.

## Search Functionality
The application must include a search field and button that filters the
displayed records using a parameterized `LIKE` clause against at least one
text column, along with a way to clear the search and show all records
again (for example, a "Show All" button or an empty search returning every
record).

## JTable Record Display Requirements
The `JTable` must be backed by a `DefaultTableModel` with clearly labeled
column headers matching the database columns, must be wrapped in a
`JScrollPane`, and must refresh immediately and automatically after every
successful create, update, delete, or search operation, without requiring
the application to be restarted.

## Eclipse Project Organization Requirements
The submitted project must be a complete, self-contained Eclipse Java
project, with source code organized into a sensible package (for example,
`finalproject.gui` and, optionally, a separate package for database logic),
and the SQLite JDBC driver `.jar` correctly referenced on the project's
Build Path so the project compiles and runs immediately after being
imported into a fresh Eclipse workspace.

## SQLite Database Location Requirements
The database file must be created at `database/app.db`, relative to the
project root, exactly as practiced since Week 32. The `database` folder
must exist in the submitted project (or the code must create it
automatically), and the JDBC connection URL used throughout the project's
code must consistently reference this same path.

## Test Cases
| Test | Input/Action | Expected Result |
|---|---|---|
| Fresh run, no existing database | Delete database/app.db, run the application | Table is created automatically; empty table displayed with no errors |
| Successful insert | Enter valid data, click Save/Add | New record appears in the table immediately |
| Unsuccessful validation | Leave a required field blank, click Save | Clear validation message shown; no record added |
| Loading an empty table | Run the application before any records exist | Table displays with correct column headers and zero rows, no crash |
| Record not found (update/delete edge case) | Attempt an update or delete after the underlying row was removed elsewhere | Application reports zero rows affected rather than crashing |
| Update of an existing record | Select a record, change a field, click Save | Same row updates in the table; no duplicate row created |
| Deletion confirmation | Select a record, click Delete | Confirmation dialog appears; confirming removes the row, canceling leaves it unchanged |
| Search with no matching result | Search for a keyword that matches nothing | Clear "no results" message or empty table; no crash |
| Search with a matching result | Search for a keyword known to match at least one record | Only matching records displayed |
| Application restart persistence | Close and reopen the application | All previously saved records are still present |

## Project Demonstration Procedure
Each student demonstrates their application live, in Eclipse, running from
source (not a pre-built executable), following this sequence: launch the
application from a clean database state; add at least two new records;
demonstrate selecting and updating a record; demonstrate searching for a
record; demonstrate deleting a record with confirmation; and finally,
restart the application to prove data persistence.

## Presentation Requirements
Each student presents for approximately 5 to 8 minutes, explaining the
purpose of their chosen data domain, walking through their application's
features live, and answering at least two follow-up questions from the
instructor about their implementation (for example, explaining why a
`PreparedStatement` was used instead of string concatenation, or how their
update logic determines which record to modify).

## Suggested Presentation Sequence
1. Briefly introduce the application's purpose and data domain (30–60
   seconds).
2. Demonstrate adding a new record.
3. Demonstrate the table display and explain how it refreshes.
4. Demonstrate updating an existing record.
5. Demonstrate searching for a record.
6. Demonstrate deleting a record, including the confirmation step.
7. Restart the application to show persistence.
8. Answer instructor questions.

## Source-Code Submission Requirements
The complete Eclipse project folder must be submitted, either zipped or
through the institution's designated submission system, including all
`.java` source files, but excluding unnecessary build artifacts where
practical. The project must compile and run immediately after being
imported into a fresh Eclipse workspace, assuming the SQLite JDBC driver is
re-added to the Build Path if it was not included in the submission.

## Database Submission Requirements
Students must submit their project with the `database` folder present. If
the `app.db` file itself is included, it should contain at least the sample
data used during development; if it is not included, the application's
`CREATE TABLE IF NOT EXISTS` logic must correctly create a fresh, empty
database automatically the first time it runs after submission.

## Documentation Requirements
Each student must submit a short project summary (half a page to one page)
describing: the chosen data domain and why it was chosen; the database
table structure (column names and types); a list of implemented features
mapped to CRUD operations; and any known limitations or incomplete
features, described honestly rather than omitted.

## Final Project 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Application compiles and runs immediately after import | 10 |
| Layout managers used correctly; no absolute positioning | 10 |
| Complete Create functionality with validation | 10 |
| Complete Read functionality: JTable correctly displays and refreshes | 10 |
| Complete Update functionality | 15 |
| Complete Delete functionality with confirmation | 15 |
| Search functionality correctly implemented | 10 |
| Database correctly stored at database/app.db and persists correctly | 10 |
| Documentation and code organization | 10 |
| **Total** | **100** |

## Practical Examination Scoring Guide
| Category | Points |
|---|---:|
| Correct component selection and layout for the assigned short task | 10 |
| Correct event handling | 10 |
| Correct validation (blank check, parsing, range check) | 10 |
| Correct basic database operation (insert or select) if included in the assigned task | 10 |
| **Total** | **40** |

## Presentation Evaluation Criteria
| Category | Points |
|---|---:|
| Clear explanation of the application's purpose and design | 15 |
| Live demonstration successfully covers create, read, update, delete, and search | 25 |
| Confident, accurate answers to instructor questions | 10 |
| Demonstration of data persistence after restart | 10 |
| **Total** | **60** |

## Academic Integrity Requirements
While the final project may be developed over several weeks with reasonable
use of the student's own notes, laboratory code, and instructor-provided
examples from this course, the completed project must represent the
individual student's own work and understanding. Students must be prepared
to explain, modify, or extend any part of their submitted code live during
the presentation and question period. Copying another student's project
structure, database schema, or substantial blocks of code without
significant independent modification and understanding will be treated
according to the institution's academic integrity policy. Use of AI code
generation tools or unauthorized external help to produce the final project
without genuine personal understanding is not permitted; students should be
able to explain every design decision in their own words during the
presentation.
