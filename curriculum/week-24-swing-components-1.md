# Week 24: Swing Components I

## Main Topic
`JLabel`, `JTextField`, `JPasswordField`, `JButton`, `JTextArea`, and basic
component properties.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Add labels, text fields, password fields, buttons, and text areas to a
   `JFrame`.
2. Explain the purpose of a content pane and add components to it correctly.
3. Set basic component properties such as text, font, editability, and size.
4. Arrange several components inside a window using a simple layout so they
   do not overlap.
5. Read and set the text of components programmatically, in preparation for
   event handling in Week 26.

## Prerequisite Knowledge
Students must already be able to create and configure a basic `JFrame`, as
taught in Week 23, including setting its size, title, close operation, and
visibility. No prior knowledge of Swing input or display components is
assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of `JFrame` basics from Week 23 |
| 2 | 20 min | `JLabel`: displaying text and images |
| 3 | 25 min | `JTextField` and `JPasswordField`: accepting user input |
| 4 | 20 min | `JButton`: creating clickable controls (behavior deferred to Week 26) |
| 5 | 20 min | `JTextArea`: multi-line text display and input |
| 6 | 25 min | Live coding: assembling a simple form with all five components |

## Detailed Lesson Discussion

Last week's `JFrame` opened as an empty window because no components were
ever placed inside it. Before a component such as a button or a text field
can appear on screen, it must be added to the frame's **content pane**, which
is the actual surface inside the window where visual elements live. Every
`JFrame` has a content pane automatically, and calling `add(component)` on the
frame is a shortcut that Swing provides for adding directly to that content
pane, so `frame.add(myLabel)` and `frame.getContentPane().add(myLabel)`
accomplish the same thing. This week's components are the most frequently
used building blocks in Swing, and nearly every form built for the rest of
this course will include several of them.

A `JLabel` displays a short piece of read-only text, and sometimes an image,
that the user cannot edit directly. Labels are most often used to describe
what a nearby field is for, such as placing a label containing the text
"Name:" directly before a text field where the user types their name. A label
is created with `new JLabel("Name:")`, and its text can be changed later using
`setText(...)`. Because a label cannot be typed into, it is the simplest of
this week's components, but it plays an essential role in making a form
understandable to the person using it.

A `JTextField` is a single-line box that accepts typed input from the user. It
is created with `new JTextField(20)`, where the number 20 is not a character
limit but a hint about the field's preferred width, measured in an
approximate number of average-width characters. The text currently inside a
text field is retrieved using `getText()`, which returns a `String`, and it
can be replaced using `setText(...)`. Because `getText()` always returns a
`String`, converting that text into a number for calculations requires the
same techniques already used in console programs, such as
`Integer.parseInt(...)` or `Double.parseDouble(...)`, combined with the
exception handling learned in Week 17, since a user could always type letters
into a field meant for numbers.

A `JPasswordField` behaves almost exactly like a `JTextField`, except that it
masks each typed character with a symbol such as an asterisk so that the
actual text cannot be read over someone's shoulder. Because password fields
are meant to protect sensitive input, calling `getText()` on a
`JPasswordField` is not allowed by modern Swing; instead, the correct method
is `getPassword()`, which returns a `char[]` array rather than a `String`.
This is a deliberate security design: `String` objects can remain in memory
for an unpredictable length of time, while a `char[]` can be manually cleared
immediately after use. For beginner coursework, it is enough to read the
array and, if needed, convert it with `new String(passwordChars)` for
comparison, while understanding that production systems typically avoid
holding raw passwords as `String` values for longer than necessary.

A `JButton` is a clickable control labeled with text, created with
`new JButton("Submit")`. On its own this week, a button will appear and can be
visually clicked, but nothing will happen yet, because responding to a click
requires an **event listener**, which is the subject of Week 26. Introducing
the button now, before event handling, lets students focus first on layout
and appearance without the added complexity of writing listener code, and
then revisit the exact same button next week to make it functionally
interactive.

A `JTextArea` is similar to a `JTextField`, but it supports multiple lines of
text rather than just one, making it useful for comments, descriptions, or
any input that may span more than a single line. A `JTextArea` is created
with `new JTextArea(rows, columns)`, where both numbers describe the
preferred size in terms of character rows and columns. Unlike a text field, a
plain `JTextArea` does not automatically scroll when the text grows beyond
its visible area; adding scrolling behavior requires wrapping it inside a
`JScrollPane`, which will be covered in detail in Week 30 once students have
more experience with component composition.

Placing several of these components into the same window at once requires
some way to arrange them so they do not overlap. This week, before formal
layout managers are introduced in Week 27, the simplest approach is to set the
frame's layout to `null` and manually position each component using
`setBounds(x, y, width, height)`, which places a component at exact pixel
coordinates measured from the top-left corner of the content pane. This
approach, sometimes called **absolute positioning**, is not considered good
practice for real-world applications because it does not adapt to different
window sizes, but it is a useful teaching tool this week because it lets
students focus entirely on the components themselves without the added
complexity of layout manager rules, which are introduced properly in Week 27.

## Important Terminology

Key terms for this week: **content pane**, **JLabel**, **JTextField**,
**JPasswordField**, **JButton**, **JTextArea**, **absolute positioning**, and
`setBounds`.

## Complete Java Programming Example

```java
package week24.gui;

import java.awt.Font;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPasswordField;
import javax.swing.JTextArea;
import javax.swing.JTextField;

/**
 * A simple student registration form demonstrating JLabel, JTextField,
 * JPasswordField, JButton, and JTextArea. The button does not yet respond
 * to clicks; that behavior is added in Week 26.
 */
public class StudentRegistrationForm extends JFrame {

    private final JTextField nameField;
    private final JPasswordField passwordField;
    private final JTextArea notesArea;

    public StudentRegistrationForm() {
        super("Student Registration");
        setSize(420, 380);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);   // absolute positioning for this week's lesson

        JLabel titleLabel = new JLabel("New Student Registration");
        titleLabel.setFont(new Font("Arial", Font.BOLD, 16));
        titleLabel.setBounds(90, 10, 300, 25);
        add(titleLabel);

        JLabel nameLabel = new JLabel("Full Name:");
        nameLabel.setBounds(20, 50, 100, 25);
        add(nameLabel);

        nameField = new JTextField(20);
        nameField.setBounds(130, 50, 250, 25);
        add(nameField);

        JLabel passwordLabel = new JLabel("Password:");
        passwordLabel.setBounds(20, 90, 100, 25);
        add(passwordLabel);

        passwordField = new JPasswordField(20);
        passwordField.setBounds(130, 90, 250, 25);
        add(passwordField);

        JLabel notesLabel = new JLabel("Notes:");
        notesLabel.setBounds(20, 130, 100, 25);
        add(notesLabel);

        notesArea = new JTextArea(6, 20);
        notesArea.setLineWrap(true);
        notesArea.setBounds(130, 130, 250, 120);
        add(notesArea);

        JButton submitButton = new JButton("Submit");
        submitButton.setBounds(150, 270, 100, 30);
        add(submitButton);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new StudentRegistrationForm();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and choose **File > New > Java Project**; name it
   `Week24-Registration`.
2. Create a package named `week24.gui`.
3. Create a class named `StudentRegistrationForm` inside that package, with
   the `main` method stub generated.
4. Replace the generated code with the complete example above.
5. Save the file. If any import shows a red underline, use **Ctrl+Shift+O**
   (Organize Imports) to let Eclipse add the missing `javax.swing` and
   `java.awt` imports automatically.
6. Right-click the file and choose **Run As > Java Application**.
7. Observe the result: the form should appear with a title label, a name
   field, a masked password field, a multi-line notes area, and a submit
   button, all positioned without overlapping.
8. Click inside the name field and type a sample name; click inside the
   password field and type a sample password, confirming the characters are
   masked with dots or asterisks.
9. Click the Submit button and confirm that, while it visually responds to
   the mouse (it appears pressed), nothing else happens yet, since no
   listener has been attached.
10. Intentionally change one component's `setBounds` values so that it
    overlaps another component, run the program again, and observe the
    visual overlap, then restore the correct values.

## Guided Worked Example

**Problem description:** Build a smaller "Quick Contact" form containing only
a label, a text field, and a button, to reinforce the exact same concepts with
a different, simpler layout.

```java
package week24.gui;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;

public class QuickContactForm extends JFrame {

    public QuickContactForm() {
        super("Quick Contact");
        setSize(320, 180);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        JLabel emailLabel = new JLabel("Email:");
        emailLabel.setBounds(20, 30, 80, 25);
        add(emailLabel);

        JTextField emailField = new JTextField(15);
        emailField.setBounds(100, 30, 180, 25);
        add(emailField);

        JButton sendButton = new JButton("Send");
        sendButton.setBounds(110, 80, 100, 30);
        add(sendButton);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new QuickContactForm();
    }
}
```

**Walkthrough:** Each component is created, given bounds, and added to the
frame in the same three-step pattern used in the main example: construct,
position, add. Notice that the label's bounds end at x = 100 (20 + 80), which
is exactly where the text field's bounds begin, avoiding any overlap. This
kind of manual arithmetic is exactly why absolute positioning becomes
difficult to maintain as forms grow larger, motivating the layout managers
introduced next week.

**Expected behavior:** A small window appears with an email label, a text
field, and a Send button arranged vertically with no overlapping components.

**Test data:** Typing `student@example.edu` into the email field should
display normally, since `JTextField` does not mask its input.

**Likely errors:** If a component does not appear at all, the most likely
cause is that `add(component)` was never called after creating it, or that
`setBounds` placed it outside the visible area of the frame, such as using a
y-coordinate larger than the frame's height.

## Code Walkthrough

The line `setLayout(null)` turns off automatic layout management, which is
required whenever `setBounds` is used directly, since a layout manager would
otherwise ignore the bounds and reposition components automatically. Each
component follows the same three-line pattern: create it with `new`, call
`setBounds(x, y, width, height)` to place it, and call `add(component)` to
attach it to the frame. The `JTextArea` additionally calls
`setLineWrap(true)`, which tells the component to wrap long lines onto the
next visual line instead of extending off-screen, which is important because
a plain `JTextArea` does not wrap by default. The fields declared as instance
variables (`nameField`, `passwordField`, `notesArea`) rather than local
variables inside the constructor are stored this way so that other methods
added in later weeks, such as an event handler, will be able to read their
contents; a component declared only as a local variable inside the
constructor cannot be accessed from anywhere else in the class.

## Expected GUI or Program Behavior

Running `StudentRegistrationForm` displays a window containing a bold title
label, a name field, a masked password field, a resizable-looking (but not
yet scrollable) notes area, and a submit button. Typing works normally in the
name field and notes area; typing in the password field displays masking
characters instead of the typed text. Clicking Submit produces no visible
result yet, which is expected and correct for this week.

## Laboratory Session (3 Hours)

### Laboratory Title
Assembling a Multi-Component Course Enrollment Form

### Objectives
1. Combine `JLabel`, `JTextField`, `JPasswordField`, `JButton`, and
   `JTextArea` in a single, purposeful form.
2. Practice manual component placement using `setBounds`.
3. Correctly retrieve text from a `JTextField` and a `JPasswordField` using
   `getText()` and `getPassword()`.
4. Recognize and fix overlapping or missing components.

### Required Software and Materials
Eclipse IDE with a configured JDK; no external libraries required.

### Required Java Concepts
Classes and constructors (Week 11), instance variables and access modifiers
(Week 12), and string handling from earlier weeks, combined with this week's
Swing components.

### Development Requirements
Create an Eclipse project named `Week24-Lab` containing a package
`week24.lab` with a single class `CourseEnrollmentForm` that builds a form for
a fictional "Course Enrollment" system, containing: a title label, a label
and text field for "Student Number", a label and text field for "Full Name", a
label and password field for "Portal Password", a label and text area for
"Special Accommodations", and a button labeled "Enroll".

### Detailed Laboratory Procedure

1. Create the project and package as described above.
2. Create `CourseEnrollmentForm` extending `JFrame`, sized 450x420, with
   `setLayout(null)`.
3. Add a bold title label at the top of the form reading
   "Course Enrollment Form".
4. Add the "Student Number" label and its text field, sized for roughly 10
   characters of input.
5. Add the "Full Name" label and its text field, sized for roughly 20
   characters of input.
6. Add the "Portal Password" label and its password field, sized for roughly
   15 characters of input.
7. Add the "Special Accommodations" label and a `JTextArea` with 5 rows and
   20 columns, calling `setLineWrap(true)`.
8. Add the "Enroll" button below all other components, centered
   horizontally.
9. Run the form and manually verify, by clicking into each field, that text
   can be typed into every input component and that the password field masks
   its input.
10. Add a temporary `System.out.println` call directly inside the
    constructor, immediately after building the button (not inside any event
    handler, since those are not covered until Week 26), that prints
    `nameField.getText()` and `new String(passwordField.getPassword())` to the
    console to confirm both methods work as expected before the form is
    closed. Note: because no listener exists yet, this line only shows the
    fields' *initial* contents (typically empty strings) at the moment the
    window is built, which is an important limitation to record in the lab
    report.
11. Remove the temporary println statement once its behavior has been
    recorded and observed.

### Required Features
- All six labeled components (title plus five field pairs) must be present
  and correctly bounded with no overlap.
- The password field must mask typed characters.
- The text area must wrap long lines instead of extending off-screen.
- The Enroll button must be visible and centered near the bottom of the form.

### Testing Procedure
Run the application, click into every input component in order from top to
bottom, type sample data into each, and visually confirm correct behavior and
no overlapping components.

### Normal Test Cases
| Test | Input | Expected Result |
|---|---|---|
| Type student number | `2025-00147` | Displays normally in the text field |
| Type full name | `Maria Santos` | Displays normally in the text field |
| Type password | `MyPortal2025` | Displays as masked dots/asterisks |
| Type accommodations note | A two-sentence note | Text wraps onto a second visible line |

### Boundary Test Cases
| Test | Input | Expected Result |
|---|---|---|
| Empty fields | Leave all fields blank | Form still displays correctly; no crash occurs since no validation exists yet |
| Very long name | A 60-character name | Text scrolls within the field rather than resizing it |

### Invalid Test Cases
| Test | Input | Expected Result |
|---|---|---|
| Non-numeric student number | `ABCDE` | Accepted as plain text since no validation exists yet (validation is introduced in Week 31) |
| Extremely long accommodations text | Several paragraphs | Text area shows only the visible portion; without a JScrollPane (Week 30), older lines scroll out of view |

### Troubleshooting Guidance
If a component never appears, confirm that `add(component)` was called and
that `setBounds` values place it within the frame's visible dimensions. If
the password field shows plain text instead of masking characters, confirm
that `JPasswordField` was used rather than `JTextField`. A `NullPointerException`
referencing one of the fields usually means the field was declared as a local
variable inside a different method than where it is used, rather than as an
instance variable declared at the top of the class.

### Required Deliverables
1. Eclipse project `Week24-Lab` with the completed `CourseEnrollmentForm`
   class.
2. A short written note describing what was printed to the console when
   testing `getText()` and `getPassword()` before any listener existed, and
   why the fields appeared empty at that point.
3. A screenshot of the completed form with sample data typed into every
   field.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| All required components present and correctly labeled | 25 |
| Correct use of JPasswordField with masked input | 15 |
| JTextArea correctly wraps text | 15 |
| No overlapping components; clean visual layout | 20 |
| Correct use of getText() / getPassword() demonstrated | 15 |
| Submission completeness (screenshot and written note) | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

A frequent error this week is confusing `JTextField` and `JPasswordField`,
particularly forgetting that `getPassword()` returns a `char[]` rather than a
`String`, which produces a compiler error if a student attempts to assign it
directly to a `String` variable. Another frequent issue is components
appearing in the wrong location or overlapping; this is almost always fixed
by recalculating `setBounds` coordinates on paper before typing them in.
Students should also watch for a `JTextArea` that appears to "cut off" text;
this is expected without a `JScrollPane`, which is introduced in Week 30, and
is not a bug to fix this week. If nothing appears in the frame at all, check
that `setLayout(null)` was called; without it, some Swing look-and-feels will
silently ignore `setBounds` values.

## Review and Practice Questions

1. What is the difference between `getText()` and `getPassword()`, and why
   does `JPasswordField` not use `getText()`?
2. Why must `setLayout(null)` be called before using `setBounds` on
   components in this week's examples?
3. What happens to text typed into a `JTextArea` that exceeds the visible
   area, and why?
4. Rewrite the "Quick Contact" worked example so the button appears above the
   text field instead of below it, adjusting bounds accordingly.
5. A classmate's password field shows the literal typed characters instead of
   masking dots. What Swing class mistake likely caused this?
6. Why are `nameField`, `passwordField`, and `notesArea` declared as instance
   variables instead of local variables inside the constructor?

## Lesson Summary

This week added the core Swing input and display components — labels, text
fields, password fields, text areas, and buttons — to the window foundation
built in Week 23, using absolute positioning to keep the focus on the
components themselves. Students practiced creating, configuring, and reading
values from these components, while also seeing the current limitation that
button clicks do not yet do anything. Week 25 continues building the
component vocabulary with selection controls, and Week 26 returns to the
button seen this week to finally make it respond to user actions.
