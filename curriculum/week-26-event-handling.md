# Week 26: Event Handling

## Main Topic
`ActionListener`, `ActionEvent`, button-click events, item events, keyboard
and mouse events, and basic form interaction.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Explain how the Event Dispatch Thread delivers events to registered
   listeners.
2. Attach an `ActionListener` to a `JButton` so that clicking it executes
   Java code.
3. Read values from text fields, checkboxes, radio buttons, combo boxes,
   lists, and spinners inside an event handler.
4. Respond to selection changes using `ItemListener` and to keyboard input
   using `KeyListener`.
5. Update labels and other components dynamically in response to user
   actions.

## Prerequisite Knowledge
Students must be comfortable creating and placing all of the components
covered in Weeks 24 and 25, including reading their initial values using
methods such as `getText()`, `isSelected()`, `getSelectedItem()`, and
`getValue()`. No prior knowledge of listeners or event objects is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 15 min | Review: why last week's button clicks did nothing |
| 2 | 25 min | `ActionListener` and `ActionEvent`: making a button respond |
| 3 | 20 min | Anonymous classes and lambda expressions for concise listeners |
| 4 | 20 min | Reading values from multiple components inside one handler |
| 5 | 20 min | `ItemListener` for checkboxes/combo boxes; `KeyListener` for keyboard input |
| 6 | 20 min | Live coding: a fully interactive form with live feedback label |

## Detailed Lesson Discussion

Every component built over the last two weeks could be clicked, typed into,
or selected, but nothing in the program ever *reacted* to those actions. This
week closes that gap by introducing **event listeners**, the mechanism Java
uses to connect a user action, such as a mouse click, to a specific block of
code that should run in response.

Recall from Week 23 that Swing applications are event-driven: the program
displays a window and then waits, and the Event Dispatch Thread continuously
watches for user actions. When the user clicks a button, the EDT packages
information about that click into an `ActionEvent` object and looks for any
listener objects that have been registered with that specific button. If one
is found, the EDT calls that listener's `actionPerformed(ActionEvent e)`
method automatically. The programmer never calls `actionPerformed` directly;
it is called *by Swing*, in response to the event, which is why this style of
programming is described as event-driven rather than sequential.

To register a listener with a button, the `ActionListener` interface must be
implemented and passed to the button's `addActionListener(...)` method.
`ActionListener` is a **functional interface**, meaning it declares exactly
one abstract method, `actionPerformed(ActionEvent e)`, which is why Java 8's
lambda expression syntax can be used to implement it very concisely. A
listener can be written in three different ways: as a separate class that
implements `ActionListener`, as an anonymous inner class created inline
exactly where it is needed, or as a lambda expression. For simple, short
handlers, this course primarily uses lambda expressions, because they keep
the listener code visually close to the component it belongs to, which makes
small programs easier to read. The syntax
`submitButton.addActionListener(e -> { ... })` means: whenever this button
fires an action event, run the code inside the braces, and the event object
itself is available inside that block under the name `e`, though for a simple
button click the event object is rarely needed directly.

Inside an event handler, the program typically needs to read values from
several components at once and combine them into a message or a result. This
is exactly where the `getText()`, `isSelected()`, `getSelectedItem()`,
`getSelectedValue()`, and `getValue()` methods introduced in the previous two
weeks become useful, because they are most often called at the moment the
user clicks a button to submit or process a form, rather than immediately
when the window first opens. A frequent pattern is to build a `String`
message piece by piece using string concatenation or a `StringBuilder`, and
then display that message either by updating a label already on the form or
by showing a popup dialog. Updating a label is done the same way as before,
by calling `setText(...)` on the label, except now that call happens inside
the event handler rather than inside the constructor, which means the label's
text changes live while the program is running, rather than being fixed at
startup.

Not every interaction happens through a button click. An `ItemListener`
responds whenever the *state* of a component changes, such as a checkbox
being checked or unchecked, or a new item being chosen in a combo box. It is
registered using `addItemListener(...)` and defines a single method,
`itemStateChanged(ItemEvent e)`. This is useful when a form should react
immediately to a selection change rather than waiting for a separate submit
button, such as instantly updating a price label when a combo box selection
changes. A `KeyListener`, registered using `addKeyListener(...)`, reacts to
individual key presses and releases on a text component, and is useful for
situations such as showing a live character count as the user types, although
for most ordinary form processing, reading the field's full text at submit
time using `getText()` remains simpler and is preferred in this course except
where live feedback is specifically required. Java also provides a
`MouseListener` for responding to mouse clicks, presses, and releases on any
component, following the same registration pattern as the other listeners
covered this week.

It is worth noticing that all of these listener interfaces follow the same
underlying design: a component allows one or more listener objects to
register interest in a certain category of event, and Swing calls the
appropriate method on that listener automatically whenever a matching event
actually occurs. Recognizing this repeated pattern — register a listener,
implement its method, let Swing call it automatically — makes it much easier
to learn additional listener types independently in the future, since they
all follow the same basic structure introduced this week.

## Important Terminology

Key terms: **event listener**, **ActionListener**, **ActionEvent**,
**actionPerformed**, **functional interface**, **lambda expression**,
**ItemListener**, **itemStateChanged**, **KeyListener**, and **MouseListener**.

## Complete Java Programming Example

```java
package week26.gui;

import javax.swing.JButton;
import javax.swing.JCheckBox;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;

/**
 * An interactive order form: entering a product name, quantity, and
 * whether gift wrap is requested, then clicking a button to calculate and
 * display a summary message.
 */
public class OrderForm extends JFrame {

    private final JTextField productField;
    private final JTextField quantityField;
    private final JComboBox<String> sizeComboBox;
    private final JCheckBox giftWrapCheckBox;
    private final JLabel resultLabel;

    public OrderForm() {
        super("Simple Order Form");
        setSize(420, 320);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        JLabel productLabel = new JLabel("Product Name:");
        productLabel.setBounds(20, 20, 110, 25);
        add(productLabel);

        productField = new JTextField(15);
        productField.setBounds(140, 20, 200, 25);
        add(productField);

        JLabel quantityLabel = new JLabel("Quantity:");
        quantityLabel.setBounds(20, 60, 110, 25);
        add(quantityLabel);

        quantityField = new JTextField(15);
        quantityField.setBounds(140, 60, 200, 25);
        add(quantityField);

        JLabel sizeLabel = new JLabel("Size:");
        sizeLabel.setBounds(20, 100, 110, 25);
        add(sizeLabel);

        sizeComboBox = new JComboBox<>(new String[] {"Small", "Medium", "Large"});
        sizeComboBox.setBounds(140, 100, 150, 25);
        add(sizeComboBox);

        giftWrapCheckBox = new JCheckBox("Gift wrap this order");
        giftWrapCheckBox.setBounds(140, 135, 200, 25);
        add(giftWrapCheckBox);

        JButton calculateButton = new JButton("Calculate Order");
        calculateButton.setBounds(140, 175, 150, 30);
        add(calculateButton);

        resultLabel = new JLabel(" ");
        resultLabel.setBounds(20, 220, 380, 60);
        add(resultLabel);

        calculateButton.addActionListener(e -> processOrder());

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void processOrder() {
        String product = productField.getText().trim();

        if (product.isEmpty()) {
            resultLabel.setText("<html>Please enter a product name.</html>");
            return;
        }

        int quantity;
        try {
            quantity = Integer.parseInt(quantityField.getText().trim());
        } catch (NumberFormatException ex) {
            resultLabel.setText("<html>Quantity must be a whole number.</html>");
            return;
        }

        String size = (String) sizeComboBox.getSelectedItem();
        boolean giftWrap = giftWrapCheckBox.isSelected();

        String summary = "<html>Order: " + quantity + " x " + product
                + " (" + size + ")<br>Gift wrap: "
                + (giftWrap ? "Yes" : "No") + "</html>";
        resultLabel.setText(summary);
    }

    public static void main(String[] args) {
        new OrderForm();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week26-OrderForm`.
2. Create a package named `week26.gui`.
3. Create the class `OrderForm` and paste in the complete example.
4. Save the file and resolve any imports using **Ctrl+Shift+O**.
5. Run the class using **Run As > Java Application**.
6. Type a product name, a quantity, choose a size, and check or uncheck the
   gift-wrap box.
7. Click **Calculate Order** and observe the summary message appear in the
   label at the bottom of the window.
8. Clear the product field and click **Calculate Order** again; confirm the
   label instead shows the "Please enter a product name" message.
9. Type letters into the quantity field instead of numbers, click
   **Calculate Order**, and confirm the label shows the "must be a whole
   number" message instead of crashing.
10. Open the Eclipse Console view and confirm no stack trace appears during
    the invalid-quantity test, since the `try-catch` block already handled
    the error.
11. Temporarily remove the `try-catch` block around the parsing code
    (replace it with a plain `Integer.parseInt(...)` call), save, run, and
    intentionally type letters into the quantity field to see the unhandled
    `NumberFormatException` printed in the Console. Restore the `try-catch`
    block afterward.

## Guided Worked Example

**Problem description:** Build a small "Feedback Counter" form containing a
text area, a button, and a label that reports the number of characters typed
whenever the button is clicked.

```java
package week26.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextArea;

public class FeedbackCounterForm extends JFrame {

    private final JTextArea feedbackArea;
    private final JLabel countLabel;

    public FeedbackCounterForm() {
        super("Feedback Counter");
        setSize(360, 280);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        feedbackArea = new JTextArea(6, 20);
        feedbackArea.setLineWrap(true);
        feedbackArea.setBounds(20, 20, 300, 120);
        add(feedbackArea);

        JButton countButton = new JButton("Count Characters");
        countButton.setBounds(90, 150, 160, 30);
        add(countButton);

        countLabel = new JLabel("Character count: 0");
        countLabel.setBounds(20, 195, 300, 25);
        add(countLabel);

        countButton.addActionListener(e -> {
            int length = feedbackArea.getText().length();
            countLabel.setText("Character count: " + length);
        });

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new FeedbackCounterForm();
    }
}
```

**Walkthrough:** The lambda passed to `addActionListener` reads
`feedbackArea.getText()` at the moment the button is clicked, not when the
window first opened, which means the count always reflects whatever is
currently in the text area. `setText(...)` on `countLabel` immediately
updates what is displayed, demonstrating that a label's text is not fixed
once the constructor finishes, but can change any number of times while the
program runs.

**Expected behavior:** Typing "Great course!" and clicking the button
displays "Character count: 13". Clearing the text area and clicking again
displays "Character count: 0".

**Test data:** Text containing line breaks should still count correctly,
since `length()` counts every character, including newline characters
created by pressing Enter inside the text area.

**Likely errors:** A `NullPointerException` here almost always means the
listener was attached before `countLabel` was constructed, or that a
different field was accidentally referenced.

## Code Walkthrough

The core addition in `OrderForm` is the line
`calculateButton.addActionListener(e -> processOrder());`, which registers a
lambda as the button's `ActionListener`. Whenever the button fires an action
event, Swing calls this lambda, which in turn calls the private method
`processOrder()`. Separating the actual logic into its own method, rather
than writing everything directly inside the lambda, keeps the constructor
focused on building the window and keeps the processing logic easy to read
and modify independently. Inside `processOrder()`, values are pulled from
every relevant component only at the moment the button is clicked, validated
one at a time, and used to build a single summary string, which is then
placed into `resultLabel` using `setText(...)`. The HTML tags inside the
string are a common Swing technique that allows a `JLabel` to display text
across multiple lines, since a plain `JLabel` otherwise treats all of its
text as a single line no matter how long it is.

## Expected GUI or Program Behavior

Running `OrderForm` and filling in valid data, then clicking **Calculate
Order**, displays a two-line summary showing the quantity, product name,
size, and gift-wrap status. Leaving the product name blank or typing
non-numeric text into the quantity field displays an appropriate short error
message in the same label instead of crashing the application.

## Laboratory Session (3 Hours)

### Laboratory Title
Making the Course Enrollment Form Interactive

### Objectives
1. Attach `ActionListener`s to buttons using lambda expressions.
2. Read and combine values from multiple component types inside a single
   event handler.
3. Provide immediate visual feedback to the user through a label.
4. Handle at least one invalid-input scenario without crashing the
   application.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
Exception handling (Week 17), all Swing components from Weeks 24–25, and
this week's `ActionListener` material.

### Development Requirements
Extend the `CourseEnrollmentForm` class built in Week 24's laboratory (or
recreate it if necessary) inside a new project `Week26-Lab`, package
`week26.lab`, adding: an "Enroll" button event handler that reads the student
number, full name, and password fields; a `JComboBox` for preferred campus
("Main Campus", "North Campus", "Online"); a `JCheckBox` for "International
Student"; and a result label that displays a formatted enrollment summary or
an appropriate error message when the student number or name field is empty.

### Detailed Laboratory Procedure

1. Recreate or copy the Week 24 `CourseEnrollmentForm` fields into a new
   class `InteractiveEnrollmentForm` in the `week26.lab` package.
2. Add a `JComboBox` for campus selection and a `JCheckBox` for
   international student status, positioned without overlapping existing
   components.
3. Add a result `JLabel`, initially blank, positioned near the bottom of the
   form.
4. Attach an `ActionListener` to the Enroll button using a lambda expression.
5. Inside the handler, first check that the student number field and full
   name field are not empty (using `.trim().isEmpty()`); if either is empty,
   set the result label to a clear error message and stop processing further
   in that same click (using `return` inside the handler method).
6. If both required fields are filled in, build a summary string containing
   the student number, name, selected campus, and whether the student
   identified as an international student, and display it in the result
   label.
7. Run the form, fill in all fields correctly, and click Enroll; confirm the
   summary appears correctly.
8. Clear the name field only, click Enroll again, and confirm the specific
   error message appears instead of a partial or incorrect summary.
9. Re-fill the name field, select each of the three campus options in turn,
   and click Enroll each time, confirming the label updates to reflect the
   newly selected campus every time.

### Required Features
- Clicking Enroll with all required fields filled must display a complete,
  correctly formatted summary.
- Clicking Enroll with the student number or name empty must display an
  error message instead of a summary.
- The selected campus and international student status must both appear
  correctly in the summary when valid.

### Testing Procedure
Test the button with a full range of valid and invalid combinations of
field values, confirming the correct message appears in the result label
each time without any exception appearing in the Eclipse Console.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Complete valid entry | Student number `2025-0044`, name `Liam Cruz`, campus `Main Campus`, unchecked | Click Enroll | Summary shows all four pieces of information correctly |
| International student | Same as above but checkbox checked | Click Enroll | Summary shows "International Student: Yes" |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Minimum valid name | Single character name, e.g. `A` | Click Enroll | Still processed successfully since only emptiness is checked, not length |
| Switching campus repeatedly | Change combo box selection three times without re-clicking Enroll | No action until Enroll clicked | Label does not change until the button is clicked, since no ItemListener is attached in this lab |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Empty student number | Leave student number blank, fill in name | Click Enroll | Error message displayed; no summary shown |
| Empty name | Fill in student number, leave name blank | Click Enroll | Error message displayed; no summary shown |
| Both fields empty | Leave both blank | Click Enroll | Error message displayed; no summary shown |

### Troubleshooting Guidance
If clicking the button appears to do nothing, confirm that
`addActionListener(...)` was called on the correct button variable and that
the lambda body actually updates the result label rather than an unused local
variable. A `NullPointerException` thrown when the button is clicked usually
means the listener references a field that has not yet been initialized at
that point in the constructor; make sure the listener is registered only
after every referenced component has already been constructed. If the error
message and the summary both seem to appear or disappear incorrectly, check
that the `return` statement is present after setting the error message, since
without it, the code will continue running and may overwrite the error
message with a summary built from missing data.

### Required Deliverables
1. Eclipse project `Week26-Lab` with the completed `InteractiveEnrollmentForm`
   class.
2. Screenshots showing a valid summary result and at least one error message
   result.
3. A short written explanation of what the `return` statement inside the
   event handler accomplishes.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| ActionListener correctly attached and triggers processing | 20 |
| Correct reading of all component values (text fields, combo box, checkbox) | 20 |
| Correct handling of empty required fields | 25 |
| Summary message correctly formatted and displayed | 20 |
| Submission completeness (screenshots and explanation) | 15 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a button that visually depresses when
clicked but produces no change anywhere on the form; this almost always
means `addActionListener` was never called, or was called on the wrong
variable due to a naming mistake. Another frequent issue is an event handler
that appears to work only the first time; this usually happens when a label
is updated using string concatenation incorrectly, overwriting itself with
stale data, or when a required `return` statement is missing after an error
condition, allowing the rest of the method to run using incomplete data. When
converting text to a number inside a handler, always wrap the conversion in a
`try-catch` block for `NumberFormatException`, since a user can always type
non-numeric text into any text field regardless of its intended purpose. If
Eclipse reports that a lambda expression "cannot be resolved," confirm that
the interface being implemented, such as `ActionListener`, is a functional
interface with exactly one abstract method; lambdas cannot be used for
interfaces with more than one abstract method.

## Review and Practice Questions

1. Explain what happens, step by step, from the moment a user clicks a
   button to the moment `actionPerformed` runs.
2. Why is `ActionListener` compatible with lambda expression syntax, while
   some other interfaces are not?
3. Why does the `processOrder()` method call `return` immediately after
   showing an error message?
4. Rewrite the "Feedback Counter" worked example so that the count also
   reports the number of words, in addition to the number of characters
   (hint: consider how you might split the text on spaces).
5. What is the difference between an `ActionListener` and an `ItemListener`,
   and give one example of a component where each would be an appropriate
   choice.
6. A classmate's calculate button throws a `NumberFormatException` that
   crashes the whole program. What is missing from their event handler?

## Lesson Summary

This week connected the static forms built in Weeks 24 and 25 to actual user
interaction by introducing `ActionListener`, lambda-based event handlers, and
the related `ItemListener` and `KeyListener` interfaces. Students learned how
Swing automatically calls a registered listener's method in response to a
user action, and practiced reading values from multiple component types
inside a single handler while validating for empty or invalid input. Week 27
builds on this interactivity by introducing formal layout managers, replacing
the absolute positioning used so far with flexible, resizable form designs.
