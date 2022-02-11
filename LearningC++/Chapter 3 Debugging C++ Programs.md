## Chapter 3 Debugging C++ Programs

Software errors are prevalent. It’s easy to make them, and it’s hard to find them. In this chapter, we’ll explore topics related to the finding and removal of bugs within our C++ programs, including learning how to use the integrated debugger that is part of our IDE.

Although debugging tools and techniques aren’t part of the C++ standard, learning to find and remove bugs in the programs you write is an extremely important part of being a successful programmer. Therefore, we’ll spend a bit of time covering such topics, so that as the programs you write become more complex, your ability to diagnose and remedy issues advances at a similar pace.

Programming can be challenging, and C++ is somewhat of a quirky language. Put those two together, and there are a lot of ways to make mistakes. Errors generally fall into one of two categories: syntax errors, and semantic errors (logic errors).

A **syntax error** occurs when you write a statement that is not valid according to the grammar of the C++ language. This includes errors such as missing semicolons, using undeclared variables, mismatched parentheses or braces, etc…

A **semantic error** occurs when a statement is syntactically valid, but does not do what the programmer intended. Sometimes these will cause your program to crash.

A general approach to debugging

Once a problem has been identified, debugging the problem generally consists of five steps:

1. Find the root cause of the problem (usually the line of code that’s not working)
2. Ensure you understand why the issue is occurring
3. Determine how you’ll fix the issue
4. Repair the issue causing the problem
5. Retest to ensure the problem has been fixed and no new problems have emerged

A Strategy for Debugging

When debugging a program, in most cases the vast majority of your time will be spent trying to find where the error actually is. Once the issue is found, the remaining steps (fixing the issue and validating that the issue was fixed) are often trivial in comparison.

In this lesson, we’ll start exploring how to find errors.

**Finding problems via code inspection**. However, as programs get more complex, finding issues by code inspection becomes more complex as well.

First, there’s a lot more code to look at. Looking at every line of code in a program that is thousands of lines long can take a really long time (not to mention it’s incredibly boring). Second, the code itself tends to be more complex, with more possible places for things to go wrong. Third, the code’s behavior may not give you many clues as to where things are going wrong. If you wrote a program to output stock recommendations and it actually output nothing at all, you probably wouldn’t have much of a lead on where to start looking for the problem.

Finally, bugs can be caused by making bad assumptions. It’s almost impossible to visually spot a bug caused by a bad assumption, because you’re likely to make the same bad assumption when inspecting the code, and not notice the error. So if we have an issue that we can’t find via code inspection, how do we find it?

**Finding problems by running the program**. Fortunately, if we can’t find an issue via code inspection, there is another avenue we can take: we can watch the behavior of the program as it runs, and try to diagnose the issue from that. This approach can be generalized as:

1. Figure out how to reproduce the problem
2. Run the program and gather information to narrow down where the problem is
3. Repeat the prior step until you find the problem

**Reproducing the problem**. The first and most important step in finding a problem is to be able to *reproduce the problem*. The reason is simple: it’s extremely hard to find an issue unless you can observe it occurring.

Basic debugging tactics

**Debugging tactic #1: Commenting out your code**

Let’s start with an easy one. If your program is exhibiting erroneous behavior, one way to reduce the amount of code you have to search through is to comment some code out and see if the issue persists. If the issue remains, the commented out code wasn’t responsible.

**Debugging tactic #2: Validating your code flow**

Another problem common in more complex programs is that the program is calling a function too many or too few times (including not at all).

In such cases, it can be helpful to place statements at the top of your functions to print the function’s name. That way, when the program runs, you can see which functions are getting called.

<font color="blue">**Tip**</font>: When printing information for debugging purposes, use std::cerr instead of std::cout. One reason for this is that std::cout may be buffered, which means there may be a pause between when you ask std::cout to output information and when it actually does. If you output using std::cout and then your program crashes immediately afterward, std::cout may or may not have actually output yet. This can mislead you about where the issue is. On the other hand, std::cerr is unbuffered, which means anything you send to it will output immediately. This helps ensure all debug output appears as soon as possible (at the cost of some performance, which we usually don’t care about when debugging).

<font color="blue">**Tip**</font>: When adding temporary debug statements, it can be helpful to not indent them. This makes them easier to find for removal later.

**Debugging tactic #3: Printing values**

With some types of bugs, the program may be calculating or passing the wrong value.

We can also output the value of variables (including parameters) or expressions to ensure that they are correct.

<font color="red">Discussion</font>: **Why using printing statements to debug isn’t great**

While adding debug statements to programs for diagnostic purposes is a common rudimentary technique, and a functional one (especially when a debugger is not available for some reason), it’s not that great for a number of reasons:

1. Debug statements clutter your code.
2. Debug statements clutter the output of your program.
3. Debug statements must be removed after you’re done with them, which makes them non-reusable.
4. Debug statements require modification of your code to both add and to remove, which can introduce new bugs.

More Debugging Tactics

**Conditionalizing your debugging code**

Consider the following program that contains some debug statements:

```c++
#include <iostream>
 
int getUserInput()
{
std::cerr << "getUserInput() called\n";
	std::cout << "Enter a number: ";
	int x{};
	std::cin >> x;
	return x;
}
 
int main()
{
std::cerr << "main() called\n";
    int x{ getUserInput() };
    std::cout << "You entered: " << x;
 
    return 0;
}
```

When you’re done with the debugging statement, you’ll either need to remove them, or comment them out. Then if you want them again later, you’ll have to add them back, or uncomment them.

One way to make it easier to disable and enable debugging throughout your program is to make your debugging statements conditional using preprocessor directives:

```C++
#include <iostream>
 
#define ENABLE_DEBUG // comment out to disable debugging
 
int getUserInput()
{
#ifdef ENABLE_DEBUG
std::cerr << "getUserInput() called\n";
#endif
	std::cout << "Enter a number: ";
	int x{};
	std::cin >> x;
	return x;
}
 
int main()
{
#ifdef ENABLE_DEBUG
std::cerr << "main() called\n";
#endif
    int x{ getUserInput() };
    std::cout << "You entered: " << x;
 
    return 0;
}
```

This addresses the issue with having to remove debug statements and the risk in doing so, but at the cost of even more code clutter. Another downside of this approach is that if you make a typo (e.g. misspell “DEBUG”) or forget to include the header into a code file, some or all of the debugging for that file may not be enabled. So although this is better than the unconditionalized version, there’s still room to improve.

**Using a logger**

An alternative approach to conditionalized debugging via the preprocessor is to send your debugging information to a log file. A **log file** is a file (normally stored on disk) that records events that occur in software. The process of writing information to a log file is called **logging**. Most applications and operating systems write log files that can be used to help diagnose issues that occur.

Log files have a few advantages. Because the information written to a log file is separated from your program’s output, you can avoid the clutter caused by mingling your normal output and debug output. Log files can also be easily sent to other people for diagnosis -- so if someone using your software has an issue, you can ask them to send you the log file, and it might help give you a clue where the issue is.

While you can write your own code to create log file and send output to them, you’re better off using one of the many existing third-party logging tools available. Which one you use is up to you.

For illustrative purposes, we’ll show what outputting to a logger looks like using the [plog](https://github.com/SergiusTheBest/plog) logger. Plog is implemented as a set of header files, so it’s easy to include anywhere you need it, and it’s lightweight and easy to use.

### 3.6 — Using an integrated debugger: Stepping

When you run your program, execution begins at the top of the *main* function, and then proceeds sequentially statement by statement, until the program ends. At any point in time while your program is running, the program is keeping track of a lot of things: the value of the variables you’re using, which functions have been called (so that when those functions return, the program will know where to go back to), and the current point of execution within the program (so it knows which statement to execute next). All of this tracked information is called your **program state** (or just *state*, for short).

Behind the techniques we’ve shown so far is an unstated assumption: that once we run the code, it will run to completion (only pausing to accept input) with no opportunity for us to intervene and inspect the results of the program at whatever point we want.

**The Debugger**

A **debugger** is a computer program that allows the programmer to control how another program executes and examine the program state while that program is running. For example, the programmer can use a debugger to execute a program line by line, examining the value of variables along the way. By comparing the actual value of variables to what is expected, or watching the path of execution through the code, the debugger can help immensely in tracking down semantic (logic) errors.

The power behind the debugger is twofold: the ability to precisely control execution of the program, and the ability to view (and modify, if desired) the program’s state.

**Debugger's History**: Early debuggers, such as [gdb](https://en.wikipedia.org/wiki/Gdb), were separate programs that had command-line interfaces, where the programmer had to type arcane commands to make them work. Later debuggers (such as early versions of Borland’s [turbo debugger](https://en.wikipedia.org/wiki/Turbo_Debugger)) were still standalone, but came with their own “graphical” front ends to make working with them easier. Most modern IDEs available these days have an **integrated debugger** -- that is, a debugger uses the same interface as the code editor, so you can debug using the same environment that you use to write your code (rather than having to switch programs).

**Stepping** is the name for a set of related debugger features that let us execute (step through) our code statement by statement.

The **step into** command executes the next statement in the normal execution path of the program, and then pauses execution of the program so we can examine the program’s state using the debugger. If the statement being executed contains a function call, *step into* causes the program to jump to the top of the function being called, where it will pause.

<font color="green">Tip</font>: In a prior lesson, we mentioned that std::cout is buffered, which means there may be a delay between when you ask std::cout to print a value, and when it actually does. Because of this, you may not see the value 5 appear at this point. To ensure that all output from std::cout is output immediately, you can add the following statement to the top of your main() function:

```C++
std::cout << std::unitbuf; // enable automatic flushing for std::cout (for debugging)
```

Like *step into*, The **step over** command executes the next statement in the normal execution path of the program. However, whereas *step into* will enter function calls and execute them line by line, *step over* will execute an entire function without stopping and return control to you after the function has been executed.

The *step over* command provides a convenient way to skip functions when you are sure they already work or are not interested in debugging them right now.

Unlike the other two stepping commands, **Step out** does not just execute the next line of code. Instead, it executes all remaining code in the function currently being executed, and then returns control to you when the function has returned.

This command is most useful when you've accidentally stepped into a function that you don't want to debug.

**Run to Cursor**

The first useful command is commonly called *Run to cursor*. This **Run to cursor** command executes the program until execution reaches the statement selected by your cursor. Then it returns control to you so you can debug starting at that point. This makes for an efficient way to start debugging at a particular point in your code, or if already debugging, to move straight to some place you want to examine further.

---

<font color="blue">For Visual Studio users</font>

In Visual Studio, the *run to cursor* command can be accessed by right clicking a statement in your code and choosing *Run to Cursor* from the context menu, or by pressing the ctrl-F10 keyboard combo.</background>

---

If you *run to cursor* to a location that doesn’t execute, *run to cursor* will simply run your program until termination.

**Continue**: Once you’re in the middle of a debugging session, you may want to just run the program from that point forward. The easiest way to do this is to use the *continue* command. The **continue** debug command simply continues running the program as per normal, either until the program terminates, or until something triggers control to return back to you again (such as a breakpoint, which we’ll cover later in this lesson).

---

<font color="blue">For Visual Studio users</font>

In Visual Studio, the *continue* command can be accessed while already debugging a program via *Debug menu > Continue*, or by pressing the F5 shortcut key.

---

**Start**: The *continue* command has a twin brother named *start*. The *start* command performs the same action as *continue*, just starting from the beginning of the program. It can only be invoked when not already in a debug session.

A **breakpoint** is a special marker that tells the debugger to stop execution of the program at the breakpoint when running in debug mode.

---

<font color="blue">**For Visual Studio users**</font>

In Visual Studio, you can set or remove a breakpoint via *Debug menu > Toggle Breakpoint*, or by right clicking on a statement and choosing *Toggle Breakpoint* from the context menu, or by pressing the F9 shortcut key, or by clicking to the left of the line number (in the light grey area).

---

Note that breakpoints placed on lines that are not in the path of execution will not cause the debugger to halt execution of the code.



**Set Next Statement**

There’s one more debugging command that’s used fairly uncommonly, but is still at least worth knowing about, even if you won’t use it very often. The **set next statement** command allows us to change the point of execution to some other statement (sometimes informally called *jumping*). This can be used to jump the point of execution forwards and skip some code that would otherwise execute, or backwards and have something that already executed run again.

---

<font color="blue">**For Visual Studio users**</font>

In Visual Studio, you can jump the point of execution by right clicking on a statement and choosing *Set next statement* from the context menu, or by pressing the Ctrl-Shift-F10 shortcut combination. This option is contextual and only occurs while already debugging a program.

---

In our exploration of basic debugging techniques, we discussed commenting out a function as a way to determine whether that function had a role in causing an issue. This requires modifying our code, and remembering to uncomment the function later. In the debugger, there’s no direct way to skip a function, so if you decide you want to do this, using *set next statement* to jump over a function call is the easiest way to do so.

Jumping backwards can also be useful if we want to watch a function that just executed run again, so we can see what it is doing.

**The Call Stack**

When your program calls a function, you already know that it bookmarks the current location, makes the function call, and then returns. How does it know where to return to? The answer is that it keeps track in the call stack.

The **call stack** is a list of all the active functions that have been called to get to the current point of execution. The call stack includes an entry for each function called, as well as which line of code will be returned to when the function returns. Whenever a new function is called, that function is added to the top of the call stack. When the current function returns to the caller, it is removed from the top of the call stack, and control returns to the function just below it.

