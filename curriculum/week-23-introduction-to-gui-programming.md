# Week 23: Introduction to Java GUI Programming

## Main Topic
Console vs. GUI applications, Swing introduction, AWT vs. Swing, event-driven
programming, `JFrame`, and creating the first window.

## Estimated Total Time
5 hours total — 2-hour lecture session + 3-hour laboratory session.

## Learning Outcomes
By the end of this week, students will be able to:

1. Explain the difference between a console application and a graphical user
   interface (GUI) application.
2. Describe the relationship between AWT and Swing, and explain why Swing is
   preferred for this course.
3. Explain what event-driven programming means and how it differs from the
   top-to-bottom execution style used in console programs.
4. Create a Java class that extends `JFrame` and display a basic application
   window using Eclipse.
5. Set the size, title, visibility, default close operation, and layout of a
   `JFrame`.
6. Run, test, and troubleshoot a simple Swing application in Eclipse.

## Prerequisite Knowledge
Students should already be comfortable with Java classes, objects, constructors,
methods, and the `main` method, since these are used to build the first window.
Students should also be able to create Java projects, packages, and classes in
Eclipse, since those steps are not re-taught in detail here. No prior knowledge
of Swing, AWT, layouts, or event handling is assumed; all of it is introduced
from scratch in this module.

## 2-Hour Lecture Plan

| Segment | Time | Activity |
|---|---:|---|
| 1 | 15 min | Review of console applications built in Weeks 1–22; discussion of their limitations |
| 2 | 20 min | Introduction to GUI applications and real-world examples |
| 3 | 20 min | AWT vs. Swing: history and why Swing is used in this course |
| 4 | 25 min | Event-driven programming concepts and how they differ from sequential execution |
| 5 | 30 min | Introducing `JFrame`: creating and configuring the first window (live coding) |
| 6 | 10 min | Common errors when the window does not appear; wrap-up and questions |

## Detailed Lesson Discussion

Every program written so far in this course has been a **console application**.
A console application reads input from the keyboard using a `Scanner`, performs
some processing, and prints results back to the terminal using
`System.out.println`. The program runs in a strict, predictable order: it starts
at the first line of `main`, executes statement by statement, and stops when it
reaches the end of the method or when the user tells it to stop. This works well
for many types of programs, but it is not how most software that people actually
use every day behaves. A word processor, a music player, a messaging app, and a
point-of-sale system all present windows, buttons, menus, and other visual
elements that the user can click, type into, and interact with in almost any
order. These are called **graphical user interface (GUI) applications**, and
building them requires a different way of thinking about program structure.

Java has provided two major toolkits for building GUI applications over the
years. The first is the **Abstract Window Toolkit (AWT)**, which was part of
the very first version of Java. AWT components are drawn using the underlying
operating system's own drawing routines, which made AWT windows look native but
also made them behave inconsistently across different operating systems. To
solve this problem, Java introduced **Swing**, a GUI toolkit built almost
entirely in Java itself rather than relying on the operating system to draw
each component. Because Swing draws its own components, a Swing application
looks and behaves the same way on Windows, macOS, and Linux. Swing also offers
a much larger set of components, including tables, tabbed panes, and trees,
which AWT never provided. For these reasons, this course uses Swing for all GUI
work, although a few foundational classes that Swing depends on, such as
`Color` and `Font`, still come from the `java.awt` package. It is normal to see
Swing programs import classes from both `javax.swing` and `java.awt` in the
same file.

The biggest conceptual shift this week is moving from **sequential
programming** to **event-driven programming**. In a console program, the
programmer controls the order of execution directly: ask for input, then
process it, then print a result. In a GUI program, the application instead
displays a window and then waits. The program does not know in advance whether
the user will click a button first, type into a text field first, or resize
the window first. Rather than writing one long sequence of steps, the
programmer writes small blocks of code — called **event handlers** — that are
attached to specific components and specific actions. When the user performs
that action, such as clicking a button, the Java runtime automatically calls
the matching event handler. The rest of the time, the program is simply idle,
waiting for the next event. This waiting loop is managed automatically by
Swing through something called the **Event Dispatch Thread (EDT)**, which
continuously checks for new events such as mouse clicks or key presses and
delivers them to the correct component. Students do not need to manage the EDT
directly at this stage, but it is useful to know that this is the mechanism
that keeps a GUI application "listening" after it starts.

The starting point for almost every Swing desktop application is the
`JFrame` class, found in the `javax.swing` package. A `JFrame` represents a
top-level window: it has a title bar, minimize and maximize buttons, a close
button, and a content area where other components such as buttons and labels
can be placed. Creating a `JFrame` on its own is simple, but a few extra steps
are required before the window will behave correctly. First, the frame needs a
size, set using the `setSize(width, height)` method, measured in pixels.
Second, it needs a **default close operation**, set using
`setDefaultCloseOperation(...)`, which tells Java what to do when the user
clicks the close button on the window. If this is not set, clicking the close
button will hide the window, but the Java program will keep running in the
background because the JVM does not automatically know that closing that
window means the whole application should stop. The constant
`JFrame.EXIT_ON_CLOSE` tells the JVM to terminate the entire application when
the window is closed, which is almost always the correct choice for a simple
standalone Swing application. Finally, and most easily forgotten by beginners,
the frame must be made visible by calling `setVisible(true)`. A `JFrame` is
invisible by default, so a program that creates a frame but never calls
`setVisible(true)` will run and then immediately exit without showing anything
on screen, which is one of the most common early mistakes in GUI programming.

A simple `JFrame` can be built in two ways: by creating an instance of
`JFrame` directly inside `main` and configuring it there, or by writing a class
that **extends** `JFrame`, moving the configuration into a constructor. The
second approach is preferred for this course because it scales naturally as
more components are added in later weeks; the constructor becomes the natural
place to build the whole window, and additional methods can be added later to
manage the window's behavior. In this style, the class itself *is* a window,
because it inherits all of the behavior of `JFrame` through inheritance, a
concept already familiar from Week 13. Calling `super(title)` inside the
constructor passes the window's title up to the `JFrame` constructor, exactly
the same way a subclass constructor calls a superclass constructor in ordinary
Java inheritance.

## Important Terminology

As you work through this week's material, make sure you are comfortable using
these terms: **GUI (graphical user interface)**, **AWT**, **Swing**,
**event-driven programming**, **event**, **event handler**, **Event Dispatch
Thread (EDT)**, **JFrame**, **content pane**, and **default close operation**.

## Complete Java Programming Example

```java
package week23.gui;

import javax.swing.JFrame;

/**
 * A minimal Swing application that displays an empty window.
 * This is the "Hello World" of GUI programming.
 */
public class FirstWindow extends JFrame {

    public FirstWindow() {
        super("My First Java Window");           // sets the title bar text
        setSize(400, 300);                        // width x height in pixels
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);              // centers the window on screen
        setVisible(true);                         // makes the window appear
    }

    public static void main(String[] args) {
        new FirstWindow();
    }
}
```

## Step-by-Step Eclipse IDE Instructions

1. Open Eclipse and select your usual workspace from the workspace launcher.
2. From the menu bar, choose **File > New > Java Project**.
3. Name the project `Week23-FirstWindow` and click **Finish**. If Eclipse asks
   whether to open the Java perspective, click **Open Perspective**.
4. In the **Package Explorer**, expand the project, right-click the `src`
   folder, and choose **New > Package**. Name the package `week23.gui`.
5. Right-click the new package and choose **New > Class**. Name the class
   `FirstWindow`, and check the box **public static void main(String[] args)**
   so Eclipse generates the method stub, then click **Finish**.
6. Delete the generated method stubs and paste in the complete code shown
   above, or type it in by hand to build muscle memory with Swing syntax.
7. Save the file with **File > Save** or **Ctrl+S**. Eclipse compiles
   automatically as soon as the file is saved.
8. If the import statement for `JFrame` shows a red underline, hover over it
   and choose **Import 'JFrame' (javax.swing)** from the Quick Fix menu, or
   simply type the import line manually as shown in the example.
9. Right-click `FirstWindow.java` in the Package Explorer and choose
   **Run As > Java Application**.
10. Observe the result: a window titled "My First Java Window" should appear
    in the center of the screen, sized 400 by 300 pixels, with no visible
    components inside it yet.
11. Click the close button on the window and confirm that the program stops
    running (the red square "terminate" icon in the Eclipse Console view
    should disappear or become gray).
12. Intentionally remove the `setVisible(true)` line, save, and run again to
    observe that the program starts and exits immediately with no window —
    this demonstrates why that line is required. Restore the line afterward.

## Guided Worked Example

**Problem description:** Modify `FirstWindow` so that the window displays a
different title, a different size, and starts in the upper-left corner of the
screen instead of the center, to confirm understanding of each configuration
method.

```java
package week23.gui;

import javax.swing.JFrame;

public class StudentPortalWindow extends JFrame {

    public StudentPortalWindow() {
        super("Student Portal - Home");
        setSize(600, 450);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocation(0, 0);              // places the window at the top-left corner
        setVisible(true);
    }

    public static void main(String[] args) {
        new StudentPortalWindow();
    }
}
```

**Walkthrough:** The call to `super("Student Portal - Home")` changes only the
title bar text; it does not affect anything drawn inside the window because no
components have been added yet. Replacing `setLocationRelativeTo(null)` with
`setLocation(0, 0)` demonstrates that window position and window centering are
two different, competing settings — calling both would cause the second call
to override the first, so only one should be used at a time. Changing
`setSize(400, 300)` to `setSize(600, 450)` shows that the two arguments are
always width first, then height, measured in pixels, matching the order used
by many other Swing sizing methods introduced in later weeks.

**Expected behavior:** Running `StudentPortalWindow` opens a 600-by-450 pixel
window titled "Student Portal - Home" anchored to the top-left corner of the
screen. Clicking the close box ends the program.

**Test data:** No user input is required for this example; the test is simply
observing the window's title, size, and position.

**Likely errors:** Students who set the size after calling `setVisible(true)`
will sometimes see the window flash at its default size before resizing,
because Swing configuration methods should generally be called before
`setVisible(true)`. Confirm the order shown above.

## Code Walkthrough

The class declaration `public class FirstWindow extends JFrame` means that
`FirstWindow` inherits every method already built into `JFrame`, including
`setSize`, `setTitle`, `setVisible`, and `setDefaultCloseOperation`. Because of
this inheritance, an instance of `FirstWindow` *is* a window; there is no
separate window object being created and attached — the object itself behaves
as the window when it is made visible. The constructor is where all one-time
setup happens: it configures the title through `super(...)`, sets pixel
dimensions, tells Java what to do when the window closes, positions the
window, and finally reveals it. The `main` method contains only one line,
`new FirstWindow();`, because creating the object automatically runs the
constructor, and the constructor is responsible for displaying the window.
This is different from most of the console programs written earlier in the
course, where `main` typically contained most of the program's logic directly.

## Expected GUI or Program Behavior

Running `FirstWindow` opens a blank 400-by-300 pixel window titled "My First
Java Window", centered on the screen. The window can be moved, minimized,
maximized, and resized by the user like any normal desktop window, even though
it currently contains no visible components. Clicking the close button
terminates the Java program because of `JFrame.EXIT_ON_CLOSE`.

## Laboratory Session (3 Hours)

### Laboratory Title
Building and Configuring Your First Swing Windows

### Objectives
By the end of this laboratory, students will be able to:

1. Create multiple independent `JFrame`-based classes in a single Eclipse
   project.
2. Correctly configure size, title, close operation, and visibility for each
   window.
3. Diagnose and correct the most common reasons a Swing window fails to
   appear.
4. Explain, in their own words, the event-driven nature of the applications
   they built.

### Required Software and Materials
Eclipse IDE with a working Java Development Kit (JDK 8 or later), no external
libraries required this week.

### Required Java Concepts
Classes, constructors, inheritance, and the `main` method, all from previous
weeks, combined with this week's new `JFrame` configuration methods.

### Development Requirements
Students must create one Eclipse project named `Week23-Lab` containing a
package `week23.lab` with **three** separate `JFrame` subclasses:
`WelcomeWindow`, `AboutWindow`, and `ContactWindow`, each representing a
different screen of a fictional "Campus Info Kiosk" application. Each class
must be independently runnable (each must contain its own `main` method).

### Detailed Laboratory Procedure

1. Create the Eclipse project `Week23-Lab` and the package `week23.lab`.
2. Create `WelcomeWindow`, extending `JFrame`, titled "Campus Info Kiosk -
   Welcome", sized 500x350, centered on screen, with `EXIT_ON_CLOSE` behavior.
3. Run `WelcomeWindow` and confirm it appears correctly before moving on.
4. Create `AboutWindow`, titled "Campus Info Kiosk - About", sized 450x300,
   positioned at coordinates (100, 100) using `setLocation`.
5. Run `AboutWindow` and confirm the window appears in the expected screen
   position rather than centered.
6. Create `ContactWindow`, titled "Campus Info Kiosk - Contact Us", sized
   400x250, using `setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE)` instead
   of `EXIT_ON_CLOSE`.
7. Run `ContactWindow`, close it, and observe in the Eclipse Console view that
   the program does **not** immediately show as terminated the way the other
   two windows did, since `DISPOSE_ON_CLOSE` only closes that window rather
   than stopping the whole JVM. Add a `System.out.println` statement after
   `setVisible(true)` in all three classes and note when it prints relative to
   the window appearing, to confirm that `main` finishes running almost
   immediately after the constructor returns, even though the window remains
   open.
8. Intentionally comment out the `setVisible(true)` line in a copy of
   `WelcomeWindow` named `BrokenWindow`, run it, and record what happens in
   the lab report.
9. Restore `BrokenWindow` by adding `setVisible(true)` back and confirm the
   fix works, demonstrating the debugging process expected in professional
   development.

### Required Features
- All three main kiosk windows must run independently without errors.
- Each window must have a distinct title, size, and screen position as
  specified.
- `ContactWindow` must use `DISPOSE_ON_CLOSE` rather than `EXIT_ON_CLOSE`.
- The intentionally broken window must be included, along with the corrected
  version, to show the debugging process.

### Testing Procedure
Run each class individually using **Run As > Java Application** and visually
confirm title text, approximate size, and position for each window.

### Normal Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Run WelcomeWindow | Right-click > Run As > Java Application | Window titled "Campus Info Kiosk - Welcome" appears centered, 500x350 |
| Run AboutWindow | Right-click > Run As > Java Application | Window appears at screen position (100,100), 450x300 |

### Boundary Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Very small size | Temporarily set ContactWindow to 100x100 | Window still opens, though title bar text may be clipped |
| Position at (0,0) | Temporarily set AboutWindow location to (0,0) | Window opens flush with the top-left corner of the screen |

### Invalid Test Cases
| Test | Action | Expected Result |
|---|---|---|
| Missing setVisible | Run BrokenWindow before the fix | No window appears; program ends immediately with no error message |
| Negative size values | Set setSize(-100, -100) | Window either fails to display meaningfully or appears with default/minimum dimensions, demonstrating why realistic sizes must always be used |

### Expected Results Summary
All three kiosk windows open, display correctly, and close according to their
configured close operation. The broken window demonstrates a missing
`setVisible(true)` call and its corrected version resolves the issue.

### Required Deliverables
1. Eclipse project `Week23-Lab` containing all four classes.
2. A short lab report (half a page) describing what happened with
   `BrokenWindow` before and after the fix, and what `DISPOSE_ON_CLOSE` did
   differently from `EXIT_ON_CLOSE`.
3. Screenshots of all three working kiosk windows.

### 100-Point Grading Rubric
| Category | Points |
|---|---:|
| WelcomeWindow correctly configured and runs | 20 |
| AboutWindow correctly configured and runs | 20 |
| ContactWindow correctly configured and runs with DISPOSE_ON_CLOSE | 20 |
| BrokenWindow demonstrates the bug, then is corrected | 15 |
| Lab report explains observations accurately | 15 |
| Code organization, naming, and package structure | 10 |
| **Total** | **100** |

## Testing and Debugging Guidance

The most common problem this week is a program that runs without errors but
shows no window at all. This almost always means `setVisible(true)` was never
called, or was called before the size and title were set and then somehow
skipped afterward. A second common problem is a window that appears but
closing it does not stop the Eclipse "Terminate" indicator; this means
`setDefaultCloseOperation` was left at its default value (`HIDE_ON_CLOSE`)
rather than being explicitly set to `EXIT_ON_CLOSE`. Students should also
double-check their import statement; typing `java.swing.JFrame` instead of
`javax.swing.JFrame` is a frequent typo that produces a "cannot find symbol"
compiler error. If Eclipse reports that the class does not have a `main`
method when trying to run it, confirm that `main` was typed exactly as
`public static void main(String[] args)`, since any deviation, such as a
lowercase `s` missing from `Args` or a missing `static` keyword, prevents
Eclipse from recognizing it as an entry point.

## Review and Practice Questions

1. In your own words, explain the difference between a console application and
   a GUI application.
2. Why does Swing produce a consistent look across operating systems while AWT
   does not?
3. What happens if a `JFrame` is created and configured but `setVisible(true)`
   is never called? Why does this happen?
4. What is the difference between `JFrame.EXIT_ON_CLOSE` and
   `JFrame.DISPOSE_ON_CLOSE`?
5. Rewrite the `FirstWindow` class so that it opens at position (250, 150)
   instead of being centered on the screen.
6. What does it mean for a program to be "event-driven," and how is this
   different from the console programs written in earlier weeks?
7. A classmate's program compiles with no errors but nothing appears on
   screen when it runs. List two possible causes and how you would check for
   each one.

## Lesson Summary

This week introduced the shift from console-based programs to graphical,
event-driven Swing applications, beginning with the `JFrame` class as the
foundation of every window built for the rest of the course. Students learned
why Swing is preferred over AWT, how to configure a window's size, title,
position, and close behavior, and how to diagnose the most common beginner
mistake of a window that never appears. The next lesson builds directly on
this foundation by adding visible components — labels, text fields, and
buttons — inside the window created this week.
