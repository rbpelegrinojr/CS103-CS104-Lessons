# Week 27: Layouts and Form Design

## Main Topic
`FlowLayout`, `BorderLayout`, `GridLayout`, `BoxLayout`, `JPanel`, component
organization, and UI design principles.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Explain the limitations of absolute positioning and the benefits of layout
   managers.
2. Apply `FlowLayout`, `BorderLayout`, `GridLayout`, and `BoxLayout` to
   arrange components.
3. Use `JPanel` to group related components and combine multiple layouts
   within a single window.
4. Redesign an existing absolutely-positioned form to use layout managers
   instead.
5. Apply basic UI design principles such as grouping, alignment, and
   consistent spacing.

## Prerequisite Knowledge
Students must already be able to build forms using absolute positioning with
`setBounds`, as practiced in Weeks 24–26, and must be comfortable attaching
event listeners to buttons as taught in Week 26. No prior knowledge of layout
managers is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 15 min | Review of absolute positioning and its limitations |
| 2 | 20 min | `FlowLayout`: simple left-to-right, wrapping arrangement |
| 3 | 20 min | `BorderLayout`: five-region arrangement |
| 4 | 20 min | `GridLayout`: equal-sized rows and columns |
| 5 | 15 min | `BoxLayout` and `JPanel` for grouping and nesting layouts |
| 6 | 20 min | Live coding: rebuilding a previous form using combined layouts |

## Detailed Lesson Discussion

Every form built so far in this course used `setLayout(null)` and manually
calculated pixel coordinates for every single component using `setBounds`.
This approach works for a fixed-size demonstration, but it breaks down
quickly in real applications: if the window is resized, components stay
exactly where they were placed and do not adjust, and if even one component
needs to be added or removed, every coordinate below it on the form may need
to be recalculated by hand. Java's layout managers solve this problem by
taking over the job of positioning and sizing components automatically,
following a defined set of rules, so the programmer only needs to decide
which layout manager fits the situation and add components to it in the
correct order.

The simplest layout manager is `FlowLayout`, which arranges components in a
single row, left to right, wrapping onto a new row automatically whenever the
current row runs out of horizontal space, similar to how words wrap in a
paragraph of text. `FlowLayout` is set using
`setLayout(new FlowLayout())` and is a reasonable default for panels
containing a small number of related controls, such as a row of buttons at
the bottom of a form, but it is not well suited to arranging an entire
complex form on its own.

`BorderLayout` divides a container into five named regions: `NORTH`, `SOUTH`,
`EAST`, `WEST`, and `CENTER`. Each region can hold at most one component
directly, although that one component is very often a `JPanel` containing
several other components, which is how `BorderLayout` supports complex
designs despite its simple five-region structure. A component is added to a
specific region using `add(component, BorderLayout.NORTH)`, and the `CENTER`
region automatically expands to fill whatever space is not used by the other
four regions, making `BorderLayout` a natural choice for windows structured
around a large central working area, such as a form with a toolbar-like
button row on top and a status label along the bottom. `JFrame`'s content
pane actually uses `BorderLayout` by default, which is why a single call to
`add(component)` without specifying a region places that component in the
`CENTER` region automatically.

`GridLayout` arranges components into a strict grid of equal-sized rows and
columns, specified using `new GridLayout(rows, columns)`. Every cell in the
grid is exactly the same size, and components are added one at a time,
filling the grid from left to right and top to bottom, similar to filling in
cells of a table one at a time. `GridLayout` is particularly well suited to
label-and-field pairs, such as a form where every row contains one label and
one input component of matching height, since specifying, for example,
`new GridLayout(5, 2)` immediately creates a tidy five-row, two-column form
layout without any manual coordinate calculation at all.

`BoxLayout` arranges components in a single row or a single column, similar
to `FlowLayout`, but with more predictable and controllable spacing, and
without automatically wrapping onto a new line. `BoxLayout` is applied
directly to a `JPanel` using
`panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS))` for a vertical
arrangement, or `BoxLayout.X_AXIS` for a horizontal one. `BoxLayout` is
useful for building a simple vertical stack of controls, such as a sidebar of
buttons, where each item should appear directly below the previous one at its
own preferred height rather than in a fixed grid cell.

The real power of layout managers becomes clear once `JPanel` is introduced.
A `JPanel` is a lightweight, invisible-by-default container that can hold its
own components and use its own layout manager, and can then itself be added
to another container, such as the frame's `CENTER` region, exactly like any
other component. This means a complex form is rarely built using a single
layout manager applied directly to the whole `JFrame`. Instead, the form is
usually broken into several logical sections — perhaps a title area, a form
area, and a button area — each built as its own `JPanel` with whichever
layout manager suits that section best, and then those panels are combined
using `BorderLayout` on the frame itself. This layered, "panels inside
panels" approach is the standard way professional Swing interfaces are built,
and it directly mirrors the way this course's final project will be
organized in later weeks.

Beyond simply choosing a layout manager, a well-designed form also follows a
few basic visual principles: related fields should be grouped together,
usually inside the same panel; labels and their matching input fields should
align consistently so the eye can scan down a form easily; and consistent
spacing between components, achieved either through the layout manager's own
gap settings or through empty border padding, makes a form look organized
rather than cluttered. These are not strict rules enforced by the compiler,
but following them consistently is what separates a professional-looking
application from one that merely functions.

## Important Terminology

Key terms: **layout manager**, **FlowLayout**, **BorderLayout**, **region
(NORTH/SOUTH/EAST/WEST/CENTER)**, **GridLayout**, **BoxLayout**, and
**JPanel**.

## Complete Java Programming Example

```java
package week27.gui;

import java.awt.BorderLayout;
import java.awt.GridLayout;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;

/**
 * A book record entry form rebuilt using layout managers instead of
 * absolute positioning: a title label in the NORTH region, a labeled
 * grid of fields in the CENTER region, and a button row in the SOUTH
 * region.
 */
public class BookRecordForm extends JFrame {

    private final JTextField titleField;
    private final JTextField authorField;
    private final JTextField yearField;
    private final JLabel statusLabel;

    public BookRecordForm() {
        super("Book Record Entry");
        setSize(420, 260);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10));

        JLabel headerLabel = new JLabel("Add a New Book Record", JLabel.CENTER);
        add(headerLabel, BorderLayout.NORTH);

        JPanel formPanel = new JPanel(new GridLayout(3, 2, 8, 8));
        formPanel.add(new JLabel("Title:"));
        titleField = new JTextField();
        formPanel.add(titleField);

        formPanel.add(new JLabel("Author:"));
        authorField = new JTextField();
        formPanel.add(authorField);

        formPanel.add(new JLabel("Year Published:"));
        yearField = new JTextField();
        formPanel.add(yearField);

        add(formPanel, BorderLayout.CENTER);

        JPanel buttonPanel = new JPanel();          // defaults to FlowLayout
        JButton saveButton = new JButton("Save Record");
        JButton clearButton = new JButton("Clear Fields");
        buttonPanel.add(saveButton);
        buttonPanel.add(clearButton);

        statusLabel = new JLabel(" ", JLabel.CENTER);

        JPanel southPanel = new JPanel(new BorderLayout());
        southPanel.add(buttonPanel, BorderLayout.NORTH);
        southPanel.add(statusLabel, BorderLayout.SOUTH);
        add(southPanel, BorderLayout.SOUTH);

        saveButton.addActionListener(e -> {
            String title = titleField.getText().trim();
            String author = authorField.getText().trim();
            if (title.isEmpty() || author.isEmpty()) {
                statusLabel.setText("Title and Author are required.");
                return;
            }
            statusLabel.setText("Saved: \"" + title + "\" by " + author);
        });

        clearButton.addActionListener(e -> {
            titleField.setText("");
            authorField.setText("");
            yearField.setText("");
            statusLabel.setText(" ");
        });

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new BookRecordForm();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week27-BookRecord`.
2. Create a package named `week27.gui`.
3. Create the class `BookRecordForm` and paste in the complete example.
4. Save and resolve imports with **Ctrl+Shift+O**; confirm `BorderLayout` and
   `GridLayout` resolve from `java.awt` while the components resolve from
   `javax.swing`.
5. Run the class using **Run As > Java Application**.
6. Observe that the header label appears at the top, the three labeled
   fields appear evenly spaced in the middle, and the two buttons with a
   status label appear at the bottom.
7. Resize the window by dragging its edge and confirm that the form area
   grows and shrinks along with the window, unlike the fixed absolute
   positioning used in earlier weeks.
8. Type a title and author, click **Save Record**, and confirm the status
   message appears.
9. Click **Clear Fields** and confirm every field empties and the status
   message resets.
10. Leave the title blank, click **Save Record**, and confirm the "required"
    message appears instead.
11. As an experiment, change `new GridLayout(3, 2, 8, 8)` to
    `new GridLayout(2, 2, 8, 8)` (too few rows for three label/field pairs),
    run again, and observe how Swing still tries to fit all six components
    into four grid cells, distorting the layout. Restore the correct value
    afterward.

## Guided Worked Example

**Problem description:** Rebuild the Week 25 "Meal Preference" form using
`BoxLayout` inside a `JPanel` instead of absolute positioning, to directly
compare the two approaches on the same content.

```java
package week27.gui;

import javax.swing.BoxLayout;
import javax.swing.ButtonGroup;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JRadioButton;

public class MealPreferenceFormV2 extends JFrame {

    public MealPreferenceFormV2() {
        super("Meal Preference (Layout Manager Version)");
        setSize(300, 220);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new BoxLayout(mainPanel, BoxLayout.Y_AXIS));

        mainPanel.add(new JLabel("Meal Type:"));
        JRadioButton veganButton = new JRadioButton("Vegan");
        JRadioButton regularButton = new JRadioButton("Regular");
        ButtonGroup mealGroup = new ButtonGroup();
        mealGroup.add(veganButton);
        mealGroup.add(regularButton);
        mainPanel.add(veganButton);
        mainPanel.add(regularButton);

        mainPanel.add(new JLabel("Portion Size:"));
        JComboBox<String> sizeComboBox = new JComboBox<>(new String[] {
            "Small", "Regular", "Large"
        });
        mainPanel.add(sizeComboBox);

        add(mainPanel);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new MealPreferenceFormV2();
    }
}
```

**Walkthrough:** Because no explicit layout was set on the frame itself, the
default `BorderLayout` places `mainPanel` in the `CENTER` region, filling the
entire window. Inside `mainPanel`, `BoxLayout` with `Y_AXIS` stacks every
added component directly below the previous one, in the exact order they
were added, which is why the label, radio buttons, second label, and combo
box appear in a clean vertical column without any coordinate calculations at
all.

**Expected behavior:** All components appear stacked vertically, filling the
available window height, and the two radio buttons remain mutually exclusive
exactly as they were in the Week 25 version.

**Test data:** Selecting "Vegan" then "Regular" should behave identically to
the Week 25 absolute-positioning version; the visual arrangement differs, but
the underlying `ButtonGroup` logic is unchanged.

**Likely errors:** Forgetting to call `setLayout(new BoxLayout(mainPanel,
BoxLayout.Y_AXIS))` on the panel before adding components will cause the
panel to fall back to its own default `FlowLayout`, arranging components
left-to-right instead of top-to-bottom.

## Code Walkthrough

The `BookRecordForm` constructor sets `BorderLayout` directly on the frame
with an interior gap of ten pixels between regions, avoiding the cramped
appearance that would result from components touching the window's edges
and each other. The `formPanel` uses `GridLayout(3, 2, 8, 8)`, meaning three
rows, two columns, and eight pixels of horizontal and vertical spacing
between cells; three labels and three fields are added one at a time,
filling the grid left-to-right and top-to-bottom automatically. The button
row uses a plain `JPanel` with no explicit layout call, which means it falls
back to its default `FlowLayout`, appropriate for a short row of buttons.
Nesting `buttonPanel` and `statusLabel` inside a further `southPanel` using
its own `BorderLayout` demonstrates how panels can be nested multiple levels
deep to achieve a more specific arrangement than any single layout manager
could provide alone.

## Expected GUI or Program Behavior

Running `BookRecordForm` displays a centered header, a neat two-column grid
of labeled fields, and a bottom section containing two buttons above a status
message. Resizing the window causes the grid and button areas to resize
proportionally, unlike the fixed pixel positions used in earlier weeks.
Saving with empty required fields shows a validation message; saving with
valid data shows a confirmation message; clearing resets every field.

## Laboratory Session (3 Hours)

### Laboratory Title
Redesigning the Enrollment Form with Layout Managers

### Objectives
1. Replace absolute positioning with an appropriate combination of layout
   managers.
2. Use nested `JPanel` objects to organize a form into logical sections.
3. Preserve all existing event-handling behavior while changing only the
   layout.
4. Evaluate how the redesigned form behaves when resized.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
All Swing components from Weeks 24–25 and the `ActionListener` pattern from
Week 26, combined with this week's layout managers.

### Development Requirements
Create an Eclipse project `Week27-Lab` with package `week27.lab` containing a
class `LayoutManagedEnrollmentForm` that reproduces the functional behavior
of the Week 26 `InteractiveEnrollmentForm` (student number, full name,
password, campus combo box, international-student checkbox, Enroll button,
and result label) but built entirely using layout managers and `JPanel`
objects instead of `setBounds`.

### Detailed Laboratory Procedure

1. Create the project and package as described.
2. Design the form using a `BorderLayout` on the frame: a header label in
   `NORTH`, a form panel in `CENTER`, and a button-and-result section in
   `SOUTH`.
3. Build the form panel using `GridLayout` with one row per label/field pair
   (student number, name, password, campus, international-student checkbox),
   choosing an appropriate number of rows and two columns.
4. Build the button/result section as a nested panel: a `FlowLayout` panel
   containing the Enroll button, placed above a centered result label, using
   a `BorderLayout` exactly as shown in the main example.
5. Reattach the exact same validation logic used in Week 26: required
   student number and name fields, with an appropriate error message if
   either is empty, and a formatted summary otherwise.
6. Run the form and confirm all field values are read correctly and the
   Enroll button still works exactly as it did in Week 26.
7. Resize the window wider and taller, and confirm that the grid of fields
   and the button row both resize sensibly rather than leaving empty space
   or overlapping.
8. Resize the window very small and observe what happens to the layout,
   recording the observation in the lab report.

### Required Features
- The form must use `BorderLayout`, `GridLayout`, and `FlowLayout` together
  (at least three different layout managers/containers combined).
- No component in the final submission may use `setBounds` or
  `setLayout(null)`.
- All validation and event-handling behavior from Week 26 must be preserved
  exactly.

### Testing Procedure
Test using the same normal, boundary, and invalid cases as Week 26's
laboratory, plus new tests specific to resizing behavior.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Complete valid entry | All fields filled correctly | Click Enroll | Correct summary appears |
| Resize window larger | Drag window edge outward | N/A | Grid and buttons expand proportionally |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Resize window very small | Drag window edge to near-minimum size | N/A | Components shrink or compress but remain visible without overlapping garbage |
| Single-character inputs | Very short but non-empty name | Click Enroll | Still processed successfully |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Empty required fields | Leave student number and name blank | Click Enroll | Error message shown, same as Week 26 behavior |

### Troubleshooting Guidance
If components appear bunched into the top-left corner of a panel, the layout
manager was probably left at its default `FlowLayout` when a `GridLayout` or
`BorderLayout` was intended; confirm `setLayout(...)` was called on that
specific panel. If nested panels do not appear at all, confirm each panel was
itself added to its parent container using `add(...)`, since creating a
`JPanel` without adding it anywhere has no visible effect. A `GridLayout`
with too few or too many cells for the number of added components will
silently compress or stretch the grid rather than throwing an error, so
carefully count labels and fields against the declared row and column
numbers.

### Required Deliverables
1. Eclipse project `Week27-Lab` with the completed
   `LayoutManagedEnrollmentForm` class.
2. Two screenshots: the form at its normal size and the same form resized
   larger.
3. A short written comparison of the absolute-positioning version from Week
   26 versus this week's layout-managed version.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Correct use of BorderLayout for overall structure | 20 |
| Correct use of GridLayout for the form fields | 20 |
| Correct use of FlowLayout or BoxLayout for the button area | 15 |
| All Week 26 validation and event behavior preserved | 20 |
| Form resizes cleanly without overlapping components | 15 |
| Submission completeness (screenshots and comparison) | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most frequent problem this week is components appearing in an
unexpected corner, usually caused by adding components to a panel before
calling `setLayout` on that panel, or by forgetting that every container has
its own default layout manager (`FlowLayout` for a plain `JPanel`,
`BorderLayout` for a `JFrame`'s content pane). Another common issue is a
component that appears to disappear entirely after switching from
`setBounds` to a layout manager; this typically means the same component was
accidentally added to two different containers, and only the second `add`
call actually takes effect. If a `GridLayout` looks stretched or squeezed
unexpectedly, recount the actual number of components added against the
declared row and column counts, since `GridLayout` does not automatically
adjust to a mismatched number of children.

## Review and Practice Questions

1. What specific problem do layout managers solve that absolute positioning
   does not?
2. Name the five regions of a `BorderLayout` and describe which region
   expands to fill unused space.
3. When would `GridLayout` be a better choice than `BoxLayout` for arranging
   a set of labeled fields?
4. Rewrite the "Meal Preference" `BoxLayout` worked example so that the
   panel uses `X_AXIS` instead of `Y_AXIS`, and describe how the resulting
   arrangement changes.
5. Why is it common to nest several `JPanel` objects, each with its own
   layout manager, instead of relying on a single layout manager for the
   entire form?
6. A classmate's `GridLayout` shows six equally-sized cells but only four
   contain visible components. What could explain the two empty cells?

## Lesson Summary

This week replaced the absolute positioning used since Week 24 with proper
layout managers — `FlowLayout`, `BorderLayout`, `GridLayout`, and `BoxLayout`
— and introduced `JPanel` as the tool for grouping and nesting layouts to
build well-organized, resizable forms. Students practiced rebuilding an
existing interactive form without changing any of its validation or
event-handling logic, isolating layout as an independent design concern.
Week 28 pauses new instruction to assess everything covered since Week 19
through a midterm examination, after which Week 29 introduces multiple
windows and dialogs built on top of these same layout skills.
