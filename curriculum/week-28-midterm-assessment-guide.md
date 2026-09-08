# Week 28: Midterm Examination — Assessment Guide

## Week Number and Assessment Title
Week 28 — Midterm Assessment Guide: Java Swing GUI Fundamentals

## Coverage
This assessment covers all material from Weeks 19 through 27:

- Week 19: Java review — OOP, arrays, files, and exceptions for application
  development.
- Week 20: File-based Java applications and structured program design.
- Week 21: Advanced exception handling and defensive input validation.
- Week 22: Event-driven thinking and Java application planning.
- Week 23: Console vs. GUI applications, Swing vs. AWT, event-driven
  programming, and `JFrame`.
- Week 24: `JLabel`, `JTextField`, `JPasswordField`, `JButton`, `JTextArea`.
- Week 25: `JCheckBox`, `JRadioButton`, `ButtonGroup`, `JComboBox`, `JList`,
  `JSpinner`.
- Week 26: `ActionListener`, `ActionEvent`, item events, keyboard/mouse
  events.
- Week 27: `FlowLayout`, `BorderLayout`, `GridLayout`, `BoxLayout`, `JPanel`.

No new Java concepts are introduced this week. The examination measures how
well students can combine everything learned so far into a single working
Swing application.

## Intended Learning Outcomes Being Assessed
1. Build a `JFrame`-based application with correctly configured size, title,
   and close behavior.
2. Select and place appropriate Swing components for a given set of data
   requirements.
3. Organize components using layout managers and `JPanel` rather than
   absolute positioning.
4. Attach event listeners that correctly read component values and respond
   to user actions.
5. Apply basic input validation and exception handling within a GUI
   application.
6. Explain, in writing, the reasoning behind specific Swing design choices.

## Written Examination Structure
The written portion lasts 45 minutes and contains 30 questions, worth a total
of 50 points, using a mix of formats: multiple choice, short identification,
code-reading, and short-answer explanation.

## Suggested Distribution of Questions
| Topic Area | Number of Questions | Points |
|---|---:|---:|
| GUI concepts, Swing vs. AWT, event-driven programming (Week 23) | 5 | 8 |
| Component selection and properties (Weeks 24–25) | 6 | 10 |
| Event handling: ActionListener, ItemListener, KeyListener (Week 26) | 6 | 10 |
| Layout managers and JPanel (Week 27) | 6 | 10 |
| Code-reading: identify output/behavior of a short Swing snippet | 4 | 8 |
| Exception handling and validation review (Weeks 19–21) | 3 | 4 |
| **Total** | **30** | **50** |

## Practical Programming Examination
The practical portion lasts 90 minutes and is completed individually in
Eclipse, worth 50 points, using the rubric provided below.

## Practical Examination Instructions
Each student must build a single-window Swing application, entirely from
memory and personal notes (no internet access, no copying from previous
laboratory files), that satisfies the assigned programming task. The
application must compile and run without errors and must be submitted as a
complete Eclipse project.

## Required Eclipse Project Setup
1. Open Eclipse and create a new Java project named
   `Midterm_[YourLastName]`.
2. Create a package named `midterm.app`.
3. Create a single class named `MidtermApplication` containing a `main`
   method that launches the required window.
4. Save all work periodically throughout the examination using **Ctrl+S**.

## Suggested Programming Tasks
Instructors may select one of the following equivalent tasks, or assign
different students different tasks of comparable difficulty to reduce
academic dishonesty:

**Task A — Simple Product Order Form:** Build a form with fields for product
name and quantity, a combo box for shipping method, a checkbox for
"expedited processing," and a button that calculates and displays an order
summary, validating that the product name is not empty and the quantity is a
valid positive whole number.

**Task B — Student Club Sign-Up Form:** Build a form with a text field for
student name, a `ButtonGroup` of radio buttons for year level, a `JList` of
available clubs, and a button that displays a summary of the selected
options, validating that the student name is not empty.

**Task C — Simple Grade Calculator Form:** Build a form with a text field
for a student's name and three text fields for quiz scores, a button that
calculates and displays the average, validating that all three scores are
valid numbers between 0 and 100.

All three tasks must be built using at least one layout manager other than
`setLayout(null)`, and must include at least one working `ActionListener`.

## Expected Program Behavior
For any of the tasks above, launching the application must display a
correctly laid out window with no overlapping components. Entering valid
data and clicking the action button must display a correctly computed and
formatted result. Entering invalid or missing data must display a clear
error message rather than crashing or displaying incorrect results.

## Test Cases
| Test | Input | Expected Result |
|---|---|---|
| Valid data (any task) | All required fields filled correctly | Correct summary/result displayed |
| Missing required text field | Required field left blank | Validation message displayed instead of a crash |
| Non-numeric value in a numeric field | Letters typed into a numeric field | Validation message displayed; no NumberFormatException reaches the console |
| Out-of-range numeric value (Task C) | A score such as 150 | Validation message indicating the value must be between 0 and 100 |
| Resizing the window | Drag window edge | Components rearrange according to the layout manager used, without disappearing |

## Submission Requirements
1. The complete Eclipse project folder, exported or zipped as instructed by
   the proctor.
2. A single screenshot showing the completed application with valid sample
   data entered and the result displayed.
3. The written examination answer sheet.

## Academic Integrity Reminders
Students must complete both the written and practical portions individually,
without communicating with classmates during the examination window, without
using internet search or AI code-generation tools, and without copying code
from previous laboratory submissions belonging to themselves or others beyond
what has already been taught in class. Any evidence of copied code between
students' submissions, such as identical variable naming, comments, or
formatting irregularities, will be investigated according to the
institution's academic integrity policy. Students may refer only to their own
personal handwritten or typed notes from Weeks 19–27.

## Written Examination Scoring Guide
| Category | Points |
|---|---:|
| Multiple choice and identification items (18 items, ~1 point each) | 18 |
| Code-reading items (4 items, 2 points each) | 8 |
| Short-answer explanation items (8 items, 3 points each) | 24 |
| **Total** | **50** |

## Practical Examination 100-Point Rubric
*(Note: the practical examination is worth 50 points toward the overall
midterm grade; the rubric below is expressed on a 100-point scale and then
scaled by 0.5 when combined with the written score, so that written (50) +
practical (50) = 100 total midterm points.)*

| Category | Points |
|---|---:|
| Application compiles and runs without errors | 15 |
| Correct and complete component selection for the assigned task | 20 |
| Layout manager used correctly (no setLayout(null)) | 15 |
| ActionListener correctly reads values and triggers processing | 20 |
| Input validation handles empty/invalid/out-of-range input | 20 |
| Code organization and naming | 10 |
| **Total** | **100** |

## Common Evaluation Criteria
When grading the practical examination, instructors should watch for the
same recurring issues seen throughout Weeks 23–27: components added without
being placed in any container, a missing `setVisible(true)` call, a
`ButtonGroup` created but never populated, listener code that never calls
`getText()` or an equivalent value-reading method, and unhandled
`NumberFormatException` errors on numeric fields. Partial credit should be
awarded for a program that compiles and displays a mostly correct interface,
even if the event-handling logic contains errors, since component selection
and layout are assessed independently from event-handling correctness.

## Instructor Preparation Checklist
1. Confirm every student has a working Eclipse installation before the exam
   begins.
2. Distribute the written examination and confirm the 45-minute timer
   before starting.
3. Collect written examinations before revealing the practical task.
4. Assign practical tasks (A, B, or C) to reduce the chance of identical
   submissions between adjacent students.
5. Confirm the 90-minute timer for the practical portion.
6. Remind students of the submission requirements five minutes before time
   expires.
7. Collect all Eclipse project folders and written answer sheets before
   students leave.
8. Verify each submitted project actually compiles before final grading
   begins.
