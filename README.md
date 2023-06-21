# BHHI Stata Style Guide
Eve Perry

- [1. Files](#files)
  - [1.1 Names](#names)
  - [1.2 Internal structure](#internal-structure)
- [2 Syntax](#syntax)
  - [2.1 Variable names](#variable-names)
  - [2.2 Spacing](#spacing)
  - [2.3 Loops](#loops)
  - [2.4 Commands](#commands)
  - [2.5 Comments](#comments)
  - [2.6 Semicolons](#semicolons)
  - [2.7 Long lines](#long-lines)

This is the BHHI guide to writing Stata code. Its goal is to create
consistency in our code and to make our code more readable and
understandable for each other and for our future selves.

The guide draws heavily on the [R Tidyverse style
guide](https://style.tidyverse.org/) by Hadley Wickham
([license](https://creativecommons.org/licenses/by-sa/3.0/)), with
modifications for the Stata language.

## 1. Files

### 1.1 Names

File names should be meaningful and end in .do. Avoid using special
characters in file names - stick with numbers, letters, -, and \_.

    // Good
    fit_models.do
    clean_data.do

    // Bad
    fit models.do
    foo.do
    stuff.do

If files should be run in a particular order, prefix them with numbers.
If it seems likely you’ll have more than 10 files, left pad with zero:

    00_download.do
    01_explore.do
    ...
    09_model.do
    10_visualize.do

If you later realise that you’ve missed some steps, it’s tempting to use
02a, 02b, etc. However, we think it’s generally better to bite the
bullet and rename all files.

Pay attention to capitalization, since you, or some of your
collaborators, might be using an operating system with a
case-insensitive file system (e.g., Microsoft Windows or OS X) which can
lead to problems with (case-sensitive) revision control systems. Prefer
file names that are all lower case, and never have names that differ
only in their capitalization.

### 1.2 Internal structure

Use commented lines of - and = to break up your file into easily
readable chunks.

``` stata
// Load data ---------------------------

// Plot data ---------------------------
```

If your script uses external `.ado` files or packages, note their use in
a comment at the top of the file so others can ensure they have those
files or packages installed.

## 2 Syntax

### 2.1 Variable names

Stata has rules for variable names:

- Names can only contain letters, digits, and underscores (`_`)
- Names can only begin with a letter or `_`
- Names can only be 32 characters long

We have a few additional standards for variable names:

- Only use lowercase letters, numbers, and `_`
- Use `_` to separate words within a name
- Avoid starting variable names with an `_` (because these are typically
  used for built-in variables)

``` stata
// Good
day_one
day_1

// Bad
DayOne
dayone
_1_day
```

### 2.2 Spacing

The spacebar is your friend! Putting spaces in your code makes it more
readable and easier to understand, both for others and for your future
self.

#### 2.2.1 Operators

Most operators (`+`, `-`, `=`, `==`, `<`, `<=`, etc.) should have spaces
on both sides.

``` stata
// Good
gen test = 1
gen addition = foo + bar

gen variable_high if variable > 1000

replace fruit = "apple" if fruit != "banana"

// Bad

gen test=1
gen addition = foo+bar

gen variable_high if variable>1000

replace fruit="apple" if fruit!="banana"
```

#### 2.2.2 Parentheses

Do not put spaces inside or outside parentheses for function calls.

``` stata
// Good
egen mean = mean(lexp)

// Bad
egen mean = mean (lexp)
egen mean = mean( lexp )
```

#### 2.2.3 Commas

Always put a space after a comma, never before, just like in regular
English.

``` stata
// Good
tab foo bar, row

// Bad
tab foo bar,row
tab foo bar , row
```

### 2.3 Loops

Try to avoid repeating yourself when writing code. If you find yourself
cut and pasting a single line multiple times, consider if it’s possible
to use a loop.

It’s much easier to make changes to code that uses loops, than when
lines are repeated multiple times with small alterations.

When writing loops, put the `for` statement on its own line with a space
before the `{` and then indent the code in the loop before closing the
loop with the `}` on its own line.

``` stata
// Good
foreach var in foo bar biz {
  replace `var' = `var' + 1
}

// Bad
replace foo = foo + 1
replace bar = bar + 1
replace biz = biz + 1

foreach var in foo bar biz{
replace `var' = `var' + 1
}
```

### 2.4 Commands

Stata doesn’t require you to type the full name of a command (ie.
`generate`, `gen`, and `g` are all legal ways to invoke the `generate`
command).

There is a tradeoff here between typing ease, readability, and Stata
conventions. It’s annoying to type the full name for some commands
(especially frequently used ones), but cutting the name too short hurts
readability.

To balance this tradeoff, use **at least 3 characters** of a command’s
name and strongly consider using the full name for less common commands.

``` stata
// Good
gen foo = 1
generate foo = 1

tab foo bar
tabulate foo bar

// Bad
g foo = 1

ta foo bar
```

### 2.5 Comments

Comments should describe *why* you are writing a particular piece of
code that needs additional explanation, not *how* the code works. The
code itself explains the *how* but commenting the *why* (especially if
doing something non-standard) is most useful for another person or your
future self.

When using comments to record notes on the analysis, don’t just rely on
adding dates, be proactive on removing comments once the code changes
and the comment is no longer relavent.

**But strongly consider using [`dyndoc` (Stata
markdown)](https://www.stata.com/features/overview/markdown/) for
analysis notes.** This format combines both code, output, and
explanation text in one document that makes it much easier to understand
what’s happening than code comments and a log file.

#### 2.5.1 Single-line comments

Use `//` instead of `*` for line comments. While you can start a line
comment with `*` in Stata, it’s not a good idea because it’s too easily
confused with multiplication.

``` stata
// Good

* Bad
```

#### 2.5.2 Multi-line comments

Use `/*` and `*/` for multi-line comments

``` stata
// Good

/*
* Multi
* line
* comment
*/

// Bad

// Multi
// line
// comment

/*
Multi
line
comment
*/
```

### 2.6 Semicolons

Unless you have a good reason don’t use semicolons (`;`) to denote line
ends. Most of the time you should use the line continuation operator
(`///`) if your command needs to stretch over more than one line.

And never use semicolons to put multiple commands on one line.

``` stata
// Good

gen one = 1
gen two = 2

tabstat variable_1 variable_2 variable_3 ///
  variable_4 variable_5 variable_6, stats(mean)

// Bad
#delimit ;
gen one = 1;
gen two = 2;
gen three = 3; gen four = 4;

// Avoid
#delimit ;
tabstat variable_1 variable_2 variable_3 
  variable_4 variable_5 variable_6, stats(mean);
#delimit cr
```

### 2.7 Long lines

Try to keep individual lines of code to 80 characters. This is a
standard across many languages that makes it easier to read and review
code.
