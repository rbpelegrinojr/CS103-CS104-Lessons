# Week 25: Swing Components II

## Main Topic
`JCheckBox`, `JRadioButton`, `ButtonGroup`, `JComboBox`, `JList`, and
`JSpinner`.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Add checkboxes to a form to represent independent yes/no options.
2. Group radio buttons using `ButtonGroup` so only one option can be selected
   at a time.
3. Add a `JComboBox` to present a drop-down list of choices.
4. Add a `JList` to present a scrollable list of selectable items.
5. Add a `JSpinner` to let the user select a numeric value or a value from a
   defined sequence.
6. Read the currently selected value from each of these selection controls.

## Prerequisite Knowledge
Students must already be able to build a `JFrame` and place `JLabel`,
`JTextField`, `JPasswordField`, `JButton`, and `JTextArea` components inside
it using absolute positioning, as taught in Week 24. No prior knowledge of
selection controls is assumed.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 10 min | Review of Week 24 components |
| 2 | 20 min | `JCheckBox`: independent yes/no options |
| 3 | 25 min | `JRadioButton` and `ButtonGroup`: mutually exclusive options |
| 4 | 20 min | `JComboBox`: drop-down selection |
| 5 | 20 min | `JList`: scrollable list selection |
| 6 | 25 min | `JSpinner`: numeric and sequential selection; live coding a combined form |

## Detailed Lesson Discussion

The components introduced last week were mostly about free-form text entry.
Many real forms, however, ask the user to choose from a fixed set of options
rather than typing anything at all, and Swing provides a family of components
specifically for that purpose.

A `JCheckBox` represents a single option that can be either checked or
unchecked, independent of every other checkbox on the form. Because each
checkbox is independent, a form can have several checked at once, none
checked, or all checked; there is no built-in restriction linking them
together. A checkbox is created with `new JCheckBox("Vegetarian Meal")`, and
its current state is read using the boolean method `isSelected()`, which
returns `true` if the box is currently checked. Checkboxes are the right
choice whenever the options on a form do not affect each other, such as a
list of optional add-ons or preferences.

A `JRadioButton` looks similar to a checkbox but is intended for situations
where the user must choose exactly one option out of several, such as
selecting a year level or a payment method. A single radio button by itself
behaves like a checkbox with a different appearance, so radio buttons only
gain their real meaning once they are placed inside a `ButtonGroup`. A
`ButtonGroup` is not a visual component — it does not appear anywhere on
screen — but is instead a logical grouping object that enforces the rule that
only one of its member radio buttons can be selected at any time. Selecting
one radio button in the group automatically deselects whichever one was
previously selected. The typical pattern is to create each `JRadioButton`
individually, add each one to the frame so it is visible, and separately add
each one to a shared `ButtonGroup` so the mutual exclusivity rule is enforced;
forgetting the second step is a common beginner mistake that results in
several radio buttons appearing on screen but behaving as independent
checkboxes instead of a single exclusive choice.

A `JComboBox` presents a compact drop-down list; only one item is visible at a
time until the user clicks the arrow to reveal the full list of choices. A
combo box is created with `new JComboBox<>(new String[] {"Freshman",
"Sophomore", "Junior", "Senior"})`, and the currently selected item is
retrieved with `getSelectedItem()`, which returns an `Object` that typically
needs to be cast back to the expected type, such as `String`, or read using
the generic type parameter used when the combo box was declared. Combo boxes
are especially useful when there are many possible options but only limited
screen space, since the full list is hidden until needed.

A `JList` displays several items at once in a scrollable area, allowing the
user to see multiple options simultaneously rather than one at a time as with
a combo box. A list is created with `new JList<>(new String[] {"Math",
"Science", "English", "History"})`, and its currently selected item is
retrieved with `getSelectedValue()`. By default, a `JList` allows only a
single selection unless its selection mode is explicitly changed, which is
sufficient for this course's needs. Unlike a `JComboBox`, a `JList` is often
placed inside a `JScrollPane` so that long lists remain fully visible; the
`JScrollPane` component itself is formally covered in Week 30, but a plain
`JList` without one will still display correctly this week as long as the
list of items is short enough to fit within its bounds.

A `JSpinner` provides a small text box paired with up and down arrows that
step through a defined sequence of values, most commonly numbers. A numeric
spinner is created with `new JSpinner(new SpinnerNumberModel(initialValue,
minimumValue, maximumValue, stepSize))`, which automatically restricts the
value to the given range and increases or decreases it by the given step each
time an arrow is clicked. The current value is read using `getValue()`, which
returns an `Object` that is typically cast to `Integer` or another appropriate
type. A spinner is a good choice whenever a value naturally falls within a
known, limited range, such as a quantity field limited between 1 and 10, since
it prevents the user from ever typing an out-of-range value in the first
place.

Choosing the right component for a given situation is itself an important
design skill. Independent options belong in checkboxes; mutually exclusive
options belong in grouped radio buttons; a small number of options with
limited space favors a combo box; a moderate number of options where several
should be visible at once favors a list; and a bounded numeric value favors a
spinner. Recognizing which situation calls for which component will make
later form designs, including the final Swing + SQLite project, considerably
easier to plan.

## Important Terminology

Key terms: **JCheckBox**, **isSelected()**, **JRadioButton**, **ButtonGroup**,
**JComboBox**, **getSelectedItem()**, **JList**, **getSelectedValue()**,
**JSpinner**, and **SpinnerNumberModel**.

## Complete Java Programming Example

```java
package week25.gui;

import javax.swing.ButtonGroup;
import javax.swing.JCheckBox;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JList;
import javax.swing.JRadioButton;
import javax.swing.JSpinner;
import javax.swing.SpinnerNumberModel;

/**
 * A student activity sign-up form demonstrating checkboxes, radio buttons,
 * a combo box, a list, and a spinner.
 */
public class ActivitySignUpForm extends JFrame {

    private final JCheckBox sportsCheckBox;
    private final JCheckBox musicCheckBox;
    private final JRadioButton freshmanButton;
    private final JRadioButton sophomoreButton;
    private final JRadioButton juniorButton;
    private final JComboBox<String> dayComboBox;
    private final JList<String> clubList;
    private final JSpinner slotsSpinner;

    public ActivitySignUpForm() {
        super("Student Activity Sign-Up");
        setSize(480, 430);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        JLabel interestsLabel = new JLabel("Interests:");
        interestsLabel.setBounds(20, 20, 100, 25);
        add(interestsLabel);

        sportsCheckBox = new JCheckBox("Sports");
        sportsCheckBox.setBounds(130, 20, 100, 25);
        add(sportsCheckBox);

        musicCheckBox = new JCheckBox("Music");
        musicCheckBox.setBounds(230, 20, 100, 25);
        add(musicCheckBox);

        JLabel yearLabel = new JLabel("Year Level:");
        yearLabel.setBounds(20, 60, 100, 25);
        add(yearLabel);

        freshmanButton = new JRadioButton("Freshman");
        freshmanButton.setBounds(130, 60, 100, 25);
        add(freshmanButton);

        sophomoreButton = new JRadioButton("Sophomore");
        sophomoreButton.setBounds(230, 60, 110, 25);
        add(sophomoreButton);

        juniorButton = new JRadioButton("Junior");
        juniorButton.setBounds(340, 60, 100, 25);
        add(juniorButton);

        ButtonGroup yearGroup = new ButtonGroup();
        yearGroup.add(freshmanButton);
        yearGroup.add(sophomoreButton);
        yearGroup.add(juniorButton);

        JLabel dayLabel = new JLabel("Preferred Day:");
        dayLabel.setBounds(20, 100, 100, 25);
        add(dayLabel);

        dayComboBox = new JComboBox<>(new String[] {
            "Monday", "Wednesday", "Friday"
        });
        dayComboBox.setBounds(130, 100, 150, 25);
        add(dayComboBox);

        JLabel clubLabel = new JLabel("Club Options:");
        clubLabel.setBounds(20, 140, 100, 25);
        add(clubLabel);

        clubList = new JList<>(new String[] {
            "Robotics Club", "Debate Club", "Chess Club", "Drama Club"
        });
        clubList.setBounds(130, 140, 200, 80);
        add(clubList);

        JLabel slotsLabel = new JLabel("Number of Slots:");
        slotsLabel.setBounds(20, 240, 120, 25);
        add(slotsLabel);

        slotsSpinner = new JSpinner(new SpinnerNumberModel(1, 1, 5, 1));
        slotsSpinner.setBounds(150, 240, 60, 25);
        add(slotsSpinner);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new ActivitySignUpForm();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and create a new Java project named `Week25-ActivitySignUp`.
2. Create a package named `week25.gui`.
3. Create the class `ActivitySignUpForm` and paste in the complete example.
4. Save the file and use **Ctrl+Shift+O** to resolve any missing imports for
   `javax.swing` classes.
5. Run the class using **Run As > Java Application**.
6. Observe that the form displays two checkboxes, three grouped radio
   buttons, a combo box, a list, and a spinner, none overlapping.
7. Click both checkboxes and confirm both can be checked simultaneously,
   demonstrating that they are independent of each other.
8. Click each radio button in turn and confirm that selecting one always
   deselects whichever one was previously selected.
9. Click the combo box arrow, confirm the three day options appear, and
   select one.
10. Click an item in the list and confirm it becomes highlighted to show it
    is selected.
11. Click the spinner's up and down arrows and confirm the value only moves
    between 1 and 5.
12. As an experiment, remove the `yearGroup.add(...)` calls (but leave the
    radio buttons on the form), run the program again, and confirm that all
    three radio buttons can now be selected at the same time, demonstrating
    why `ButtonGroup` is required for mutual exclusivity. Restore the
    `ButtonGroup` code afterward.

## Guided Worked Example

**Problem description:** Build a smaller "Meal Preference" form using only a
`ButtonGroup` of radio buttons and a `JComboBox`, to isolate and reinforce
those two specific components.

```java
package week25.gui;

import javax.swing.ButtonGroup;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JRadioButton;

public class MealPreferenceForm extends JFrame {

    public MealPreferenceForm() {
        super("Meal Preference");
        setSize(350, 220);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        JLabel mealLabel = new JLabel("Meal Type:");
        mealLabel.setBounds(20, 20, 100, 25);
        add(mealLabel);

        JRadioButton veganButton = new JRadioButton("Vegan");
        veganButton.setBounds(130, 20, 100, 25);
        add(veganButton);

        JRadioButton regularButton = new JRadioButton("Regular");
        regularButton.setBounds(130, 50, 100, 25);
        add(regularButton);

        ButtonGroup mealGroup = new ButtonGroup();
        mealGroup.add(veganButton);
        mealGroup.add(regularButton);

        JLabel sizeLabel = new JLabel("Portion Size:");
        sizeLabel.setBounds(20, 90, 100, 25);
        add(sizeLabel);

        JComboBox<String> sizeComboBox = new JComboBox<>(new String[] {
            "Small", "Regular", "Large"
        });
        sizeComboBox.setBounds(130, 90, 120, 25);
        add(sizeComboBox);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        new MealPreferenceForm();
    }
}
```

**Walkthrough:** The two radio buttons are added both to the frame, so they
are visible, and to the `ButtonGroup`, so they behave exclusively; both steps
are required and one without the other produces incomplete behavior. The
combo box needs only to be added to the frame, since combo boxes already
restrict selection to one item by their nature.

**Expected behavior:** Selecting "Vegan" automatically deselects "Regular"
and vice versa. Clicking the portion size combo box shows Small, Regular, and
Large as options.

**Test data:** Selecting "Vegan" and "Large" should leave both selections
visibly marked at the same time, since they belong to different, unrelated
controls.

**Likely errors:** A student who forgets to create the `ButtonGroup` will see
two radio buttons that can both appear selected simultaneously, which is
incorrect for a true either/or choice.

## Code Walkthrough

Each new component follows the same three-step pattern used in Week 24:
create, set bounds, add to the frame. The one addition this week is the
`ButtonGroup`, which is created separately from the radio buttons themselves
and does not use `setBounds` or `add(...)` to the frame, because it is a
logical object, not a visual one. The `JSpinner` is constructed with a
`SpinnerNumberModel` rather than being empty, because a spinner always needs
to know its starting value, minimum, maximum, and step size in order to
behave correctly; omitting the model would leave the spinner without any
defined range. The `JList` is constructed directly from an array of strings,
matching the generic type `JList<String>` declared for the field, which
avoids the need for an unchecked cast when retrieving the selected value
later.

## Expected GUI or Program Behavior

Running `ActivitySignUpForm` shows a window with two independent checkboxes,
three mutually exclusive radio buttons, a drop-down day selector, a
multi-item scrollable-looking list, and a numeric spinner limited between 1
and 5. All controls can be interacted with using the mouse, and their
selections are visually reflected immediately, even though no button yet
reads or reports the selected values (that requires the event handling
covered in Week 26).

## Laboratory Session (3 Hours)

### Laboratory Title
Building a Course Preference Survey Form

### Objectives
1. Correctly use `JCheckBox` for independent selections.
2. Correctly use `JRadioButton` with `ButtonGroup` for exclusive selections.
3. Correctly use `JComboBox` and `JList` for choosing from predefined options.
4. Correctly use `JSpinner` with a bounded numeric range.

### Required Software and Materials
Eclipse IDE with a configured JDK.

### Required Java Concepts
Object construction and instance variables from earlier weeks, combined with
this week's five selection components.

### Development Requirements
Create an Eclipse project `Week25-Lab` with a package `week25.lab` containing
a class `CoursePreferenceSurvey` that models a fictional survey for students
choosing electives, including: checkboxes for at least three independent
interest areas (for example, "Programming", "Design", "Robotics"), a
`ButtonGroup` of at least three radio buttons for class schedule preference
(for example, "Morning", "Afternoon", "Evening"), a combo box listing at
least four elective course names, a `JList` listing at least four extra
elective options, and a spinner limited between 1 and 3 representing the
number of electives the student wants to take.

### Detailed Laboratory Procedure

1. Create the project and package as described.
2. Add a title label at the top of the form.
3. Add the three interest checkboxes, positioned in a single row without
   overlap.
4. Add the three schedule radio buttons and group them using a single shared
   `ButtonGroup`.
5. Add the elective combo box with at least four sample course names.
6. Add the `JList` with at least four different sample elective names.
7. Add the spinner using `SpinnerNumberModel(1, 1, 3, 1)`.
8. Run the form and manually test each control: check and uncheck the
   checkboxes independently; click through all three radio buttons to
   confirm exclusivity; open the combo box and select each option; click each
   list item; move the spinner to its minimum and maximum values.
9. As a deliberate debugging exercise, temporarily remove one radio button
   from the `ButtonGroup` (leave it visible on the form) and confirm it can
   now be selected alongside another radio button, then restore it to the
   group.

### Required Features
- All three checkboxes must be independently selectable.
- All three radio buttons must be mutually exclusive through a single
  `ButtonGroup`.
- The combo box and list must each display at least four options.
- The spinner must be constrained between 1 and 3.

### Testing Procedure
Run the form and interact with every control at least once, confirming
visually correct behavior for each, including the exclusivity of the radio
buttons and the bounded range of the spinner.

### Normal Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Check two checkboxes | Click "Programming" and "Robotics" | Both remain checked simultaneously |
| Select a radio button | Click "Afternoon" | Only "Afternoon" remains selected |
| Change spinner value | Click the up arrow twice from 1 | Spinner shows 3 |

### Boundary Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Spinner at minimum | Click down arrow while at 1 | Value stays at 1; does not go below the minimum |
| Spinner at maximum | Click up arrow while at 3 | Value stays at 3; does not go above the maximum |

### Invalid Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Radio button not grouped (debugging step) | Remove one button from ButtonGroup and select two buttons | Both appear selected, demonstrating incorrect behavior before the fix |
| No selection made anywhere | Run the form and take no action | Form still displays correctly with default/no selections, since no validation exists yet |

### Troubleshooting Guidance
If radio buttons do not behave exclusively, confirm each one was added to the
same `ButtonGroup` object, not just to the frame. If the combo box or list
appears empty, confirm the array passed into the constructor is not empty and
that the generic type declared matches the array's element type. A common
compile error occurs when mixing raw types and generics inconsistently, such
as declaring `JList<String>` but constructing `new JList(someArray)` without
the diamond operator; always match the generic type on both sides.

### Required Deliverables
1. Eclipse project `Week25-Lab` with the completed `CoursePreferenceSurvey`
   class.
2. A short written explanation of what happened during the deliberate
   `ButtonGroup` debugging step and why.
3. A screenshot of the completed form with sample selections made in every
   control.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| Checkboxes behave independently | 15 |
| Radio buttons correctly grouped and mutually exclusive | 25 |
| Combo box populated and functional | 15 |
| List populated and functional | 15 |
| Spinner correctly bounded between 1 and 3 | 15 |
| Submission completeness and written explanation | 15 |
| **Total** | **100** |

## Testing and Debugging Guidance

The single most common mistake this week is creating radio buttons but
forgetting to add them to a shared `ButtonGroup`, which results in a set of
buttons that all look like radio buttons but behave like independent
checkboxes. Another common issue is a `ClassCastException` when reading a
value from a `JComboBox` or `JList` if the generic type used at construction
does not match the type expected when reading the selection later; using
consistent generic types, such as `JComboBox<String>` throughout, avoids this
entirely. If a `JSpinner` throws an exception when created, check that the
`SpinnerNumberModel` arguments are in the correct order — initial value,
minimum, maximum, then step — and that the initial value actually falls
within the minimum and maximum range.

## Review and Practice Questions

1. Why does a `JRadioButton` need to be added to both the frame and a
   `ButtonGroup`, while a `JCheckBox` only needs to be added to the frame?
2. When would a `JComboBox` be a better design choice than a `JList`, and
   vice versa?
3. What four arguments does `SpinnerNumberModel` take, and what does each one
   control?
4. Rewrite the "Meal Preference" worked example to add a third radio button
   option, "Gluten-Free", to the existing group.
5. A classmate's spinner allows values below the intended minimum. What is
   the most likely cause?
6. Explain, in your own words, why `ButtonGroup` is not itself a visible
   Swing component.

## Lesson Summary

This week expanded the Swing component vocabulary to include selection
controls: checkboxes for independent choices, grouped radio buttons for
exclusive choices, combo boxes and lists for choosing among predefined
options, and spinners for bounded numeric values. Students practiced
combining all five controls into a single coherent form and saw firsthand
what happens when a `ButtonGroup` is omitted. Week 26 will finally make the
buttons introduced in Week 24 and used throughout this week respond to user
clicks through event handling.
