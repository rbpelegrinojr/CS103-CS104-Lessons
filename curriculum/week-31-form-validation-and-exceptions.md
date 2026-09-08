# Week 31: Form Validation and Exception Handling

## Main Topic
Required fields, numeric validation, input checking, try-catch in GUI
applications, and validation or error messages.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Identify the categories of invalid input a GUI form must guard against.
2. Write reusable validation methods that check required fields and numeric
   ranges.
3. Combine `try-catch` blocks with manual validation checks inside a single
   event handler.
4. Display clear, specific error messages to the user through labels and
   `JOptionPane`.
5. Prevent a form from proceeding to "save" or "submit" logic until all
   validation checks pass.

## Prerequisite Knowledge
Students must already be comfortable with `try-catch` blocks from Week 17,
event handling from Week 26, and `JOptionPane` from Week 29. No new Swing
components are introduced this week; the focus is entirely on validating
data already collected using components taught in previous weeks.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 15 min | Review: why unvalidated forms are a real-world risk |
| 2 | 20 min | Required-field validation patterns |
| 3 | 25 min | Numeric validation: parsing safely and checking ranges |
| 4 | 20 min | Combining multiple validation checks in one handler |
| 5 | 20 min | Displaying validation feedback: labels vs. JOptionPane |
| 6 | 20 min | Live coding: building a fully validated registration form |

## Detailed Lesson Discussion

Every form built since Week 24 has collected data from the user, but only a
few of them, starting in Week 26, checked whether that data made any sense
before using it. A production-quality application must always assume the
user might leave a field blank, type letters into a numeric field, enter a
number outside a reasonable range, or otherwise provide input the program
did not expect. This week focuses specifically on strengthening that
validation, bringing together the exception handling learned in Week 17 with
the GUI techniques built over the preceding weeks.

The simplest and most common validation check is confirming that a required
text field is not empty. Because `getText()` always returns a `String`, even
when nothing was typed, this check is written as
`field.getText().trim().isEmpty()`, where `trim()` removes leading and
trailing whitespace so that a field containing only spaces is correctly
treated as empty rather than accidentally accepted as valid input. When
several fields on the same form are all required, it is common to write a
small reusable helper method, such as
`private boolean isBlank(JTextField field) { return
field.getText().trim().isEmpty(); }`, so that the same check does not need to
be retyped for every field, and so that a single, consistent definition of
"blank" is used throughout the class.

Numeric validation involves two separate concerns that are often confused by
beginners: whether the text can be converted into a number at all, and
whether that number, once converted, falls within an acceptable range. The
first concern is handled with a `try-catch` block around
`Integer.parseInt(...)` or `Double.parseDouble(...)`, catching
`NumberFormatException`, exactly as practiced since Week 26. The second
concern is an ordinary conditional check performed only *after* parsing
succeeds, such as confirming that an age is not negative or that a quiz score
does not exceed 100. Both checks are necessary because they catch different
kinds of mistakes: a `try-catch` block alone would happily accept the text
"-5" as a valid integer, even though a negative age or negative quantity may
not make sense for a given form, so a range check is still required
afterward.

When a form has several fields to validate, the recommended structure is to
check them one at a time, in a sensible order, and stop as soon as the first
problem is found. This is exactly the pattern used since Week 26: display a
specific message describing exactly what is wrong, then immediately `return`
from the event handler so that no further processing happens using
incomplete or invalid data. It might be tempting to try to check every field
at once and report every problem simultaneously, but for a beginning course,
checking and reporting one issue at a time keeps both the code and the user
experience simpler to follow; the user sees one specific fix needed at a
time, and the code needed to produce that behavior remains a straightforward
sequence of `if` checks rather than a more complex combined validation
result.

Deciding how to display a validation problem to the user is itself a small
design decision. A dedicated status label, updated with `setText(...)`
directly on the form, works well for lightweight, low-friction feedback that
the user can read without any interruption, and was the technique used in
Week 26 and Week 27's examples. A `JOptionPane.showMessageDialog(...)`, shown
in Week 29, is more appropriate when a validation problem is serious enough
that the user's attention should be forced onto it immediately, since a modal
dialog cannot be ignored until it is dismissed. Both approaches are valid
throughout this course, and the choice between them is a matter of how urgent
or disruptive that particular piece of feedback should feel.

It is worth explicitly connecting this week's material back to the security
implications discussed briefly in earlier weeks: validating input on the
client side, inside the Swing application itself, is about giving the user
clear, immediate feedback and about preventing the program from crashing on
bad input. It is not, by itself, a security boundary — a point that will
matter again starting in Week 33, when database operations begin, since
malicious or malformed input handled carelessly at the database layer creates
a very different kind of risk than a `NumberFormatException` in a desktop
form. For now, the goal is a form that never crashes, always tells the user
specifically what needs to be fixed, and never proceeds with an operation
using bad data.

## Important Terminology

Key terms: **required field**, **trim()**, **isEmpty()**,
**NumberFormatException**, **range validation**, **validation helper
method**, and **fail-fast validation**.

## Complete Java Programming Example

```java
package week31.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JTextField;

/**
 * A student quiz score entry form demonstrating required-field validation,
 * numeric parsing with try-catch, and range checking, all combined in a
 * single event handler.
 */
public class QuizScoreForm extends JFrame {

    private final JTextField nameField;
    private final JTextField scoreField;
    private final JLabel statusLabel;

    public QuizScoreForm() {
        super("Quiz Score Entry");
        setSize(380, 220);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel formPanel = new JPanel();
        formPanel.add(new JLabel("Student Name:"));
        nameField = new JTextField(15);
        formPanel.add(nameField);

        formPanel.add(new JLabel("Quiz Score (0-100):"));
        scoreField = new JTextField(5);
        formPanel.add(scoreField);

        add(formPanel, java.awt.BorderLayout.CENTER);

        JButton submitButton = new JButton("Submit Score");
        statusLabel = new JLabel(" ", JLabel.CENTER);

        JPanel bottomPanel = new JPanel(new java.awt.BorderLayout());
        bottomPanel.add(submitButton, java.awt.BorderLayout.NORTH);
        bottomPanel.add(statusLabel, java.awt.BorderLayout.SOUTH);
        add(bottomPanel, java.awt.BorderLayout.SOUTH);

        submitButton.addActionListener(e -> validateAndSubmit());

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private boolean isBlank(JTextField field) {
        return field.getText().trim().isEmpty();
    }

    private void validateAndSubmit() {
        if (isBlank(nameField)) {
            statusLabel.setText("Student name is required.");
            return;
        }

        if (isBlank(scoreField)) {
            statusLabel.setText("Quiz score is required.");
            return;
        }

        int score;
        try {
            score = Integer.parseInt(scoreField.getText().trim());
        } catch (NumberFormatException ex) {
            statusLabel.setText("Quiz score must be a whole number.");
            return;
        }

        if (score < 0 || score > 100) {
            statusLabel.setText("Quiz score must be between 0 and 100.");
            return;
        }

        String name = nameField.getText().trim();
        statusLabel.setText("Recorded: " + name + " scored " + score + ".");
    }

    public static void main(String[] args) {
        new QuizScoreForm();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a Java project named `Week31-QuizScore`.
2. Create a package named `week31.gui`.
3. Create the class `QuizScoreForm` and paste in the complete example.
4. Save and resolve imports with **Ctrl+Shift+O**.
5. Run the class using **Run As > Java Application**.
6. Leave both fields blank and click **Submit Score**; confirm the "name is
   required" message appears.
7. Fill in a name, leave the score blank, click **Submit Score**; confirm
   the "score is required" message appears.
8. Fill in a name and type letters into the score field, click **Submit
   Score**; confirm the "must be a whole number" message appears without any
   stack trace in the Console.
9. Fill in a name and type `150` into the score field, click **Submit
   Score**; confirm the "must be between 0 and 100" message appears.
10. Fill in a valid name and a score such as `88`, click **Submit Score**,
    and confirm the correct "Recorded" message appears.
11. As an experiment, temporarily move the range check (`score < 0 ||
    score > 100`) above the `try-catch` block instead of after it, and try to
    predict what compiler error this produces, since `score` would be used
    before Java can guarantee it was assigned; restore the correct order
    afterward.

## Guided Worked Example

**Problem description:** Build a small "Temperature Converter" form that
validates a Celsius temperature input is a valid number within a realistic
range (-100 to 100) before converting it to Fahrenheit.

```java
package week31.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;

public class TemperatureConverterForm extends JFrame {

    private final JTextField celsiusField;
    private final JLabel resultLabel;

    public TemperatureConverterForm() {
        super("Temperature Converter");
        setSize(340, 180);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel = new JPanel();
        panel.add(new JLabel("Celsius:"));
        celsiusField = new JTextField(8);
        panel.add(celsiusField);

        JButton convertButton = new JButton("Convert");
        panel.add(convertButton);
        add(panel, java.awt.BorderLayout.CENTER);

        resultLabel = new JLabel(" ", JLabel.CENTER);
        add(resultLabel, java.awt.BorderLayout.SOUTH);

        convertButton.addActionListener(e -> convert());

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void convert() {
        String text = celsiusField.getText().trim();
        if (text.isEmpty()) {
            resultLabel.setText("Please enter a temperature.");
            return;
        }

        double celsius;
        try {
            celsius = Double.parseDouble(text);
        } catch (NumberFormatException ex) {
            resultLabel.setText("Enter a valid numeric temperature.");
            return;
        }

        if (celsius < -100 || celsius > 100) {
            resultLabel.setText("Enter a temperature between -100 and 100.");
            return;
        }

        double fahrenheit = (celsius * 9.0 / 5.0) + 32;
        resultLabel.setText(celsius + "C is " + fahrenheit + "F");
    }

    public static void main(String[] args) {
        new TemperatureConverterForm();
    }
}
```

**Walkthrough:** The method checks emptiness first, then attempts parsing
inside `try-catch`, then checks the numeric range, following the exact same
three-stage order established in the main example: required-field check,
safe parsing, range check. Only once all three checks pass does the actual
conversion formula run.

**Expected behavior:** Entering `100` displays "100.0C is 212.0F"; entering
`abc` displays a parsing error message; entering `500` displays a range error
message; leaving the field blank displays a required-field message.

**Test data:** Test with `0` (freezing point, should convert to 32.0F), `-40`
(the point where Celsius and Fahrenheit are equal, should convert to -40.0F),
and an out-of-range value such as `200`.

**Likely errors:** Placing the range check before the `try-catch` block would
cause a compiler error, since `celsius` would not yet be definitely assigned
at that point in the code.

## Code Walkthrough

`validateAndSubmit()` in the main example performs four checks in a strict
order: whether the name field is blank, whether the score field is blank,
whether the score field's text can be parsed as an integer, and whether the
resulting number falls between 0 and 100. Each check ends with `return`
immediately after setting an error message, guaranteeing that later code,
including the final "Recorded" message, only runs once every prior check has
passed. The helper method `isBlank(JTextField field)` demonstrates how a
small, reusable method can eliminate repeated code, since the same blank
check would otherwise need to be written out twice with slightly different
variable names for `nameField` and `scoreField`.

## Expected GUI or Program Behavior

Running `QuizScoreForm` and testing progressively more complete input shows
one specific validation message at a time, updating as each problem is
fixed, until finally a valid name and in-range score together produce a
"Recorded" confirmation message. At no point does typing invalid data into
either field cause the application to crash or display an unhandled
exception in the Eclipse Console.

## Laboratory Session (3 Hours)

### Laboratory Title
Fully Validating the Product Order Form

### Objectives
1. Apply required-field validation to multiple text fields on a single form.
2. Apply safe numeric parsing with `try-catch` for both integer and decimal
   values.
3. Apply range validation appropriate to the meaning of each field.
4. Ensure the form only proceeds to its final action once all validation
   passes.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
Exception handling (Week 17), event handling (Week 26), and layout managers
(Week 27), combined with this week's validation patterns.

### Development Requirements
Create an Eclipse project `Week31-Lab` with package `week31.lab` containing a
class `ValidatedProductOrderForm` with fields for product name (required
text), quantity (required whole number, must be between 1 and 100), and unit
price (required decimal number, must be greater than 0 and no more than
10,000), plus a "Place Order" button and a status label, following the
same validation order used in this week's lecture example: blank check,
safe parsing, range check, for each field in turn.

### Detailed Laboratory Procedure

1. Create the project and package as described.
2. Build the form with the three required fields, the "Place Order" button,
   and a status label, using a layout manager from Week 27.
3. Write a private `isBlank(JTextField field)` helper method exactly as
   shown in the lecture example.
4. Inside the button's event handler, validate the product name field first,
   returning immediately with an appropriate message if blank.
5. Validate the quantity field: check for blank, then parse as an integer
   inside `try-catch`, then check the range 1 to 100, returning immediately
   with a specific message at each failing step.
6. Validate the unit price field: check for blank, then parse as a `double`
   inside `try-catch`, then check that the value is greater than 0 and no
   more than 10,000, returning immediately with a specific message at each
   failing step.
7. If all validation passes, compute the total cost (quantity multiplied by
   unit price) and display a confirmation message including the product
   name, quantity, unit price, and total cost.
8. Test each of the test cases listed below, confirming the exact expected
   message appears for each one.

### Required Features
- All three fields must be validated for blank input before any parsing is
  attempted.
- Quantity and unit price must each be validated with `try-catch` before any
  range check.
- The order must only be confirmed once every validation check passes for
  every field.

### Testing Procedure
Systematically test the form field by field, confirming that each specific
type of invalid input produces its own distinct, correctly worded message,
and that a fully valid order produces a correct total cost.

### Normal Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Valid order | Name `Whiteboard Marker`, quantity `20`, price `1.25` | Click Place Order | Confirmation message with total cost 25.00 |
| Valid boundary quantity | Quantity `100` | Click Place Order | Accepted, since 100 is within range |

### Boundary Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Minimum valid quantity | Quantity `1` | Click Place Order | Accepted |
| Maximum valid price | Price `10000` | Click Place Order | Accepted |
| Just above maximum quantity | Quantity `101` | Click Place Order | Range error message |

### Invalid Test Cases
| Test | Input | Action | Expected Result |
|---|---|---|---|
| Empty product name | Leave name blank | Click Place Order | "Product name is required" message |
| Non-numeric quantity | Quantity `twenty` | Click Place Order | "Quantity must be a whole number" message |
| Zero quantity | Quantity `0` | Click Place Order | Range error message, since minimum is 1 |
| Negative price | Price `-5.00` | Click Place Order | Range error message |
| Non-numeric price | Price `free` | Click Place Order | "Unit price must be a valid number" message |

### Troubleshooting Guidance
If a valid form still shows an error message, re-check the order of
operations: blank check, then parsing, then range check, since skipping a
step or checking range before confirming a successful parse can produce
incorrect results or a compiler error about a possibly unassigned variable.
If an exception still reaches the Eclipse Console despite a `try-catch`
block, confirm the block actually surrounds the `parseInt` or `parseDouble`
call itself, rather than surrounding unrelated code nearby. If the total cost
displays as an unexpectedly rounded or scientific-notation number, remember
that `double` values can display with many decimal places; this is expected
behavior for this lab and is not required to be fixed here.

### Required Deliverables
1. Eclipse project `Week31-Lab` with the completed `ValidatedProductOrderForm`
   class.
2. Screenshots showing at least three different validation error messages
   and one successful order confirmation.
3. A short written explanation of why range checks must always come after,
   not before, the `try-catch` parsing block.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Product name required-field validation | 15 |
| Quantity blank check, parsing, and range validation | 25 |
| Unit price blank check, parsing, and range validation | 25 |
| Correct total cost calculation on success | 15 |
| Clear, specific messages for every failure case | 10 |
| Submission completeness | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common mistake this week is checking a numeric range before
confirming the text was successfully parsed, which either produces a
compiler error about a variable not being definitely assigned, or, if a
default value was used to work around that error, produces confusing
validation behavior. Another common issue is forgetting to `return`
immediately after displaying an error message, which allows the method to
continue running and potentially overwrite the error message with a
different result, or attempt to use a variable that was never successfully
assigned. Students should also watch for order-of-operations mistakes when
validating multiple fields, since checking fields in an inconsistent order
across different forms makes the resulting error messages feel unpredictable
to the user. If a `try-catch` block appears to "swallow" an error silently
with no visible message at all, confirm that the `catch` block actually
calls `setText(...)` or shows a dialog, rather than being left empty.

## Review and Practice Questions

1. Why must a required-field check happen before an attempt to parse that
   field's text as a number?
2. Explain the difference between a `NumberFormatException` and a value that
   parses successfully but falls outside an acceptable range.
3. Why does `validateAndSubmit()` call `return` immediately after each
   failed validation check instead of continuing to check the remaining
   fields?
4. Rewrite the "Temperature Converter" worked example to also reject the
   exact boundary values -100 and 100, so only strictly interior values are
   accepted (hint: change `<` and `>` to `<=` and `>=`).
5. When would displaying validation feedback in a status label be
   preferable to using `JOptionPane.showMessageDialog`, and vice versa?
6. A classmate's form shows a "Recorded" message even when the score field
   contains `150`. What is the most likely missing piece of validation
   logic?

## Lesson Summary

This week strengthened every form built since Week 24 by combining
required-field checks, safe numeric parsing with `try-catch`, and range
validation into a consistent, fail-fast pattern that stops processing at the
first problem found and reports it clearly to the user. This validation
discipline is essential preparation for the database operations beginning in
Week 32, where invalid or malformed data can cause far more serious problems
than a crashed desktop form. Week 32 introduces database concepts, SQLite,
and JDBC, beginning the final project arc that will run through Week 35.
