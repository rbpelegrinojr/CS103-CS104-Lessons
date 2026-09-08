# Week 29: Multiple Windows and Navigation

## Main Topic
Opening and closing `JFrame` windows, passing data between forms,
`JOptionPane`, and `JDialog`.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Open a second `JFrame` window from within an existing application.
2. Pass data from one window to another through constructor parameters.
3. Close one window while keeping another window open, using the correct
   close operation for each.
4. Use `JOptionPane` to display information, confirmation, and input dialogs.
5. Create and display a custom `JDialog` for a focused, modal task.

## Prerequisite Knowledge
Students must already be comfortable creating `JFrame` windows, placing
components using layout managers, and attaching `ActionListener`s, as taught
in Weeks 23–27. No prior knowledge of multi-window navigation, dialogs, or
`JOptionPane` is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Weeks 23–27; introducing the idea of multi-screen applications |
| 2 | 20 min | Opening a second `JFrame` from a button click |
| 3 | 20 min | Passing data between windows through constructors |
| 4 | 20 min | Managing close operations across multiple open windows |
| 5 | 25 min | `JOptionPane`: message, confirmation, and input dialogs |
| 6 | 25 min | `JDialog`: building a custom modal window; live coding a two-screen application |

## Detailed Lesson Discussion

Every application built so far in this course has consisted of exactly one
window. Real applications, however, almost always involve moving between
multiple screens: a login screen followed by a main menu, a list screen that
opens a detail screen, or a main form that opens a small pop-up to confirm an
action. This week introduces the techniques needed to build applications with
more than one window.

Opening a second window from a running application is done exactly the way
any object is created in Java: by calling `new` on a `JFrame` subclass from
inside an event handler, most commonly a button's `ActionListener`. For
example, a "View Details" button's handler might contain the single line
`new DetailsWindow();`, which constructs and displays the new window
immediately, following the same construction pattern used since Week 23.
Because Swing runs on the Event Dispatch Thread, both the original window and
the new window continue to exist and respond to input independently once the
new window is created; nothing about opening a second window requires
stopping or pausing the first one.

A key design question when opening a second window is what should happen to
the first window. If both windows should remain open and usable at the same
time, no special action is required beyond creating the new window. If the
first window should close once the second one opens, the first window's
`dispose()` method can be called, which closes and releases that specific
window without affecting the rest of the running program; this is different
from `System.exit(0)`, which would forcibly end the entire application,
closing every window at once. This distinction is exactly why multi-window
applications typically avoid using `JFrame.EXIT_ON_CLOSE` on every single
window; a secondary window is often configured with
`JFrame.DISPOSE_ON_CLOSE` instead, so that closing it does not accidentally
terminate the whole application while a main window is still meant to be
open.

Very often, a second window needs information from the first window in order
to display something meaningful, such as showing a details screen for
whichever student record was selected on a list screen. The cleanest way to
pass this information is through the second window's constructor, exactly
the same technique used for passing arguments to any constructor since Week
11. A `DetailsWindow` class might declare a constructor
`public DetailsWindow(String studentName, int studentAge)`, store those
values into instance variables, and use them immediately while building its
labels and fields. The calling code, from the first window's event handler,
would then read its own fields' current values and pass them directly, such
as `new DetailsWindow(nameField.getText(), age);`. This is the same idea as
passing data between methods, just applied to passing data into a newly
created window rather than into an ordinary method.

For very short interactions that do not need an entire custom window, Swing
provides `JOptionPane`, a utility class with several static methods that pop
up common, ready-made dialog boxes. `JOptionPane.showMessageDialog(parent,
message)` displays a simple message with an OK button, useful for
confirmations or error notices without needing to design an entire form for
that purpose. `JOptionPane.showConfirmDialog(parent, message)` asks a
yes/no/cancel-style question and returns an `int` representing which button
the user clicked, which can be compared against constants such as
`JOptionPane.YES_OPTION` to decide what to do next. `JOptionPane.showInputDialog(parent,
message)` displays a small text field alongside the message and returns
whatever the user typed as a `String`, or `null` if the user clicked Cancel,
which must always be checked before using the result to avoid a
`NullPointerException`. The `parent` argument in each of these calls is
typically the current window (often written as `this` from inside that
window's own class), which tells Swing which window the dialog should be
centered over and treated as logically connected to.

When a situation calls for more than what `JOptionPane` provides, but still
represents a short, focused task rather than a full independent screen, a
custom `JDialog` is the appropriate tool. A `JDialog` is built almost exactly
like a `JFrame` — it can contain labels, fields, and buttons arranged with
layout managers — but it is specifically designed to be attached to an owner
window and is most often made **modal**, meaning that while the dialog is
open, the user cannot interact with its owner window until the dialog is
closed. Modality is set by calling `setModal(true)`, and a dialog is
typically constructed by passing the owner frame into its constructor, such
as `new JDialog(ownerFrame, "Confirm Action", true)`. Choosing between
`JOptionPane` and a custom `JDialog` usually comes down to complexity: a
short yes/no question or single input value fits `JOptionPane` well, while a
task requiring several fields or more specific layout deserves its own
`JDialog`.

## Important Terminology

Key terms: **multi-window application**, **dispose()**, **DISPOSE_ON_CLOSE**,
**constructor-based data passing**, **JOptionPane**, **showMessageDialog**,
**showConfirmDialog**, **showInputDialog**, **JDialog**, and **modal
dialog**.

## Complete Java Programming Example

```java
package week29.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JTextField;

/**
 * A two-window contact management demo. The main window collects a name
 * and email, then opens a details window showing that data, demonstrating
 * data passing between windows and the use of JOptionPane.
 */
public class ContactMainWindow extends JFrame {

    private final JTextField nameField;
    private final JTextField emailField;

    public ContactMainWindow() {
        super("Contact Manager - Main");
        setSize(360, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel formPanel = new JPanel();
        formPanel.add(new JLabel("Name:"));
        nameField = new JTextField(15);
        formPanel.add(nameField);

        formPanel.add(new JLabel("Email:"));
        emailField = new JTextField(15);
        formPanel.add(emailField);

        add(formPanel);

        JButton viewDetailsButton = new JButton("View Details");
        add(viewDetailsButton, java.awt.BorderLayout.SOUTH);

        viewDetailsButton.addActionListener(e -> {
            String name = nameField.getText().trim();
            String email = emailField.getText().trim();

            if (name.isEmpty() || email.isEmpty()) {
                JOptionPane.showMessageDialog(this,
                        "Please enter both a name and an email address.",
                        "Missing Information",
                        JOptionPane.WARNING_MESSAGE);
                return;
            }

            int choice = JOptionPane.showConfirmDialog(this,
                    "Open details window for " + name + "?",
                    "Confirm",
                    JOptionPane.YES_NO_OPTION);

            if (choice == JOptionPane.YES_OPTION) {
                new ContactDetailsWindow(name, email);
            }
        });

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new ContactMainWindow();
    }
}
```

```java
package week29.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;

/**
 * A secondary window that displays contact details passed in through its
 * constructor. Closing this window does not end the whole application.
 */
public class ContactDetailsWindow extends JFrame {

    public ContactDetailsWindow(String name, String email) {
        super("Contact Details");
        setSize(320, 180);
        setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);

        JPanel panel = new JPanel();
        panel.add(new JLabel("Name: " + name));
        panel.add(new JLabel("Email: " + email));
        add(panel);

        JButton closeButton = new JButton("Close");
        add(closeButton, java.awt.BorderLayout.SOUTH);
        closeButton.addActionListener(e -> dispose());

        setLocationRelativeTo(null);
        setVisible(true);
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week29-ContactManager`.
2. Create a package named `week29.gui`.
3. Create the class `ContactMainWindow` and paste in its complete code.
4. Create a second class `ContactDetailsWindow` in the same package and paste
   in its complete code.
5. Save both files and resolve imports using **Ctrl+Shift+O** on each.
6. Right-click `ContactMainWindow.java` and choose **Run As > Java
   Application** (do not run `ContactDetailsWindow` directly, since it has no
   `main` method).
7. Type a name and email, click **View Details**, and confirm the "Confirm"
   dialog appears.
8. Click **Yes** and confirm the details window opens showing the entered
   name and email.
9. Close the details window using its **Close** button, and confirm the main
   window remains open and usable.
10. Click **View Details** again with the fields cleared, and confirm the
    "Missing Information" warning dialog appears instead of opening a details
    window.
11. Click the details window's own operating-system close button (the "X" in
    its title bar) instead of the custom **Close** button, and confirm it
    behaves the same way, since `DISPOSE_ON_CLOSE` applies to both.
12. As an experiment, temporarily change `ContactDetailsWindow`'s close
    operation to `JFrame.EXIT_ON_CLOSE`, run the program again, open a
    details window, and close it; observe that the entire application,
    including the main window, now terminates. Restore `DISPOSE_ON_CLOSE`
    afterward.

## Guided Worked Example

**Problem description:** Build a small utility that asks the user for their
favorite number using `JOptionPane.showInputDialog`, then reports whether the
number is even or odd using `JOptionPane.showMessageDialog`.

```java
package week29.gui;

import javax.swing.JFrame;
import javax.swing.JOptionPane;

public class EvenOddChecker {

    public static void main(String[] args) {
        JFrame invisibleOwner = new JFrame();

        String input = JOptionPane.showInputDialog(invisibleOwner,
                "Enter your favorite whole number:");

        if (input == null) {
            System.out.println("User cancelled the dialog.");
            return;
        }

        try {
            int number = Integer.parseInt(input.trim());
            String result = (number % 2 == 0) ? "even" : "odd";
            JOptionPane.showMessageDialog(invisibleOwner,
                    number + " is " + result + ".");
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(invisibleOwner,
                    "That was not a valid whole number.",
                    "Invalid Input",
                    JOptionPane.ERROR_MESSAGE);
        }
    }
}
```

**Walkthrough:** Because this small utility has no visible main window of its
own, an invisible `JFrame` is created purely to serve as the "owner" for the
dialogs; it is never made visible with `setVisible(true)`. The result of
`showInputDialog` is checked against `null` before anything else happens,
since clicking Cancel or closing the dialog without typing anything returns
`null` rather than an empty string, and calling `.trim()` on a `null`
reference would throw a `NullPointerException`.

**Expected behavior:** Entering `14` displays "14 is even."; entering `7`
displays "7 is odd."; entering `abc` displays an error dialog; clicking
Cancel prints a message to the console instead of showing any further dialog.

**Test data:** Test with `0` (should report even), a negative number such as
`-3` (should report odd), and a Cancel click.

**Likely errors:** Skipping the `null` check on the input dialog's result is
the most common mistake and produces a `NullPointerException` the first time
a user clicks Cancel.

## Code Walkthrough

In `ContactMainWindow`, the button handler first validates that both fields
are non-empty, exactly as practiced in Week 26, before doing anything else.
If validation passes, `JOptionPane.showConfirmDialog` is used to ask the user
to confirm before actually opening a new window, demonstrating that dialogs
and full windows can be combined within a single interaction. Only if the
user clicks Yes does the code call `new ContactDetailsWindow(name, email)`,
passing the two `String` values directly into the second window's
constructor. Inside `ContactDetailsWindow`, those two parameters are used
immediately to build labels, rather than being stored as fields, since this
particular window has no further need to reference them again after
construction. The `Close` button's handler calls `dispose()` rather than
`System.exit(0)`, ensuring only this specific window closes.

## Expected GUI or Program Behavior

Running `ContactMainWindow`, entering valid data, confirming the dialog, and
clicking View Details opens a second window displaying exactly the name and
email that were entered in the first window. Closing the second window,
through either its Close button or its title bar close box, leaves the main
window open and fully functional. Attempting to view details with empty
fields shows a warning dialog instead of opening a new window.

## Laboratory Session (3 Hours)

### Laboratory Title
Building a Two-Screen Inventory Lookup Application

### Objectives
1. Open a second window from a button click in an existing application.
2. Pass structured data between two windows through constructor parameters.
3. Use `JOptionPane` for both validation warnings and confirmation prompts.
4. Correctly manage close operations across two simultaneously open windows.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
Constructors and instance variables (Weeks 11–12), event handling (Week 26),
and layout managers (Week 27), combined with this week's multi-window and
dialog material.

### Development Requirements
Create an Eclipse project `Week29-Lab` with package `week29.lab` containing
two classes: `InventoryMainWindow`, with fields for product name, quantity,
and unit price, and a button labeled "Lookup Details"; and
`InventoryDetailsWindow`, which receives the product name, quantity, and unit
price through its constructor and displays them along with a computed total
value (quantity multiplied by unit price).

### Detailed Laboratory Procedure

1. Create the project and package as described.
2. Build `InventoryMainWindow` with a text field for product name, a text
   field for quantity, a text field for unit price, and a "Lookup Details"
   button, arranged using a layout manager from Week 27 (not
   `setLayout(null)`).
3. In the button's event handler, validate that the product name is not
   empty and that quantity and unit price are both valid numbers, showing an
   appropriate `JOptionPane` warning message for each type of invalid input.
4. If validation passes, show a `JOptionPane` confirmation dialog asking
   "Open details for [product name]?" before proceeding.
5. If the user confirms, construct `InventoryDetailsWindow`, passing the
   product name, quantity, and unit price.
6. Build `InventoryDetailsWindow` to display the product name, quantity, unit
   price, and a computed total value, formatted clearly, with a Close button
   that calls `dispose()`, and a close operation of `DISPOSE_ON_CLOSE`.
7. Run the application, enter valid data, confirm the dialog, and verify the
   details window shows the correct computed total.
8. Close the details window and confirm the main window is still fully
   usable, including being able to open a second details window with
   different data.
9. Test each invalid case listed below and confirm the correct warning
   dialog appears in each case.

### Required Features
- The main window must validate product name (non-empty), quantity (valid
  positive integer), and unit price (valid non-negative number) before
  opening the details window.
- The details window must correctly compute and display the total value.
- Closing the details window must never close the main window.

### Testing Procedure
Test the application with several combinations of valid and invalid data,
confirming correct dialog messages, correct data passed to the details
window, and correct independent closing behavior for each window.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Valid entry | Name `USB Cable`, quantity `10`, price `5.50` | Click Lookup Details, confirm Yes | Details window shows total value 55.00 |
| Open two details windows in sequence | Enter first product, view details, close it, enter second product, view details | Repeat lookup twice | Both details windows show correct, independent data |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Quantity of 0 | Name `Stapler`, quantity `0`, price `12.00` | Click Lookup Details | Accepted as valid (zero is a legitimate quantity); total shown as 0.00 |
| Very small price | Price `0.01` | Click Lookup Details | Accepted; total computed correctly with decimals |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Empty product name | Leave name blank | Click Lookup Details | Warning dialog about missing product name |
| Non-numeric quantity | Quantity `ten` | Click Lookup Details | Warning dialog about invalid quantity |
| Negative unit price | Price `-5.00` | Click Lookup Details | Warning dialog about invalid price |
| Cancel confirmation dialog | Valid data entered, click No on confirmation | N/A | Details window does not open |

### Troubleshooting Guidance
If clicking "Lookup Details" appears to do nothing, confirm the
`ActionListener` was correctly attached and that validation is not silently
failing due to a missing `else` or `return`. A `NullPointerException` when
reading a `JOptionPane` input result usually means the returned value was
used before being checked for `null`. If the details window opens but shows
"null" for one of its fields, confirm the constructor parameters are being
passed in the same order they are declared. If closing the details window
also closes the main window, confirm the details window's close operation is
set to `DISPOSE_ON_CLOSE`, not `EXIT_ON_CLOSE`.

### Required Deliverables
1. Eclipse project `Week29-Lab` with both completed classes.
2. Screenshots showing a successful details lookup and at least one warning
   dialog triggered by invalid input.
3. A short written explanation of why `InventoryDetailsWindow` uses
   `DISPOSE_ON_CLOSE` instead of `EXIT_ON_CLOSE`.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Validation correctly implemented with appropriate JOptionPane warnings | 25 |
| Confirmation dialog correctly implemented before opening details window | 15 |
| Data correctly passed between windows through constructor | 20 |
| Total value correctly computed and displayed | 15 |
| Correct close operations; main window unaffected by closing details window | 15 |
| Submission completeness | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

A common problem this week is an application that exits entirely when only
the secondary window was meant to close; this is almost always caused by
leaving the secondary window's close operation at `EXIT_ON_CLOSE` instead of
`DISPOSE_ON_CLOSE`. Another frequent issue is a `NullPointerException`
immediately after an input dialog, caused by not checking the result of
`JOptionPane.showInputDialog` for `null` before using it. If a details window
opens but the data displayed does not match what was entered, check the
order of constructor parameters against the order of arguments used when
calling `new` — Java matches parameters by position, not by name. If the
second window never appears at all, confirm that `new
SecondWindowClass(...)` is actually being reached in the code, which can be
verified with a temporary `System.out.println` immediately before that line.

## Review and Practice Questions

1. What is the difference between calling `dispose()` on a window and
   calling `System.exit(0)`?
2. Why must the result of `JOptionPane.showInputDialog` always be checked
   for `null` before use?
3. Describe how data is passed from `ContactMainWindow` into
   `ContactDetailsWindow` in this week's main example.
4. When would a custom `JDialog` be a better choice than `JOptionPane`?
5. Rewrite the "Even/Odd Checker" worked example so that it also reports
   whether the number is positive, negative, or zero, in addition to even or
   odd.
6. A classmate's secondary window closes the entire application when its
   close button is clicked. What single line most likely needs to change?

## Lesson Summary

This week extended single-window applications into multi-window
applications, covering how to open a second window from an event handler,
pass data between windows through constructors, manage independent close
behavior using `dispose()` and `DISPOSE_ON_CLOSE`, and use `JOptionPane` for
quick messages, confirmations, and input prompts, alongside custom `JDialog`
windows for more complex short interactions. Week 30 builds on this
navigation foundation by introducing menus, tables, scroll panes, and tabbed
panes for organizing more complex application interfaces.
