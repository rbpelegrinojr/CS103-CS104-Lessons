# Week 30: Menus and Advanced Swing Components

## Main Topic
`JMenuBar`, `JMenu`, `JMenuItem`, `JTable`, `JScrollPane`, and `JTabbedPane`.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Build a menu bar with menus and menu items attached to a `JFrame`.
2. Attach `ActionListener`s to menu items to trigger actions from a menu.
3. Display tabular data using `JTable` backed by a two-dimensional data
   structure.
4. Wrap scrollable components such as `JTable` and `JList` inside a
   `JScrollPane`.
5. Organize multiple sections of an application using `JTabbedPane`.

## Prerequisite Knowledge
Students must already be comfortable building windows with layout managers
(Week 27) and attaching `ActionListener`s (Week 26), as well as opening
secondary windows and dialogs (Week 29). No prior knowledge of menus,
tables, scroll panes, or tabbed panes is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Weeks 26–29 |
| 2 | 25 min | `JMenuBar`, `JMenu`, and `JMenuItem`: building an application menu |
| 3 | 30 min | `JTable`: displaying tabular data |
| 4 | 20 min | `JScrollPane`: making tables and lists scrollable |
| 5 | 20 min | `JTabbedPane`: organizing multiple views in one window |
| 6 | 15 min | Live coding: combining a menu, a table, and tabs in one application |

## Detailed Lesson Discussion

Applications with many features are often organized using a menu bar rather
than filling the window itself with dozens of buttons. Swing builds menus
from three cooperating classes: `JMenuBar`, which is the horizontal bar
attached to the top of a frame; `JMenu`, which is a single drop-down menu
such as "File" or "Edit"; and `JMenuItem`, which is a single clickable
command inside a menu, such as "Save" or "Exit". A menu bar is attached to a
frame using `setJMenuBar(menuBar)`, a distinct method from the ordinary
`add(...)` used for regular components, because a menu bar occupies its own
special region at the very top of the window rather than participating in
the frame's main layout. Building a menu follows a nesting pattern: create
the `JMenuBar`, create one or more `JMenu` objects and add them to the menu
bar, then create one or more `JMenuItem` objects and add them to the
appropriate menu. A `JMenuItem` responds to clicks exactly like a `JButton`,
using the same `ActionListener` and `addActionListener(...)` pattern already
familiar from Week 26, which means nothing new needs to be learned about
handling menu clicks beyond recognizing that a menu item is just another
clickable component.

Many applications need to display more than a handful of individual data
values; they need to display an entire table of records at once, such as a
list of products or students. `JTable` is the Swing component built for
exactly this purpose. The simplest way to create a table is
`new JTable(dataArray, columnNames)`, where `dataArray` is a two-dimensional
`Object[][]` array representing the rows and columns of data, and
`columnNames` is a one-dimensional array of `String` column headers. Each
inner array inside `dataArray` represents one row, and its elements
correspond, in order, to the column names provided. A `JTable` automatically
handles drawing grid lines, column headers, and even basic column resizing
and reordering through the mouse, none of which needs to be programmed
manually.

A `JTable`, much like a `JTextArea` seen in Week 24, does not manage its own
scrolling. If a table contains more rows than fit within the visible window,
the extra rows are simply cut off unless the table is placed inside a
`JScrollPane`. A `JScrollPane` is a container that wraps a single component
and automatically adds horizontal and/or vertical scroll bars whenever that
component's content is larger than the visible area. Using a `JScrollPane`
is straightforward: rather than adding the table directly to the frame or
panel, the table is passed into a new `JScrollPane`, and that scroll pane is
added instead — `add(new JScrollPane(myTable))`. This same technique applies
equally well to a `JList` with many items, and it is considered standard
practice to wrap almost every `JTable` in a `JScrollPane`, even when the
table currently contains few enough rows to fit without scrolling, since more
rows may be added later.

When an application has several distinct, related sections — such as a
"Browse Records" view and a "Add New Record" view — `JTabbedPane` provides a
clean way to let the user switch between them without opening separate
windows. A `JTabbedPane` is created with `new JTabbedPane()`, and each tab is
added using `addTab("Tab Title", componentForThatTab)`, where the component
is typically a `JPanel` containing that tab's specific interface. Clicking a
tab automatically shows its associated component and hides the others; no
manual show/hide logic is required, since `JTabbedPane` manages this
switching internally. Using tabs is often a better organizational choice than
building several separate `JFrame` windows for closely related tasks, since
it keeps the user within a single, consistent window rather than juggling
multiple independent windows on the screen.

Combining these three tools — a menu bar for global commands, a table
wrapped in a scroll pane for viewing data, and tabs for organizing multiple
views — gives students, for the first time in this course, the structural
building blocks of a genuinely professional-looking desktop application, and
these exact tools will reappear directly in the Swing + SQLite project built
in Weeks 32 through 35, where `JTable` in particular becomes the primary way
database records are displayed to the user.

## Important Terminology

Key terms: **JMenuBar**, **JMenu**, **JMenuItem**, **setJMenuBar**,
**JTable**, **JScrollPane**, and **JTabbedPane**.

## Complete Java Programming Example

```java
package week30.gui;

import javax.swing.JFrame;
import javax.swing.JMenu;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTabbedPane;
import javax.swing.JTable;

/**
 * A simple "Product Catalog" viewer demonstrating a menu bar, a table
 * wrapped in a scroll pane, and a tabbed pane organizing two views.
 */
public class ProductCatalogWindow extends JFrame {

    public ProductCatalogWindow() {
        super("Product Catalog");
        setSize(500, 350);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        setJMenuBar(buildMenuBar());

        JTabbedPane tabbedPane = new JTabbedPane();
        tabbedPane.addTab("Browse Products", buildProductsTab());
        tabbedPane.addTab("About", buildAboutTab());

        add(tabbedPane);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private JMenuBar buildMenuBar() {
        JMenuBar menuBar = new JMenuBar();

        JMenu fileMenu = new JMenu("File");
        JMenuItem refreshItem = new JMenuItem("Refresh");
        JMenuItem exitItem = new JMenuItem("Exit");

        refreshItem.addActionListener(e ->
                JOptionPane.showMessageDialog(this, "Catalog refreshed."));
        exitItem.addActionListener(e -> System.exit(0));

        fileMenu.add(refreshItem);
        fileMenu.addSeparator();
        fileMenu.add(exitItem);

        menuBar.add(fileMenu);
        return menuBar;
    }

    private JPanel buildProductsTab() {
        String[] columnNames = {"Product ID", "Name", "Price", "Stock"};
        Object[][] data = {
            {"P-001", "Notebook", 2.50, 120},
            {"P-002", "Ballpoint Pen", 0.75, 300},
            {"P-003", "Backpack", 24.99, 45},
            {"P-004", "USB Flash Drive", 8.20, 60}
        };

        JTable productTable = new JTable(data, columnNames);
        JScrollPane scrollPane = new JScrollPane(productTable);

        JPanel panel = new JPanel(new java.awt.BorderLayout());
        panel.add(scrollPane, java.awt.BorderLayout.CENTER);
        return panel;
    }

    private JPanel buildAboutTab() {
        JPanel panel = new JPanel();
        panel.add(new javax.swing.JLabel(
                "Product Catalog Demo - Week 30 Swing Example"));
        return panel;
    }

    public static void main(String[] args) {
        new ProductCatalogWindow();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week30-ProductCatalog`.
2. Create a package named `week30.gui`.
3. Create the class `ProductCatalogWindow` and paste in the complete
   example.
4. Save and resolve imports with **Ctrl+Shift+O**.
5. Run the class using **Run As > Java Application**.
6. Confirm a "File" menu appears at the top of the window; click it and
   confirm "Refresh" and "Exit" appear, separated by a divider line.
7. Click **Refresh** and confirm the confirmation dialog appears.
8. Confirm the "Browse Products" tab shows a table with four columns and
   four rows of sample data.
9. Click the "About" tab and confirm it switches to a different, simpler
   view, then click back to "Browse Products" and confirm the table is
   still there.
10. Resize the window smaller and confirm the `JScrollPane` around the table
    shows scroll bars if the table no longer fits, rather than the table
    simply being cut off.
11. Click **Exit** from the File menu and confirm the entire application
    closes.
12. As an experiment, remove the `JScrollPane` wrapper (add the `JTable`
    directly to the panel instead), resize the window very small, and
    observe that the table's extra rows are now simply clipped rather than
    scrollable. Restore the `JScrollPane` afterward.

## Guided Worked Example

**Problem description:** Build a smaller "Class Roster Viewer" containing
only a menu with a single "Close" item and a scrollable table of student
names and grades, to isolate menu and table behavior without the added
complexity of tabs.

```java
package week30.gui;

import javax.swing.JFrame;
import javax.swing.JMenu;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JScrollPane;
import javax.swing.JTable;

public class ClassRosterViewer extends JFrame {

    public ClassRosterViewer() {
        super("Class Roster Viewer");
        setSize(360, 260);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JMenuBar menuBar = new JMenuBar();
        JMenu fileMenu = new JMenu("File");
        JMenuItem closeItem = new JMenuItem("Close");
        closeItem.addActionListener(e -> dispose());
        fileMenu.add(closeItem);
        menuBar.add(fileMenu);
        setJMenuBar(menuBar);

        String[] columns = {"Student Name", "Grade"};
        Object[][] data = {
            {"Ana Reyes", 92},
            {"Ben Torres", 85},
            {"Carla Uy", 78},
            {"Dexter Ong", 90}
        };
        JTable rosterTable = new JTable(data, columns);
        add(new JScrollPane(rosterTable));

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new ClassRosterViewer();
    }
}
```

**Walkthrough:** The "Close" menu item calls `dispose()` rather than
`System.exit(0)`, closing only this window; in a larger application, this
distinction would matter if other windows were open at the same time. The
table is wrapped directly in `new JScrollPane(rosterTable)` at the moment it
is added to the frame, avoiding an unused intermediate variable.

**Expected behavior:** The window opens showing a table of four students and
their grades, with a working File menu containing a single Close command.

**Test data:** Resizing the window to a very short height should reveal a
vertical scroll bar rather than hiding rows entirely.

**Likely errors:** Forgetting to call `setJMenuBar(menuBar)` is a common
mistake that results in a menu bar being fully built in code but never
appearing on screen, since building the `JMenuBar` object alone does not
attach it to the window.

## Code Walkthrough

`ProductCatalogWindow` separates its construction into three private helper
methods — `buildMenuBar()`, `buildProductsTab()`, and `buildAboutTab()` —
each returning a fully built object rather than assembling everything
directly inside the constructor. This organization keeps the constructor
short and readable, and mirrors how larger Swing applications are typically
structured once a window contains more than a few components. Inside
`buildProductsTab()`, the `Object[][]` array mixes `String`, `double`, and
`int` values within the same rows, which `JTable` accepts without complaint
because its underlying model treats every cell value as a generic `Object`;
each column's data does not need to be declared as a single fixed type. The
`JScrollPane` wraps the table before it is added to the panel, ensuring
scrolling works automatically the moment the tab is shown.

## Expected GUI or Program Behavior

Running `ProductCatalogWindow` shows a window with a File menu, a tabbed
interface defaulting to the "Browse Products" tab, and a four-column,
four-row product table with working scroll bars if resized smaller.
Switching to the "About" tab shows a simple label and hides the table until
the user switches back. Selecting Exit from the File menu closes the
application.

## Laboratory Session (3 Hours)

### Laboratory Title
Building a Tabbed Task Manager with a Menu and Table View

### Objectives
1. Build a working `JMenuBar` with at least one `JMenu` and multiple
   `JMenuItem`s.
2. Display a data set using `JTable` wrapped in a `JScrollPane`.
3. Organize the application into at least two tabs using `JTabbedPane`.
4. Attach working `ActionListener`s to at least two menu items.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
`ActionListener` (Week 26), layout managers and `JPanel` (Week 27), and
`JOptionPane` (Week 29), combined with this week's menu, table, scroll pane,
and tabbed pane material.

### Development Requirements
Create an Eclipse project `Week30-Lab` with package `week30.lab` containing a
class `TaskManagerWindow` that displays a fictional list of at least five
tasks (with columns for Task Name, Priority, and Status) in a `JTable`
wrapped in a `JScrollPane`, organized inside a `JTabbedPane` with at least two
tabs ("Task List" and "Summary"), and a `JMenuBar` with a "File" menu
containing "Refresh" (shows a confirmation message) and "Exit" (closes the
application) menu items.

### Detailed Laboratory Procedure

1. Create the project and package as described.
2. Build the `JMenuBar` with a "File" menu containing "Refresh" and "Exit"
   menu items, attaching `setJMenuBar(...)` to the frame.
3. Attach an `ActionListener` to "Refresh" that shows a `JOptionPane` message
   dialog confirming the action.
4. Attach an `ActionListener` to "Exit" that calls `System.exit(0)`.
5. Build the "Task List" tab containing a `JTable` with at least five rows
   of fictional task data across three columns (Task Name, Priority,
   Status), wrapped in a `JScrollPane`.
6. Build the "Summary" tab containing at least one label summarizing the
   total number of tasks (this may be a fixed value calculated by counting
   the array, since dynamic recalculation is not required this week).
7. Combine both tabs into a `JTabbedPane` and add it to the frame.
8. Run the application and confirm the menu, table, scroll pane, and tabs
   all function correctly.
9. Resize the window smaller than the table's natural size and confirm
   scroll bars appear rather than rows disappearing.
10. Click between both tabs multiple times and confirm the correct content
    is shown each time.

### Required Features
- A functioning menu bar with at least two working menu items.
- A `JTable` with at least five rows and three columns of fictional data.
- The table must be wrapped in a `JScrollPane`.
- At least two tabs organized using `JTabbedPane`.

### Testing Procedure
Run the application, exercise every menu item, switch between tabs multiple
times, and resize the window to confirm scrolling behavior.

### Normal Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Click Refresh | File > Refresh | Confirmation dialog appears |
| Switch tabs | Click "Summary", then "Task List" | Correct content shown each time |

### Boundary Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Resize window to minimum practical size | Drag window edge inward repeatedly | Scroll bars appear on the table instead of content disappearing |
| Table with exactly enough rows to fit | Resize window so all rows are just barely visible | No scroll bar appears yet, or appears only when needed |

### Invalid Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Click Exit accidentally | File > Exit | Application closes entirely (expected behavior, but should be noted as a real risk in real applications, which is why Week 31 introduces confirmation before destructive actions) |

### Troubleshooting Guidance
If the menu bar never appears, confirm `setJMenuBar(...)` was called on the
frame itself, since simply constructing a `JMenuBar` object without attaching
it produces no visible menu. If the table appears with no data at all,
double-check that the `Object[][]` array's inner array lengths match the
number of column names exactly; a mismatch produces a confusing but
non-crashing display. If clicking a tab does nothing, confirm that
`addTab(...)` was called with a distinct component for each tab, since
accidentally adding the same panel object to two tabs will cause both tabs to
appear to show the same content or behave unexpectedly.

### Required Deliverables
1. Eclipse project `Week30-Lab` with the completed `TaskManagerWindow` class.
2. A screenshot of each tab.
3. A short written note explaining why the table is wrapped in a
   `JScrollPane`.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Menu bar built correctly with working Refresh and Exit items | 20 |
| Table correctly displays required data with correct columns | 20 |
| Table correctly wrapped in JScrollPane | 15 |
| Tabbed pane correctly organizes at least two views | 20 |
| Application resizes and scrolls cleanly | 15 |
| Submission completeness | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a menu that was built in code but never
appears; this is almost always caused by forgetting `setJMenuBar(...)`.
Another frequent issue is a table that appears completely blank; this usually
means the two-dimensional data array was declared but never actually
populated with sample rows, or that the column names array has a different
length than each row in the data array. If scroll bars never appear even when
the table clearly has more rows than fit, confirm the table was added to a
`JScrollPane` and that the scroll pane itself, not the raw table, was the
component added to the frame or panel. If clicking between tabs shows the
same content on every tab, check that a separate panel object was built for
each tab rather than reusing one panel reference multiple times.

## Review and Practice Questions

1. What is the difference between `JMenuBar`, `JMenu`, and `JMenuItem`?
2. Why does a `JTable` typically need to be wrapped in a `JScrollPane`?
3. Describe the two-dimensional array structure expected by
   `new JTable(data, columnNames)`.
4. Rewrite the "Class Roster Viewer" worked example to add a third column,
   "Attendance Rate", with sample percentage values for each student.
5. When would `JTabbedPane` be a better organizational choice than opening
   multiple separate `JFrame` windows?
6. A classmate's table shows the correct number of rows but every column
   header reads "A", "B", "C" instead of their intended names. What is the
   most likely cause?

## Lesson Summary

This week introduced three tools for organizing more complex applications:
`JMenuBar` with `JMenu` and `JMenuItem` for global commands, `JTable`
combined with `JScrollPane` for displaying and scrolling tabular data, and
`JTabbedPane` for grouping multiple views inside a single window. These three
components, especially `JTable`, will become central to the Swing + SQLite
project beginning in Week 32, where database records replace the fictional
sample data used this week. Week 31 shifts focus to strengthening form
validation and exception handling within GUI applications before database
work begins.
