<!--
a.summary-letter {text-decoration: none}
blockquote.indentedblock {margin-right: 0em}
blockquote.smallindentedblock {margin-right: 0em; font-size: smaller}
blockquote.smallquotation {font-size: smaller}
div.display {margin-left: 3.2em}
div.example {margin-left: 3.2em}
div.lisp {margin-left: 3.2em}
div.smalldisplay {margin-left: 3.2em}
div.smallexample {margin-left: 3.2em}
div.smalllisp {margin-left: 3.2em}
kbd {font-style: oblique}
pre.display {font-family: inherit}
pre.format {font-family: inherit}
pre.menu-comment {font-family: serif}
pre.menu-preformatted {font-family: serif}
pre.smalldisplay {font-family: inherit; font-size: smaller}
pre.smallexample {font-size: smaller}
pre.smallformat {font-family: inherit; font-size: smaller}
pre.smalllisp {font-size: smaller}
span.nolinebreak {white-space: nowrap}
span.roman {font-family: initial; font-weight: normal}
span.sansserif {font-family: sans-serif; font-weight: normal}
ul.no-bullet {list-style: none}
-->

# The GNU Awk User&rsquo;s Guide

Next: [Foreword3](#foreword-to-the-third-edition), Up: [(dir)](/manual)   [[Contents](#table-of-contents)][[Index](#Index)]

# General Introduction

This file documents `awk`, a program that you can use to select
particular records in a file and perform operations upon them.

Copyright &copy; 1989, 1991, 1992, 1993, 1996&ndash;2005, 2007, 2009&ndash;2018 

Free Software Foundation, Inc.

This is Edition 4.2 of GAWK: Effective AWK Programming: A User&rsquo;s Guide for GNU Awk,
for the 4.2.1 (or later) version of the GNU
implementation of AWK.

Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.3 or
any later version published by the Free Software Foundation; with the
Invariant Sections being &ldquo;GNU General Public License&rdquo;, with the
Front-Cover Texts being &ldquo;A GNU Manual&rdquo;, and with the Back-Cover Texts
as in (a) below.
A copy of the license is included in the section entitled
&ldquo;GNU Free Documentation License&rdquo;.

1.  The FSF&rsquo;s Back-Cover Text is: &ldquo;You have the freedom to
copy and modify this GNU manual.&rdquo;

| toc | description
| --- | ---
| &bull; [Foreword3](#foreword-to-the-third-edition) | Some nice words about this Web page.
| &bull; [Foreword4](#foreword-to-the-fourth-edition) | More nice words.
| &bull; [Preface](#preface) | What this Web page is about; brief history and acknowledgments.
| &bull; [Getting Started](#10-a-library-of-awk-functions) | A basic introduction to using `awk`. How to run an `awk` program. Command-line syntax.
| &bull; [Invoking Gawk](#2-running-awk-and-gawk) | How to run `gawk`.
| &bull; [Regexp](#3-regular-expressions) | All about matching things using regular expressions.
| &bull; [Reading Files](#4-reading-input-files) | How to read files and manipulate fields.
| &bull; [Printing](#5-printing-output) | How to print using `awk`. Describes the `print` and `printf` statements. Also describes redirection of output.
| &bull; [Expressions](#6-expressions) | Expressions are the basic building blocks of statements.
| &bull; [Patterns and Actions](#7-patterns-actions-and-variables) | Overviews of patterns and actions.
| &bull; [Arrays](#8-arrays-in-awk) | The description and use of arrays. Also includes array-oriented control statements.
| &bull; [Functions](#9-functions) | Built-in and user-defined functions.
| &bull; [Library Functions]((#10-a-library-of-awk-functions)) | A Library of `awk` Functions.
| &bull; [Sample Programs](#11-practical-awk-programs) | Many `awk` programs with complete explanations.
| &bull; [Advanced Features](#Advanced-Features) | Stuff for advanced users, specific to `gawk`.
| &bull; [Internationalization](#Internationalization) | Getting `gawk` to speak your language.
| &bull; [Debugger](#Debugger) | The `gawk` debugger.
| &bull; [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic) | Arbitrary precision arithmetic with `gawk`.
| &bull; [Dynamic Extensions](#Dynamic-Extensions) | Adding new built-in functions to `gawk`.
| &bull; [Language History](#Language-History) | The evolution of the `awk` language.
| &bull; [Installation](#Installation) | Installing `gawk` under various operating systems.
| &bull; [Notes](#Notes) | Notes about adding things to `gawk` and possible future work.
| &bull; [Basic Concepts](#Basic-Concepts) | A very quick introduction to programming concepts.
| &bull; [Glossary](#Glossary) | An explanation of some unfamiliar terms.
| &bull; [Copying](#Copying) | Your right to copy and distribute `gawk`.
| &bull; [GNU Free Documentation License](#GNU-Free-Documentation-License) | The license for this Web page.
| &bull; [Index](#Index) | Concept and Variable Index.
| &bull; [History](#history-of-awk-and-gawk) | The history of `gawk` and `awk`.
| &bull; [Names](#a-rose-by-any-other-name) | What name to use to find `awk`.
| &bull; [This Manual](#using-this-book) | Using this Web page. Includes sample input files that you can use.
| &bull; [Conventions](#typographical-conventions) | Typographical Conventions.
| &bull; [Manual History](#the-gnu-project-and-this-book) | Brief history of the GNU project and this Web page.
| &bull; [How To Contribute](#how-to-contribute) | Helping to save the world.
| &bull; [Acknowledgments](#acknowledgments) | Acknowledgments.
| &bull; [Running gawk](#11-how-to-run-awk-programs) | How to run `gawk` programs; includes command-line syntax.
| &bull; [One-shot](#111-one-shot-throwaway-awk-programs) | Running a short throwaway `awk` program.
| &bull; [Read Terminal](#112-running-awk-without-input-files) | Using no input files (input from the keyboard instead).
| &bull; [Long](#113-running-long-programs) | Putting permanent `awk` programs in files.
| &bull; [Executable Scripts](#114-executable-awk-programs) | Making self-contained `awk` programs.
| &bull; [Comments](#115-comments-in-awk-programs) | Adding documentation to `gawk` programs.
| &bull; [Quoting](#116-shell-quoting-issues) | More discussion of shell quoting issues.
| &bull; [DOS Quoting](#1161-quoting-in-ms-windows-batch-files) | Quoting in Windows Batch Files.
| &bull; [Sample Data Files](#12-data-files-for-the-examples) | Sample data files for use in the `awk` programs illustrated in this Web page.
| &bull; [Very Simple](#13-some-simple-examples) | A very simple example.
| &bull; [Two Rules](#14-an-example-with-two-rules) | A less simple one-line example using two rules.
| &bull; [More Complex](#15-a-more-complex-example) | A more complex example.
| &bull; [Statements/Lines](#16-awk-statements-versus-lines) | Subdividing or combining statements into lines.
| &bull; [Other Features](#17-other-features-of-awk) | Other Features of `awk`.
| &bull; [When](#18-when-to-use-awk) | When to use `gawk` and when to use other things.
| &bull; [Intro Summary](#19-summary) | Summary of the introduction.
| &bull; [Command Line](#21-invoking-awk) | How to run `awk`.
| &bull; [Options](#22-command-line-options) | Command-line options and their meanings.
| &bull; [Other Arguments](#23-other-command-line-arguments) | Input file names and variable assignments.
| &bull; [Naming Standard Input](#24-naming-standard-input) | How to specify standard input with other files.
| &bull; [Environment Variables](#25-the-environment-variables-gawk-uses) | The environment variables `gawk` uses.
| &bull; [AWKPATH Variable](#251-the-awkpath-environment-variable) | Searching directories for `awk` programs.
| &bull; [AWKLIBPATH Variable](#252-the-awklibpath-environment-variable) | Searching directories for `awk` shared libraries.
| &bull; [Other Environment Variables](#253-other-environment-variables) | The environment variables.
| &bull; [Exit Status](#26-gawkrsquos-exit-status) | `gawk`&rsquo;s exit status.
| &bull; [Include Files](#27-including-other-files-into-your-program) | Including other files into your program.
| &bull; [Loading Shared Libraries](#28-loading-dynamic-extensions-into-your-program) | Loading shared libraries into your program.
| &bull; [Obsolete](#29-obsolete-options-andor-features) | Obsolete Options and/or features.
| &bull; [Undocumented](#210-undocumented-options-and-features) | Undocumented Options and Features.
| &bull; [Invoking Summary](#211-summary) | Invocation summary.
| &bull; [Regexp Usage](#31-how-to-use-regular-expressions) | How to Use Regular Expressions.
| &bull; [Escape Sequences](#32-escape-sequences) | How to write nonprinting characters.
| &bull; [Regexp Operators](#33-regular-expression-operators) | Regular Expression Operators.
| &bull; [Bracket Expressions](#34-using-bracket-expressions) | What can go between &lsquo;[...]&rsquo;.
| &bull; [Leftmost Longest](#35-how-much-text-matches) | How much text matches.
| &bull; [Computed Regexps](#36-using-dynamic-regexps) | Using Dynamic Regexps.
| &bull; [GNU Regexp Operators](#37-gawk-specific-regexp-operators) | Operators specific to GNU software.
| &bull; [Case-sensitivity](#38-case-sensitivity-in-matching) | How to do case-insensitive matching.
| &bull; [Regexp Summary](#39-summary) | Regular expressions summary.
| &bull; [Records](#41-how-input-is-split-into-records) | Controlling how data is split into records.
| &bull; [awk split records](#411-record-splitting-with-standard-awk) | How standard `awk` splits records.
| &bull; [gawk split records](#412-record-splitting-with-gawk) | How `gawk` splits records.
| &bull; [Fields](#42-examining-fields) | An introduction to fields.
| &bull; [Nonconstant Fields](#43-nonconstant-field-numbers) | Nonconstant Field Numbers.
| &bull; [Changing Fields](#44-changing-the-contents-of-a-field) | Changing the Contents of a Field.
| &bull; [Field Separators](#45-specifying-how-fields-are-separated) | The field separator and how to change it.
| &bull; [Default Field Splitting](#451-whitespace-normally-separates-fields) | How fields are normally separated.
| &bull; [Regexp Field Splitting](#452-using-regular-expressions-to-separate-fields) | Using regexps as the field separator.
| &bull; [Single Character Fields](#453-making-each-character-a-separate-field) | Making each character a separate field.
| &bull; [Command Line Field Separator](#454-setting-fs-from-the-command-line) | Setting `FS` from the command line.
| &bull; [Full Line Fields](#455-making-the-full-line-be-a-single-field) | Making the full line be a single field.
| &bull; [Field Splitting Summary](#456-field-splitting-summary) | Some final points and a summary table.
| &bull; [Constant Size](#46-reading-fixed-width-data) | Reading constant width data.
| &bull; [Fixed width data](#461-processing-fixed-width-data) | Processing fixed-width data.
| &bull; [Skipping intervening](#462-skipping-intervening-fields) | Skipping intervening fields.
| &bull; [Allowing trailing data](#463-capturing-optional-trailing-data) | Capturing optional trailing data.
| &bull; [Fields with fixed data](#464-field-values-with-fixed-width-data) | Field values with fixed-width data.
| &bull; [Splitting By Content](#47-defining-fields-by-content) | Defining Fields By Content
| &bull; [Testing field creation](#48-checking-how-gawk-is-splitting-records) | Checking how `gawk` is splitting records.
| &bull; [Multiple Line](#49-multiple-line-records) | Reading multiline records.
| &bull; [Getline](#410-explicit-input-with-getline) | Reading files under explicit program control using the `getline` function.
| &bull; [Plain Getline](#4101-using-getline-with-no-arguments) | Using `getline` with no arguments.
| &bull; [Getline/Variable](#4102-using-getline-into-a-variable) | Using `getline` into a variable.
| &bull; [Getline/File](#4103-using-getline-from-a-file) | Using `getline` from a file.
| &bull; [Getline/Variable/File](#4104-using-getline-into-a-variable-from-a-file) | Using `getline` into a variable from a file.
| &bull; [Getline/Pipe](#4105-using-getline-from-a-pipe) | Using `getline` from a pipe.
| &bull; [Getline/Variable/Pipe](#4106-using-getline-into-a-variable-from-a-pipe) | Using `getline` into a variable from a pipe.
| &bull; [Getline/Coprocess](#4107-using-getline-from-a-coprocess) | Using `getline` from a coprocess.
| &bull; [Getline/Variable/Coprocess](#4108-using-getline-into-a-variable-from-a-coprocess) | Using `getline` into a variable from a coprocess.
| &bull; [Getline Notes](#4109-points-to-remember-about-getline) | Important things to know about `getline`.
| &bull; [Getline Summary](#41010-summary-of-getline-variants) | Summary of `getline` Variants.
| &bull; [Read Timeout](#411-reading-input-with-a-timeout) | Reading input with a timeout.
| &bull; [Retrying Input](#412-retrying-reads-after-certain-input-errors) | Retrying input after certain errors.
| &bull; [Command-line directories](#413-directories-on-the-command-line) | What happens if you put a directory on the command line.
| &bull; [Input Summary](#414-summary) | Input summary.
| &bull; [Input Exercises](#415-exercises) | Exercises.
| &bull; [Print](#51-the-print-statement) | The `print` statement.
| &bull; [Print Examples](#52-print-statement-examples) | Simple examples of `print` statements.
| &bull; [Output Separators](#53-output-separators) | The output separators and how to change them.
| &bull; [OFMT](#54-controlling-numeric-output-with-print) | Controlling Numeric Output With `print`.
| &bull; [Printf](#55-using-printf-statements-for-fancier-printing) | The `printf` statement.
| &bull; [Basic Printf](#551-introduction-to-the-printf-statement) | Syntax of the `printf` statement.
| &bull; [Control Letters](#552-format-control-letters) | Format-control letters.
| &bull; [Format Modifiers](#553-modifiers-for-printf-formats) | Format-specification modifiers.
| &bull; [Printf Examples](#554-examples-using-printf) | Several examples.
| &bull; [Redirection](#56-redirecting-output-of-print-and-printf) | How to redirect output to multiple files and pipes.
| &bull; [Special FD](#57-special-files-for-standard-preopened-data-streams) | Special files for I/O.
| &bull; [Special Files](#58-special-file-names-in-gawk) | File name interpretation in `gawk`. `gawk` allows access to inherited file descriptors.
| &bull; [Other Inherited Files](#581-accessing-other-open-files-with-gawk) | Accessing other open files with `gawk`.
| &bull; [Special Network](#582-special-files-for-network-communications) | Special files for network communications.
| &bull; [Special Caveats](#583-special-file-name-caveats) | Things to watch out for.
| &bull; [Close Files And Pipes](#59-closing-input-and-output-redirections) | Closing Input and Output Files and Pipes.
| &bull; [Nonfatal](#510-enabling-nonfatal-output) | Enabling Nonfatal Output.
| &bull; [Output Summary](#511-summary) | Output summary.
| &bull; [Output Exercises](#512-exercises) | Exercises.
| &bull; [Values](#61-constants-variables-and-conversions) | Constants, Variables, and Regular Expressions.
| &bull; [Constants](#611-constant-expressions) | String, numeric and regexp constants.
| &bull; [Scalar Constants](#6111-numeric-and-string-constants) | Numeric and string constants.
| &bull; [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers) | What are octal and hex numbers.
| &bull; [Regexp Constants](#6113-regular-expression-constants) | Regular Expression constants.
| &bull; [Using Constant Regexps](#612-using-regular-expression-constants) | When and how to use a regexp constant.
| &bull; [Standard Regexp Constants](#6121-standard-regular-expression-constants) | Regexp constants in standard `awk`.
| &bull; [Strong Regexp Constants](#6122-strongly-typed-regexp-constants) | Strongly typed regexp constants.
| &bull; [Variables](#613-variables) | Variables give names to values for later use.
| &bull; [Using Variables](#6131-using-variables-in-a-program) | Using variables in your programs.
| &bull; [Assignment Options](#6132-assigning-variables-on-the-command-line) | Setting variables on the command line and a summary of command-line syntax.  This is an advanced method of input.
| &bull; [Conversion](#614-conversion-of-strings-and-numbers) | The conversion of strings to numbers and vice versa.
| &bull; [Strings And Numbers](#6141-how-awk-converts-between-strings-and-numbers) | How `awk` Converts Between Strings And Numbers.
| &bull; [Locale influences conversions](#6142-locales-can-influence-conversion) | How the locale may affect conversions.
| &bull; [All Operators](#62-operators-doing-something-with-values) | `gawk`&rsquo;s operators.
| &bull; [Arithmetic Ops](#621-arithmetic-operators) | Arithmetic operations (&lsquo;+&rsquo;, &lsquo;-&rsquo;, etc.)
| &bull; [Concatenation](#622-string-concatenation) | Concatenating strings.
| &bull; [Assignment Ops](#623-assignment-expressions) | Changing the value of a variable or a field.
| &bull; [Increment Ops](#624-increment-and-decrement-operators) | Incrementing the numeric value of a variable.
| &bull; [Truth Values and Conditions](#63-truth-values-and-conditions) | Testing for true and false.
| &bull; [Truth Values](#631-true-and-false-in-awk) | What is &ldquo;true&rdquo; and what is &ldquo;false&rdquo;.
| &bull; [Typing and Comparison](#6322-comparison-operators) | How variables acquire types and how this affects comparison of numbers and strings with &lsquo;<&rsquo;, etc.
| &bull; [Variable Typing](#6321-string-type-versus-numeric-type) | String type versus numeric type.
| &bull; [Comparison Operators](#6322-comparison-operators) | The comparison operators.
| &bull; [POSIX String Comparison](#6323-string-comparison-based-on-locale-collating-order) | String comparison with POSIX rules.
| &bull; [Boolean Ops](#633-boolean-expressions) | Combining comparison expressions using boolean operators &lsquo;||&rsquo; (&ldquo;or&rdquo;), &lsquo;&&&rsquo; (&ldquo;and&rdquo;) and &lsquo;!&rsquo; (&ldquo;not&rdquo;).
| &bull; [Conditional Exp](#634-conditional-expressions) | Conditional expressions select between two subexpressions under control of a third subexpression.
| &bull; [Function Calls](#64-function-calls) | A function call is an expression.
| &bull; [Precedence](#65-operator-precedence-how-operators-nest) | How various operators nest.
| &bull; [Locales](#66-where-you-are-makes-a-difference) | How the locale affects things.
| &bull; [Expressions Summary](#67-summary) | Expressions summary.
| &bull; [Pattern Overview](#71-pattern-elements) | What goes into a pattern.
| &bull; [Regexp Patterns](#711-regular-expressions-as-patterns) | Using regexps as patterns.
| &bull; [Expression Patterns](#712-expressions-as-patterns) | Any expression can be used as a pattern.
| &bull; [Ranges](#713-specifying-record-ranges-with-patterns) | Pairs of patterns specify record ranges.
| &bull; [BEGIN/END](#714-the-begin-and-end-special-patterns) | Specifying initialization and cleanup rules.
| &bull; [Using BEGIN/END](#7141-startup-and-cleanup-actions) | How and why to use BEGIN/END rules.
| &bull; [I/O And BEGIN/END](#7142-inputoutput-from-begin-and-end-rules) | I/O issues in BEGIN/END rules.
| &bull; [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns) | Two special patterns for advanced control.
| &bull; [Empty](#716-the-empty-pattern) | The empty pattern, which matches every record.
| &bull; [Using Shell Variables](#72-using-shell-variables-in-programs) | How to use shell variables with `awk`.
| &bull; [Action Overview](#73-actions) | What goes into an action.
| &bull; [Statements](#74-control-statements-in-actions) | Describes the various control statements in detail.
| &bull; [If Statement](#741-the-if-else-statement) | Conditionally execute some `awk` statements.
| &bull; [While Statement](#742-the-while-statement) | Loop until some condition is satisfied.
| &bull; [Do Statement](#743-the-do-while-statement) | Do specified action while looping until some condition is satisfied.
| &bull; [For Statement](#744-the-for-statement) | Another looping statement, that provides initialization and increment clauses.
| &bull; [Switch Statement](#745-the-switch-statement) | Switch/case evaluation for conditional execution of statements based on a value.
| &bull; [Break Statement](#746-the-break-statement) | Immediately exit the innermost enclosing loop.
| &bull; [Continue Statement](#747-the-continue-statement) | Skip to the end of the innermost enclosing loop.
| &bull; [Next Statement](#748-the-next-statement) | Stop processing the current input record.
| &bull; [Nextfile Statement](#749-the-nextfile-statement) | Stop processing the current file.
| &bull; [Exit Statement](#7410-the-exit-statement) | Stop execution of `awk`.
| &bull; [Built-in Variables](#75-predefined-variables) | Summarizes the predefined variables.
| &bull; [User-modified](#751-built-in-variables-that-control-awk) | Built-in variables that you change to control `awk`.
| &bull; [Auto-set](#752-built-in-variables-that-convey-information) | Built-in variables where `awk` gives you information.
| &bull; [ARGC and ARGV](#753-using-argc-and-argv) | Ways to use `ARGC` and `ARGV`.
| &bull; [Pattern Action Summary](#76-summary) | Patterns and Actions summary.
| &bull; [Array Basics](#81-the-basics-of-arrays) | The basics of arrays.
| &bull; [Array Intro](#811-introduction-to-arrays) | Introduction to Arrays
| &bull; [Reference to Elements](#812-referring-to-an-array-element) | How to examine one element of an array.
| &bull; [Assigning Elements](#813-assigning-array-elements) | How to change an element of an array.
| &bull; [Array Example](#814-basic-array-example) | Basic Example of an Array
| &bull; [Scanning an Array](#815-scanning-all-elements-of-an-array) | A variation of the `for` statement. It loops through the indices of an array&rsquo;s existing elements.
| &bull; [Controlling Scanning](#816-using-predefined-array-scanning-orders-with-gawk) | Controlling the order in which arrays are scanned.
| &bull; [Numeric Array Subscripts](#82-using-numbers-to-subscript-arrays) | How to use numbers as subscripts in `awk`.
| &bull; [Uninitialized Subscripts](#83-using-uninitialized-variables-as-subscripts) | Using Uninitialized variables as subscripts.
| &bull; [Delete](#84-the-delete-statement) | The `delete` statement removes an element from an array.
| &bull; [Multidimensional](#85-multidimensional-arrays) | Emulating multidimensional arrays in `awk`.
| &bull; [Multiscanning](#851-scanning-multidimensional-arrays) | Scanning multidimensional arrays.
| &bull; [Arrays of Arrays](#86-arrays-of-arrays) | True multidimensional arrays.
| &bull; [Arrays Summary](#87-summary) | Summary of arrays.
| &bull; [Built-in](#91-built-in-functions) | Summarizes the built-in functions.
| &bull; [Calling Built-in](#911-calling-built-in-functions) | How to call built-in functions.
| &bull; [Numeric Functions](#912-numeric-functions) | Functions that work with numbers, including `int()`, `sin()` and `rand()`.
| &bull; [String Functions](#913-string-manipulation-functions) | Functions for string manipulation, such as `split()`, `match()` and `sprintf()`.
| &bull; [Gory Details](#9131-more-about-lsquorsquo-and-lsquorsquo-with-sub-gsub-and-gensub) | More than you want to know about &lsquo;\&rsquo; and &lsquo;&&rsquo; with `sub()`, `gsub()`, and `gensub()`.
| &bull; [I/O Functions](#914-inputoutput-functions) | Functions for files and shell commands.
| &bull; [Time Functions](#915-time-functions) | Functions for dealing with timestamps.
| &bull; [Bitwise Functions](#916-bit-manipulation-functions) | Functions for bitwise operations.
| &bull; [Type Functions](#917-getting-type-information) | Functions for type information.
| &bull; [I18N Functions](#918-string-translation-functions) | Functions for string translation.
| &bull; [User-defined](#92-user-defined-functions) | Describes User-defined functions in detail.
| &bull; [Definition Syntax](#921-function-definition-syntax) | How to write definitions and what they mean.
| &bull; [Function Example](#922-function-definition-examples) | An example function definition and what it does.
| &bull; [Function Caveats](#923-calling-user-defined-functions) | Things to watch out for.
| &bull; [Calling A Function](#9231-writing-a-function-call) | Don&rsquo;t use spaces.
| &bull; [Variable Scope](#9232-controlling-variable-scope) | Controlling variable scope.
| &bull; [Pass By Value/Reference](#9233-passing-function-arguments-by-value-or-by-reference) | Passing parameters.
| &bull; [Return Statement](#924-the-return-statement) | Specifying the value a function returns.
| &bull; [Dynamic Typing](#925-functions-and-their-effects-on-variable-typing) | How variable types can change at runtime.
| &bull; [Indirect Calls](#93-indirect-function-calls) | Choosing the function to call at runtime.
| &bull; [Functions Summary](#94-summary) | Summary of functions.
| &bull; [Library Names](#101-naming-library-function-global-variables) | How to best name private global variables in library functions.
| &bull; [General Functions](#102-general-programming) | Functions that are of general use.
| &bull; [Strtonum Function](#1021-converting-strings-to-numbers) | A replacement for the built-in `strtonum()` function.
| &bull; [Assert Function](#1022-assertions) | A function for assertions in `awk` programs.
| &bull; [Round Function](#1023-rounding-numbers) | A function for rounding if `sprintf()` does not do it correctly.
| &bull; [Cliff Random Function](#1024-the-cliff-random-number-generator) | The Cliff Random Number Generator.
| &bull; [Ordinal Functions](#1025-translating-between-characters-and-numbers) | Functions for using characters as numbers and vice versa.
| &bull; [Join Function](#1026-merging-an-array-into-a-string) | A function to join an array into a string.
| &bull; [Getlocaltime Function](#1027-managing-the-time-of-day) | A function to get formatted times.
| &bull; [Readfile Function](#1028-reading-a-whole-file-at-once) | A function to read an entire file at once.
| &bull; [Shell Quoting](#1029-quoting-strings-to-pass-to-the-shell) | A function to quote strings for the shell.
| &bull; [Data File Management](#103-data-file-management) | Functions for managing command-line data files.
| &bull; [Filetrans Function](#1031-noting-data-file-boundaries) | A function for handling data file transitions.
| &bull; [Rewind Function](#1032-rereading-the-current-file) | A function for rereading the current file.
| &bull; [File Checking](#1033-checking-for-readable-data-files) | Checking that data files are readable.
| &bull; [Empty Files](#1034-checking-for-zero-length-files) | Checking for zero-length files.
| &bull; [Ignoring Assigns](#1035-treating-assignments-as-file-names) | Treating assignments as file names.
| &bull; [Getopt Function](#104-processing-command-line-options) | A function for processing command-line arguments.
| &bull; [Passwd Functions](#105-reading-the-user-database) | Functions for getting user information.
| &bull; [Group Functions](#106-reading-the-group-database) | Functions for getting group information.
| &bull; [Walking Arrays](#107-traversing-arrays-of-arrays) | A function to walk arrays of arrays.
| &bull; [Library Functions Summary](#108-summary) | Summary of library functions.
| &bull; [Library Exercises](#109-exercises) | Exercises.
| &bull; [Running Examples](#111-one-shot-throwaway-awk-programs) | How to run these examples.
| &bull; [Clones](#112-running-awk-without-input-files) | Clones of common utilities.
| &bull; [Cut Program](#1121-cutting-out-fields-and-columns) | The `cut` utility.
| &bull; [Egrep Program](#1122-searching-for-regular-expressions-in-files) | The `egrep` utility.
| &bull; [Id Program](#1123-printing-out-user-information) | The `id` utility.
| &bull; [Split Program](#1124-splitting-a-large-file-into-pieces) | The `split` utility.
| &bull; [Tee Program](#1125-duplicating-output-into-multiple-files) | The `tee` utility.
| &bull; [Uniq Program](#1126-printing-nonduplicated-lines-of-text) | The `uniq` utility.
| &bull; [Wc Program](#1127-counting-things) | The `wc` utility.
| &bull; [Miscellaneous Programs](#113-running-long-programs) | Some interesting `awk` programs.
| &bull; [Dupword Program](#1131-finding-duplicated-words-in-a-document) | Finding duplicated words in a document.
| &bull; [Alarm Program](#1132-an-alarm-clock-program) | An alarm clock.
| &bull; [Translate Program](#1133-transliterating-characters) | A program similar to the `tr` utility.
| &bull; [Labels Program](#1134-printing-mailing-labels) | Printing mailing labels.
| &bull; [Word Sorting](#1135-generating-word-usage-counts) | A program to produce a word usage count.
| &bull; [History Sorting](#1136-removing-duplicates-from-unsorted-text) | Eliminating duplicate entries from a history file.
| &bull; [Extract Program](#1137-extracting-programs-from-texinfo-source-files) | Pulling out programs from Texinfo source files.
| &bull; [Simple Sed](#1138-a-simple-stream-editor) | A Simple Stream Editor.
| &bull; [Igawk Program](#1139-an-easy-way-to-use-library-functions) | A wrapper for `awk` that includes files.
| &bull; [Anagram Program](#11310-finding-anagrams-from-a-dictionary) | Finding anagrams from a dictionary.
| &bull; [Signature Program](#11311-and-now-for-something-completely-different) | People do amazing things with too much time on their hands.
| &bull; [Programs Summary](#114-summary) | Summary of programs.
| &bull; [Programs Exercises](#115-exercises) | Exercises.
| &bull; [Nondecimal Data](#Nondecimal-Data) | Allowing nondecimal input data.
| &bull; [Array Sorting](#Array-Sorting) | Facilities for controlling array traversal and sorting arrays.
| &bull; [Controlling Array Traversal](#Controlling-Array-Traversal) | How to use PROCINFO["sorted_in"].
| &bull; [Array Sorting Functions](#Array-Sorting-Functions) | How to use `asort()` and `asorti()`.
| &bull; [Two-way I/O](#Two_002dway-I_002fO) | Two-way communications with another process.
| &bull; [TCP/IP Networking](#TCP_002fIP-Networking) | Using `gawk` for network programming.
| &bull; [Profiling](#Profiling) | Profiling your `awk` programs.
| &bull; [Advanced Features Summary](#Advanced-Features-Summary) | Summary of advanced features.
| &bull; [I18N and L10N](#I18N-and-L10N) | Internationalization and Localization.
| &bull; [Explaining gettext](#Explaining-gettext) | How GNU `gettext` works.
| &bull; [Programmer i18n](#Programmer-i18n) | Features for the programmer.
| &bull; [Translator i18n](#Translator-i18n) | Features for the translator.
| &bull; [String Extraction](#String-Extraction) | Extracting marked strings.
| &bull; [Printf Ordering](#Printf-Ordering) | Rearranging `printf` arguments.
| &bull; [I18N Portability](#I18N-Portability) | `awk`-level portability issues.
| &bull; [I18N Example](#I18N-Example) | A simple i18n example.
| &bull; [Gawk I18N](#Gawk-I18N) | `gawk` is also internationalized.
| &bull; [I18N Summary](#I18N-Summary) | Summary of I18N stuff.
| &bull; [Debugging](#Debugging) | Introduction to `gawk` debugger.
| &bull; [Debugging Concepts](#Debugging-Concepts) | Debugging in General.
| &bull; [Debugging Terms](#Debugging-Terms) | Additional Debugging Concepts.
| &bull; [Awk Debugging](#Awk-Debugging) | Awk Debugging.
| &bull; [Sample Debugging Session](#Sample-Debugging-Session) | Sample debugging session.
| &bull; [Debugger Invocation](#Debugger-Invocation) | How to Start the Debugger.
| &bull; [Finding The Bug](#Finding-The-Bug) | Finding the Bug.
| &bull; [List of Debugger Commands](#List-of-Debugger-Commands) | Main debugger commands.
| &bull; [Breakpoint Control](#Breakpoint-Control) | Control of Breakpoints.
| &bull; [Debugger Execution Control](#Debugger-Execution-Control) | Control of Execution.
| &bull; [Viewing And Changing Data](#Viewing-And-Changing-Data) | Viewing and Changing Data.
| &bull; [Execution Stack](#Execution-Stack) | Dealing with the Stack.
| &bull; [Debugger Info](#Debugger-Info) | Obtaining Information about the Program and the Debugger State.
| &bull; [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands) | Miscellaneous Commands.
| &bull; [Readline Support](#Readline-Support) | Readline support.
| &bull; [Limitations](#Limitations) | Limitations and future plans.
| &bull; [Debugging Summary](#Debugging-Summary) | Debugging summary.
| &bull; [Computer Arithmetic](#Computer-Arithmetic) | A quick intro to computer math.
| &bull; [Math Definitions](#Math-Definitions) | Defining terms used.
| &bull; [MPFR features](#MPFR-features) | The MPFR features in `gawk`.
| &bull; [FP Math Caution](#FP-Math-Caution) | Things to know.
| &bull; [Inexactness of computations](#Inexactness-of-computations) | Floating point math is not exact.
| &bull; [Inexact representation](#Inexact-representation) | Numbers are not exactly represented.
| &bull; [Comparing FP Values](#Comparing-FP-Values) | How to compare floating point values.
| &bull; [Errors accumulate](#Errors-accumulate) | Errors get bigger as they go.
| &bull; [Getting Accuracy](#Getting-Accuracy) | Getting more accuracy takes some work.
| &bull; [Try To Round](#Try-To-Round) | Add digits and round.
| &bull; [Setting precision](#Setting-precision) | How to set the precision.
| &bull; [Setting the rounding mode](#Setting-the-rounding-mode) | How to set the rounding mode.
| &bull; [Arbitrary Precision Integers](#Arbitrary-Precision-Integers) | Arbitrary Precision Integer Arithmetic with `gawk`.
| &bull; [Checking for MPFR](#Checking-for-MPFR) | How to check if MPFR is available.
| &bull; [POSIX Floating Point Problems](#POSIX-Floating-Point-Problems) | Standards Versus Existing Practice.
| &bull; [Floating point summary](#Floating-point-summary) | Summary of floating point discussion.
| &bull; [Extension Intro](#Extension-Intro) | What is an extension.
| &bull; [Plugin License](#Plugin-License) | A note about licensing.
| &bull; [Extension Mechanism Outline](#Extension-Mechanism-Outline) | An outline of how it works.
| &bull; [Extension API Description](#Extension-API-Description) | A full description of the API.
| &bull; [Extension API Functions Introduction](#Extension-API-Functions-Introduction) | Introduction to the API functions.
| &bull; [General Data Types](#General-Data-Types) | The data types.
| &bull; [Memory Allocation Functions](#Memory-Allocation-Functions) | Functions for allocating memory.
| &bull; [Constructor Functions](#Constructor-Functions) | Functions for creating values.
| &bull; [Registration Functions](#Registration-Functions) | Functions to register things with `gawk`.
| &bull; [Extension Functions](#Extension-Functions) | Registering extension functions.
| &bull; [Exit Callback Functions](#Exit-Callback-Functions) | Registering an exit callback.
| &bull; [Extension Version String](#Extension-Version-String) | Registering a version string.
| &bull; [Input Parsers](#Input-Parsers) | Registering an input parser.
| &bull; [Output Wrappers](#Output-Wrappers) | Registering an output wrapper.
| &bull; [Two-way processors](#Two_002dway-processors) | Registering a two-way processor.
| &bull; [Printing Messages](#Printing-Messages) | Functions for printing messages.
| &bull; [Updating `ERRNO`](#Updating-ERRNO) | Functions for updating `ERRNO`.
| &bull; [Requesting Values](#Requesting-Values) | How to get a value.
| &bull; [Accessing Parameters](#Accessing-Parameters) | Functions for accessing parameters.
| &bull; [Symbol Table Access](#Symbol-Table-Access) | Functions for accessing global variables.
| &bull; [Symbol table by name](#Symbol-table-by-name) | Accessing variables by name.
| &bull; [Symbol table by cookie](#Symbol-table-by-cookie) | Accessing variables by &ldquo;cookie&rdquo;.
| &bull; [Cached values](#Cached-values) | Creating and using cached values.
| &bull; [Array Manipulation](#Array-Manipulation) | Functions for working with arrays.
| &bull; [Array Data Types](#Array-Data-Types) | Data types for working with arrays.
| &bull; [Array Functions](#Array-Functions) | Functions for working with arrays.
| &bull; [Flattening Arrays](#Flattening-Arrays) | How to flatten arrays.
| &bull; [Creating Arrays](#Creating-Arrays) | How to create and populate arrays.
| &bull; [Redirection API](#Redirection-API) | How to access and manipulate redirections.
| &bull; [Extension API Variables](#Extension-API-Variables) | Variables provided by the API.
| &bull; [Extension Versioning](#Extension-Versioning) | API Version information.
| &bull; [Extension GMP/MPFR Versioning](#Extension-GMP_002fMPFR-Versioning) | Version information about GMP and MPFR.
| &bull; [Extension API Informational Variables](#Extension-API-Informational-Variables) | Variables providing information about `gawk`&rsquo;s invocation.
| &bull; [Extension API Boilerplate](#Extension-API-Boilerplate) | Boilerplate code for using the API.
| &bull; [Changes from API V1](#Changes-from-API-V1) | Changes from V1 of the API.
| &bull; [Finding Extensions](#Finding-Extensions) | How `gawk` finds compiled extensions.
| &bull; [Extension Example](#Extension-Example) | Example C code for an extension.
| &bull; [Internal File Description](#Internal-File-Description) | What the new functions will do.
| &bull; [Internal File Ops](#Internal-File-Ops) | The code for internal file operations.
| &bull; [Using Internal File Ops](#Using-Internal-File-Ops) | How to use an external extension.
| &bull; [Extension Samples](#Extension-Samples) | The sample extensions that ship with `gawk`.
| &bull; [Extension Sample File Functions](#Extension-Sample-File-Functions) | The file functions sample.
| &bull; [Extension Sample Fnmatch](#Extension-Sample-Fnmatch) | An interface to `fnmatch()`.
| &bull; [Extension Sample Fork](#Extension-Sample-Fork) | An interface to `fork()` and other process functions.
| &bull; [Extension Sample Inplace](#Extension-Sample-Inplace) | Enabling in-place file editing.
| &bull; [Extension Sample Ord](#Extension-Sample-Ord) | Character to value to character conversions.
| &bull; [Extension Sample Readdir](#Extension-Sample-Readdir) | An interface to `readdir()`.
| &bull; [Extension Sample Revout](#Extension-Sample-Revout) | Reversing output sample output wrapper.
| &bull; [Extension Sample Rev2way](#Extension-Sample-Rev2way) | Reversing data sample two-way processor.
| &bull; [Extension Sample Read write array](#Extension-Sample-Read-write-array) | Serializing an array to a file.
| &bull; [Extension Sample Readfile](#Extension-Sample-Readfile) | Reading an entire file into a string.
| &bull; [Extension Sample Time](#Extension-Sample-Time) | An interface to `gettimeofday()` and `sleep()`.
| &bull; [Extension Sample API Tests](#Extension-Sample-API-Tests) | Tests for the API.
| &bull; [gawkextlib](#gawkextlib) | The `gawkextlib` project.
| &bull; [Extension summary](#Extension-summary) | Extension summary.
| &bull; [Extension Exercises](#Extension-Exercises) | Exercises.
| &bull; [V7/SVR3.1](#V7_002fSVR3_002e1) | The major changes between V7 and System V Release 3.1.
| &bull; [SVR4](#SVR4) | Minor changes between System V Releases 3.1 and 4.
| &bull; [POSIX](#POSIX) | New features from the POSIX standard.
| &bull; [BTL](#BTL) | New features from Brian Kernighan&rsquo;s version of `awk`.
| &bull; [POSIX/GNU](#POSIX_002fGNU) | The extensions in `gawk` not in POSIX `awk`.
| &bull; [Feature History](#Feature-History) | The history of the features in `gawk`.
| &bull; [Common Extensions](#Common-Extensions) | Common Extensions Summary.
| &bull; [Ranges and Locales](#Ranges-and-Locales) | How locales used to affect regexp ranges.
| &bull; [Contributors](#Contributors) | The major contributors to `gawk`.
| &bull; [History summary](#History-summary) | History summary.
| &bull; [Gawk Distribution](#Gawk-Distribution) | What is in the `gawk` distribution.
| &bull; [Getting](#Getting) | How to get the distribution.
| &bull; [Extracting](#Extracting) | How to extract the distribution.
| &bull; [Distribution contents](#Distribution-contents) | What is in the distribution.
| &bull; [Unix Installation](#Unix-Installation) | Installing `gawk` under various versions of Unix.
| &bull; [Quick Installation](#Quick-Installation) | Compiling `gawk` under Unix.
| &bull; [Shell Startup Files](#Shell-Startup-Files) | Shell convenience functions.
| &bull; [Additional Configuration Options](#Additional-Configuration-Options) | Other compile-time options.
| &bull; [Configuration Philosophy](#Configuration-Philosophy) | How it&rsquo;s all supposed to work.
| &bull; [Non-Unix Installation](#Non_002dUnix-Installation) | Installation on Other Operating Systems.
| &bull; [PC Installation](#PC-Installation) | Installing and Compiling `gawk` on Microsoft Windows.
| &bull; [PC Binary Installation](#PC-Binary-Installation) | Installing a prepared distribution.
| &bull; [PC Compiling](#PC-Compiling) | Compiling `gawk` for Windows32.
| &bull; [PC Using](#PC-Using) | Running `gawk` on Windows32.
| &bull; [Cygwin](#Cygwin) | Building and running `gawk` for Cygwin.
| &bull; [MSYS](#MSYS) | Using `gawk` In The MSYS Environment.
| &bull; [VMS Installation](#VMS-Installation) | Installing `gawk` on VMS.
| &bull; [VMS Compilation](#VMS-Compilation) | How to compile `gawk` under VMS.
| &bull; [VMS Dynamic Extensions](#VMS-Dynamic-Extensions) | Compiling `gawk` dynamic extensions on VMS.
| &bull; [VMS Installation Details](#VMS-Installation-Details) | How to install `gawk` under VMS.
| &bull; [VMS Running](#VMS-Running) | How to run `gawk` under VMS.
| &bull; [VMS GNV](#VMS-GNV) | The VMS GNV Project.
| &bull; [VMS Old Gawk](#VMS-Old-Gawk) | An old version comes with some VMS systems.
| &bull; [Bugs](#Bugs) | Reporting Problems and Bugs.
| &bull; [Bug address](#Bug-address) | Where to send reports to.
| &bull; [Usenet](#Usenet) | Where not to send reports to.
| &bull; [Maintainers](#Maintainers) | Maintainers of non-*nix ports.
| &bull; [Other Versions](#Other-Versions) | Other freely available `awk` implementations.
| &bull; [Installation summary](#Installation-summary) | Summary of installation.
| &bull; [Compatibility Mode](#Compatibility-Mode) | How to disable certain `gawk` extensions.
| &bull; [Additions](#Additions) | Making Additions To `gawk`.
| &bull; [Accessing The Source](#Accessing-The-Source) | Accessing the Git repository.
| &bull; [Adding Code](#Adding-Code) | Adding code to the main body of `gawk`.
| &bull; [New Ports](#New-Ports) | Porting `gawk` to a new operating system.
| &bull; [Derived Files](#Derived-Files) | Why derived files are kept in the Git repository.
| &bull; [Future Extensions](#Future-Extensions) | New features that may be implemented one day.
| &bull; [Implementation Limitations](#Implementation-Limitations) | Some limitations of the implementation.
| &bull; [Extension Design](#Extension-Design) | Design notes about the extension API.
| &bull; [Old Extension Problems](#Old-Extension-Problems) | Problems with the old mechanism.
| &bull; [Extension New Mechanism Goals](#Extension-New-Mechanism-Goals) | Goals for the new mechanism.
| &bull; [Extension Other Design Decisions](#Extension-Other-Design-Decisions) | Some other design decisions.
| &bull; [Extension Future Growth](#Extension-Future-Growth) | Some room for future growth.
| &bull; [Old Extension Mechanism](#Old-Extension-Mechanism) | Some compatibility for old extensions.
| &bull; [Notes summary](#Notes-summary) | Summary of implementation notes.
| &bull; [Basic High Level](#Basic-High-Level) | The high level view.
| &bull; [Basic Data Typing](#Basic-Data-Typing) | A very quick intro to data types.

## Short Table of Contents

*    <a href="#foreword-to-the-third-edition">Foreword to the Third Edition</a>
*    <a href="#foreword-to-the-fourth-edition">Foreword to the Fourth Edition</a>
*    <a href="#preface">Preface</a>
* [Part I:](#part-i)
    * [1 Getting Started with `awk`](#1-getting-started-with-awk)
    * [2 Running `awk` and `gawk`](#2-running-awk-and-gawk)
    * [3 Regular Expressions](#3-regular-expressions)
    * [4 Reading Input Files](#4-reading-input-files)
    * [5 Printing Output](#5-printing-output)
    * [6 Expressions](#6-expressions)
    * [7 Patterns, Actions, and Variables](#7-patterns-actions-and-variables)
    * [8 Arrays in `awk`](#8-arrays-in-awk)
    * [9 Functions](#9-functions)

* [Part II:](#part-ii)
    * [10 A Library of `awk` Functions](#10-a-library-of-awk-functions)
    * [11 Practical `awk` Programs](#11-practical-awk-programs)

## Table of Contents

*    <a href="#foreword-to-the-third-edition">Foreword to the Third Edition</a>
*    <a href="#foreword-to-the-fourth-edition">Foreword to the Fourth Edition</a>
*    <a href="#preface">Preface</a>
     *    [History of `awk` and `gawk`](#history-of-awk-and-gawk)
     *    [A Rose by Any Other Name](#a-rose-by-any-other-name)
     *    [Using This Book](#using-this-book)
     *    [Typographical Conventions](#typographical-conventions)
         *     [Dark Corners](#dark-corners)
     *    [The GNU Project and This Book](#the-gnu-project-and-this-book)
     *    [How to Contribute](#how-to-contribute)
     *    [Acknowledgments](#acknowledgments)
* [Part I:](#part-i)
    * [1 Getting Started with `awk`](#1-getting-started-with-awk)
        * [1.1 How to Run `awk` Programs](#11-how-to-run-awk-programs)
            * [1.1.1 One-Shot Throwaway `awk` Programs](#111-one-shot-throwaway-awk-programs)
            * [1.1.2 Running `awk` Without Input Files](#112-running-awk-without-input-files)
            * [1.1.3 Running Long Programs](#113-running-long-programs)
            * [1.1.4 Executable `awk` Programs](#114-executable-awk-programs)
            * [1.1.5 Comments in `awk` Programs](#115-comments-in-awk-programs)
            * [1.1.6 Shell Quoting Issues](#116-shell-quoting-issues)
            * [1.1.6.1 Quoting in MS-Windows Batch Files](#1161-quoting-in-ms-windows-batch-files)
        * [1.2 Data files for the Examples](#12-data-files-for-the-examples)
        * [1.3 Some Simple Examples](#13-some-simple-examples)
        * [1.4 An Example with Two Rules](#14-an-example-with-two-rules)
        * [1.5 A More Complex Example](#15-a-more-complex-example)
        * [1.6 `awk` Statements Versus Lines](#16-awk-statements-versus-lines)
        * [1.7 Other Features of `awk`](#17-other-features-of-awk)
        * [1.8 When to Use `awk`](#18-when-to-use-awk)
        * [1.9 Summary](#19-summary)
    * [2 Running `awk` and `gawk`](#2-running-awk-and-gawk)
        * [2.1 Invoking `awk`](#21-invoking-awk)
        * [2.2 Command-Line Options](#22-command-line-options)
        * [2.3 Other Command-Line Arguments](#23-other-command-line-arguments)
        * [2.4 Naming Standard Input](#24-naming-standard-input)
        * [2.5 The Environment Variables `gawk` Uses](#25-the-environment-variables-gawk-uses)
            * [2.5.1 The `AWKPATH` Environment Variable](#251-the-awkpath-environment-variable)
            * [2.5.2 The `AWKLIBPATH` Environment Variable](#252-the-awklibpath-environment-variable)
            * [2.5.3 Other Environment Variables](#253-other-environment-variables)
        * [2.6 `gawk`&rsquo;s Exit Status](#26-gawkrsquos-exit-status)
        * [2.7 Including Other Files into Your Program](#27-including-other-files-into-your-program)
        * [2.8 Loading Dynamic Extensions into Your Program](#28-loading-dynamic-extensions-into-your-program)
        * [2.9 Obsolete Options and/or Features](#29-obsolete-options-andor-features)
        * [2.10 Undocumented Options and Features](#210-undocumented-options-and-features)
        * [2.11 Summary](#211-summary)
    * [3 Regular Expressions](#3-regular-expressions)
        * [3.1 How to Use Regular Expressions](#31-how-to-use-regular-expressions)
        * [3.2 Escape Sequences](#32-escape-sequences)
        * [3.3 Regular Expression Operators](#33-regular-expression-operators)
        * [3.4 Using Bracket Expressions](#34-using-bracket-expressions)
        * [3.5 How Much Text Matches?](#35-how-much-text-matches)
        * [3.6 Using Dynamic Regexps](#36-using-dynamic-regexps)
        * [3.7 `gawk`-Specific Regexp Operators](#37-gawk-specific-regexp-operators)
        * [3.8 Case Sensitivity in Matching](#38-case-sensitivity-in-matching)
        * [3.9 Summary](#39-summary)
    * [4 Reading Input Files](#4-reading-input-files)
        * [4.1 How Input Is Split into Records](#41-how-input-is-split-into-records)
            * [4.1.1 Record Splitting with Standard `awk`](#411-record-splitting-with-standard-awk)
            * [4.1.2 Record Splitting with `gawk`](#412-record-splitting-with-gawk)
        * [4.2 Examining Fields](#42-examining-fields)
        * [4.3 Nonconstant Field Numbers](#43-nonconstant-field-numbers)
        * [4.4 Changing the Contents of a Field](#44-changing-the-contents-of-a-field)
        * [4.5 Specifying How Fields Are Separated](#45-specifying-how-fields-are-separated)
            * [4.5.1 Whitespace Normally Separates Fields](#451-whitespace-normally-separates-fields)
            * [4.5.2 Using Regular Expressions to Separate Fields](#452-using-regular-expressions-to-separate-fields)
            * [4.5.3 Making Each Character a Separate Field](#453-making-each-character-a-separate-field)
            * [4.5.4 Setting `FS` from the Command Line](#454-setting-fs-from-the-command-line)
            * [4.5.5 Making the Full Line Be a Single Field](#455-making-the-full-line-be-a-single-field)
            * [4.5.6 Field-Splitting Summary](#456-field-splitting-summary)
        * [4.6 Reading Fixed-Width Data](#46-reading-fixed-width-data)
            * [4.6.1 Processing Fixed-Width Data](#461-processing-fixed-width-data)
            * [4.6.2 Skipping Intervening Fields](#462-skipping-intervening-fields)
            * [4.6.3 Capturing Optional Trailing Data](#463-capturing-optional-trailing-data)
            * [4.6.4 Field Values With Fixed-Width Data](#464-field-values-with-fixed-width-data)
        * [4.7 Defining Fields by Content](#47-defining-fields-by-content)
        * [4.8 Checking How `gawk` Is Splitting Records](#48-checking-how-gawk-is-splitting-records)
        * [4.9 Multiple-Line Records](#49-multiple-line-records)
        * [4.10 Explicit Input with `getline`](#410-explicit-input-with-getline)
            * [4.10.1 Using `getline` with No Arguments](#4101-using-getline-with-no-arguments)
            * [4.10.2 Using `getline` into a Variable](#4102-using-getline-into-a-variable)
            * [4.10.3 Using `getline` from a File](#4103-using-getline-from-a-file)
            * [4.10.4 Using `getline` into a Variable from a File](#4104-using-getline-into-a-variable-from-a-file)
            * [4.10.5 Using `getline` from a Pipe](#4105-using-getline-from-a-pipe)
            * [4.10.6 Using `getline` into a Variable from a Pipe](#4106-using-getline-into-a-variable-from-a-pipe)
            * [4.10.7 Using `getline` from a Coprocess](#4107-using-getline-from-a-coprocess)
            * [4.10.8 Using `getline` into a Variable from a Coprocess](#4108-using-getline-into-a-variable-from-a-coprocess)
            * [4.10.9 Points to Remember About `getline`](#4109-points-to-remember-about-getline)
            * [4.10.10 Summary of `getline` Variants](#41010-summary-of-getline-variants)
        * [4.11 Reading Input with a Timeout](#411-reading-input-with-a-timeout)
        * [4.12 Retrying Reads After Certain Input Errors](#412-retrying-reads-after-certain-input-errors)
        * [4.13 Directories on the Command Line](#413-directories-on-the-command-line)
        * [4.14 Summary](#414-summary)
        * [4.15 Exercises](#415-exercises)
    * [5 Printing Output](#5-printing-output)
        * [5.1 The `print` Statement](#51-the-print-statement)
        * [5.2 `print` Statement Examples](#52-print-statement-examples)
        * [5.3 Output Separators](#53-output-separators)
        * [5.4 Controlling Numeric Output with `print`](#54-controlling-numeric-output-with-print)
        * [5.5 Using `printf` Statements for Fancier Printing](#55-using-printf-statements-for-fancier-printing)
            * [5.5.1 Introduction to the `printf` Statement](#551-introduction-to-the-printf-statement)
            * [5.5.2 Format-Control Letters](#552-format-control-letters)
            * [5.5.3 Modifiers for `printf` Formats](#553-modifiers-for-printf-formats)
            * [5.5.4 Examples Using `printf`](#554-examples-using-printf)
        * [5.6 Redirecting Output of `print` and `printf`](#56-redirecting-output-of-print-and-printf)
        * [5.7 Special Files for Standard Preopened Data Streams](#57-special-files-for-standard-preopened-data-streams)
        * [5.8 Special File names in `gawk`](#58-special-file-names-in-gawk)
            * [5.8.1 Accessing Other Open Files with `gawk`](#581-accessing-other-open-files-with-gawk)
            * [5.8.2 Special Files for Network Communications](#582-special-files-for-network-communications)
            * [5.8.3 Special File name Caveats](#583-special-file-name-caveats)
        * [5.9 Closing Input and Output Redirections](#59-closing-input-and-output-redirections)
        * [5.10 Enabling Nonfatal Output](#510-enabling-nonfatal-output)
        * [5.11 Summary](#511-summary)
        * [5.12 Exercises](#512-exercises)
    * [6 Expressions](#6-expressions)
        * [6.1 Constants, Variables, and Conversions](#61-constants-variables-and-conversions)
            * [6.1.1 Constant Expressions](#611-constant-expressions)
            * [6.1.1.1 Numeric and String Constants](#6111-numeric-and-string-constants)
            * [6.1.1.2 Octal and Hexadecimal Numbers](#6112-octal-and-hexadecimal-numbers)
            * [6.1.1.3 Regular Expression Constants](#6113-regular-expression-constants)
            * [6.1.2 Using Regular Expression Constants](#612-using-regular-expression-constants)
            * [6.1.2.1 Standard Regular Expression Constants](#6121-standard-regular-expression-constants)
            * [6.1.2.2 Strongly Typed Regexp Constants](#6122-strongly-typed-regexp-constants)
            * [6.1.3 Variables](#613-variables)
            * [6.1.3.1 Using Variables in a Program](#6131-using-variables-in-a-program)
            * [6.1.3.2 Assigning Variables on the Command Line](#6132-assigning-variables-on-the-command-line)
            * [6.1.4 Conversion of Strings and Numbers](#614-conversion-of-strings-and-numbers)
            * [6.1.4.1 How `awk` Converts Between Strings and Numbers](#6141-how-awk-converts-between-strings-and-numbers)
            * [6.1.4.2 Locales Can Influence Conversion](#6142-locales-can-influence-conversion)
        * [6.2 Operators: Doing Something with Values](#62-operators-doing-something-with-values)
            * [6.2.1 Arithmetic Operators](#621-arithmetic-operators)
            * [6.2.2 String Concatenation](#622-string-concatenation)
            * [6.2.3 Assignment Expressions](#623-assignment-expressions)
            * [6.2.4 Increment and Decrement Operators](#624-increment-and-decrement-operators)
        * [6.3 Truth Values and Conditions](#63-truth-values-and-conditions)
            * [6.3.1 True and False in `awk`](#631-true-and-false-in-awk)
            * [6.3.2 Variable Typing and Comparison Expressions](#632-variable-typing-and-comparison-expressions)
            * [6.3.2.1 String Type versus Numeric Type](#6321-string-type-versus-numeric-type)
            * [6.3.2.2 Comparison Operators](#6322-comparison-operators)
            * [6.3.2.3 String Comparison Based on Locale Collating Order](#6323-string-comparison-based-on-locale-collating-order)
            * [6.3.3 Boolean Expressions](#633-boolean-expressions)
            * [6.3.4 Conditional Expressions](#634-conditional-expressions)
        * [6.4 Function Calls](#64-function-calls)
        * [6.5 Operator Precedence (How Operators Nest)](#65-operator-precedence-how-operators-nest)
        * [6.6 Where You Are Makes a Difference](#66-where-you-are-makes-a-difference)
        * [6.7 Summary](#67-summary)
    * [7 Patterns, Actions, and Variables](#7-patterns-actions-and-variables)
        * [7.1 Pattern Elements](#71-pattern-elements)
            * [7.1.1 Regular Expressions as Patterns](#711-regular-expressions-as-patterns)
            * [7.1.2 Expressions as Patterns](#712-expressions-as-patterns)
            * [7.1.3 Specifying Record Ranges with Patterns](#713-specifying-record-ranges-with-patterns)
            * [7.1.4 The `BEGIN` and `END` Special Patterns](#714-the-begin-and-end-special-patterns)
            * [7.1.4.1 Startup and Cleanup Actions](#7141-startup-and-cleanup-actions)
            * [7.1.4.2 Input/Output from `BEGIN` and `END` Rules](#7142-inputoutput-from-begin-and-end-rules)
            * [7.1.5 The `BEGINFILE` and `ENDFILE` Special Patterns](#715-the-beginfile-and-endfile-special-patterns)
            * [7.1.6 The Empty Pattern](#716-the-empty-pattern)
        * [7.2 Using Shell Variables in Programs](#72-using-shell-variables-in-programs)
        * [7.3 Actions](#73-actions)
        * [7.4 Control Statements in Actions](#74-control-statements-in-actions)
            * [7.4.1 The `if`-`else` Statement](#741-the-if-else-statement)
            * [7.4.2 The `while` Statement](#742-the-while-statement)
            * [7.4.3 The `do`-`while` Statement](#743-the-do-while-statement)
            * [7.4.4 The `for` Statement](#744-the-for-statement)
            * [7.4.5 The `switch` Statement](#745-the-switch-statement)
            * [7.4.6 The `break` Statement](#746-the-break-statement)
            * [7.4.7 The `continue` Statement](#747-the-continue-statement)
            * [7.4.8 The `next` Statement](#748-the-next-statement)
            * [7.4.9 The `nextfile` Statement](#749-the-nextfile-statement)
            * [7.4.10 The `exit` Statement](#7410-the-exit-statement)
        * [7.5 Predefined Variables](#75-predefined-variables)
            * [7.5.1 Built-in Variables That Control `awk`](#751-built-in-variables-that-control-awk)
            * [7.5.2 Built-in Variables That Convey Information](#752-built-in-variables-that-convey-information)
            * [7.5.3 Using `ARGC` and `ARGV`](#753-using-argc-and-argv)
        * [7.6 Summary](#76-summary)
    * [8 Arrays in `awk`](#8-arrays-in-awk)
        * [8.1 The Basics of Arrays](#81-the-basics-of-arrays)
            * [8.1.1 Introduction to Arrays](#811-introduction-to-arrays)
            * [8.1.2 Referring to an Array Element](#812-referring-to-an-array-element)
            * [8.1.3 Assigning Array Elements](#813-assigning-array-elements)
            * [8.1.4 Basic Array Example](#814-basic-array-example)
            * [8.1.5 Scanning All Elements of an Array](#815-scanning-all-elements-of-an-array)
            * [8.1.6 Using Predefined Array Scanning Orders with `gawk`](#816-using-predefined-array-scanning-orders-with-gawk)
        * [8.2 Using Numbers to Subscript Arrays](#82-using-numbers-to-subscript-arrays)
        * [8.3 Using Uninitialized Variables as Subscripts](#83-using-uninitialized-variables-as-subscripts)
        * [8.4 The `delete` Statement](#84-the-delete-statement)
        * [8.5 Multidimensional Arrays](#85-multidimensional-arrays)
            * [8.5.1 Scanning Multidimensional Arrays](#851-scanning-multidimensional-arrays)
        * [8.6 Arrays of Arrays](#86-arrays-of-arrays)
        * [8.7 Summary](#87-summary)
    * [9 Functions](#9-functions)
        * [9.1 Built-in Functions](#91-built-in-functions)
            * [9.1.1 Calling Built-in Functions](#911-calling-built-in-functions)
            * [9.1.2 Numeric Functions](#912-numeric-functions)
            * [9.1.3 String-Manipulation Functions](#913-string-manipulation-functions)
            * [9.1.3.1 More about `\` and `&` with `sub()`, `gsub()`, and `gensub()`](#9131-more-about-lsquorsquo-and-lsquorsquo-with-sub-gsub-and-gensub)
            * [9.1.4 Input/Output Functions](#914-inputoutput-functions)
            * [9.1.5 Time Functions](#915-time-functions)
            * [9.1.6 Bit-Manipulation Functions](#916-bit-manipulation-functions)
            * [9.1.7 Getting Type Information](#917-getting-type-information)
            * [9.1.8 String-Translation Functions](#918-string-translation-functions)
        * [9.2 User-Defined Functions](#92-user-defined-functions)
            * [9.2.1 Function Definition Syntax](#921-function-definition-syntax)
            * [9.2.2 Function Definition Examples](#922-function-definition-examples)
            * [9.2.3 Calling User-Defined Functions](#923-calling-user-defined-functions)
            * [9.2.3.1 Writing a Function Call](#9231-writing-a-function-call)
            * [9.2.3.2 Controlling Variable Scope](#9232-controlling-variable-scope)
            * [9.2.3.3 Passing Function Arguments by Value Or by Reference](#9233-passing-function-arguments-by-value-or-by-reference)
            * [9.2.4 The `return` Statement](#924-the-return-statement)
            * [9.2.5 Functions and Their Effects on Variable Typing](#925-functions-and-their-effects-on-variable-typing)
        * [9.3 Indirect Function Calls](#93-indirect-function-calls)
        * [9.4 Summary](#94-summary)
* [Part II:](#part-ii)
    * [10 A Library of `awk` Functions](#10-a-library-of-awk-functions)
        * [10.1 Naming Library Function Global Variables](#101-naming-library-function-global-variables)
        * [10.2 General Programming](#102-general-programming)
            * [10.2.1 Converting Strings to Numbers](#1021-converting-strings-to-numbers)
            * [10.2.2 Assertions](#1022-assertions)
            * [10.2.3 Rounding Numbers](#1023-rounding-numbers)
            * [10.2.4 The Cliff Random Number Generator](#1024-the-cliff-random-number-generator)
            * [10.2.5 Translating Between Characters and Numbers](#1025-translating-between-characters-and-numbers)
            * [10.2.6 Merging an Array into a String](#1026-merging-an-array-into-a-string)
            * [10.2.7 Managing the Time of Day](#1027-managing-the-time-of-day)
            * [10.2.8 Reading a Whole File at Once](#1028-reading-a-whole-file-at-once)
            * [10.2.9 Quoting Strings to Pass to the Shell](#1029-quoting-strings-to-pass-to-the-shell)
        * [10.3 Data file Management](#103-data-file-management)
            * [10.3.1 Noting Data file Boundaries](#1031-noting-data-file-boundaries)
            * [10.3.2 Rereading the Current File](#1032-rereading-the-current-file)
            * [10.3.3 Checking for Readable Data files](#1033-checking-for-readable-data-files)
            * [10.3.4 Checking for Zero-Length Files](#1034-checking-for-zero-length-files)
            * [10.3.5 Treating Assignments as File names](#1035-treating-assignments-as-file-names)
        * [10.4 Processing Command-Line Options](#104-processing-command-line-options)
        * [10.5 Reading the User Database](#105-reading-the-user-database)
        * [10.6 Reading the Group Database](#106-reading-the-group-database)
        * [10.7 Traversing Arrays of Arrays](#107-traversing-arrays-of-arrays)
        * [10.8 Summary](#108-summary)
        * [10.9 Exercises](#109-exercises)
    * [11 Practical `awk` Programs](#11-practical-awk-programs)
        * [11.1 Running the Example Programs](#111-running-the-example-programs)
        * [11.2 Reinventing Wheels for Fun and Profit](#112-reinventing-wheels-for-fun-and-profit)
            * [11.2.1 Cutting Out Fields and Columns](#1121-cutting-out-fields-and-columns)
            * [11.2.2 Searching for Regular Expressions in Files](#1122-searching-for-regular-expressions-in-files)
            * [11.2.3 Printing Out User Information](#1123-printing-out-user-information)
            * [11.2.4 Splitting a Large File into Pieces](#1124-splitting-a-large-file-into-pieces)
            * [11.2.5 Duplicating Output into Multiple Files](#1125-duplicating-output-into-multiple-files)
            * [11.2.6 Printing Nonduplicated Lines of Text](#1126-printing-nonduplicated-lines-of-text)
            * [11.2.7 Counting Things](#1127-counting-things)
        * [11.3 A Grab Bag of `awk` Programs](#113-a-grab-bag-of-awk-programs)
            * [11.3.1 Finding Duplicated Words in a Document](#1131-finding-duplicated-words-in-a-document)
            * [11.3.2 An Alarm Clock Program](#1132-an-alarm-clock-program)
            * [11.3.3 Transliterating Characters](#1133-transliterating-characters)
            * [11.3.4 Printing Mailing Labels](#1134-printing-mailing-labels)
            * [11.3.5 Generating Word-Usage Counts](#1135-generating-word-usage-counts)
            * [11.3.6 Removing Duplicates from Unsorted Text](#1136-removing-duplicates-from-unsorted-text)
            * [11.3.7 Extracting Programs from Texinfo Source Files](#1137-extracting-programs-from-texinfo-source-files)
            * [11.3.8 A Simple Stream Editor](#1138-a-simple-stream-editor)
            * [11.3.9 An Easy Way to Use Library Functions](#1139-an-easy-way-to-use-library-functions)
            * [11.3.10 Finding Anagrams from a Dictionary](#11310-finding-anagrams-from-a-dictionary)
            * [11.3.11 And Now for Something Completely Different](#11311-and-now-for-something-completely-different)
        * [11.4 Summary](#114-summary)
        * [11.5 Exercises](#115-exercises)

---

Next: [Foreword4](#foreword-to-the-fourth-edition), Previous: [Top](#Top), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## Foreword to the Third Edition

Arnold Robbins and I are good friends. We were introduced
in 1990
by circumstances&mdash;and our favorite programming language, AWK.
The circumstances started a couple of years
earlier. I was working at a new job and noticed an unplugged
Unix computer sitting in the corner.  No one knew how to use it,
and neither did I.  However,
a couple of days later, it was running, and
I was `root` and the one-and-only user.
That day, I began the transition from statistician to Unix programmer.

On one of many trips to the library or bookstore in search of
books on Unix, I found the gray AWK book, a.k.a.
Alfred V. Aho, Brian W. Kernighan, and
Peter J. Weinberger&rsquo;s The AWK Programming Language (Addison-Wesley,
1988).  `awk`&rsquo;s simple programming paradigm&mdash;find a pattern in the
input and then perform an action&mdash;often reduced complex or tedious
data manipulations to a few lines of code.  I was excited to try my
hand at programming in AWK.

Alas,  the `awk` on my computer was a limited version of the
language described in the gray book.  I discovered that my computer
had &ldquo;old `awk`&rdquo; and the book described
&ldquo;new `awk`.&rdquo;
I learned that this was typical; the old version refused to step
aside or relinquish its name.  If a system had a new `awk`, it was
invariably called `nawk`, and few systems had it.
The best way to get a new `awk` was to `ftp` the source code for
`gawk` from `prep.ai.mit.edu`.  `gawk` was a version of
new `awk` written by David Trueman and Arnold, and available under
the GNU General Public License.

(Incidentally,
it&rsquo;s no longer difficult to find a new `awk`. `gawk` ships with
GNU/Linux, and you can download binaries or source code for almost
any system; my wife uses `gawk` on her VMS box.)

My Unix system started out unplugged from the wall; it certainly was not
plugged into a network.  So, oblivious to the existence of `gawk`
and the Unix community in general, and desiring a new `awk`, I wrote
my own, called `mawk`.
Before I was finished, I knew about `gawk`,
but it was too late to stop, so I eventually posted
to a `comp.sources` newsgroup.

A few days after my posting, I got a friendly email
from Arnold introducing
himself.   He suggested we share design and algorithms and
attached a draft of the POSIX standard so
that I could update `mawk` to support language extensions added
after publication of The AWK Programming Language.

Frankly, if our roles had
been reversed, I would not have been so open and we probably would
have never met.  I&rsquo;m glad we did meet.
He is an AWK expert&rsquo;s AWK expert and a genuinely nice person.
Arnold contributes significant amounts of his
expertise and time to the Free Software Foundation.

This book is the `gawk` reference manual, but at its core it
is a book about AWK programming that
will appeal to a wide audience.
It is a definitive reference to the AWK language as defined by the
1987 Bell Laboratories release and codified in the 1992 POSIX Utilities
standard.

On the other hand, the novice AWK programmer can study
a wealth of practical programs that emphasize
the power of AWK&rsquo;s basic idioms:
data-driven control flow, pattern matching with regular expressions,
and associative arrays.
Those looking for something new can try out `gawk`&rsquo;s
interface to network protocols via special /inet files.

The programs in this book make clear that an AWK program is
typically much smaller and faster to develop than
a counterpart written in C.
Consequently, there is often a payoff to prototyping an
algorithm or design in AWK to get it running quickly and expose
problems early. Often, the interpreted performance is adequate
and the AWK prototype becomes the product.

The new `pgawk` (profiling `gawk`), produces
program execution counts.
I recently experimented with an algorithm that for
*n*
lines of input, exhibited
~ C n^2
performance, while
theory predicted
~ C n log n
behavior. A few minutes poring
over the awkprof.out profile pinpointed the problem to
a single line of code.  `pgawk` is a welcome addition to
my programmer&rsquo;s toolbox.

Arnold has distilled over a decade of experience writing and
using AWK programs, and developing `gawk`, into this book.  If you use
AWK or want to learn how, then read this book.

    Michael Brennan
    Author of mawk
    March 2001
    

---

Next: [Preface](#preface), Previous: [Foreword3](#foreword-to-the-third-edition), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## Foreword to the Fourth Edition

Some things don&rsquo;t change.  Thirteen years ago I wrote:
&ldquo;If you use AWK or want to learn how, then read this book.&rdquo;
True then, and still true today.

Learning to use a programming language is about more than mastering the
syntax.  One needs to acquire an understanding of how to use the
features of the language to solve practical programming problems.
A focus of this book is many examples that show how to use AWK.

Some things do change. Our computers are much faster and have more memory.
Consequently, speed and storage inefficiencies of a high-level language
matter less.  Prototyping in AWK and then rewriting in C for performance
reasons happens less, because more often the prototype is fast enough.

Of course, there are computing operations that are best done in C or C++.
With `gawk` 4.1 and later, you do not have to choose between writing
your program in AWK or in C/C++.  You can write most of your
program in AWK and the aspects that require C/C++ capabilities can be written
in C/C++, and then the pieces glued together when the `gawk` module loads
the C/C++ module as a dynamic plug-in.
[Dynamic Extensions](gawk-man-2.md#16-writing-extensions-for-gawk),
has all the
details, and, as expected, many examples to help you learn the ins and outs.

I enjoy programming in AWK and had fun (re)reading this book.
I think you will too.

    Michael Brennan
    Author of mawk
    October 2014
    

---

Next: [Getting Started](#10-a-library-of-awk-functions), Previous: [Foreword4](#foreword-to-the-fourth-edition), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## Preface

Several kinds of tasks occur repeatedly when working with text files.
You might want to extract certain lines and discard the rest.  Or you
may need to make changes wherever certain patterns appear, but leave the
rest of the file alone.  Such jobs are often easy with `awk`.
The `awk` utility interprets a special-purpose programming
language that makes it easy to handle simple data-reformatting jobs.

The GNU implementation of `awk` is called `gawk`; if you
invoke it with the proper options or environment variables,
it is fully compatible with
the POSIX[1](#FOOT1)
specification of the `awk` language
and with the Unix version of `awk` maintained
by Brian Kernighan.
This means that all
properly written `awk` programs should work with `gawk`.
So most of the time, we don&rsquo;t distinguish between `gawk` and other
`awk` implementations.

Using `awk` you can:

-  Manage small, personal databases

-  Generate reports

-  Validate data

-  Produce indexes and perform other document-preparation tasks

-  Experiment with algorithms that you can adapt later to other computer
languages

In addition,
`gawk`
provides facilities that make it easy to:

-  Extract bits and pieces of data for processing

-  Sort data

-  Perform simple network communications

-  Profile and debug `awk` programs

-  Extend the language with functions written in C or C++

This Web page teaches you about the `awk` language and
how you can use it effectively.  You should already be familiar with basic
system commands, such as `cat` and `ls`,[2](#FOOT2) as well as basic shell
facilities, such as input/output (I/O) redirection and pipes.

Implementations of the `awk` language are available for many
different computing environments.  This Web page, while describing
the `awk` language in general, also describes the particular
implementation of `awk` called `gawk` (which stands for
&ldquo;GNU `awk`&rdquo;).  `gawk` runs on a broad range of Unix systems,
ranging from Intel-architecture PC-based computers
up through large-scale systems.
`gawk` has also been ported to Mac OS X,
Microsoft Windows
(all versions),
and OpenVMS.[3](#FOOT3)

| []() | []()
| - | -
| &bull; [History](#history-of-awk-and-gawk) | The history of `gawk` and `awk`.
| &bull; [Names](#a-rose-by-any-other-name) | What name to use to find `awk`.
| &bull; [This Manual](#using-this-book) | Using this Web page. Includes sample input files that you can use.
| &bull; [Conventions](#typographical-conventions) | Typographical Conventions.
| &bull; [Manual History](#the-gnu-project-and-this-book) | Brief history of the GNU project and this Web page.
| &bull; [How To Contribute](#how-to-contribute) | Helping to save the world.
| &bull; [Acknowledgments](#acknowledgments) | Acknowledgments.

---

Next: [Names](#a-rose-by-any-other-name), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### History of `awk` and `gawk`

Recipe for a Programming Language
1 part  `egrep`1 part  `snobol`2 parts `ed`3 parts C
Blend all parts well using `lex` and `yacc`.
Document minimally and release.

After eight years, add another part `egrep` and two
more parts C.  Document very well and release.

The name `awk` comes from the initials of its designers: Alfred V.
Aho, Peter J. Weinberger, and Brian W. Kernighan.  The original version of
`awk` was written in 1977 at AT&T Bell Laboratories.
In 1985, a new version made the programming
language more powerful, introducing user-defined functions, multiple input
streams, and computed regular expressions.
This new version became widely available with Unix System V
Release 3.1 (1987).
The version in System V Release 4 (1989) added some new features and cleaned
up the behavior in some of the &ldquo;dark corners&rdquo; of the language.
The specification for `awk` in the POSIX Command Language
and Utilities standard further clarified the language.
Both the `gawk` designers and the original `awk` designers at Bell Laboratories
provided feedback for the POSIX specification.

Paul Rubin wrote `gawk` in 1986.
Jay Fenlason completed it, with advice from Richard Stallman.  John Woods
contributed parts of the code as well.  In 1988 and 1989, David Trueman, with
help from me, thoroughly reworked `gawk` for compatibility
with the newer `awk`.
Circa 1994, I became the primary maintainer.
Current development focuses on bug fixes,
performance improvements, standards compliance, and, occasionally, new features.

In May 1997, J&uuml;rgen Kahrs felt the need for network access
from `awk`, and with a little help from me, set about adding
features to do this for `gawk`.  At that time, he also
wrote the bulk of
TCP/IP Internetworking with `gawk`
(a separate document, available as part of the `gawk` distribution).
His code finally became part of the main `gawk` distribution
with `gawk` version 3.1.

John Haque rewrote the `gawk` internals, in the process providing
an `awk`-level debugger. This version became available as
`gawk` version 4.0 in 2011.

See [Contributors](#Contributors)
for a full list of those who have made important contributions to `gawk`.

---

Next: [This Manual](#using-this-book), Previous: [History](#history-of-awk-and-gawk), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### A Rose by Any Other Name

The `awk` language has evolved over the years. Full details are
provided in [Language History](#Language-History).
The language described in this Web page
is often referred to as &ldquo;new `awk`.&rdquo;
By analogy, the original version of `awk` is
referred to as &ldquo;old `awk`.&rdquo;

On most current systems, when you run the `awk` utility
you get some version of new `awk`.[4](#FOOT4) If your system&rsquo;s standard
`awk` is the old one, you will see something like this
if you try the test program:

<pre>    $ awk 1 /dev/null
    error&rarr; awk: syntax error near line 1
    error&rarr; awk: bailing out near line 1</pre>
    

In this case, you should find a version of new `awk`,
or just install `gawk`!

Throughout this Web page, whenever we refer to a language feature
that should be available in any complete implementation of POSIX `awk`,
we simply use the term `awk`.  When referring to a feature that is
specific to the GNU implementation, we use the term `gawk`.

---

Next: [Conventions](#typographical-conventions), Previous: [Names](#a-rose-by-any-other-name), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### Using This Book

The term `awk` refers to a particular program as well as to the language you
use to tell this program what to do.  When we need to be careful, we call
the language &ldquo;the `awk` language,&rdquo;
and the program &ldquo;the `awk` utility.&rdquo;
This Web page explains
both how to write programs in the `awk` language and how to
run the `awk` utility.
The term &ldquo;`awk` program&rdquo; refers to a program written by you in
the `awk` programming language.

Primarily, this Web page explains the features of `awk`
as defined in the POSIX standard.  It does so in the context of the
`gawk` implementation.  While doing so, it also
attempts to describe important differences between `gawk`
and other `awk`
implementations.[5](#FOOT5)
Finally, it notes any `gawk` features that are not in
the POSIX standard for `awk`.

This Web page has the difficult task of being both a tutorial and a reference.
If you are a novice, feel free to skip over details that seem too complex.
You should also ignore the many cross-references; they are for the
expert user and for the Info and
[HTML](https://www.gnu.org/software/gawk/manual/)
versions of the Web page.

There are sidebars
scattered throughout the Web page.
They add a more complete explanation of points that are relevant, but not likely
to be of interest on first reading.
All appear in the index, under the heading &ldquo;sidebar.&rdquo;

Most of the time, the examples use complete `awk` programs.
Some of the more advanced sections show only the part of the `awk`
program that illustrates the concept being described.

Although this Web page is aimed principally at people who have not been
exposed
to `awk`, there is a lot of information here that even the `awk`
expert should find useful.  In particular, the description of POSIX
`awk` and the example programs in
[Library Functions]((#10-a-library-of-awk-functions)), and
in
[Sample Programs](#11-practical-awk-programs),
should be of interest.

This Web page is split into several parts, as follows:

-  **Part I** describes the `awk` language and the `gawk` program in detail.
It starts with the basics, and continues through all of the features of `awk`.
It contains the following chapters:

    - [Getting Started](#10-a-library-of-awk-functions),
provides the essentials you need to know to begin using `awk`.

    - [Invoking Gawk](#2-running-awk-and-gawk),
describes how to run `gawk`, the meaning of its
command-line options, and how it finds `awk`
program source files.

    - [Regexp](#3-regular-expressions),
introduces regular expressions in general, and in particular the flavors
supported by POSIX `awk` and `gawk`.

    - [Reading Files](#4-reading-input-files),
describes how `awk` reads your data.
It introduces the concepts of records and fields, as well
as the `getline` command.
I/O redirection is first described here.
Network I/O is also briefly introduced here.

    - [Printing](#5-printing-output),
describes how `awk` programs can produce output with
`print` and `printf`.

    - [Expressions](#6-expressions),
describes expressions, which are the basic building blocks
for getting most things done in a program.

    - [Patterns and Actions](#7-patterns-actions-and-variables),
describes how to write patterns for matching records, actions for
doing something when a record is matched, and the predefined variables
`awk` and `gawk` use.

    - [Arrays](#8-arrays-in-awk),
covers `awk`&rsquo;s one-and-only data structure: the associative array.
Deleting array elements and whole arrays is described, as well as
sorting arrays in `gawk`.  The chapter also describes how
`gawk` provides arrays of arrays.

    - [Functions](#9-functions),
describes the built-in functions `awk` and `gawk` provide,
as well as how to define your own functions.  It also discusses how
`gawk` lets you call functions indirectly.

-  **Part II** shows how to use `awk` and `gawk` for problem solving.
There is lots of code here for you to read and learn from.
This part contains the following chapters:

    - [Library Functions]((#10-a-library-of-awk-functions)), provides a number of functions meant to
be used from main `awk` programs.

    - [Sample Programs](#11-practical-awk-programs),
provides many sample `awk` programs.

Reading these two chapters allows you to see `awk`
solving real problems.

-  **Part III** focuses on features specific to `gawk`.
It contains the following chapters:

    - [Advanced Features](#Advanced-Features),
describes a number of advanced features.
Of particular note
are the abilities to control the order of array traversal,
have two-way communications with another process,
perform TCP/IP networking, and
profile your `awk` programs.

    - [Internationalization](#Internationalization),
describes special features for translating program
messages into different languages at runtime.

    - [Debugger](#Debugger), describes the `gawk` debugger.

    - [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic),
describes advanced arithmetic facilities.

    - [Dynamic Extensions](#Dynamic-Extensions), describes how to add new variables and
functions to `gawk` by writing extensions in C or C++.

-  **Part IV** provides the appendices, the Glossary, and two licenses that cover
the `gawk` source code and this Web page, respectively.
It contains the following appendices:

    - [Language History](#Language-History),
describes how the `awk` language has evolved since
its first release to the present.  It also describes how `gawk`
has acquired features over time.

    - [Installation](#Installation),
describes how to get `gawk`, how to compile it
on POSIX-compatible systems,
and how to compile and use it on different
non-POSIX systems.  It also describes how to report bugs
in `gawk` and where to get other freely
available `awk` implementations.

    - [Notes](#Notes),
describes how to disable `gawk`&rsquo;s extensions, as
well as how to contribute new code to `gawk`,
and some possible future directions for `gawk` development.

    - [Basic Concepts](#Basic-Concepts),
provides some very cursory background material for those who
are completely unfamiliar with computer programming.  The [Glossary](#Glossary), defines most, if not all, of the significant terms used
throughout the Web page.  If you find terms that you aren&rsquo;t familiar with,
try looking them up here.

    - [Copying](#Copying), and
[GNU Free Documentation License](#GNU-Free-Documentation-License),
present the licenses that cover the `gawk` source code
and this Web page, respectively.

---

Next: [Manual History](#the-gnu-project-and-this-book), Previous: [This Manual](#using-this-book), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### Typographical Conventions

This Web page is written in [Texinfo](https://www.gnu.org/software/texinfo/),
the GNU documentation formatting language.
A single Texinfo source file is used to produce both the printed and online
versions of the documentation.
Because of this, the typographical conventions
are slightly different than in other books you may have read.

Examples you would type at the command line are preceded by the common
shell primary and secondary prompts, &lsquo;$&rsquo; and &lsquo;>&rsquo;.
Input that you type is shown like this.
Output from the command is preceded by the glyph &ldquo;-|&rdquo;.
This typically represents the command&rsquo;s standard output.
Error messages and other output on the command&rsquo;s standard error are preceded
by the glyph &ldquo;error&rarr;&rdquo;.  For example:

<pre>  $ <i>echo hi on stdout</i>
  -| hi on stdout
  $ <i>echo hello on stderr 1>&2</i>
  error&rarr; hello on stderr</pre>
    

In the text, almost anything related to programming, such as
command names,
variable and function names, and string, numeric and regexp constants
appear in `this font`. Code fragments
appear in the same font and quoted, &lsquo;`like this`&rsquo;.
Things that are replaced by the user or programmer
appear in *this font*.
Options look like this: `-f`.
File names are indicated like this: `/path/to/ourfile`.
Some things are
emphasized *like this*, and if a point needs to be made
strongly, it is done **like this**.
The first occurrence of
a new term is usually its *definition* and appears in the same
font as the previous occurrence of &ldquo;definition&rdquo; in this sentence.

Characters that you type at the keyboard look <code><i>like this</i></code>.  In particular,
there are special characters called &ldquo;control characters.&rdquo;  These are
characters that you type by holding down both the <code><i>CONTROL</i></code> key and
another key, at the same time.  For example, a <code><i>Ctrl-d</i></code> is typed
by first pressing and holding the <code><i>CONTROL</i></code> key, next
pressing the <code><i>d</i></code> key, and finally releasing both keys.

For the sake of brevity, throughout this Web page, we refer to
Brian Kernighan&rsquo;s version of `awk` as &ldquo;BWK `awk`.&rdquo;
(See [Other Versions](#Other-Versions) for information on his and other versions.)

#### Dark Corners

> <i>Dark corners are basically fractal&mdash;no matter how much
> you illuminate, there&rsquo;s always a smaller but darker one.</i>

&mdash; *Brian Kernighan*

Until the POSIX standard (and GAWK: Effective AWK Programming),
many features of `awk` were either poorly documented or not
documented at all.  Descriptions of such features
(often called &ldquo;dark corners&rdquo;) are noted in this Web page with
&ldquo;(d.c.).&rdquo;
They also appear in the index under the heading &ldquo;dark corner.&rdquo;

But, as noted by the opening quote, any coverage of dark
corners is by definition incomplete.

Extensions to the standard `awk` language that are supported by
more than one `awk` implementation are marked
&ldquo;(c.e.),&rdquo; and listed in the index under &ldquo;common extensions&rdquo;
and &ldquo;extensions, common.&rdquo;

---

Next: [How To Contribute](#how-to-contribute), Previous: [Conventions](#typographical-conventions), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### The GNU Project and This Book

The Free Software Foundation (FSF) is a nonprofit organization dedicated
to the production and distribution of freely distributable software.
It was founded by Richard M. Stallman, the author of the original
Emacs editor.  GNU Emacs is the most widely used version of Emacs today.

The GNU[6](#FOOT6)
Project is an ongoing effort on the part of the Free Software
Foundation to create a complete, freely distributable, POSIX-compliant
computing environment.
The FSF uses the GNU General Public License (GPL) to ensure that
its software&rsquo;s
source code is always available to the end user.
A copy of the GPL is included
in this Web page
for your reference
(see [Copying](#Copying)).
The GPL applies to the C language source code for `gawk`.
To find out more about the FSF and the GNU Project online,
see [the GNU Project&rsquo;s home page](https://www.gnu.org).
This Web page may also be read from
[GNU&rsquo;s website](https://www.gnu.org/software/gawk/manual/).

A shell, an editor (Emacs), highly portable optimizing C, C++, and
Objective-C compilers, a symbolic debugger and dozens of large and
small utilities (such as `gawk`), have all been completed and are
freely available.  The GNU operating
system kernel (the HURD), has been released but remains in an early
stage of development.

Until the GNU operating system is more fully developed, you should
consider using GNU/Linux, a freely distributable, Unix-like operating
system for Intel,
Power Architecture,
Sun SPARC, IBM S/390, and other
systems.[7](#FOOT7)
Many GNU/Linux distributions are
available for download from the Internet.

The Web page you are reading is actually free&mdash;at least, the
information in it is free to anyone.  The machine-readable
source code for the Web page comes with `gawk`.
(Take a moment to check the Free Documentation
License in [GNU Free Documentation License](#GNU-Free-Documentation-License).)

The Web page itself has gone through multiple previous editions.
Paul Rubin wrote the very first draft of The GAWK Manual;
it was around 40 pages long.
Diane Close and Richard Stallman improved it, yielding a
version that was
around 90 pages and barely described the original, &ldquo;old&rdquo;
version of `awk`.

I started working with that version in the fall of 1988.
As work on it progressed,
the FSF published several preliminary versions (numbered 0.x).
In 1996, edition 1.0 was released with `gawk` 3.0.0.
The FSF published the first two editions under
the title The GNU Awk User&rsquo;s Guide.

This edition maintains the basic structure of the previous editions.
For FSF edition 4.0, the content was thoroughly reviewed and updated. All
references to `gawk` versions prior to 4.0 were removed.
Of significant note for that edition was the addition of [Debugger](#Debugger).

For FSF edition
4.2,
the content has been reorganized into parts,
and the major new additions are [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic),
and [Dynamic Extensions](#Dynamic-Extensions).

This Web page will undoubtedly continue to evolve.  If you
find an error in the Web page, please report it!  See [Bugs](#Bugs)
for information on submitting problem reports electronically.

---

Next: [Acknowledgments](#acknowledgments), Previous: [Manual History](#the-gnu-project-and-this-book), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### How to Contribute

As the maintainer of GNU `awk`, I once thought that I would be
able to manage a collection of publicly available `awk` programs
and I even solicited contributions.  Making things available on the Internet
helps keep the `gawk` distribution down to manageable size.

The initial collection of material, such as it is, is still available
at <a href="ftp://ftp.freefriends.org/arnold/Awkstuff">ftp://ftp.freefriends.org/arnold/Awkstuff</a>.

In the hopes of doing something more broad, I acquired the
`awklang.org` domain.  Late in 2017, a volunteer took on the task
of managing it.

If you have written an interesting `awk` program, that
you would like to share with the rest of the world, please see
[http://www.awklang.org](http://www.awklang.org) and use the &ldquo;Contact&rdquo; link.

If you have written a `gawk` extension, please see
[gawkextlib](#gawkextlib).

---

Previous: [How To Contribute](#how-to-contribute), Up: [Preface](#preface)   [[Contents](#table-of-contents)][[Index](#Index)]

### Acknowledgments

The initial draft of The GAWK Manual had the following acknowledgments:

> <i>Many people need to be thanked for their assistance in producing this</i>
> <i>manual.  Jay Fenlason contributed many ideas and sample programs.  Richard</i>
> <i>Mlynarik and Robert Chassell gave helpful comments on drafts of this</i>
> <i>manual.  The paper A Supplemental Document for AWK by John W.</i>
> <i>Pierce of the Chemistry Department at UC San Diego, pinpointed several</i>
> <i>issues relevant both to `awk` implementation and to this manual, that</i>
> <i>would otherwise have escaped us.</i>

I would like to acknowledge Richard M. Stallman, for his vision of a
better world and for his courage in founding the FSF and starting the
GNU Project.

Earlier editions of this Web page had the following acknowledgements:

> The following people (in alphabetical order)
> provided helpful comments on various
> versions of this book:
> Rick Adams,
> Dr. Nelson H.F. Beebe,
> Karl Berry,
> Dr. Michael Brennan,
> Rich Burridge,
> Claire Cloutier,
> Diane Close,
> Scott Deifik,
> Christopher (&ldquo;Topher&rdquo;) Eliot,
> Jeffrey Friedl,
> Dr. Darrel Hankerson,
> Michal Jaegermann,
> Dr. Richard J. LeBlanc,
> Michael Lijewski,
> Pat Rankin,
> Miriam Robbins,
> Mary Sheehan,
> and
> Chuck Toporek.
> 
> 
> Robert J. Chassell provided much valuable advice on
> the use of Texinfo.
> He also deserves special thanks for
> convincing me *not* to title this Web page
> How to Gawk Politely.
> Karl Berry helped significantly with the TeX part of Texinfo.
> 
> 
> I would like to thank Marshall and Elaine Hartholz of Seattle and
> Dr. Bert and Rita Schreiber of Detroit for large amounts of quiet vacation
> time in their homes, which allowed me to make significant progress on
> this Web page and on `gawk` itself.
> 
> 
> Phil Hughes of SSC
> contributed in a very important way by loaning me his laptop GNU/Linux
> system, not once, but twice, which allowed me to do a lot of work while
> away from home.
> 
> 
> David Trueman deserves special credit; he has done a yeoman job
> of evolving `gawk` so that it performs well and without bugs.
> Although he is no longer involved with `gawk`,
> working with him on this project was a significant pleasure.
> 
> 
> The intrepid members of the GNITS mailing list, and most notably Ulrich
> Drepper, provided invaluable help and feedback for the design of the
> internationalization features.
> 
> 
> Chuck Toporek, Mary Sheehan, and Claire Cloutier of O&rsquo;Reilly & Associates contributed
> significant editorial help for this Web page for the
> 3.1 release of `gawk`.

Dr. Nelson Beebe,
Andreas Buening,
Dr. Manuel Collado,
Antonio Colombo,
Stephen Davies,
Scott Deifik,
Akim Demaille,
Daniel Richard G.,
Juan Manuel Guerrero,
Darrel Hankerson,
Michal Jaegermann,
J&uuml;rgen Kahrs,
Stepan Kasal,
John Malmberg,
Dave Pitts,
Chet Ramey,
Pat Rankin,
Andrew Schorr,
Corinna Vinschen,
and Eli Zaretskii
(in alphabetical order)
make up the current `gawk` &ldquo;crack portability team.&rdquo;  Without
their hard work and help, `gawk` would not be nearly the robust,
portable program it is today.  It has been and continues to be a pleasure
working with this team of fine people.

Notable code and documentation contributions were made by
a number of people. See [Contributors](#Contributors) for the full list.

Thanks to Michael Brennan for the Forewords.

Thanks to Patrice Dumas for the new `makeinfo` program.
Thanks to Karl Berry, who continues to work to keep
the Texinfo markup language sane.

Robert P.J. Day, Michael Brennan, and Brian Kernighan kindly acted as
reviewers for the 2015 edition of this Web page. Their feedback
helped improve the final work.

I would also like to thank Brian Kernighan for his invaluable assistance during the
testing and debugging of `gawk`, and for his ongoing
help and advice in clarifying numerous points about the language.
We could not have done nearly as good a job on either `gawk`
or its documentation without his help.

Brian is in a class by himself as a programmer and technical
author.  I have to thank him (yet again) for his ongoing friendship
and for being a role model to me for close to 30 years!
Having him as a reviewer is an exciting privilege. It has also
been extremely humbling...

I must thank my wonderful wife, Miriam, for her patience through
the many versions of this project, for her proofreading,
and for sharing me with the computer.
I would like to thank my parents for their love, and for the grace with
which they raised and educated me.
Finally, I also must acknowledge my gratitude to G-d, for the many opportunities
He has sent my way, as well as for the gifts He has given me with which to
take advantage of those opportunities.

Arnold Robbins 
Nof Ayalon 
Israel 
February 2015

---

Next: [Invoking Gawk](#2-running-awk-and-gawk), Previous: [Preface](#preface), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

<h1>Part I:</br>
The <code>awk</code> Language</h1>

---

## 1 Getting Started with `awk`

The basic function of `awk` is to search files for lines (or other
units of text) that contain certain patterns.  When a line matches one
of the patterns, `awk` performs specified actions on that line.
`awk` continues to process input lines in this way until it reaches
the end of the input files.

Programs in `awk` are different from programs in most other languages,
because `awk` programs are *data driven* (i.e., you describe
the data you want to work with and then what to do when you find it).
Most other languages are *procedural*; you have to describe, in great
detail, every step the program should take.  When working with procedural
languages, it is usually much
harder to clearly describe the data your program will process.
For this reason, `awk` programs are often refreshingly easy to
read and write.

When you run `awk`, you specify an `awk`*program* that
tells `awk` what to do.  The program consists of a series of
*rules* (it may also contain *function definitions*,
an advanced feature that we will ignore for now;
see [User-defined](#92-user-defined-functions)).  Each rule specifies one
pattern to search for and one action to perform
upon finding the pattern.

Syntactically, a rule consists of a *pattern* followed by an
*action*.  The action is enclosed in braces to separate it from the
pattern.  Newlines usually separate rules.  Therefore, an `awk`
program looks like this:

<pre>  <i>pattern</i> { action }
  <i>pattern</i> { action }
   &hellip;</pre>
    
| []() | []()
| - | -
| &bull; [Running gawk](#11-how-to-run-awk-programs) | How to run `gawk` programs; includes command-line syntax.
| &bull; [Sample Data Files](#12-data-files-for-the-examples) | Sample data files for use in the `awk` programs illustrated in this Web page.
| &bull; [Very Simple](#13-some-simple-examples) | A very simple example.
| &bull; [Two Rules](#14-an-example-with-two-rules) | A less simple one-line example using two rules.
| &bull; [More Complex](#15-a-more-complex-example) | A more complex example.
| &bull; [Statements/Lines](#16-awk-statements-versus-lines) | Subdividing or combining statements into lines.
| &bull; [Other Features](#17-other-features-of-awk) | Other Features of `awk`.
| &bull; [When](#18-when-to-use-awk) | When to use `gawk` and when to use other things.
| &bull; [Intro Summary](#19-summary) | Summary of the introduction.

---

Next: [Sample Data Files](#12-data-files-for-the-examples), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.1 How to Run `awk` Programs

There are several ways to run an `awk` program.  If the program is
short, it is easiest to include it in the command that runs `awk`,
like this:

   <pre>awk <i>'program' input-file1input-file2</i> &hellip;</pre>
    

When the program is long, it is usually more convenient to put it in a file
and run it with a command like this:

   <pre>awk -f <i>program-fileinput-file1input-file2</i> &hellip;</pre>
    

This section discusses both mechanisms, along with several
variations of each.

| []() | []()
| - | -
| &bull; [One-shot](#111-one-shot-throwaway-awk-programs) | Running a short throwaway `awk` program.
| &bull; [Read Terminal](#112-running-awk-without-input-files) | Using no input files (input from the keyboard instead).
| &bull; [Long](#113-running-long-programs) | Putting permanent `awk` programs in files.
| &bull; [Executable Scripts](#114-executable-awk-programs) | Making self-contained `awk` programs.
| &bull; [Comments](#115-comments-in-awk-programs) | Adding documentation to `gawk` programs.
| &bull; [Quoting](#116-shell-quoting-issues) | More discussion of shell quoting issues.

---

Next: [Read Terminal](#112-running-awk-without-input-files), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.1 One-Shot Throwaway `awk` Programs

Once you are familiar with `awk`, you will often type in simple
programs the moment you want to use them.  Then you can write the
program as the first argument of the `awk` command, like this:

   <pre>awk <i>'program' input-file1input-file2</i> &hellip;</pre>
    

where program consists of a series of patterns and
actions, as described earlier.

This command format instructs the *shell*, or command interpreter,
to start `awk` and use the program to process records in the
input file(s).  There are single quotes around program so
the shell won&rsquo;t interpret any `awk` characters as special shell
characters.  The quotes also cause the shell to treat all of program as
a single argument for `awk`, and allow program to be more
than one line long.

This format is also useful for running short or medium-sized `awk`
programs from shell scripts, because it avoids the need for a separate
file for the `awk` program.  A self-contained shell script is more
reliable because there are no other files to misplace.

Later in this chapter, in
[Very Simple](#13-some-simple-examples),
we&rsquo;ll see examples of several short,
self-contained programs.

---

Next: [Long](#113-running-long-programs), Previous: [One-shot](#111-one-shot-throwaway-awk-programs), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.2 Running `awk` Without Input Files

You can also run `awk` without any input files.  If you type the
following command line:

<pre>   awk <i>'program'</i></pre>
    

`awk` applies the program to the *standard input*,
which usually means whatever you type on the keyboard.  This continues
until you indicate end-of-file by typing Ctrl-d.
(On non-POSIX operating systems, the end-of-file character may be different.)

As an example, the following program prints a friendly piece of advice
(from Douglas Adams&rsquo;s The Hitchhiker&rsquo;s Guide to the Galaxy),
to keep you from worrying about the complexities of computer
programming:

<pre>
    $ <i>awk 'BEGIN { print "Don\47t Panic!" }'</i>
    -| Don't Panic!
</pre>

`awk` executes statements associated with `BEGIN` before
reading any input.  If there are no other statements in your program,
as is the case here, `awk` just stops, instead of trying to read
input it doesn&rsquo;t know how to process.
The &lsquo;\47&rsquo; is a magic way (explained later) of getting a single quote into
the program, without having to engage in ugly shell quoting tricks.

> NOTE: If you use Bash as your shell, you should execute the
> command &lsquo;set +H&rsquo; before running this program interactively, to
> disable the C shell-style command history, which treats &lsquo;!&rsquo; as a
> special character. We recommend putting this command into your personal
> startup file.

This next simple `awk` program
emulates the `cat` utility; it copies whatever you type on the
keyboard to its standard output (why this works is explained shortly):

<pre>
  $ <i>awk '{ print }'
  Now is the time for all good men</i>
  -| Now is the time for all good men
  <i>to come to the aid of their country.</i>
  -| to come to the aid of their country.
  <i>Four score and seven years ago, ...</i>
  -| Four score and seven years ago, ...
  <i>What, me worry?</i>
  -| What, me worry?
  <i>Ctrl-d</i>
</pre>

---

Next: [Executable Scripts](#114-executable-awk-programs), Previous: [Read Terminal](#112-running-awk-without-input-files), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.3 Running Long Programs

Sometimes `awk` programs are very long.  In these cases, it is
more convenient to put the program into a separate file.  In order to tell
`awk` to use that file for its program, you type:

  <pre>awk -f <i>source-fileinput-file1input-file2</i> &hellip;</pre>
    

The -f instructs the `awk` utility to get the
`awk` program from the file source-file (see [Options](#22-command-line-options)).
Any file name can be used for source-file.  For example, you
could put the program:

```awk
  BEGIN { print "Don't Panic!" }
```    

into the file advice.  Then this command:

```awk
  awk -f advice
```    

does the same thing as this one:

```awk
  awk 'BEGIN { print "Don\47t Panic!" }'
```    

This was explained earlier
(see [Read Terminal](#112-running-awk-without-input-files)).
Note that you don&rsquo;t usually need single quotes around the file name that you
specify with -f, because most file names don&rsquo;t contain any of the shell&rsquo;s
special characters.  Notice that in advice, the `awk`
program did not have single quotes around it.  The quotes are only needed
for programs that are provided on the `awk` command line.
(Also, placing the program in a file allows us to use a literal single quote in the program
text, instead of the magic &lsquo;\47&rsquo;.)

If you want to clearly identify an `awk` program file as such,
you can add the extension .awk to the file name.  This doesn&rsquo;t
affect the execution of the `awk` program but it does make
&ldquo;housekeeping&rdquo; easier.

---

Next: [Comments](#115-comments-in-awk-programs), Previous: [Long](#113-running-long-programs), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.4 Executable `awk` Programs

Once you have learned `awk`, you may want to write self-contained
`awk` scripts, using the &lsquo;#!&rsquo; script mechanism.  You can do
this on many systems.[8](#FOOT8)
For example, you could update the file advice to look like this:

```awk
  #! /bin/awk -f
    
  BEGIN { print "Don't Panic!" }
```
    

After making this file executable (with the `chmod` utility),
simply type &lsquo;advice&rsquo;
at the shell and the system arranges to run `awk` as if you had
typed &lsquo;awk -f advice&rsquo;:

<pre>
  $ <i>chmod +x advice</i>
  $ <i>advice</i>
  -| Don't Panic!
</pre>    

(We assume you have the current directory in your shell&rsquo;s search
path variable [typically `$PATH`].  If not, you may need
to type &lsquo;./advice&rsquo; at the shell.)

Self-contained `awk` scripts are useful when you want to write a
program that users can invoke without their having to know that the program is
written in `awk`.

Understanding &lsquo;#!&rsquo;

`awk` is an *interpreted* language. This means that the
`awk` utility reads your program and then processes your data
according to the instructions in your program. (This is different
from a *compiled* language such as C, where your program is first
compiled into machine code that is executed directly by your system&rsquo;s
processor.)  The `awk` utility is thus termed an *interpreter*.
Many modern languages are interpreted.

The line beginning with &lsquo;#!&rsquo; lists the full file name of an
interpreter to run and a single optional initial command-line argument
to pass to that interpreter.  The operating system then runs the
interpreter with the given argument and the full argument list of the
executed program.  The first argument in the list is the full file name
of the `awk` program.  The rest of the argument list contains
either options to `awk`, or data files, or both. (Note that on
many systems `awk` may be found in /usr/bin instead of
in /bin.)

Some systems limit the length of the interpreter name to 32 characters.
Often, this can be dealt with by using a symbolic link.

You should not put more than one argument on the &lsquo;#!&rsquo;
line after the path to `awk`. It does not work. The operating system
treats the rest of the line as a single argument and passes it to `awk`.
Doing this leads to confusing behavior&mdash;most likely a usage diagnostic
of some sort from `awk`.

Finally, the value of `ARGV[0]`
(see [Built-in Variables](#75-predefined-variables))
varies depending upon your operating system.
Some systems put &lsquo;awk&rsquo; there, some put the full pathname
of `awk` (such as /bin/awk), and some put the name
of your script (&lsquo;advice&rsquo;).  (d.c.)
Don&rsquo;t rely on the value of `ARGV[0]`
to provide your script name.

---

Next: [Quoting](#116-shell-quoting-issues), Previous: [Executable Scripts](#114-executable-awk-programs), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.5 Comments in `awk` Programs

A *comment* is some text that is included in a program for the sake
of human readers; it is not really an executable part of the program.  Comments
can explain what the program does and how it works.  Nearly all
programming languages have provisions for comments, as programs are
typically hard to understand without them.

In the `awk` language, a comment starts with the number sign
character (&lsquo;#&rsquo;) and continues to the end of the line.
The &lsquo;#&rsquo; does not have to be the first character on the line. The
`awk` language ignores the rest of a line following a number sign.
For example, we could have put the following into advice:

```awk
  # This program prints a nice, friendly message.  It helps
  # keep novice users from being afraid of the computer.
  BEGIN    { print "Don't Panic!" }
```

You can put comment lines into keyboard-composed throwaway `awk`
programs, but this usually isn&rsquo;t very useful; the purpose of a
comment is to help you or another person understand the program
when reading it at a later time.

> <b>CAUTION:</b> As mentioned in
> [One-shot](#111-one-shot-throwaway-awk-programs),
> you can enclose short to medium-sized programs in single quotes,
> in order to keep
> your shell scripts self-contained.  When doing so, *don&rsquo;t* put
> an apostrophe (i.e., a single quote) into a comment (or anywhere else
> in your program). The shell interprets the quote as the closing
> quote for the entire program. As a result, usually the shell
> prints a message about mismatched quotes, and if `awk` actually
> runs, it will probably print strange messages about syntax errors.
> For example, look at the following:
> 
> 
> 
>   <pre>$ <i>awk 'BEGIN { print "hello" } # let's be cute'</i>
>   ></pre>
>     
> 
> 
> 
> The shell sees that the first two quotes match, and that
> a new quoted object begins at the end of the command line.
> It therefore prompts with the secondary prompt, waiting for more input.
> With Unix `awk`, closing the quoted string produces this result:
> 
> 
> 
>   <pre>$ <i>awk '{ print "hello" } # let's be cute'</i>
>   > '
>   error&rarr; awk: can't open file be
>   error&rarr;  source line number 1</pre>
>     
> 
> 
> 
> Putting a backslash before the single quote in &lsquo;let's&rsquo; wouldn&rsquo;t help,
> because backslashes are not special inside single quotes.
> The next subsection describes the shell&rsquo;s quoting rules.

---

Previous: [Comments](#115-comments-in-awk-programs), Up: [Running gawk](#11-how-to-run-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.6 Shell Quoting Issues
&bull; [DOS Quoting](#1161-quoting-in-ms-windows-batch-files):  Quoting in Windows Batch Files.

For short to medium-length `awk` programs, it is most convenient
to enter the program on the `awk` command line.
This is best done by enclosing the entire program in single quotes.
This is true whether you are entering the program interactively at
the shell prompt, or writing it as part of a larger shell script:

<pre>  awk <i>'program text' input-file1input-file2</i> &hellip;</pre>
    

Once you are working with the shell, it is helpful to have a basic
knowledge of shell quoting rules.  The following rules apply only to
POSIX-compliant, Bourne-style shells (such as Bash, the GNU Bourne-Again
Shell).  If you use the C shell, you&rsquo;re on your own.

Before diving into the rules, we introduce a concept that appears
throughout this Web page, which is that of the *null*,
or empty, string.

The null string is character data that has no value.
In other words, it is empty.  It is written in `awk` programs
like this: `""`. In the shell, it can be written using single
or double quotes: `""` or `''`. Although the null string has
no characters in it, it does exist. For example, consider this command:

<pre>  $ echo ""</pre>

Here, the `echo` utility receives a single argument, even
though that argument has no characters in it. In the rest of this
Web page, we use the terms *null string* and *empty string*
interchangeably.  Now, on to the quoting rules:

<ul>
<li>Quoted items can be concatenated with nonquoted items as well as with other
quoted items.  The shell turns everything into one argument for
the command.</li>

<li>Preceding any single character with a backslash (&lsquo;\&rsquo;) quotes
that character.  The shell removes the backslash and passes the quoted
character on to the command.</li>

<li>Single quotes protect everything between the opening and closing quotes.
The shell does no interpretation of the quoted text, passing it on verbatim
to the command.
It is *impossible* to embed a single quote inside single-quoted text.
Refer back to
[Comments](#115-comments-in-awk-programs)
for an example of what happens if you try.</li>

<li>Double quotes protect most things between the opening and closing quotes.
The shell does at least variable and command substitution on the quoted text.
Different shells may do additional kinds of processing on double-quoted text.

Because certain characters within double-quoted text are processed by the shell,
they must be *escaped* within the text.  Of note are the characters
&lsquo;$&rsquo;, &lsquo;`&rsquo;, &lsquo;\&rsquo;, and &lsquo;"&rsquo;, all of which must be preceded by
a backslash within double-quoted text if they are to be passed on literally
to the program.  (The leading backslash is stripped first.)
Thus, the example seen
previously
in [Read Terminal](#112-running-awk-without-input-files):

```awk
awk 'BEGIN { print "Don\47t Panic!" }'
``` 

could instead be written this way:

```awk
$ awk "BEGIN { print \"Don't Panic!\" }"
-| Don't Panic!
```    

Note that the single quote is not special within double quotes.</li>

<li>Null strings are removed when they occur as part of a non-null
command-line argument, while explicit null objects are kept.
For example, to specify that the field separator `FS` should
be set to the null string, use:

```awk
awk -F "" 'program' files # correct
```    

Don&rsquo;t use this:

```awk
awk -F"" 'program' files  # wrong!
```    

In the second case, `awk` attempts to use the text of the program
as the value of `FS`, and the first file name as the text of the program!
This results in syntax errors at best, and confusing behavior at worst.</li></ul>

Mixing single and double quotes is difficult.  You have to resort
to shell quoting tricks, like this:

```awk
$ awk 'BEGIN { print "Here is a single quote <'"'"'>" }'
-| Here is a single quote <'>
```    
    

This program consists of three concatenated quoted strings.  The first and the
third are single-quoted, and the second is double-quoted.

This can be &ldquo;simplified&rdquo; to:

```awk
$ awk 'BEGIN { print "Here is a single quote <'\''>" }'
-| Here is a single quote <'>
```    

Judge for yourself which of these two is the more readable.

Another option is to use double quotes, escaping the embedded, `awk`-level
double quotes:

```awk
$ awk "BEGIN { print \"Here is a single quote <'>\" }"
-| Here is a single quote <'>
```    

This option is also painful, because double quotes, backslashes, and dollar signs
are very common in more advanced `awk` programs.

A third option is to use the octal escape sequence equivalents
(see [Escape Sequences](#32-escape-sequences))
for the
single- and double-quote characters, like so:

```awk
$ awk 'BEGIN { print "Here is a single quote <\47>" }'
-| Here is a single quote <'>
$ awk 'BEGIN { print "Here is a double quote <\42>" }'
-| Here is a double quote <">
```    

This works nicely, but you should comment clearly what the
escapes mean.

A fourth option is to use command-line variable assignment, like this:

```awk
$ awk -v sq="'" 'BEGIN { print "Here is a single quote <" sq ">" }'
-| Here is a single quote <'>
```    

(Here, the two string constants and the value of `sq` are concatenated
into a single string that is printed by `print`.)

If you really need both single and double quotes in your `awk`
program, it is probably best to move it into a separate file, where
the shell won&rsquo;t be part of the picture and you can say what you mean.

---

Up: [Quoting](#116-shell-quoting-issues)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 1.1.6.1 Quoting in MS-Windows Batch Files

Although this Web page generally only worries about POSIX systems and the
POSIX shell, the following issue arises often enough for many users that
it is worth addressing.

The &ldquo;shells&rdquo; on Microsoft Windows systems use the double-quote
character for quoting, and make it difficult or impossible to include an
escaped double-quote character in a command-line script.  The following
example, courtesy of Jeroen Brink, shows how to escape the double quotes
from this one liner script that prints all lines in a file surrounded by
double quotes:

```awk
  { print "\"" $0 "\"" }
```    

In an MS-Windows command-line the one-liner script above may be passed as
follows:

```awk
  gawk "{ print \"\042\" $0 \"\042\" }" file
```

In this example the &lsquo;\042&rsquo; is the octal code for a double-quote;
`gawk` converts it into a real double-quote for output by
the `print` statement.

In MS-Windows escaping double-quotes is a little tricky because you use
backslashes to escape double-quotes, but backslashes themselves are not
escaped in the usual way; indeed they are either duplicated or not,
depending upon whether there is a subsequent double-quote.  The MS-Windows
rule for double-quoting a string is the following:

 1. For each double quote in the original string, let N be the number
of backslash(es) before it, N might be zero. Replace these N
backslash(es) by *2*N+1* backslash(es)

 2. Let N be the number of backslash(es) tailing the original string,
N might be zero. Replace these N backslash(es) by
*2*N* backslash(es)

 3. Surround the resulting string by double-quotes.

So to double-quote the one-liner script &lsquo;{ print "\"" $0 "\"" }&rsquo;
from the previous example you would do it this way:

```awk
  gawk "{ print \"\\\"\" $0 \"\\\"\" }" file
```

However, the use of &lsquo;\042&rsquo; instead of &lsquo;\\\"&rsquo; is also possible
and easier to read, because backslashes that are not followed by a
double-quote don&rsquo;t need duplication.

---

Next: [Very Simple](#13-some-simple-examples), Previous: [Running gawk](#11-how-to-run-awk-programs), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.2 Data files for the Examples

Many of the examples in this Web page take their input from two sample
data files.  The first, mail-list, represents a list of peoples&rsquo; names
together with their email addresses and information about those people.
The second data file, called inventory-shipped, contains
information about monthly shipments.  In both files,
each line is considered to be one *record*.

In mail-list, each record contains the name of a person,
his/her phone number, his/her email address, and a code for his/her relationship
with the author of the list.
The columns are aligned using spaces.
An &lsquo;A&rsquo; in the last column
means that the person is an acquaintance.  An &lsquo;F&rsquo; in the last
column means that the person is a friend.
An &lsquo;R&rsquo; means that the person is a relative:

    Amelia       555-5553     amelia.zodiacusque@gmail.com    F
    Anthony      555-3412     anthony.asserturo@hotmail.com   A
    Becky        555-7685     becky.algebrarum@gmail.com      A
    Bill         555-1675     bill.drowning@hotmail.com       A
    Broderick    555-0542     broderick.aliquotiens@yahoo.com R
    Camilla      555-2912     camilla.infusarum@skynet.be     R
    Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
    Julie        555-6699     julie.perscrutabor@skeeve.com   F
    Martin       555-6480     martin.codicibus@hotmail.com    A
    Samuel       555-3430     samuel.lanceolis@shu.edu        A
    Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
    

The data file inventory-shipped represents
information about shipments during the year.
Each record contains the month, the number
of green crates shipped, the number of red boxes shipped, the number of
orange bags shipped, and the number of blue packages shipped,
respectively.  There are 16 entries, covering the 12 months of last year
and the first four months of the current year.
An empty line separates the data for the two years:

    Jan  13  25  15 115
    Feb  15  32  24 226
    Mar  15  24  34 228
    Apr  31  52  63 420
    May  16  34  29 208
    Jun  31  42  75 492
    Jul  24  34  67 436
    Aug  15  34  47 316
    Sep  13  55  37 277
    Oct  29  54  68 525
    Nov  20  87  82 577
    Dec  17  35  61 401
    
    Jan  21  36  64 620
    Feb  26  58  80 652
    Mar  24  75  70 495
    Apr  21  70  74 514
    

The sample files are included in the `gawk` distribution,
in the directory awklib/eg/data.

---

Next: [Two Rules](#14-an-example-with-two-rules), Previous: [Sample Data Files](#12-data-files-for-the-examples), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.3 Some Simple Examples

The following command runs a simple `awk` program that searches the
input file mail-list for the character string &lsquo;li&rsquo; (a
grouping of characters is usually called a *string*;
the term *string* is based on similar usage in English, such
as &ldquo;a string of pearls&rdquo; or &ldquo;a string of cars in a train&rdquo;):

```awk
  awk '/li/ { print $0 }' mail-list
```    

When lines containing &lsquo;li&rsquo; are found, they are printed because
&lsquo;print $0&rsquo; means print the current line.  (Just &lsquo;print&rsquo; by
itself means the same thing, so we could have written that
instead.)

You will notice that slashes (&lsquo;/&rsquo;) surround the string &lsquo;li&rsquo;
in the `awk` program.  The slashes indicate that &lsquo;li&rsquo;
is the pattern to search for.  This type of pattern is called a
*regular expression*, which is covered in more detail later
(see [Regexp](#3-regular-expressions)).
The pattern is allowed to match parts of words.
There are
single quotes around the `awk` program so that the shell won&rsquo;t
interpret any of it as special shell characters.

Here is what this program prints:

```awk
  $ awk '/li/ { print $0 }' mail-list
  -| Amelia       555-5553     amelia.zodiacusque@gmail.com    F
  -| Broderick    555-0542     broderick.aliquotiens@yahoo.com R
  -| Julie        555-6699     julie.perscrutabor@skeeve.com   F
  -| Samuel       555-3430     samuel.lanceolis@shu.edu        A
```    

In an `awk` rule, either the pattern or the action can be omitted,
but not both.  If the pattern is omitted, then the action is performed
for *every* input line.  If the action is omitted, the default
action is to print all lines that match the pattern.

Thus, we could leave out the action (the `print` statement and the
braces) in the previous example and the result would be the same:
`awk` prints all lines matching the pattern &lsquo;li&rsquo;.  By comparison,
omitting the `print` statement but retaining the braces makes an
empty action that does nothing (i.e., no lines are printed).

Many practical `awk` programs are just a line or two long.  Following is a
collection of useful, short programs to get you started.  Some of these
programs contain constructs that haven&rsquo;t been covered yet. (The description
of the program will give you a good idea of what is going on, but you&rsquo;ll
need to read the rest of the Web page to become an `awk` expert!)
Most of the examples use a data file named data.  This is just a
placeholder; if you use these programs yourself, substitute
your own file names for data.
For future reference, note that there is often more than
one way to do things in `awk`.  At some point, you may want
to look back at these examples and see if
you can come up with different ways to do the same things shown here:

<ul>
<li>Print every line that is longer than 80 characters:

```awk
  awk 'length($0) > 80' data
```    

The sole rule has a relational expression as its pattern and has no
action&mdash;so it uses the default action, printing the record.</li>

<li>Print the length of the longest input line:

```awk
  awk '{ if (length($0) > max) max = length($0) }
       END { print max }' data
```    

The code associated with `END` executes after all
input has been read; it&rsquo;s the other side of the coin to `BEGIN`.</li>

<li>Print the length of the longest line in data:

```awk
  expand data | awk '{ if (x < length($0)) x = length($0) }
                    END { print "maximum line length is " x }'
```    

This example differs slightly from the previous one:
the input is processed by the `expand` utility to change TABs
into spaces, so the widths compared are actually the right-margin columns,
as opposed to the number of input characters on each line.</li>

<li>Print every line that has at least one field:

```awk
  awk 'NF > 0' data
```   

This is an easy way to delete blank lines from a file (or rather, to
create a new file similar to the old file but from which the blank lines
have been removed).</li>

<li>Print seven random numbers from 0 to 100, inclusive:

```awk
  awk 'BEGIN { for (i = 1; i <= 7; i++)
                   print int(101 * rand()) }'
```
</li>

<li>Print the total number of bytes used by files:

```awk
  ls -l files | awk '{ x += $5 }
                     END { print "total bytes: " x }'
```    
</li>

<li>Print the total number of kilobytes used by files:

```awk
  ls -l files | awk '{ x += $5 }
     END { print "total K-bytes:", x / 1024 }'
```    
</li>
<li>Print a sorted list of the login names of all users:

```awk
  awk -F: '{ print $1 }' /etc/passwd | sort
```    
</li>
<li>Count the lines in a file:

```awk
  awk 'END { print NR }' data
```    
</li>
<li>Print the even-numbered lines in the data file:

```awk
  awk 'NR % 2 == 0' data
```    
</li>
</ul>
If you used the expression &lsquo;NR % 2 == 1&rsquo; instead,
the program would print the odd-numbered lines.

---

Next: [More Complex](#15-a-more-complex-example), Previous: [Very Simple](#13-some-simple-examples), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.4 An Example with Two Rules

The `awk` utility reads the input files one line at a
time.  For each line, `awk` tries the patterns of each rule.
If several patterns match, then several actions execute in the order in
which they appear in the `awk` program.  If no patterns match, then
no actions run.

After processing all the rules that match the line (and perhaps there are none),
`awk` reads the next line.  (However,
see [Next Statement](#748-the-next-statement)
and also see [Nextfile Statement](#749-the-nextfile-statement).)
This continues until the program reaches the end of the file.
For example, the following `awk` program contains two rules:

```awk
  /12/  { print $0 }
  /21/  { print $0 }
```   

The first rule has the string &lsquo;12&rsquo; as the
pattern and &lsquo;print $0&rsquo; as the action.  The second rule has the
string &lsquo;21&rsquo; as the pattern and also has &lsquo;print $0&rsquo; as the
action.  Each rule&rsquo;s action is enclosed in its own pair of braces.

This program prints every line that contains the string
&lsquo;12&rsquo; *or* the string &lsquo;21&rsquo;.  If a line contains both
strings, it is printed twice, once by each rule.

This is what happens if we run this program on our two sample data files,
mail-list and inventory-shipped:

```awk
  $ awk '/12/ { print $0 }
  >      /21/ { print $0 }' mail-list inventory-shipped
  -| Anthony      555-3412     anthony.asserturo@hotmail.com   A
  -| Camilla      555-2912     camilla.infusarum@skynet.be     R
  -| Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
  -| Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
  -| Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
  -| Jan  21  36  64 620
  -| Apr  21  70  74 514
```    

Note how the line beginning with &lsquo;Jean-Paul&rsquo;
in mail-list was printed twice, once for each rule.

---

Next: [Statements/Lines](#16-awk-statements-versus-lines), Previous: [Two Rules](#14-an-example-with-two-rules), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.5 A More Complex Example

Now that we&rsquo;ve mastered some simple tasks, let&rsquo;s look at
what typical `awk`
programs do.  This example shows how `awk` can be used to
summarize, select, and rearrange the output of another utility.  It uses
features that haven&rsquo;t been covered yet, so don&rsquo;t worry if you don&rsquo;t
understand all the details:

```awk
  ls -l | awk '$6 == "Nov" { sum += $5 }
               END { print sum }'
```    

This command prints the total number of bytes in all the files in the
current directory that were last modified in November (of any year).
The &lsquo;ls -l&rsquo; part of this example is a system command that gives
you a listing of the files in a directory, including each file&rsquo;s size and the date
the file was last modified. Its output looks like this:

    -rw-r--r--  1 arnold   user   1933 Nov  7 13:05 Makefile
    -rw-r--r--  1 arnold   user  10809 Nov  7 13:03 awk.h
    -rw-r--r--  1 arnold   user    983 Apr 13 12:14 awk.tab.h
    -rw-r--r--  1 arnold   user  31869 Jun 15 12:20 awkgram.y
    -rw-r--r--  1 arnold   user  22414 Nov  7 13:03 awk1.c
    -rw-r--r--  1 arnold   user  37455 Nov  7 13:03 awk2.c
    -rw-r--r--  1 arnold   user  27511 Dec  9 13:07 awk3.c
    -rw-r--r--  1 arnold   user   7989 Nov  7 13:03 awk4.c
    

The first field contains read-write permissions, the second field contains
the number of links to the file, and the third field identifies the file&rsquo;s owner.
The fourth field identifies the file&rsquo;s group.
The fifth field contains the file&rsquo;s size in bytes.  The
sixth, seventh, and eighth fields contain the month, day, and time,
respectively, that the file was last modified.  Finally, the ninth field
contains the file name.

The &lsquo;$6 == "Nov"&rsquo; in our `awk` program is an expression that
tests whether the sixth field of the output from &lsquo;ls -l&rsquo;
matches the string &lsquo;Nov&rsquo;.  Each time a line has the string
&lsquo;Nov&rsquo; for its sixth field, `awk` performs the action
&lsquo;sum += $5&rsquo;.  This adds the fifth field (the file&rsquo;s size) to the variable
`sum`.  As a result, when `awk` has finished reading all the
input lines, `sum` is the total of the sizes of the files whose
lines matched the pattern.  (This works because `awk` variables
are automatically initialized to zero.)

After the last line of output from `ls` has been processed, the
`END` rule executes and prints the value of `sum`.
In this example, the value of `sum` is 80600.

These more advanced `awk` techniques are covered in later
sections
(see [Action Overview](#73-actions)).  Before you can move on to more
advanced `awk` programming, you have to know how `awk` interprets
your input and displays your output.  By manipulating fields and using
`print` statements, you can produce some very useful and
impressive-looking reports.

---

Next: [Other Features](#17-other-features-of-awk), Previous: [More Complex](#15-a-more-complex-example), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.6 `awk` Statements Versus Lines

Most often, each line in an `awk` program is a separate statement or
separate rule, like this:

```awk
  awk '/12/  { print $0 }
       /21/  { print $0 }' mail-list inventory-shipped
```    

However, `gawk` ignores newlines after any of the following
symbols and keywords:

    ,    {    ?    :    ||    &&    do    else
    

A newline at any other point is considered the end of the
statement.[9](#FOOT9)

If you would like to split a single statement into two lines at a point
where a newline would terminate it, you can *continue* it by ending the
first line with a backslash character (&lsquo;\&rsquo;).  The backslash must be
the final character on the line in order to be recognized as a continuation
character.  A backslash is allowed anywhere in the statement, even
in the middle of a string or regular expression.  For example:

```awk
  awk '/This regular expression is too long, so continue it\
   on the next line/ { print $1 }'
```    

We have generally not used backslash continuation in our sample programs.
`gawk` places no limit on the
length of a line, so backslash continuation is never strictly necessary;
it just makes programs more readable.  For this same reason, as well as
for clarity, we have kept most statements short in the programs
presented throughout the Web page.  Backslash continuation is
most useful when your `awk` program is in a separate source file
instead of entered from the command line.  You should also note that
many `awk` implementations are more particular about where you
may use backslash continuation. For example, they may not allow you to
split a string constant using backslash continuation.  Thus, for maximum
portability of your `awk` programs, it is best not to split your
lines in the middle of a regular expression or a string.

> CAUTION:*Backslash continuation does not work as described
> with the C shell.*  It works for `awk` programs in files and
> for one-shot programs, *provided* you are using a POSIX-compliant
> shell, such as the Unix Bourne shell or Bash.  But the C shell behaves
> differently!  There you must use two backslashes in a row, followed by
> a newline.  Note also that when using the C shell, *every* newline
> in your `awk` program must be escaped with a backslash. To illustrate:
> 
> 
> 
>     % awk 'BEGIN { \
>     ?   print \\
>     ?       "hello, world" \
>     ? }'
>     -| hello, world
>     
> 
> 
> 
> Here, the &lsquo;%&rsquo; and &lsquo;?&rsquo; are the C shell&rsquo;s primary and secondary
> prompts, analogous to the standard shell&rsquo;s &lsquo;$&rsquo; and &lsquo;>&rsquo;.
> 
> 
> Compare the previous example to how it is done with a POSIX-compliant shell:
> 
> 
> 
>     $ awk 'BEGIN {
>     >   print \
>     >       "hello, world"
>     > }'
>     -| hello, world
>     

`awk` is a line-oriented language.  Each rule&rsquo;s action has to
begin on the same line as the pattern.  To have the pattern and action
on separate lines, you *must* use backslash continuation; there
is no other option.

Another thing to keep in mind is that backslash continuation and
comments do not mix. As soon as `awk` sees the &lsquo;#&rsquo; that
starts a comment, it ignores *everything* on the rest of the
line. For example:

    $ gawk 'BEGIN { print "dont panic" # a friendly \
    >                                    BEGIN rule
    > }'
    error&rarr; gawk: cmd. line:2:                BEGIN rule
    error&rarr; gawk: cmd. line:2:                ^ syntax error
    

In this case, it looks like the backslash would continue the comment onto the
next line. However, the backslash-newline combination is never even
noticed because it is &ldquo;hidden&rdquo; inside the comment. Thus, the
`BEGIN` is noted as a syntax error.

When `awk` statements within one rule are short, you might want to put
more than one of them on a line.  This is accomplished by separating the statements
with a semicolon (&lsquo;;&rsquo;).
This also applies to the rules themselves.
Thus, the program shown at the start of this section
could also be written this way:

```awk
    /12/ { print $0 } ; /21/ { print $0 }
```    

> NOTE: The requirement that states that rules on the same line must be
> separated with a semicolon was not in the original `awk`
> language; it was added for consistency with the treatment of statements
> within an action.

---

Next: [When](#18-when-to-use-awk), Previous: [Statements/Lines](#16-awk-statements-versus-lines), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.7 Other Features of `awk`

The `awk` language provides a number of predefined, or
*built-in*, variables that your programs can use to get information
from `awk`.  There are other variables your program can set
as well to control how `awk` processes your data.

In addition, `awk` provides a number of built-in functions for doing
common computational and string-related operations.
`gawk` provides built-in functions for working with timestamps,
performing bit manipulation, for runtime string translation (internationalization),
determining the type of a variable,
and array sorting.

As we develop our presentation of the `awk` language, we will introduce
most of the variables and many of the functions. They are described
systematically in [Built-in Variables](#75-predefined-variables) and in
[Built-in](#91-built-in-functions).

---

Next: [Intro Summary](#19-summary), Previous: [Other Features](#17-other-features-of-awk), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.8 When to Use `awk`

Now that you&rsquo;ve seen some of what `awk` can do,
you might wonder how `awk` could be useful for you.  By using
utility programs, advanced patterns, field separators, arithmetic
statements, and other selection criteria, you can produce much more
complex output.  The `awk` language is very useful for producing
reports from large amounts of raw data, such as summarizing information
from the output of other utility programs like `ls`.
(See [More Complex](#15-a-more-complex-example).)

Programs written with `awk` are usually much smaller than they would
be in other languages.  This makes `awk` programs easy to compose and
use.  Often, `awk` programs can be quickly composed at your keyboard,
used once, and thrown away.  Because `awk` programs are interpreted, you
can avoid the (usually lengthy) compilation part of the typical
edit-compile-test-debug cycle of software development.

Complex programs have been written in `awk`, including a complete
retargetable assembler for
eight-bit microprocessors (see [Glossary](#Glossary), for more information),
and a microcode assembler for a special-purpose Prolog
computer.
The original `awk`&rsquo;s capabilities were strained by tasks
of such complexity, but modern versions are more capable.

If you find yourself writing `awk` scripts of more than, say,
a few hundred lines, you might consider using a different programming
language.  The shell is good at string and pattern matching; in addition,
it allows powerful use of the system utilities.  Python offers a nice
balance between high-level ease of programming and access to system
facilities.[10](#FOOT10)

---

Previous: [When](#18-when-to-use-awk), Up: [Getting Started](#10-a-library-of-awk-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 1.9 Summary

-  Programs in `awk` consist of pattern&ndash;action pairs.

-  An action without a pattern always runs.  The default
action for a pattern without one is `{ print $0 }`.

-  Use either `awk 'program' files` or `awk -f program-filefiles` to run `awk`.

-  You may use the special `#!` header line to create `awk`
programs that are directly executable.

-  Comments in `awk` programs start with &lsquo;#&rsquo; and continue to
the end of the same line.

-  Be aware of quoting issues when writing `awk` programs as
part of a larger shell script (or MS-Windows batch file).

-  You may use backslash continuation to continue a source line.
Lines are automatically continued after
a comma, open brace, question mark, colon,
`||`, `&&`, `do`, and `else`.

---

Next: [Regexp](#3-regular-expressions), Previous: [Getting Started](#10-a-library-of-awk-functions), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 2 Running `awk` and `gawk`

This chapter covers how to run `awk`, both POSIX-standard
and `gawk`-specific command-line options, and what
`awk` and
`gawk` do with nonoption arguments.
It then proceeds to cover how `gawk` searches for source files,
reading standard input along with other files, `gawk`&rsquo;s
environment variables, `gawk`&rsquo;s exit status, using include files,
and obsolete and undocumented options and/or features.

Many of the options and features described here are discussed in
more detail later in the Web page; feel free to skip over
things in this chapter that don&rsquo;t interest you right now.

| []() | []()
| - | -
| &bull; [Command Line](#21-invoking-awk) | How to run `awk`.
| &bull; [Options](#22-command-line-options) | Command-line options and their meanings.
| &bull; [Other Arguments](#23-other-command-line-arguments) | Input file names and variable assignments.
| &bull; [Naming Standard Input](#24-naming-standard-input) | How to specify standard input with other files.
| &bull; [Environment Variables](#25-the-environment-variables-gawk-uses) | The environment variables `gawk` uses.
| &bull; [Exit Status](#26-gawkrsquos-exit-status) | `gawk`&rsquo;s exit status.
| &bull; [Include Files](#27-including-other-files-into-your-program) | Including other files into your program.
| &bull; [Loading Shared Libraries](#28-loading-dynamic-extensions-into-your-program) | Loading shared libraries into your program.
| &bull; [Obsolete](#29-obsolete-options-andor-features) | Obsolete Options and/or features.
| &bull; [Undocumented](#210-undocumented-options-and-features) | Undocumented Options and Features.
| &bull; [Invoking Summary](#211-summary) | Invocation summary.

---

Next: [Options](#22-command-line-options), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.1 Invoking `awk`

There are two ways to run `awk`&mdash;with an explicit program or with
one or more program files.  Here are templates for both of them; items
enclosed in [&hellip;] in these templates are optional:

<pre>    awk [<i>options</i>] -f <i>progfile</i>[--] <i>file</i> &hellip;
    awk [<i>options</i>] [--] '<i>program</i>'file &hellip;</pre>
    

In addition to traditional one-letter POSIX-style options, `gawk` also
supports GNU long options.

It is possible to invoke `awk` with an empty program:

    awk '' datafile1 datafile2
    

Doing so makes little sense, though; `awk` exits
silently when given an empty program.
(d.c.)
If --lint has
been specified on the command line, `gawk` issues a
warning that the program is empty.

---

Next: [Other Arguments](#23-other-command-line-arguments), Previous: [Command Line](#21-invoking-awk), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.2 Command-Line Options

Options begin with a dash and consist of a single character.
GNU-style long options consist of two dashes and a keyword.
The keyword can be abbreviated, as long as the abbreviation allows the option
to be uniquely identified.  If the option takes an argument, either the
keyword is immediately followed by an equals sign (&lsquo;=&rsquo;) and the
argument&rsquo;s value, or the keyword and the argument&rsquo;s value are separated
by whitespace.
If a particular option with a value is given more than once, it is the
last value that counts.

Each long option for `gawk` has a corresponding
POSIX-style short option.
The long and short options are
interchangeable in all contexts.
The following list describes options mandated by the POSIX standard:

<code>-F <b><i>fs</i></b></code></br>
<code>--field-separator <b><i>fs</i></b></code></br>
Set the `FS` variable to fs
(see[Field Separators](#45-specifying-how-fields-are-separated)).

<code>-f <i><b>source-file</b></i></code></br>
<code>--file <i><b>source-file</b></i></code></br>
Read the `awk` program source from source-file
instead of in the first nonoption argument.
This option may be given multiple times; the `awk`
program consists of the concatenation of the contents of
each specified source-file.

<code>-v var=<i><b>val</b></i></code></br>
<code>--assign var=<i><b>val</b></i></code></br>
Set the variable var to the value val*before*
execution of the program begins.  Such variable values are available
inside the `BEGIN` rule
(see [Other Arguments](#23-other-command-line-arguments)).

The -v option can only set one variable, but it can be used
more than once, setting another variable each time, like this:
&lsquo;awk -v foo=1-v bar=2 &hellip;&rsquo;.

> CAUTION: Using -v to set the values of the built-in
> variables may lead to surprising results.  `awk` will reset the
> values of those variables as it needs to, possibly ignoring any
> initial value you may have given.

<code>-W gawk-opt</code></br>
Provide an implementation-specific option.
This is the POSIX convention for providing implementation-specific options.
These options
also have corresponding GNU-style long options.
Note that the long options may be abbreviated, as long as
the abbreviations remain unique.
The full list of `gawk`-specific options is provided next.

<code>--</code></br>
Signal the end of the command-line options.  The following arguments
are not treated as options even if they begin with &lsquo;-&rsquo;.  This
interpretation of -- follows the POSIX argument parsing
conventions.

This is useful if you have file names that start with &lsquo;-&rsquo;,
or in shell scripts, if you have file names that will be specified
by the user that could start with &lsquo;-&rsquo;.
It is also useful for passing options on to the `awk`
program; see [Getopt Function](#104-processing-command-line-options).

The following list describes `gawk`-specific options:

<code>-b</code></br>
<code>--characters-as-bytes</code></br>
Cause `gawk` to treat all input data as single-byte characters.
In addition, all output written with `print` or `printf`
is treated as single-byte characters.

Normally, `gawk` follows the POSIX standard and attempts to process
its input data according to the current locale (see [Locales](#66-where-you-are-makes-a-difference)). This can often involve
converting multibyte characters into wide characters (internally), and
can lead to problems or confusion if the input data does not contain valid
multibyte characters. This option is an easy way to tell `gawk`,
&ldquo;Hands off my data!&rdquo;

<code>-c</code></br>
<code>--traditional</code></br>
Specify *compatibility mode*, in which the GNU extensions to
the `awk` language are disabled, so that `gawk` behaves just
like BWK `awk`.
See [POSIX/GNU](#POSIX_002fGNU),
which summarizes the extensions.
Also see
[Compatibility Mode](#Compatibility-Mode).

<code>-CA</code>
<code>--copyright</code>
Print the short version of the General Public License and then exit.

<code>-d[<i><b>file</b></i>]</code>
<code>--dump-variables[=<i><b>file</b></i>]</code>
Print a sorted list of global variables, their types, and final values
to file.  If no file is provided, print this
list to a file named awkvars.out in the current directory.
No space is allowed between the -d and file, if
file is supplied.

Having a list of all global variables is a good way to look for
typographical errors in your programs.
You would also use this option if you have a large program with a lot of
functions, and you want to be sure that your functions don&rsquo;t
inadvertently use global variables that you meant to be local.
(This is a particularly easy mistake to make with simple variable
names like `i`, `j`, etc.)

<code>-D[<i><b>file</b></i>]</code>
<code>--debug[=<i><b>file</b></i>]</code>
Enable debugging of `awk` programs
(see [Debugging](#Debugging)).
By default, the debugger reads commands interactively from the keyboard
(standard input).
The optional file argument allows you to specify a file with a list
of commands for the debugger to execute noninteractively.
No space is allowed between the -D and file, if
file is supplied.

<code>-e <i><b>program-text</b></i></code>
<code>--source <i><b>program-text</b></i></code>
Provide program source code in the program-text.
This option allows you to mix source code in files with source
code that you enter on the command line.
This is particularly useful
when you have library functions that you want to use from your command-line
programs (see [AWKPATH Variable](#251-the-awkpath-environment-variable)).

Note that `gawk` treats each string as if it ended with
a newline character (even if it doesn&rsquo;t). This makes building
the total program easier.

> CAUTION: At the moment, there is no requirement that each program-text
> be a full syntactic unit. I.e., the following currently works:
> 
> 
> 
>     $ gawk -e 'BEGIN { a = 5 ;' -e 'print a }'
>     -| 5
>     
> 
> 
> 
> However, this could change in the future, so it&rsquo;s not a
> good idea to rely upon this feature.

<code>-E <i><b>file</b></i></code>
<code>--exec <i><b>file</b></i></code>
Similar to -f, read `awk` program text from file.
There are two differences from -f:

-  This option terminates option processing; anything
else on the command line is passed on directly to the `awk` program.

-  Command-line variable assignments of the form
&lsquo;var=value&rsquo; are disallowed.

This option is particularly necessary for World Wide Web CGI applications
that pass arguments through the URL; using this option prevents a malicious
(or other) user from passing in options, assignments, or `awk` source
code (via -e) to the CGI application.[11](#FOOT11)
This option should be used
with &lsquo;#!&rsquo; scripts (see [Executable Scripts](#114-executable-awk-programs)), like so:

```awk
    #! /usr/local/bin/gawk -E
    
    awk program here &hellip;
```

<code>-g</code></br>
<code>--gen-pot</code></br>
Analyze the source program and
generate a GNU `gettext` portable object template file on standard
output for all string constants that have been marked for translation.
See [Internationalization](#Internationalization),
for information about this option.

<code>-h</code></br>
<code>--help</code></br>
Print a &ldquo;usage&rdquo; message summarizing the short- and long-style options
that `gawk` accepts and then exit.

<code>-i <i><b>source-file</b></i></code></br>
<code>--include <i><b>source-file</b></i></code></br>
Read an `awk` source library from source-file.  This option
is completely equivalent to using the `@include` directive inside
your program.  It is very similar to the -f option,
but there are two important differences.  First, when -i is
used, the program source is not loaded if it has been previously
loaded, whereas with -f, `gawk` always loads the file.
Second, because this option is intended to be used with code libraries,
`gawk` does not recognize such files as constituting main program
input.  Thus, after processing an -i argument, `gawk`
still expects to find the main source code via the -f option
or on the command line.

<code>-l <i><b>ext</b></i></code></br>
<code>--load <i><b>ext</b></i></code></br>
Load a dynamic extension named ext. Extensions
are stored as system shared libraries.
This option searches for the library using the `AWKLIBPATH`
environment variable.  The correct library suffix for your platform will be
supplied by default, so it need not be specified in the extension name.
The extension initialization routine should be named `dl_load()`.
An alternative is to use the `@load` keyword inside the program to load
a shared library.  This advanced feature is described in detail in [Dynamic Extensions](#Dynamic-Extensions).

<code>-L[<i><b>value</b></i>]</code></br>
<code>--lint[=<i><b>value</b></i>]</code></br>
Warn about constructs that are dubious or nonportable to
other `awk` implementations.
No space is allowed between the -L and value, if
value is supplied.
Some warnings are issued when `gawk` first reads your program.  Others
are issued at runtime, as your program executes.
With an optional argument of &lsquo;fatal&rsquo;,
lint warnings become fatal errors.
This may be drastic, but its use will certainly encourage the
development of cleaner `awk` programs.
With an optional argument of &lsquo;invalid&rsquo;, only warnings about things
that are actually invalid are issued. (This is not fully implemented yet.)

Some warnings are only printed once, even if the dubious constructs they
warn about occur multiple times in your `awk` program.  Thus,
when eliminating problems pointed out by --lint, you should take
care to search for all occurrences of each inappropriate construct. As
`awk` programs are usually short, doing so is not burdensome.

<code>-M</code></br>
<code>--bignum</code></br>
Select arbitrary-precision arithmetic on numbers. This option has no effect
if `gawk` is not compiled to use the GNU MPFR and MP libraries
(see [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)).

<code>-n</code></br>
<code>--non-decimal-data</code></br>
Enable automatic interpretation of octal and hexadecimal
values in input data
(see [Nondecimal Data](#Nondecimal-Data)).

> CAUTION: This option can severely break old programs.  Use with care.  Also note
> that this option may disappear in a future version of `gawk`.

<code>-N</code></br>
<code>--use-lc-numeric</code></br>
Force the use of the locale&rsquo;s decimal point character
when parsing numeric input data (see [Locales](#66-where-you-are-makes-a-difference)).

<code>-o[<i><b>file</b></i>]</code></br>
<code>--pretty-print[=<i><b>file</b></i>]</code></br>
Enable pretty-printing of `awk` programs.
Implies --no-optimize.
By default, the output program is created in a file named awkprof.out
(see [Profiling](#Profiling)).
The optional file argument allows you to specify a different
file name for the output.
No space is allowed between the -o and file, if
file is supplied.

> NOTE: In the past, this option would also execute your program.
> This is no longer the case.

<code>-O</code></br>
<code>--optimize</code></br>
Enable `gawk`&rsquo;s default optimizations on the internal
representation of the program.  At the moment, this includes simple
constant folding and tail recursion elimination in function calls.

These optimizations are enabled by default.
This option remains primarily for backwards compatibility. However, it may
be used to cancel the effect of an earlier -s option
(see later in this list).

<code>-p[<b><i>file</i></b>]</code></br>
<code>--profile[=<b><i>file</i></b>]</code></br>
Enable profiling of `awk` programs
(see [Profiling](#Profiling)).
Implies --no-optimize.
By default, profiles are created in a file named awkprof.out.
The optional file argument allows you to specify a different
file name for the profile file.
No space is allowed between the -p and file, if
file is supplied.

The profile contains execution counts for each statement in the program
in the left margin, and function call counts for each function.

<code>-P</code></br>
<code>--posix</code></br>
Operate in strict POSIX mode.  This disables all `gawk`
extensions (just like --traditional) and
disables all extensions not allowed by POSIX.
See [Common Extensions](#Common-Extensions) for a summary of the extensions
in `gawk` that are disabled by this option.
Also,
the following additional
restrictions apply:

- Newlines are not allowed after &lsquo;?&rsquo; or &lsquo;:&rsquo;
(see [Conditional Exp](#634-conditional-expressions)).

- Specifying &lsquo;-Ft&rsquo; on the command line does not set the value
of `FS` to be a single TAB character
(see [Field Separators](#45-specifying-how-fields-are-separated)).

- The locale&rsquo;s decimal point character is used for parsing input
data (see [Locales](#66-where-you-are-makes-a-difference)).

If you supply both --traditional and --posix on the
command line, --posix takes precedence. `gawk`
issues a warning if both options are supplied.

<code>-r</code></br>
<code>--re-interval</code></br>
Allow interval expressions
(see [Regexp Operators](#33-regular-expression-operators))
in regexps.
This is now `gawk`&rsquo;s default behavior.
Nevertheless, this option remains (both for backward compatibility
and for use in combination with --traditional).

<code>-s</code></br>
<code>--no-optimize</code></br>
Disable `gawk`&rsquo;s default optimizations on the internal
representation of the program.

<code>-S</code></br>
<code>--sandbox</code></br>
Disable the `system()` function,
input redirections with `getline`,
output redirections with `print` and `printf`,
and dynamic extensions.
This is particularly useful when you want to run `awk` scripts
from questionable sources and need to make sure the scripts
can&rsquo;t access your system (other than the specified input data file).

<code>-t</code></br>
<code>--lint-old</code></br>
Warn about constructs that are not available in the original version of
`awk` from Version 7 Unix
(see [V7/SVR3.1](#V7_002fSVR3_002e1)).

<code>-V</code></br>
<code>--version</code></br>
Print version information for this particular copy of `gawk`.
This allows you to determine if your copy of `gawk` is up to date
with respect to whatever the Free Software Foundation is currently
distributing.
It is also useful for bug reports
(see [Bugs](#Bugs)).

As long as program text has been supplied,
any other options are flagged as invalid with a warning message but
are otherwise ignored.

In compatibility mode, as a special case, if the value of fs supplied
to the -F option is &lsquo;t&rsquo;, then `FS` is set to the TAB
character (`"\t"`).  This is true only for --traditional and not
for --posix
(see [Field Separators](#45-specifying-how-fields-are-separated)).

The `-f` option may be used more than once on the command line.
If it is, `awk` reads its program source from all of the named files, as
if they had been concatenated together into one big file.  This is
useful for creating libraries of `awk` functions.  These functions
can be written once and then retrieved from a standard place, instead
of having to be included in each individual program.
The -i option is similar in this regard.
(As mentioned in
[Definition Syntax](#921-function-definition-syntax),
function names must be unique.)

With standard `awk`, library functions can still be used, even
if the program is entered at the keyboard,
by specifying &lsquo;-f /dev/tty&rsquo;.  After typing your program,
type Ctrl-d (the end-of-file character) to terminate it.
(You may also use &lsquo;-f -&rsquo; to read program source from the standard
input, but then you will not be able to also use the standard input as a
source of data.)

Because it is clumsy using the standard `awk` mechanisms to mix
source file and command-line `awk` programs, `gawk`
provides the -e option.  This does not require you to
preempt the standard input for your source code; it allows you to easily
mix command-line and library source code (see [AWKPATH Variable](#251-the-awkpath-environment-variable)).
As with -f, the -e and -i
options may also be used multiple times on the command line.

If no -f or -e option is specified, then `gawk`
uses the first nonoption command-line argument as the text of the
program source code.

If the environment variable `POSIXLY_CORRECT` exists,
then `gawk` behaves in strict POSIX mode, exactly as if
you had supplied --posix.
Many GNU programs look for this environment variable to suppress
extensions that conflict with POSIX, but `gawk` behaves
differently: it suppresses all extensions, even those that do not
conflict with POSIX, and behaves in
strict POSIX mode. If --lint is supplied on the command line
and `gawk` turns on POSIX mode because of `POSIXLY_CORRECT`,
then it issues a warning message indicating that POSIX
mode is in effect.
You would typically set this variable in your shell&rsquo;s startup file.
For a Bourne-compatible shell (such as Bash), you would add these
lines to the .profile file in your home directory:

    POSIXLY_CORRECT=true
    export POSIXLY_CORRECT
    

For a C shell-compatible
shell,[12](#FOOT12)
you would add this line to the .login file in your home directory:

    setenv POSIXLY_CORRECT true
    

Having `POSIXLY_CORRECT` set is not recommended for daily use,
but it is good for testing the portability of your programs to other
environments.

---

Next: [Naming Standard Input](#24-naming-standard-input), Previous: [Options](#22-command-line-options), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.3 Other Command-Line Arguments

Any additional arguments on the command line are normally treated as
input files to be processed in the order specified.   However, an
argument that has the form `var=value`, assigns
the value value to the variable var&mdash;it does not specify a
file at all.  (See [Assignment Options](#6132-assigning-variables-on-the-command-line).) In the following example,
count=1 is a variable assignment, not a file name:

<pre>  awk -f <i>program.awk file1 count=1 file2</i></pre>
    

All the command-line arguments are made available to your `awk` program in the
`ARGV` array (see [Built-in Variables](#75-predefined-variables)).  Command-line options
and the program text (if present) are omitted from `ARGV`.
All other arguments, including variable assignments, are
included.   As each element of `ARGV` is processed, `gawk`
sets `ARGIND` to the index in `ARGV` of the
current element.

Changing `ARGC` and `ARGV` in your `awk` program lets
you control how `awk` processes the input files; this is described
in more detail in [ARGC and ARGV](#753-using-argc-and-argv).

The distinction between file name arguments and variable-assignment
arguments is made when `awk` is about to open the next input file.
At that point in execution, it checks the file name to see whether
it is really a variable assignment; if so, `awk` sets the variable
instead of reading a file.

Therefore, the variables actually receive the given values after all
previously specified files have been read.  In particular, the values of
variables assigned in this fashion are *not* available inside a
`BEGIN` rule
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)),
because such rules are run before `awk` begins scanning the argument list.

The variable values given on the command line are processed for escape
sequences (see [Escape Sequences](#32-escape-sequences)).
(d.c.)

In some very early implementations of `awk`, when a variable assignment
occurred before any file names, the assignment would happen *before*
the `BEGIN` rule was executed.  `awk`&rsquo;s behavior was thus
inconsistent; some command-line assignments were available inside the
`BEGIN` rule, while others were not.  Unfortunately,
some applications came to depend
upon this &ldquo;feature.&rdquo;  When `awk` was changed to be more consistent,
the -v option was added to accommodate applications that depended
upon the old behavior.

The variable assignment feature is most useful for assigning to variables
such as `RS`, `OFS`, and `ORS`, which control input and
output formats, before scanning the data files.  It is also useful for
controlling state if multiple passes are needed over a data file.  For
example:

```awk
  awk 'pass == 1  { pass 1 stuff }
       pass == 2  { pass 2 stuff }' pass=1 mydata pass=2 mydata
```    

Given the variable assignment feature, the -F option for setting
the value of `FS` is not
strictly necessary.  It remains for historical compatibility.

---

Next: [Environment Variables](#25-the-environment-variables-gawk-uses), Previous: [Other Arguments](#23-other-command-line-arguments), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.4 Naming Standard Input

Often, you may wish to read standard input together with other files.
For example, you may wish to read one file, read standard input coming
from a pipe, and then read another file.

The way to name the standard input, with all versions of `awk`,
is to use a single, standalone minus sign or dash, &lsquo;-&rsquo;.  For example:

    some_command | awk -f myprog.awk file1 - file2
    

Here, `awk` first reads file1, then it reads
the output of some_command, and finally it reads
file2.

You may also use `"-"` to name standard input when reading
files with `getline` (see [Getline/File](#4103-using-getline-from-a-file)).

In addition, `gawk` allows you to specify the special
file name /dev/stdin, both on the command line and
with `getline`.
Some other versions of `awk` also support this, but it
is not standard.
(Some operating systems provide a /dev/stdin file
in the filesystem; however, `gawk` always processes
this file name itself.)

---

Next: [Exit Status](#26-gawkrsquos-exit-status), Previous: [Naming Standard Input](#24-naming-standard-input), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.5 The Environment Variables `gawk` Uses

A number of environment variables influence how `gawk`
behaves.

| []() | []()
| - | -
| &bull; [AWKPATH Variable](#251-the-awkpath-environment-variable) | Searching directories for `awk` programs.
| &bull; [AWKLIBPATH Variable](#252-the-awklibpath-environment-variable) | Searching directories for `awk` shared libraries.
| &bull; [Other Environment Variables](#253-other-environment-variables) | The environment variables.

---

Next: [AWKLIBPATH Variable](#252-the-awklibpath-environment-variable), Up: [Environment Variables](#25-the-environment-variables-gawk-uses)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 2.5.1 The `AWKPATH` Environment Variable

In most `awk`
implementations, you must supply a precise pathname for each program
file, unless the file is in the current directory.
But with `gawk`, if the file name supplied to the -f
or -i options
does not contain a directory separator &lsquo;/&rsquo;, then `gawk` searches a list of
directories (called the *search path*) one by one, looking for a
file with the specified name.

The search path is a string consisting of directory names
separated by colons.[13](#FOOT13)`gawk` gets its search path from the
`AWKPATH` environment variable.  If that variable does not exist,
or if it has an empty value,
`gawk` uses a default path (described shortly).

The search path feature is particularly helpful for building libraries
of useful `awk` functions.  The library files can be placed in a
standard directory in the default path and then specified on
the command line with a short file name.  Otherwise, you would have to
type the full file name for each file.

By using the -i or -f options, your command-line
`awk` programs can use facilities in `awk` library files
(see [Library Functions]((#10-a-library-of-awk-functions))).
Path searching is not done if `gawk` is in compatibility mode.
This is true for both --traditional and --posix.
See [Options](#22-command-line-options).

If the source code file is not found after the initial search, the path is searched
again after adding the suffix &lsquo;.awk&rsquo; to the file name.

`gawk`&rsquo;s path search mechanism is similar
to the shell&rsquo;s.
(See [The Bourne-Again SHell manual](https://www.gnu.org/software/bash/manual/).)
It treats a null entry in the path as indicating the current
directory.
(A null entry is indicated by starting or ending the path with a
colon or by placing two colons next to each other [&lsquo;::&rsquo;].)

> NOTE: To include the current directory in the path, either place .
> as an entry in the path or write a null entry in the path.
> 
> 
> Different past versions of `gawk` would also look explicitly in
> the current directory, either before or after the path search.  As of
> version 4.1.2, this no longer happens; if you wish to look
> in the current directory, you must include . either as a separate
> entry or as a null entry in the search path.

The default value for `AWKPATH` is
&lsquo;.:/usr/local/share/awk&rsquo;.[14](#FOOT14)  Since . is included at the beginning, `gawk`
searches first in the current directory and then in /usr/local/share/awk.
In practice, this means that you will rarely need to change the
value of `AWKPATH`.

See [Shell Startup Files](#Shell-Startup-Files), for information on functions that help to
manipulate the `AWKPATH` variable.

`gawk` places the value of the search path that it used into
`ENVIRON["AWKPATH"]`. This provides access to the actual search
path value from within an `awk` program.

Although you can change `ENVIRON["AWKPATH"]` within your `awk`
program, this has no effect on the running program&rsquo;s behavior.  This makes
sense: the `AWKPATH` environment variable is used to find the program
source files.  Once your program is running, all the files have been
found, and `gawk` no longer needs to use `AWKPATH`.

---

Next: [Other Environment Variables](#253-other-environment-variables), Previous: [AWKPATH Variable](#251-the-awkpath-environment-variable), Up: [Environment Variables](#25-the-environment-variables-gawk-uses)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 2.5.2 The `AWKLIBPATH` Environment Variable

The `AWKLIBPATH` environment variable is similar to the `AWKPATH`
variable, but it is used to search for loadable extensions (stored as
system shared libraries) specified with the -l option rather
than for source files.  If the extension is not found, the path is
searched again after adding the appropriate shared library suffix for
the platform.  For example, on GNU/Linux systems, the suffix &lsquo;.so&rsquo;
is used.  The search path specified is also used for extensions loaded
via the `@load` keyword (see [Loading Shared Libraries](#28-loading-dynamic-extensions-into-your-program)).

If `AWKLIBPATH` does not exist in the environment, or if it has
an empty value, `gawk` uses a default path; this
is typically &lsquo;/usr/local/lib/gawk&rsquo;, although it can vary depending
upon how `gawk` was built.

See [Shell Startup Files](#Shell-Startup-Files), for information on functions that help to
manipulate the `AWKLIBPATH` variable.

`gawk` places the value of the search path that it used into
`ENVIRON["AWKLIBPATH"]`. This provides access to the actual search
path value from within an `awk` program.

Although you can change `ENVIRON["AWKLIBPATH"]` within your
`awk` program, this has no effect on the running program&rsquo;s
behavior.  This makes sense: the `AWKLIBPATH` environment variable
is used to find any requested extensions, and they are loaded before
the program starts to run.  Once your program is running, all the
extensions have been found, and `gawk` no longer needs to use
`AWKLIBPATH`.

---

Previous: [AWKLIBPATH Variable](#252-the-awklibpath-environment-variable), Up: [Environment Variables](#25-the-environment-variables-gawk-uses)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 2.5.3 Other Environment Variables

A number of other environment variables affect `gawk`&rsquo;s
behavior, but they are more specialized. Those in the following
list are meant to be used by regular users:

`GAWK_MSEC_SLEEP`
Specifies the interval between connection retries,
in milliseconds. On systems that do not support
the `usleep()` system call,
the value is rounded up to an integral number of seconds.

`GAWK_READ_TIMEOUT`
Specifies the time, in milliseconds, for `gawk` to
wait for input before returning with an error.
See [Read Timeout](#411-reading-input-with-a-timeout).

`GAWK_SOCK_RETRIES`
Controls the number of times `gawk` attempts to
retry a two-way TCP/IP (socket) connection before giving up.
See [TCP/IP Networking](#TCP_002fIP-Networking).
Note that when nonfatal I/O is enabled (see [Nonfatal](#510-enabling-nonfatal-output)),
`gawk` only tries to open a TCP/IP socket once.

`POSIXLY_CORRECT`
Causes `gawk` to switch to POSIX-compatibility
mode, disabling all traditional and GNU extensions.
See [Options](#22-command-line-options).

The environment variables in the following list are meant
for use by the `gawk` developers for testing and tuning.
They are subject to change. The variables are:

`AWKBUFSIZE`
This variable only affects `gawk` on POSIX-compliant systems.
With a value of &lsquo;exact&rsquo;, `gawk` uses the size of each input
file as the size of the memory buffer to allocate for I/O. Otherwise,
the value should be a number, and `gawk` uses that number as
the size of the buffer to allocate.  (When this variable is not set,
`gawk` uses the smaller of the file&rsquo;s size and the &ldquo;default&rdquo;
blocksize, which is usually the filesystem&rsquo;s I/O blocksize.)

`AWK_HASH`
If this variable exists with a value of &lsquo;gst&rsquo;, `gawk`
switches to using the hash function from GNU Smalltalk for
managing arrays.
This function may be marginally faster than the standard function.

`AWKREADFUNC`
If this variable exists, `gawk` switches to reading source
files one line at a time, instead of reading in blocks. This exists
for debugging problems on filesystems on non-POSIX operating systems
where I/O is performed in records, not in blocks.

`GAWK_MSG_SRC`
If this variable exists, `gawk` includes the file name
and line number within the `gawk` source code
from which warning and/or fatal messages
are generated.  Its purpose is to help isolate the source of a
message, as there are multiple places that produce the
same warning or error message.

`GAWK_LOCALE_DIR`
Specifies the location of compiled message object files
for `gawk` itself. This is passed to the `bindtextdomain()`
function when `gawk` starts up.

`GAWK_NO_DFA`
If this variable exists, `gawk` does not use the DFA regexp matcher
for &ldquo;does it match&rdquo; kinds of tests. This can cause `gawk`
to be slower. Its purpose is to help isolate differences between the
two regexp matchers that `gawk` uses internally. (There aren&rsquo;t
supposed to be differences, but occasionally theory and practice don&rsquo;t
coordinate with each other.)

`GAWK_STACKSIZE`
This specifies the amount by which `gawk` should grow its
internal evaluation stack, when needed.

`INT_CHAIN_MAX`
This specifies intended maximum number of items `gawk` will maintain on a
hash chain for managing arrays indexed by integers.

`STR_CHAIN_MAX`
This specifies intended maximum number of items `gawk` will maintain on a
hash chain for managing arrays indexed by strings.

`TIDYMEM`
If this variable exists, `gawk` uses the `mtrace()` library
calls from the GNU C library to help track down possible memory leaks.

---

Next: [Include Files](#27-including-other-files-into-your-program), Previous: [Environment Variables](#25-the-environment-variables-gawk-uses), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.6 `gawk`&rsquo;s Exit Status

If the `exit` statement is used with a value
(see [Exit Statement](#7410-the-exit-statement)), then `gawk` exits with
the numeric value given to it.

Otherwise, if there were no problems during execution,
`gawk` exits with the value of the C constant
`EXIT_SUCCESS`.  This is usually zero.

If an error occurs, `gawk` exits with the value of
the C constant `EXIT_FAILURE`.  This is usually one.

If `gawk` exits because of a fatal error, the exit
status is two.  On non-POSIX systems, this value may be mapped
to `EXIT_FAILURE`.

---

Next: [Loading Shared Libraries](#28-loading-dynamic-extensions-into-your-program), Previous: [Exit Status](#26-gawkrsquos-exit-status), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.7 Including Other Files into Your Program

This section describes a feature that is specific to `gawk`.

The `@include` keyword can be used to read external `awk` source
files.  This gives you the ability to split large `awk` source files
into smaller, more manageable pieces, and also lets you reuse common `awk`
code from various `awk` scripts.  In other words, you can group
together `awk` functions used to carry out specific tasks
into external files. These files can be used just like function libraries,
using the `@include` keyword in conjunction with the `AWKPATH`
environment variable.  Note that source files may also be included
using the -i option.

Let&rsquo;s see an example.
We&rsquo;ll start with two (trivial) `awk` scripts, namely
test1 and test2. Here is the test1 script:

```awk
    BEGIN {
        print "This is script test1."
    }
```    

and here is test2:

```awk
    @include "test1"
    BEGIN {
        print "This is script test2."
    }
```    

Running `gawk` with test2
produces the following result:

```awk
    $ gawk -f test2
    -| This is script test1.
    -| This is script test2.
```    

`gawk` runs the test2 script, which includes test1
using the `@include`
keyword.  So, to include external `awk` source files, you just
use `@include` followed by the name of the file to be included,
enclosed in double quotes.

> NOTE: Keep in mind that this is a language construct and the file name cannot
> be a string variable, but rather just a literal string constant in double quotes.

The files to be included may be nested; e.g., given a third
script, namely test3:

```awk
    @include "test2"
    BEGIN {
        print "This is script test3."
    }
```    

Running `gawk` with the test3 script produces the
following results:

```awk
    $ gawk -f test3
    -| This is script test1.
    -| This is script test2.
    -| This is script test3.
```    

The file name can, of course, be a pathname. For example:

```awk
    @include "../io_funcs"
```    

and:

```awk
    @include "/usr/awklib/network"
```    

are both valid. The `AWKPATH` environment variable can be of great
value when using `@include`. The same rules for the use
of the `AWKPATH` variable in command-line file searches
(see [AWKPATH Variable](#251-the-awkpath-environment-variable)) apply to
`@include` also.

This is very helpful in constructing `gawk` function libraries.
If you have a large script with useful, general-purpose `awk`
functions, you can break it down into library files and put those files
in a special directory.  You can then include those &ldquo;libraries,&rdquo;
either by using the full pathnames of the files, or by setting the `AWKPATH`
environment variable accordingly and then using `@include` with
just the file part of the full pathname. Of course,
you can keep library files in more than one directory;
the more complex the working
environment is, the more directories you may need to organize the files
to be included.

Given the ability to specify multiple -f options, the
`@include` mechanism is not strictly necessary.
However, the `@include` keyword
can help you in constructing self-contained `gawk` programs,
thus reducing the need for writing complex and tedious command lines.
In particular, `@include` is very useful for writing CGI scripts
to be run from web pages.

The rules for finding a source file described in [AWKPATH Variable](#251-the-awkpath-environment-variable) also
apply to files loaded with `@include`.

---

Next: [Obsolete](#29-obsolete-options-andor-features), Previous: [Include Files](#27-including-other-files-into-your-program), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.8 Loading Dynamic Extensions into Your Program

This section describes a feature that is specific to `gawk`.

The `@load` keyword can be used to read external `awk` extensions
(stored as system shared libraries).
This allows you to link in compiled code that may offer superior
performance and/or give you access to extended capabilities not supported
by the `awk` language.  The `AWKLIBPATH` variable is used to
search for the extension.  Using `@load` is completely equivalent
to using the -l command-line option.

If the extension is not initially found in `AWKLIBPATH`, another
search is conducted after appending the platform&rsquo;s default shared library
suffix to the file name.  For example, on GNU/Linux systems, the suffix
&lsquo;.so&rsquo; is used:

```awk
    $ gawk '@load "ordchr"; BEGIN {print chr(65)}'
    -| A
```    

This is equivalent to the following example:

```awk
    $ gawk -lordchr 'BEGIN {print chr(65)}'
    -| A
```    

For command-line usage, the -l option is more convenient,
but `@load` is useful for embedding inside an `awk` source file
that requires access to an extension.

[Dynamic Extensions](#Dynamic-Extensions), describes how to write extensions (in C or C++)
that can be loaded with either `@load` or the -l option.
It also describes the `ordchr` extension.

---

Next: [Undocumented](#210-undocumented-options-and-features), Previous: [Loading Shared Libraries](#28-loading-dynamic-extensions-into-your-program), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.9 Obsolete Options and/or Features

This section describes features and/or command-line options from
previous releases of `gawk` that either are not available in the
current version or are still supported but deprecated (meaning that
they will *not* be in the next release).

The process-related special files /dev/pid, /dev/ppid,
/dev/pgrpid, and /dev/user were deprecated in `gawk`
3.1, but still worked.  As of version 4.0, they are no longer
interpreted specially by `gawk`.  (Use `PROCINFO` instead;
see [Auto-set](#752-built-in-variables-that-convey-information).)

---

Next: [Invoking Summary](#211-summary), Previous: [Obsolete](#29-obsolete-options-andor-features), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.10 Undocumented Options and Features

> Use the Source, Luke!

&mdash; *Obi-Wan*

This section intentionally left
blank.

---

Previous: [Undocumented](#210-undocumented-options-and-features), Up: [Invoking Gawk](#2-running-awk-and-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 2.11 Summary

-  Use either
&lsquo;awk 'program' files&rsquo;
or
&lsquo;awk -f program-filefiles&rsquo;
to run `awk`.

-  The three standard options for all versions of `awk` are
-f, -F, and -v.  `gawk` supplies these
and many others, as well as corresponding GNU-style long options.

-  Nonoption command-line arguments are usually treated as file names,
unless they have the form &lsquo;var=value&rsquo;, in which case
they are taken as variable assignments to be performed at that point
in processing the input.

-  All nonoption command-line arguments, excluding the program text,
are placed in the `ARGV` array.  Adjusting `ARGC` and `ARGV`
affects how `awk` processes input.

-  You can use a single minus sign (&lsquo;-&rsquo;) to refer to standard input
on the command line. `gawk` also lets you use the special
file name /dev/stdin.

- `gawk` pays attention to a number of environment variables.
`AWKPATH`, `AWKLIBPATH`, and `POSIXLY_CORRECT` are the
most important ones.

- `gawk`&rsquo;s exit status conveys information to the program
that invoked it. Use the `exit` statement from within
an `awk` program to set the exit status.

- `gawk` allows you to include other `awk` source files into
your program using the `@include` statement and/or the -i
and -f command-line options.

- `gawk` allows you to load additional functions written in C
or C++ using the `@load` statement and/or the -l option.
(This advanced feature is described later, in [Dynamic Extensions](#Dynamic-Extensions).)

---

Next: [Reading Files](#4-reading-input-files), Previous: [Invoking Gawk](#2-running-awk-and-gawk), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 3 Regular Expressions

A *regular expression*, or *regexp*, is a way of describing a
set of strings.
Because regular expressions are such a fundamental part of `awk`
programming, their format and use deserve a separate chapter.

A regular expression enclosed in slashes (&lsquo;/&rsquo;)
is an `awk` pattern that matches every input record whose text
belongs to that set.
The simplest regular expression is a sequence of letters, numbers, or
both.  Such a regexp matches any string that contains that sequence.
Thus, the regexp &lsquo;foo&rsquo; matches any string containing &lsquo;foo&rsquo;.
Thus, the pattern `/foo/` matches any input record containing
the three adjacent characters &lsquo;foo&rsquo; *anywhere* in the record.  Other
kinds of regexps let you specify more complicated classes of strings.

Initially, the examples in this chapter are simple.
As we explain more about how
regular expressions work, we present more complicated instances.

&bull; [Regexp Usage](#31-how-to-use-regular-expressions):  How to Use Regular Expressions.
&bull; [Escape Sequences](#32-escape-sequences):  How to write nonprinting characters.
&bull; [Regexp Operators](#33-regular-expression-operators):  Regular Expression Operators.
&bull; [Bracket Expressions](#34-using-bracket-expressions):  What can go between &lsquo;[...]&rsquo;.
&bull; [Leftmost Longest](#35-how-much-text-matches):  How much text matches.
&bull; [Computed Regexps](#36-using-dynamic-regexps):  Using Dynamic Regexps.
&bull; [GNU Regexp Operators](#37-gawk-specific-regexp-operators):  Operators specific to GNU software.
&bull; [Case-sensitivity](#38-case-sensitivity-in-matching):  How to do case-insensitive matching.
&bull; [Regexp Summary](#39-summary):  Regular expressions summary.

---

Next: [Escape Sequences](#32-escape-sequences), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.1 How to Use Regular Expressions

A regular expression can be used as a pattern by enclosing it in
slashes.  Then the regular expression is tested against the
entire text of each record.  (Normally, it only needs
to match some part of the text in order to succeed.)  For example, the
following prints the second field of each record where the string
&lsquo;li&rsquo; appears anywhere in the record:

    $ awk '/li/ { print $2 }' mail-list
    -| 555-5553
    -| 555-0542
    -| 555-6699
    -| 555-3430
    

Regular expressions can also be used in matching expressions.  These
expressions allow you to specify the string to match against; it need
not be the entire current input record.  The two operators &lsquo;~&rsquo;
and &lsquo;!~&rsquo; perform regular expression comparisons.  Expressions
using these operators can be used as patterns, or in `if`,
`while`, `for`, and `do` statements.
(See [Statements](#74-control-statements-in-actions).)
For example, the following is true if the expression exp (taken
as a string) matches regexp:

    exp ~ /regexp/
    

This example matches, or selects, all input records with the uppercase
letter &lsquo;J&rsquo; somewhere in the first field:

```awk
    $ awk '$1 ~ /J/' inventory-shipped
    -| Jan  13  25  15 115
    -| Jun  31  42  75 492
    -| Jul  24  34  67 436
    -| Jan  21  36  64 620
```    

So does this:

```awk
    awk '{ if ($1 ~ /J/) print }' inventory-shipped
```    

This next example is true if the expression exp
(taken as a character string)
does *not* match regexp:

```awk
    exp !~ /regexp/
```    

The following example matches,
or selects, all input records whose first field *does not* contain
the uppercase letter &lsquo;J&rsquo;:

```awk
    $ awk '$1 !~ /J/' inventory-shipped
    -| Feb  15  32  24 226
    -| Mar  15  24  34 228
    -| Apr  31  52  63 420
    -| May  16  34  29 208
    &hellip;
```    

When a regexp is enclosed in slashes, such as `/foo/`, we call it
a *regexp constant*, much like `5.27` is a numeric constant and
`"foo"` is a string constant.

---

Next: [Regexp Operators](#33-regular-expression-operators), Previous: [Regexp Usage](#31-how-to-use-regular-expressions), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.2 Escape Sequences

Some characters cannot be included literally in string constants
(`"foo"`) or regexp constants (`/foo/`).
Instead, they should be represented with *escape sequences*,
which are character sequences beginning with a backslash (&lsquo;\&rsquo;).
One use of an escape sequence is to include a double-quote character in
a string constant.  Because a plain double quote ends the string, you
must use &lsquo;\"&rsquo; to represent an actual double-quote character as a
part of the string.  For example:

```awk
    $ awk 'BEGIN { print "He said \"hi!\" to her." }'
    -| He said "hi!" to her.
```    

The  backslash character itself is another character that cannot be
included normally; you must write &lsquo;\\&rsquo; to put one backslash in the
string or regexp.  Thus, the string whose contents are the two characters
&lsquo;"&rsquo; and &lsquo;\&rsquo; must be written `"\"\\"`.

Other escape sequences represent unprintable characters
such as TAB or newline.  There is nothing to stop you from entering most
unprintable characters directly in a string constant or regexp constant,
but they may look ugly.

The following list presents
all the escape sequences used in `awk` and
what they represent. Unless noted otherwise, all these escape
sequences apply to both string constants and regexp constants:

<code>\\</code>
A literal backslash, &lsquo;\&rsquo;.

<code>\a</code>
The &ldquo;alert&rdquo; character, Ctrl-g, ASCII code 7 (BEL).
(This often makes some sort of audible noise.)

<code>\b</code>
Backspace, Ctrl-h, ASCII code 8 (BS).

<code>\f</code>
Formfeed, Ctrl-l, ASCII code 12 (FF).

<code>\n</code>
Newline, Ctrl-j, ASCII code 10 (LF).

<code>\r</code>
Carriage return, Ctrl-m, ASCII code 13 (CR).

<code>\t</code>
Horizontal TAB, Ctrl-i, ASCII code 9 (HT).

<code>\v</code>
Vertical TAB, Ctrl-k, ASCII code 11 (VT).

<code>\nnn</code>
The octal value nnn, where nnn stands for 1 to 3 digits
between &lsquo;0&rsquo; and &lsquo;7&rsquo;.  For example, the code for the ASCII ESC
(escape) character is &lsquo;\033&rsquo;.

<code>\xhh&hellip;</code>
The hexadecimal value hh, where hh stands for a sequence
of hexadecimal digits (&lsquo;0&rsquo;&ndash;&lsquo;9&rsquo;, and either &lsquo;A&rsquo;&ndash;&lsquo;F&rsquo;
or &lsquo;a&rsquo;&ndash;&lsquo;f&rsquo;).  A maximum of two digts are allowed after
the &lsquo;\x&rsquo;. Any further hexadecimal digits are treated as simple
letters or numbers.  (c.e.)
(The &lsquo;\x&rsquo; escape sequence is not allowed in POSIX awk.)

> CAUTION: In ISO C, the escape sequence continues until the first nonhexadecimal
> digit is seen.
> For many years, `gawk` would continue incorporating
> hexadecimal digits into the value until a non-hexadecimal digit
> or the end of the string was encountered.
> However, using more than two hexadecimal digits produced
> undefined results.
> As of version 4.2, only two digits
> are processed.

<code>\/</code>
A literal slash (necessary for regexp constants only).
This sequence is used when you want to write a regexp
constant that contains a slash
(such as `/.*:\/home\/[[:alnum:]]+:.*/`; the &lsquo;[[:alnum:]]&rsquo;
notation is discussed in [Bracket Expressions](#34-using-bracket-expressions)).
Because the regexp is delimited by
slashes, you need to escape any slash that is part of the pattern,
in order to tell `awk` to keep processing the rest of the regexp.

<code>\"</code>
A literal double quote (necessary for string constants only).
This sequence is used when you want to write a string
constant that contains a double quote
(such as `"He said \"hi!\" to her."`).
Because the string is delimited by
double quotes, you need to escape any quote that is part of the string,
in order to tell `awk` to keep processing the rest of the string.

In `gawk`, a number of additional two-character sequences that begin
with a backslash have special meaning in regexps.
See [GNU Regexp Operators](#37-gawk-specific-regexp-operators).

In a regexp, a backslash before any character that is not in the previous list
and not listed in
[GNU Regexp Operators](#37-gawk-specific-regexp-operators)
means that the next character should be taken literally, even if it would
normally be a regexp operator.  For example, `/a\+b/` matches the three
characters &lsquo;a+b&rsquo;.

For complete portability, do not use a backslash before any character not
shown in the previous list or that is not an operator.

Backslash Before Regular Characters

If you place a backslash in a string constant before something that is
not one of the characters previously listed, POSIX `awk` purposely
leaves what happens as undefined.  There are two choices:

Strip the backslash out
This is what BWK `awk` and `gawk` both do.
For example, `"a\qc"` is the same as `"aqc"`.
(Because this is such an easy bug both to introduce and to miss,
`gawk` warns you about it.)
Consider &lsquo;FS = "[ \t]+\|[ \t]+"&rsquo; to use vertical bars
surrounded by whitespace as the field separator. There should be
two backslashes in the string: &lsquo;FS = "[ \t]+\\|[ \t]+"&rsquo;.)

Leave the backslash alone
Some other `awk` implementations do this.
In such implementations, typing `"a\qc"` is the same as typing
`"a\\qc"`.

To summarize:

-  The escape sequences in the preceding list are always processed first,
for both string constants and regexp constants. This happens very early,
as soon as `awk` reads your program.

- `gawk` processes both regexp constants and dynamic regexps
(see [Computed Regexps](#36-using-dynamic-regexps)),
for the special operators listed in
[GNU Regexp Operators](#37-gawk-specific-regexp-operators).

-  A backslash before any other character means to treat that character
literally.

Escape Sequences for Metacharacters

Suppose you use an octal or hexadecimal
escape to represent a regexp metacharacter.
(See [Regexp Operators](#33-regular-expression-operators).)
Does `awk` treat the character as a literal character or as a regexp
operator?

Historically, such characters were taken literally.
(d.c.)
However, the POSIX standard indicates that they should be treated
as real metacharacters, which is what `gawk` does.
In compatibility mode (see [Options](#22-command-line-options)),
`gawk` treats the characters represented by octal and hexadecimal
escape sequences literally when used in regexp constants. Thus,
`/a\52b/` is equivalent to `/a\*b/`.

---

Next: [Bracket Expressions](#34-using-bracket-expressions), Previous: [Escape Sequences](#32-escape-sequences), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.3 Regular Expression Operators

You can combine regular expressions with special characters,
called *regular expression operators* or *metacharacters*, to
increase the power and versatility of regular expressions.

The escape sequences described
earlier
in [Escape Sequences](#32-escape-sequences)
are valid inside a regexp.  They are introduced by a &lsquo;\&rsquo; and
are recognized and converted into corresponding real characters as
the very first step in processing regexps.

Here is a list of metacharacters.  All characters that are not escape
sequences and that are not listed here stand for themselves:

<code>\\</code>
This suppresses the special meaning of a character when
matching.  For example, &lsquo;\$&rsquo;
matches the character &lsquo;$&rsquo;.

<code>^</code>
This matches the beginning of a string.  &lsquo;^@chapter&rsquo;
matches &lsquo;@chapter&rsquo; at the beginning of a string,
for example, and can be used
to identify chapter beginnings in Texinfo source files.
The &lsquo;^&rsquo; is known as an *anchor*, because it anchors the pattern to
match only at the beginning of the string.

It is important to realize that &lsquo;^&rsquo; does not match the beginning of
a line (the point right after a &lsquo;\n&rsquo; newline character) embedded in a string.
The condition is not true in the following example:

```awk
    if ("line1\nLINE 2" ~ /^L/) &hellip;
```    

<code>$</code>
This is similar to &lsquo;^&rsquo;, but it matches only at the end of a string.
For example, &lsquo;p$&rsquo;
matches a record that ends with a &lsquo;p&rsquo;.  The &lsquo;$&rsquo; is an anchor
and does not match the end of a line
(the point right before a &lsquo;\n&rsquo; newline character)
embedded in a string.
The condition in the following example is not true:

```awk
    if ("line1\nLINE 2" ~ /1$/) &hellip;
```    

<code>.(period)</code>
This matches any single character,
*including* the newline character.  For example, &lsquo;.P&rsquo;
matches any single character followed by a &lsquo;P&rsquo; in a string.  Using
concatenation, we can make a regular expression such as &lsquo;U.A&rsquo;, which
matches any three-character sequence that begins with &lsquo;U&rsquo; and ends
with &lsquo;A&rsquo;.

In strict POSIX mode (see [Options](#22-command-line-options)),
&lsquo;.&rsquo; does not match the NUL
character, which is a character with all bits equal to zero.
Otherwise, NUL is just another character. Other versions of `awk`
may not be able to match the NUL character.

<code>[&hellip;]</code>
This is called a *bracket expression*.[15](#FOOT15)
It matches any *one* of the characters that are enclosed in
the square brackets.  For example, &lsquo;[MVX]&rsquo; matches any one of
the characters &lsquo;M&rsquo;, &lsquo;V&rsquo;, or &lsquo;X&rsquo; in a string.  A full
discussion of what can be inside the square brackets of a bracket expression
is given in
[Bracket Expressions](#34-using-bracket-expressions).

<code>[^&hellip;]</code>
This is a *complemented bracket expression*.  The first character after
the &lsquo;[&rsquo; *must* be a &lsquo;^&rsquo;.  It matches any characters
*except* those in the square brackets.  For example, &lsquo;[^awk]&rsquo;
matches any character that is not an &lsquo;a&rsquo;, &lsquo;w&rsquo;,
or &lsquo;k&rsquo;.

<code>|</code>
This is the *alternation operator* and it is used to specify
alternatives.  The &lsquo;|&rsquo; has the lowest precedence of all the regular
expression operators.  For example, &lsquo;^P|[aeiouy]&rsquo; matches any string
that matches either &lsquo;^P&rsquo; or &lsquo;[aeiouy]&rsquo;.  This means it matches
any string that starts with &lsquo;P&rsquo; or contains (anywhere within it)
a lowercase English vowel.

The alternation applies to the largest possible regexps on either side.

<code>(&hellip;)</code>
Parentheses are used for grouping in regular expressions, as in
arithmetic.  They can be used to concatenate regular expressions
containing the alternation operator, &lsquo;|&rsquo;.  For example,
&lsquo;@(samp|code)\{[^}]+\}&rsquo; matches both &lsquo;@code{foo}&rsquo; and
&lsquo;@samp{bar}&rsquo;.
(These are Texinfo formatting control sequences. The &lsquo;+&rsquo; is
explained further on in this list.)

<code>*</code>
This symbol means that the preceding regular expression should be
repeated as many times as necessary to find a match.  For example, &lsquo;ph*&rsquo;
applies the &lsquo;*&rsquo; symbol to the preceding &lsquo;h&rsquo; and looks for matches
of one &lsquo;p&rsquo; followed by any number of &lsquo;h&rsquo;s.  This also matches
just &lsquo;p&rsquo; if no &lsquo;h&rsquo;s are present.

There are two subtle points to understand about how &lsquo;*&rsquo; works.
First, the &lsquo;*&rsquo; applies only to the single preceding regular expression
component (e.g., in &lsquo;ph*&rsquo;, it applies just to the &lsquo;h&rsquo;).
To cause &lsquo;*&rsquo; to apply to a larger subexpression, use parentheses:
&lsquo;(ph)*&rsquo; matches &lsquo;ph&rsquo;, &lsquo;phph&rsquo;, &lsquo;phphph&rsquo;, and so on.

Second, &lsquo;*&rsquo; finds as many repetitions as possible. If the text
to be matched is &lsquo;phhhhhhhhhhhhhhooey&rsquo;, &lsquo;ph*&rsquo; matches all of
the &lsquo;h&rsquo;s.

<code>+</code>
This symbol is similar to &lsquo;*&rsquo;, except that the preceding expression must be
matched at least once.  This means that &lsquo;wh+y&rsquo;
would match &lsquo;why&rsquo; and &lsquo;whhy&rsquo;, but not &lsquo;wy&rsquo;, whereas
&lsquo;wh*y&rsquo; would match all three.

<code>?</code>
This symbol is similar to &lsquo;*&rsquo;, except that the preceding expression can be
matched either once or not at all.  For example, &lsquo;fe?d&rsquo;
matches &lsquo;fed&rsquo; and &lsquo;fd&rsquo;, but nothing else.

<code>{n}{n,}{n,m}</code>
One or two numbers inside braces denote an *interval expression*.
If there is one number in the braces, the preceding regexp is repeated
n times.
If there are two numbers separated by a comma, the preceding regexp is
repeated n to m times.
If there is one number followed by a comma, then the preceding regexp
is repeated at least n times:

<code>wh{3}y</code>
Matches &lsquo;whhhy&rsquo;, but not &lsquo;why&rsquo; or &lsquo;whhhhy&rsquo;.

<code>wh{3,5}y</code>
Matches &lsquo;whhhy&rsquo;, &lsquo;whhhhy&rsquo;, or &lsquo;whhhhhy&rsquo; only.

<code>wh{2,}y</code>
Matches &lsquo;whhy&rsquo;, &lsquo;whhhy&rsquo;, and so on.

Interval expressions were not traditionally available in `awk`.
They were added as part of the POSIX standard to make `awk`
and `egrep` consistent with each other.

Initially, because old programs may use &lsquo;{&rsquo; and &lsquo;}&rsquo; in regexp
constants,
`gawk` did *not* match interval expressions
in regexps.

However, beginning with version 4.0,
`gawk` does match interval expressions by default.
This is because compatibility with POSIX has become more
important to most `gawk` users than compatibility with
old programs.

For programs that use &lsquo;{&rsquo; and &lsquo;}&rsquo; in regexp constants,
it is good practice to always escape them with a backslash.  Then the
regexp constants are valid and work the way you want them to, using
any version of `awk`.[16](#FOOT16)

Finally, when &lsquo;{&rsquo; and &lsquo;}&rsquo; appear in regexp constants
in a way that cannot be interpreted as an interval expression
(such as `/q{a}/`), then they stand for themselves.

In regular expressions, the &lsquo;*&rsquo;, &lsquo;+&rsquo;, and &lsquo;?&rsquo; operators,
as well as the braces &lsquo;{&rsquo; and &lsquo;}&rsquo;,
have
the highest precedence, followed by concatenation, and finally by &lsquo;|&rsquo;.
As in arithmetic, parentheses can change how operators are grouped.

In POSIX `awk` and `gawk`, the &lsquo;*&rsquo;, &lsquo;+&rsquo;, and
&lsquo;?&rsquo; operators stand for themselves when there is nothing in the
regexp that precedes them.  For example, `/+/` matches a literal
plus sign.  However, many other versions of `awk` treat such a
usage as a syntax error.

If `gawk` is in compatibility mode (see [Options](#22-command-line-options)), interval
expressions are not available in regular expressions.

---

Next: [Leftmost Longest](#35-how-much-text-matches), Previous: [Regexp Operators](#33-regular-expression-operators), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.4 Using Bracket Expressions

As mentioned earlier, a bracket expression matches any character among
those listed between the opening and closing square brackets.

Within a bracket expression, a *range expression* consists of two
characters separated by a hyphen.  It matches any single character that
sorts between the two characters, based upon the system&rsquo;s native character
set.  For example, &lsquo;[0-9]&rsquo; is equivalent to &lsquo;[0123456789]&rsquo;.
(See [Ranges and Locales](#Ranges-and-Locales) for an explanation of how the POSIX
standard and `gawk` have changed over time.  This is mainly
of historical interest.)

With the increasing popularity of the
[Unicode character standard](http://www.unicode.org),
there is an additional wrinkle to consider. Octal and hexadecimal
escape sequences inside bracket expressions are taken to represent
only single-byte characters (characters whose values fit within
the range 0&ndash;256).  To match a range of characters where the endpoints
of the range are larger than 256, enter the multibyte encodings of
the characters directly.

To include one of the characters &lsquo;\&rsquo;, &lsquo;]&rsquo;, &lsquo;-&rsquo;, or &lsquo;^&rsquo; in a
bracket expression, put a &lsquo;\&rsquo; in front of it.  For example:

    [d\]]
    

matches either &lsquo;d&rsquo; or &lsquo;]&rsquo;.
Additionally, if you place &lsquo;]&rsquo; right after the opening
&lsquo;[&rsquo;, the closing bracket is treated as one of the
characters to be matched.

The treatment of &lsquo;\&rsquo; in bracket expressions
is compatible with other `awk`
implementations and is also mandated by POSIX.
The regular expressions in `awk` are a superset
of the POSIX specification for Extended Regular Expressions (EREs).
POSIX EREs are based on the regular expressions accepted by the
traditional `egrep` utility.

*Character classes* are a feature introduced in the POSIX standard.
A character class is a special notation for describing
lists of characters that have a specific attribute, but the
actual characters can vary from country to country and/or
from character set to character set.  For example, the notion of what
is an alphabetic character differs between the United States and France.

A character class is only valid in a regexp *inside* the
brackets of a bracket expression.  Character classes consist of &lsquo;[:&rsquo;,
a keyword denoting the class, and &lsquo;:]&rsquo;.
[Table 3.1](#table_002dchar_002dclasses) lists the character classes defined by the
POSIX standard.

ClassMeaning`[:alnum:]`Alphanumeric characters`[:alpha:]`Alphabetic characters`[:blank:]`Space and TAB characters`[:cntrl:]`Control characters`[:digit:]`Numeric characters`[:graph:]`Characters that are both printable and visible
(a space is printable but not visible, whereas an &lsquo;a&rsquo; is both)`[:lower:]`Lowercase alphabetic characters`[:print:]`Printable characters (characters that are not control characters)`[:punct:]`Punctuation characters (characters that are not letters, digits,
control characters, or space characters)`[:space:]`Space characters (such as space, TAB, and formfeed, to name a few)`[:upper:]`Uppercase alphabetic characters`[:xdigit:]`Characters that are hexadecimal digits

**Table 3.1: **POSIX character classes

For example, before the POSIX standard, you had to write `/[A-Za-z0-9]/`
to match alphanumeric characters.  If your
character set had other alphabetic characters in it, this would not
match them.
With the POSIX character classes, you can write
`/[[:alnum:]]/` to match the alphabetic
and numeric characters in your character set.

Some utilities that match regular expressions provide a nonstandard
&lsquo;[:ascii:]&rsquo; character class; `awk` does not. However, you
can simulate such a construct using &lsquo;[\x00-\x7F]&rsquo;.  This matches
all values numerically between zero and 127, which is the defined
range of the ASCII character set.  Use a complemented character list
(&lsquo;[^\x00-\x7F]&rsquo;) to match any single-byte characters that are not
in the ASCII range.

Two additional special sequences can appear in bracket expressions.
These apply to non-ASCII character sets, which can have single symbols
(called *collating elements*) that are represented with more than one
character. They can also have several characters that are equivalent for
*collating*, or sorting, purposes.  (For example, in French, a plain &ldquo;e&rdquo;
and a grave-accented &ldquo;&egrave;&rdquo; are equivalent.)
These sequences are:

Collating symbols
Multicharacter collating elements enclosed between
&lsquo;[.&rsquo; and &lsquo;.]&rsquo;.  For example, if &lsquo;ch&rsquo; is a collating element,
then &lsquo;[[.ch.]]&rsquo; is a regexp that matches this collating element, whereas
&lsquo;[ch]&rsquo; is a regexp that matches either &lsquo;c&rsquo; or &lsquo;h&rsquo;.

Equivalence classes
Locale-specific names for a list of
characters that are equal. The name is enclosed between
&lsquo;[=&rsquo; and &lsquo;=]&rsquo;.
For example, the name &lsquo;e&rsquo; might be used to represent all of
&ldquo;e,&rdquo; &ldquo;&ecirc;,&rdquo; &ldquo;&egrave;,&rdquo; and &ldquo;&eacute;.&rdquo; In this case, &lsquo;[[=e=]]&rsquo; is a regexp
that matches any of &lsquo;e&rsquo;, &lsquo;&ecirc;&rsquo;, &lsquo;&eacute;&rsquo;, or &lsquo;&egrave;&rsquo;.

These features are very valuable in non-English-speaking locales.

> CAUTION: The library functions that `gawk` uses for regular
> expression matching currently recognize only POSIX character classes;
> they do not recognize collating symbols or equivalence classes.

Inside a bracket expression, an opening bracket (&lsquo;[&rsquo;) that does
not start a character class, collating element or equivalence class is
taken literally. This is also true of &lsquo;.&rsquo; and &lsquo;*&rsquo;.

---

Next: [Computed Regexps](#36-using-dynamic-regexps), Previous: [Bracket Expressions](#34-using-bracket-expressions), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.5 How Much Text Matches?

Consider the following:

```awk
    echo aaaabcd | awk '{ sub(/a+/, "<A>"); print }'
```    

This example uses the `sub()` function to make a change to the input
record.  (`sub()` replaces the first instance of any text matched
by the first argument with the string provided as the second argument;
see [String Functions](#913-string-manipulation-functions).)  Here, the regexp `/a+/` indicates &ldquo;one
or more &lsquo;a&rsquo; characters,&rdquo; and the replacement text is &lsquo;<A>&rsquo;.

The input contains four &lsquo;a&rsquo; characters.
`awk` (and POSIX) regular expressions always match
the leftmost, *longest* sequence of input characters that can
match.  Thus, all four &lsquo;a&rsquo; characters are
replaced with &lsquo;<A>&rsquo; in this example:

```awk
    $ echo aaaabcd | awk '{ sub(/a+/, "<A>"); print }'
    -| <A>bcd
```    

For simple match/no-match tests, this is not so important. But when doing
text matching and substitutions with the `match()`, `sub()`, `gsub()`,
and `gensub()` functions, it is very important.
Understanding this principle is also important for regexp-based record
and field splitting (see [Records](#41-how-input-is-split-into-records),
and also see [Field Separators](#45-specifying-how-fields-are-separated)).

---

Next: [GNU Regexp Operators](#37-gawk-specific-regexp-operators), Previous: [Leftmost Longest](#35-how-much-text-matches), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.6 Using Dynamic Regexps

The righthand side of a &lsquo;~&rsquo; or &lsquo;!~&rsquo; operator need not be a
regexp constant (i.e., a string of characters between slashes).  It may
be any expression.  The expression is evaluated and converted to a string
if necessary; the contents of the string are then used as the
regexp.  A regexp computed in this way is called a *dynamic
regexp* or a *computed regexp*:

```awk
    BEGIN { digits_regexp = "[[:digit:]]+" }
    $0 ~ digits_regexp    { print }
```    

This sets `digits_regexp` to a regexp that describes one or more digits,
and tests whether the input record matches this regexp.

> NOTE: When using the &lsquo;~&rsquo; and &lsquo;!~&rsquo;
> operators, be aware that there is a difference between a regexp constant
> enclosed in slashes and a string constant enclosed in double quotes.
> If you are going to use a string constant, you have to understand that
> the string is, in essence, scanned *twice*: the first time when
> `awk` reads your program, and the second time when it goes to
> match the string on the lefthand side of the operator with the pattern
> on the right.  This is true of any string-valued expression (such as
> `digits_regexp`, shown in the previous example), not just string constants.

What difference does it make if the string is
scanned twice? The answer has to do with escape sequences, and particularly
with backslashes.  To get a backslash into a regular expression inside a
string, you have to type two backslashes.

For example, `/\*/` is a regexp constant for a literal &lsquo;*&rsquo;.
Only one backslash is needed.  To do the same thing with a string,
you have to type `"\\*"`.  The first backslash escapes the
second one so that the string actually contains the
two characters &lsquo;\&rsquo; and &lsquo;*&rsquo;.

Given that you can use both regexp and string constants to describe
regular expressions, which should you use?  The answer is &ldquo;regexp
constants,&rdquo; for several reasons:

-  String constants are more complicated to write and
more difficult to read. Using regexp constants makes your programs
less error-prone.  Not understanding the difference between the two
kinds of constants is a common source of errors.

-  It is more efficient to use regexp constants. `awk` can note
that you have supplied a regexp and store it internally in a form that
makes pattern matching more efficient.  When using a string constant,
`awk` must first convert the string into this internal form and
then perform the pattern matching.

-  Using regexp constants is better form; it shows clearly that you
intend a regexp match.

Using `\n` in Bracket Expressions of Dynamic Regexps

Some older versions of `awk` do not allow the newline
character to be used inside a bracket expression for a dynamic regexp:

```awk
    $ awk '$0 ~ "[ \t\n]"'
    error&rarr; awk: newline in character class [
    error&rarr; ]...
    error&rarr;  source line number 1
    error&rarr;  context is
    error&rarr;        $0 ~ "[ >>>  \t\n]" <<<
```    

But a newline in a regexp constant works with no problem:

```awk
    $ awk '$0 ~ /[ \t\n]/'here is a sample line
    -| here is a sample line
    Ctrl-d
```

`gawk` does not have this problem, and it isn&rsquo;t likely to
occur often in practice, but it&rsquo;s worth noting for future reference.

---

Next: [Case-sensitivity](#38-case-sensitivity-in-matching), Previous: [Computed Regexps](#36-using-dynamic-regexps), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.7 `gawk`-Specific Regexp Operators

GNU software that deals with regular expressions provides a number of
additional regexp operators.  These operators are described in this
section and are specific to `gawk`;
they are not available in other `awk` implementations.
Most of the additional operators deal with word matching.
For our purposes, a *word* is a sequence of one or more letters, digits,
or underscores (&lsquo;_&rsquo;):

<code>\s</code>
Matches any whitespace character.
Think of it as shorthand for
&lsquo;[[:space:]]&rsquo;.

<code>\S</code>
Matches any character that is not whitespace.
Think of it as shorthand for
&lsquo;[^[:space:]]&rsquo;.

<code>\w</code>
Matches any word-constituent character&mdash;that is, it matches any
letter, digit, or underscore. Think of it as shorthand for
&lsquo;[[:alnum:]_]&rsquo;.

<code>\W</code>
Matches any character that is not word-constituent.
Think of it as shorthand for
&lsquo;[^[:alnum:]_]&rsquo;.

<code>\<</code>
Matches the empty string at the beginning of a word.
For example, `/\<away/` matches &lsquo;away&rsquo; but not
&lsquo;stowaway&rsquo;.

<code>\></code>
Matches the empty string at the end of a word.
For example, `/stow\>/` matches &lsquo;stow&rsquo; but not &lsquo;stowaway&rsquo;.

<code>\y</code>
Matches the empty string at either the beginning or the
end of a word (i.e., the word boundar**y**).  For example, &lsquo;\yballs?\y&rsquo;
matches either &lsquo;ball&rsquo; or &lsquo;balls&rsquo;, as a separate word.

<code>\B</code>
Matches the empty string that occurs between two
word-constituent characters. For example,
`/\Brat\B/` matches &lsquo;crate&rsquo;, but it does not match &lsquo;dirty rat&rsquo;.
&lsquo;\B&rsquo; is essentially the opposite of &lsquo;\y&rsquo;.

There are two other operators that work on buffers.  In Emacs, a
*buffer* is, naturally, an Emacs buffer.
Other GNU programs, including `gawk`,
consider the entire string to match as the buffer.
The operators are:

<code>\`</code>
Matches the empty string at the
beginning of a buffer (string)

<code>\'</code>
Matches the empty string at the
end of a buffer (string)

Because &lsquo;^&rsquo; and &lsquo;$&rsquo; always work in terms of the beginning
and end of strings, these operators don&rsquo;t add any new capabilities
for `awk`.  They are provided for compatibility with other
GNU software.

In other GNU software, the word-boundary operator is &lsquo;\b&rsquo;. However,
that conflicts with the `awk` language&rsquo;s definition of &lsquo;\b&rsquo;
as backspace, so `gawk` uses a different letter.
An alternative method would have been to require two backslashes in the
GNU operators, but this was deemed too confusing. The current
method of using &lsquo;\y&rsquo; for the GNU &lsquo;\b&rsquo; appears to be the
lesser of two evils.

The various command-line options
(see [Options](#22-command-line-options))
control how `gawk` interprets characters in regexps:

No options
In the default case, `gawk` provides all the facilities of
POSIX regexps and the
previously described
GNU regexp operators.
GNU regexp operators described
in [Regexp Operators](#33-regular-expression-operators).

<code>--posix</code>
Match only POSIX regexps; the GNU operators are not special
(e.g., &lsquo;\w&rsquo; matches a literal &lsquo;w&rsquo;).  Interval expressions
are allowed.

<code>--traditional</code>
Match traditional Unix `awk` regexps. The GNU operators
are not special, and interval expressions are not available.
Because BWK `awk` supports them,
the POSIX character classes (&lsquo;[[:alnum:]]&rsquo;, etc.) are available.
Characters described by octal and hexadecimal escape sequences are
treated literally, even if they represent regexp metacharacters.

<code>--re-interval</code>
Allow interval expressions in regexps, if --traditional
has been provided.
Otherwise, interval expressions are available by default.

---

Next: [Regexp Summary](#39-summary), Previous: [GNU Regexp Operators](#37-gawk-specific-regexp-operators), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.8 Case Sensitivity in Matching

Case is normally significant in regular expressions, both when matching
ordinary characters (i.e., not metacharacters) and inside bracket
expressions.  Thus, a &lsqlsquo;w&rsquo; in a regular expression matches only a lowercase
&lsquo;w&rsquo; and not an uppercase &lsquo;W&rsquo;.

The simplest way to do a case-independent match is to use a bracket
expression&mdash;for example, &lsquo;[Ww]&rsquo;.  However, this can be cumbersome if
you need to use it often, and it can make the regular expressions harder
to read.  There are two alternatives that you might prefer.

One way to perform a case-insensitive match at a particular point in the
program is to convert the data to a single case, using the
`tolower()` or `toupper()` built-in string functions (which we
haven&rsquo;t discussed yet;
see [String Functions](#913-string-manipulation-functions)).
For example:

```awk
    tolower($1) ~ /foo/  { &hellip; }
```    

converts the first field to lowercase before matching against it.
This works in any POSIX-compliant `awk`.

Another method, specific to `gawk`, is to set the variable
`IGNORECASE` to a nonzero value (see [Built-in Variables](#75-predefined-variables)).
When `IGNORECASE` is not zero, *all* regexp and string
operations ignore case.

Changing the value of `IGNORECASE` dynamically controls the
case sensitivity of the program as it runs.  Case is significant by
default because `IGNORECASE` (like most variables) is initialized
to zero:

```awk
    x = "aB"
    if (x ~ /ab/) &hellip;   # this test will fail
    
    IGNORECASE = 1
    if (x ~ /ab/) &hellip;   # now it will succeed
```    

In general, you cannot use `IGNORECASE` to make certain rules
case insensitive and other rules case sensitive, as there is no
straightforward way
to set `IGNORECASE` just for the pattern of
a particular rule.[17](#FOOT17)
To do this, use either bracket expressions or `tolower()`.  However, one
thing you can do with `IGNORECASE` only is dynamically turn
case sensitivity on or off for all the rules at once.

`IGNORECASE` can be set on the command line or in a `BEGIN` rule
(see [Other Arguments](#23-other-command-line-arguments); also
see [Using BEGIN/END](#7141-startup-and-cleanup-actions)).
Setting `IGNORECASE` from the command line is a way to make
a program case insensitive without having to edit it.

In multibyte locales,
the equivalences between upper-
and lowercase characters are tested based on the wide-character values of
the locale&rsquo;s character set.
Otherwise, the characters are tested based
on the ISO-8859-1 (ISO Latin-1)
character set. This character set is a superset of the traditional 128
ASCII characters, which also provides a number of characters suitable
for use with European languages.[18](#FOOT18)

The value of `IGNORECASE` has no effect if `gawk` is in
compatibility mode (see [Options](#22-command-line-options)).
Case is always significant in compatibility mode.

---

Previous: [Case-sensitivity](#38-case-sensitivity-in-matching), Up: [Regexp](#3-regular-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 3.9 Summary

-  Regular expressions describe sets of strings to be matched.
In `awk`, regular expression constants are written enclosed
between slashes: `/`&hellip;`/`.

-  Regexp constants may be used standalone in patterns and
in conditional expressions, or as part of matching expressions
using the &lsquo;~&rsquo; and &lsquo;!~&rsquo; operators.

-  Escape sequences let you represent nonprintable characters and
also let you represent regexp metacharacters as literal characters
to be matched.

-  Regexp operators provide grouping, alternation, and repetition.

-  Bracket expressions give you a shorthand for specifying sets
of characters that can match at a particular point in a regexp.
Within bracket expressions, POSIX character classes let you specify
certain groups of characters in a locale-independent fashion.

-  Regular expressions match the leftmost longest text in the string being
matched.  This matters for cases where you need to know the extent of
the match, such as for text substitution and when the record separator
is a regexp.

-  Matching expressions may use dynamic regexps (i.e., string values
treated as regular expressions).

- `gawk`&rsquo;s `IGNORECASE` variable lets you control the
case sensitivity of regexp matching.  In other `awk`
versions, use `tolower()` or `toupper()`.

---

Next: [Printing](#5-printing-output), Previous: [Regexp](#3-regular-expressions), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 4 Reading Input Files

In the typical `awk` program,
`awk` reads all input either from the
standard input (by default, this is the keyboard, but often it is a pipe from another
command) or from files whose names you specify on the `awk`
command line.  If you specify input files, `awk` reads them
in order, processing all the data from one before going on to the next.
The name of the current input file can be found in the predefined variable
`FILENAME`
(see [Built-in Variables](#75-predefined-variables)).

The input is read in units called *records*, and is processed by the
rules of your program one record at a time.
By default, each record is one line.  Each
record is automatically split into chunks called *fields*.
This makes it more convenient for programs to work on the parts of a record.

On rare occasions, you may need to use the `getline` command.
The  `getline` command is valuable both because it
can do explicit input from any number of files, and because the files
used with it do not have to be named on the `awk` command line
(see [Getline](#410-explicit-input-with-getline)).

| []() | []()
| - | -
| &bull; [Records](#41-how-input-is-split-into-records) | Controlling how data is split into records.
| &bull; [Fields](#42-examining-fields) | An introduction to fields.
| &bull; [Nonconstant Fields](#43-nonconstant-field-numbers) | Nonconstant Field Numbers.
| &bull; [Changing Fields](#44-changing-the-contents-of-a-field) | Changing the Contents of a Field.
| &bull; [Field Separators](#45-specifying-how-fields-are-separated) | The field separator and how to change it.
| &bull; [Constant Size](#46-reading-fixed-width-data) | Reading constant width data.
| &bull; [Splitting By Content](#47-defining-fields-by-content) | Defining Fields By Content
| &bull; [Testing field creation](#48-checking-how-gawk-is-splitting-records) | Checking how `gawk` is splitting records.
| &bull; [Multiple Line](#49-multiple-line-records) | Reading multiline records.
| &bull; [Getline](#410-explicit-input-with-getline) | Reading files under explicit program control using the `getline` function.
| &bull; [Read Timeout](#411-reading-input-with-a-timeout) | Reading input with a timeout.
| &bull; [Retrying Input](#412-retrying-reads-after-certain-input-errors) | Retrying input after certain errors.
| &bull; [Command-line directories](#413-directories-on-the-command-line) | What happens if you put a directory on the command line.
| &bull; [Input Summary](#414-summary) | Input summary.
| &bull; [Input Exercises](#415-exercises) | Exercises.

---

Next: [Fields](#42-examining-fields), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.1 How Input Is Split into Records

`awk` divides the input for your program into records and fields.
It keeps track of the number of records that have been read so far from
the current input file.  This value is stored in a predefined variable
called `FNR`, which is reset to zero every time a new file is started.
Another predefined variable, `NR`, records the total number of input
records read so far from all data files.  It starts at zero, but is
never automatically reset to zero.

| []() | []()
| - | -
| &bull; [awk split records](#411-record-splitting-with-standard-awk) | How standard `awk` splits records.
| &bull; [gawk split records](#412-record-splitting-with-gawk) | How `gawk` splits records.

---

Next: [gawk split records](#412-record-splitting-with-gawk), Up: [Records](#41-how-input-is-split-into-records)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.1.1 Record Splitting with Standard `awk`

Records are separated by a character called the *record separator*.
By default, the record separator is the newline character.
This is why records are, by default, single lines.
To use a different character for the record separator,
simply assign that character to the predefined variable `RS`.

Like any other variable,
the value of `RS` can be changed in the `awk` program
with the assignment operator, &lsquo;=&rsquo;
(see [Assignment Ops](#623-assignment-expressions)).
The new record-separator character should be enclosed in quotation marks,
which indicate a string constant.  Often, the right time to do this is
at the beginning of execution, before any input is processed,
so that the very first record is read with the proper separator.
To do this, use the special `BEGIN` pattern
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)).
For example:

```awk
    awk 'BEGIN { RS = "u" }
         { print $0 }' mail-list
```    

changes the value of `RS` to &lsquo;u&rsquo;, before reading any input.
The new value is a string whose first character is the letter &ldquo;u&rdquo;; as a result, records
are separated by the letter &ldquo;u&rdquo;.  Then the input file is read, and the second
rule in the `awk` program (the action with no pattern) prints each
record.  Because each `print` statement adds a newline at the end of
its output, this `awk` program copies the input
with each &lsquo;u&rsquo; changed to a newline.  Here are the results of running
the program on mail-list:

```awk
    $ awk 'BEGIN { RS = "u" }
    >      { print $0 }' mail-list

    -| Amelia       555-5553     amelia.zodiac
    -| sq
    -| e@gmail.com    F
    -| Anthony      555-3412     anthony.assert
    -| ro@hotmail.com   A
    -| Becky        555-7685     becky.algebrar
    -| m@gmail.com      A
    -| Bill         555-1675     bill.drowning@hotmail.com       A
    -| Broderick    555-0542     broderick.aliq
    -| otiens@yahoo.com R
    -| Camilla      555-2912     camilla.inf
    -| sar
    -| m@skynet.be     R
    -| Fabi
    -| s       555-1234     fabi
    -| s.
    -| ndevicesim
    -| s@
    -| cb.ed
    -|     F
    -| J
    -| lie        555-6699     j
    -| lie.perscr
    -| tabor@skeeve.com   F
    -| Martin       555-6480     martin.codicib
    -| s@hotmail.com    A
    -| Sam
    -| el       555-3430     sam
    -| el.lanceolis@sh
    -| .ed
    -|         A
    -| Jean-Pa
    -| l    555-2127     jeanpa
    -| l.campanor
    -| m@ny
    -| .ed
    -|      R
    -|
```    

Note that the entry for the name &lsquo;Bill&rsquo; is not split.
In the original data file
(see [Sample Data Files](#12-data-files-for-the-examples)),
the line looks like this:

    Bill         555-1675     bill.drowning@hotmail.com       A
    

It contains no &lsquo;u&rsquo;, so there is no reason to split the record,
unlike the others, which each have one or more occurrences of the &lsquo;u&rsquo;.
In fact, this record is treated as part of the previous record;
the newline separating them in the output
is the original newline in the data file, not the one added by
`awk` when it printed the record!

Another way to change the record separator is on the command line,
using the variable-assignment feature
(see [Other Arguments](#23-other-command-line-arguments)):

```awk
    awk '{ print $0 }' RS="u" mail-list
```    

This sets `RS` to &lsquo;u&rsquo; before processing mail-list.

Using an alphabetic character such as &lsquo;u&rsquo; for the record separator
is highly likely to produce strange results.
Using an unusual character such as &lsquo;/&rsquo; is more likely to
produce correct behavior in the majority of cases, but there
are no guarantees. The moral is: Know Your Data.

When using regular characters as the record separator,
there is one unusual case that occurs when `gawk` is
being fully POSIX-compliant (see [Options](#22-command-line-options)).
Then, the following (extreme) pipeline prints a surprising &lsquo;1&rsquo;:

```awk
    $ echo | gawk --posix 'BEGIN { RS = "a" } ; { print NF }'
    -| 1
```    

There is one field, consisting of a newline.  The value of the built-in
variable `NF` is the number of fields in the current record.
(In the normal case, `gawk` treats the newline as whitespace,
printing &lsquo;0&rsquo; as the result. Most other versions of `awk`
also act this way.)

Reaching the end of an input file terminates the current input record,
even if the last character in the file is not the character in `RS`.
(d.c.)

The empty string `""` (a string without any characters)
has a special meaning
as the value of `RS`. It means that records are separated
by one or more blank lines and nothing else.
See [Multiple Line](#49-multiple-line-records) for more details.

If you change the value of `RS` in the middle of an `awk` run,
the new value is used to delimit subsequent records, but the record
currently being processed, as well as records already processed, are not
affected.

After the end of the record has been determined, `gawk`
sets the variable `RT` to the text in the input that matched
`RS`.

---

Previous: [awk split records](#411-record-splitting-with-standard-awk), Up: [Records](#41-how-input-is-split-into-records)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.1.2 Record Splitting with `gawk`

When using `gawk`,
the value of `RS` is not limited to a one-character
string.  It can be any regular expression
(see [Regexp](#3-regular-expressions)). (c.e.)
In general, each record
ends at the next string that matches the regular expression; the next
record starts at the end of the matching string.  This general rule is
actually at work in the usual case, where `RS` contains just a
newline: a record ends at the beginning of the next matching string (the
next newline in the input), and the following record starts just after
the end of this string (at the first character of the following line).
The newline, because it matches `RS`, is not part of either record.

When `RS` is a single character, `RT`
contains the same single character. However, when `RS` is a
regular expression, `RT` contains
the actual input text that matched the regular expression.

If the input file ends without any text matching `RS`,
`gawk` sets `RT` to the null string.

The following example illustrates both of these features.
It sets `RS` equal to a regular expression that
matches either a newline or a series of one or more uppercase letters
with optional leading and/or trailing whitespace:

```awk
    $ echo record 1 AAAA record 2 BBBB record 3 |
    > gawk 'BEGIN { RS = "\n|( *[[:upper:]]+ *)" }
    >             { print "Record =", $0,"and RT = [" RT "]" }'

    -| Record = record 1 and RT = [ AAAA ]
    -| Record = record 2 and RT = [ BBBB ]
    -| Record = record 3 and RT = [
    -| ]
```    

The square brackets delineate the contents of `RT`, letting you
see the leading and trailing whitespace. The final value of
`RT` is a newline.
See [Simple Sed](#1138-a-simple-stream-editor) for a more useful example
of `RS` as a regexp and `RT`.

If you set `RS` to a regular expression that allows optional
trailing text, such as &lsquo;RS = "abc(XYZ)?"&rsquo;, it is possible, due
to implementation constraints, that `gawk` may match the leading
part of the regular expression, but not the trailing part, particularly
if the input text that could match the trailing part is fairly long.
`gawk` attempts to avoid this problem, but currently, there&rsquo;s
no guarantee that this will never happen.

> NOTE: Remember that in `awk`, the &lsquo;^&rsquo; and &lsquo;$&rsquo; anchor
> metacharacters match the beginning and end of a *string*, and not
> the beginning and end of a *line*.  As a result, something like
> &lsquo;RS = "^[[:upper:]]"&rsquo; can only match at the beginning of a file.
> This is because `gawk` views the input file as one long string
> that happens to contain newline characters.
> It is thus best to avoid anchor metacharacters in the value of `RS`.

The use of `RS` as a regular expression and the `RT`
variable are `gawk` extensions; they are not available in
compatibility mode
(see [Options](#22-command-line-options)).
In compatibility mode, only the first character of the value of
`RS` determines the end of the record.

`RS = "\0"` Is Not Portable

There are times when you might want to treat an entire data file as a
single record.  The only way to make this happen is to give `RS`
a value that you know doesn&rsquo;t occur in the input file.  This is hard
to do in a general way, such that a program always works for arbitrary
input files.

You might think that for text files, the NUL character, which
consists of a character with all bits equal to zero, is a good
value to use for `RS` in this case:

```awk
    BEGIN { RS = "\0" }  # whole file becomes one record?
```    

`gawk` in fact accepts this, and uses the NUL
character for the record separator.
This works for certain special files, such as /proc/environ on
GNU/Linux systems, where the NUL character is in fact the record separator.
However, this usage is *not* portable
to most other `awk` implementations.

Almost all other `awk` implementations[19](#FOOT19) store strings internally as C-style strings.  C strings use the
NUL character as the string terminator.  In effect, this means that
&lsquo;RS = "\0"&rsquo; is the same as &lsquo;RS = ""&rsquo;.
(d.c.)

It happens that recent versions of `mawk` can use the NUL
character as a record separator. However, this is a special case:
`mawk` does not allow embedded NUL characters in strings.
(This may change in a future version of `mawk`.)

See [Readfile Function](#1028-reading-a-whole-file-at-once) for an interesting way to read
whole files.  If you are using `gawk`, see [Extension Sample Readfile](#Extension-Sample-Readfile) for another option.

---

Next: [Nonconstant Fields](#43-nonconstant-field-numbers), Previous: [Records](#41-how-input-is-split-into-records), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.2 Examining Fields

When `awk` reads an input record, the record is
automatically *parsed* or separated by the `awk` utility into chunks
called *fields*.  By default, fields are separated by *whitespace*,
like words in a line.
Whitespace in `awk` means any string of one or more spaces,
TABs, or newlines; other characters
that are considered whitespace by other languages
(such as formfeed, vertical tab, etc.) are *not* considered
whitespace by `awk`.

The purpose of fields is to make it more convenient for you to refer to
these pieces of the record.  You don&rsquo;t have to use them&mdash;you can
operate on the whole record if you want&mdash;but fields are what make
simple `awk` programs so powerful.

You use a dollar sign (&lsquo;$&rsquo;)
to refer to a field in an `awk` program,
followed by the number of the field you want.  Thus, `$1`
refers to the first field, `$2` to the second, and so on.
(Unlike in the Unix shells, the field numbers are not limited to single digits.
`$127` is the 127th field in the record.)
For example, suppose the following is a line of input:

    This seems like a pretty nice example.
    

Here the first field, or `$1`, is &lsquo;This&rsquo;, the second field, or
`$2`, is &lsquo;seems&rsquo;, and so on.  Note that the last field,
`$7`, is &lsquo;example.&rsquo;.  Because there is no space between the
&lsquo;e&rsquo; and the &lsquo;.&rsquo;, the period is considered part of the seventh
field.

`NF` is a predefined variable whose value is the number of fields
in the current record.  `awk` automatically updates the value
of `NF` each time it reads a record.  No matter how many fields
there are, the last field in a record can be represented by `$NF`.
So, `$NF` is the same as `$7`, which is &lsquo;example.&rsquo;.
If you try to reference a field beyond the last
one (such as `$8` when the record has only seven fields), you get
the empty string.  (If used in a numeric operation, you get zero.)

The use of `$0`, which looks like a reference to the &ldquo;zeroth&rdquo; field, is
a special case: it represents the whole input record. Use it
when you are not interested in specific fields.
Here are some more examples:

```awk
    $ awk '$1 ~ /li/ { print $0 }' mail-list
    -| Amelia       555-5553     amelia.zodiacusque@gmail.com    F
    -| Julie        555-6699     julie.perscrutabor@skeeve.com   F
```    

This example prints each record in the file mail-list whose first
field contains the string &lsquo;li&rsquo;.

By contrast, the following example looks for &lsquo;li&rsquo; in *the
entire record* and prints the first and last fields for each matching
input record:

```awk
    $ awk '/li/ { print $1, $NF }' mail-list
    -| Amelia F
    -| Broderick R
    -| Julie F
    -| Samuel A
```    

---

Next: [Changing Fields](#44-changing-the-contents-of-a-field), Previous: [Fields](#42-examining-fields), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.3 Nonconstant Field Numbers

A field number need not be a constant.  Any expression in
the `awk` language can be used after a &lsquo;$&rsquo; to refer to a
field.  The value of the expression specifies the field number.  If the
value is a string, rather than a number, it is converted to a number.
Consider this example:

```awk
    awk '{ print $NR }'
```    

Recall that `NR` is the number of records read so far: one in the
first record, two in the second, and so on.  So this example prints the first
field of the first record, the second field of the second record, and so
on.  For the twentieth record, field number 20 is printed; most likely,
the record has fewer than 20 fields, so this prints a blank line.
Here is another example of using expressions as field numbers:

```awk
    awk '{ print $(2*2) }' mail-list
```    

`awk` evaluates the expression &lsquo;(2*2)&rsquo; and uses
its value as the number of the field to print.  The &lsquo;*&rsquo;
represents multiplication, so the expression &lsquo;2*2&rsquo; evaluates to four.
The parentheses are used so that the multiplication is done before the
&lsquo;$&rsquo; operation; they are necessary whenever there is a binary
operator[20](#FOOT20)
in the field-number expression.  This example, then, prints the
type of relationship (the fourth field) for every line of the file
mail-list.  (All of the `awk` operators are listed, in
order of decreasing precedence, in
[Precedence](#65-operator-precedence-how-operators-nest).)

If the field number you compute is zero, you get the entire record.
Thus, &lsquo;$(2-2)&rsquo; has the same value as `$0`.  Negative field
numbers are not allowed; trying to reference one usually terminates
the program.  (The POSIX standard does not define
what happens when you reference a negative field number.  `gawk`
notices this and terminates your program.  Other `awk`
implementations may behave differently.)

As mentioned in [Fields](#42-examining-fields),
`awk` stores the current record&rsquo;s number of fields in the built-in
variable `NF` (also see [Built-in Variables](#75-predefined-variables)).  Thus, the expression
`$NF` is not a special feature&mdash;it is the direct consequence of
evaluating `NF` and using its value as a field number.

---

Next: [Field Separators](#45-specifying-how-fields-are-separated), Previous: [Nonconstant Fields](#43-nonconstant-field-numbers), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.4 Changing the Contents of a Field

The contents of a field, as seen by `awk`, can be changed within an
`awk` program; this changes what `awk` perceives as the
current input record.  (The actual input is untouched; `awk`*never*
modifies the input file.)
Consider the following example and its output:

```awk
    $ awk '{ nboxes = $3 ; $3 = $3 - 10
    >        print nboxes, $3 }' inventory-shipped
    -| 25 15
    -| 32 22
    -| 24 14
    &hellip;
```    

The program first saves the original value of field three in the variable
`nboxes`.
The &lsquo;-&rsquo; sign represents subtraction, so this program reassigns
field three, `$3`, as the original value of field three minus ten:
&lsquo;$3 - 10&rsquo;.  (See [Arithmetic Ops](#621-arithmetic-operators).)
Then it prints the original and new values for field three.
(Someone in the warehouse made a consistent mistake while inventorying
the red boxes.)

For this to work, the text in `$3` must make sense
as a number; the string of characters must be converted to a number
for the computer to do arithmetic on it.  The number resulting
from the subtraction is converted back to a string of characters that
then becomes field three.
See [Conversion](#614-conversion-of-strings-and-numbers).

When the value of a field is changed (as perceived by `awk`), the
text of the input record is recalculated to contain the new field where
the old one was.  In other words, `$0` changes to reflect the altered
field.  Thus, this program
prints a copy of the input file, with 10 subtracted from the second
field of each line:

```awk
    $ awk '{ $2 = $2 - 10; print $0 }' inventory-shipped
    -| Jan 3 25 15 115
    -| Feb 5 32 24 226
    -| Mar 5 24 34 228
    &hellip;
```    

It is also possible to assign contents to fields that are out
of range.  For example:

```awk
    $ awk '{ $6 = ($5 + $4 + $3 + $2)
    >        print $6 }' inventory-shipped
    -| 168
    -| 297
    -| 301
    &hellip;
```    

We&rsquo;ve just created `$6`, whose value is the sum of fields
`$2`, `$3`, `$4`, and `$5`.  The &lsquo;+&rsquo; sign
represents addition.  For the file inventory-shipped, `$6`
represents the total number of parcels shipped for a particular month.

Creating a new field changes `awk`&rsquo;s internal copy of the current
input record, which is the value of `$0`.  Thus, if you do &lsquo;print $0&rsquo;
after adding a field, the record printed includes the new field, with
the appropriate number of field separators between it and the previously
existing fields.

This recomputation affects and is affected by
`NF` (the number of fields; see [Fields](#42-examining-fields)).
For example, the value of `NF` is set to the number of the highest
field you create.
The exact format of `$0` is also affected by a feature that has not been discussed yet:
the *output field separator*, `OFS`,
used to separate the fields (see [Output Separators](#53-output-separators)).

Note, however, that merely *referencing* an out-of-range field
does *not* change the value of either `$0` or `NF`.
Referencing an out-of-range field only produces an empty string.  For
example:

```awk
    if ($(NF+1) != "")
        print "can't happen"
    else
        print "everything is normal"
```    

should print &lsquo;everything is normal&rsquo;, because `NF+1` is certain
to be out of range.  (See [If Statement](#741-the-if-else-statement)
for more information about `awk`&rsquo;s `if-else` statements.
See [Typing and Comparison](#6322-comparison-operators)
for more information about the &lsquo;!=&rsquo; operator.)

It is important to note that making an assignment to an existing field
changes the
value of `$0` but does not change the value of `NF`,
even when you assign the empty string to a field.  For example:

```awk
    $ echo a b c d | awk '{ OFS = ":"; $2 = ""
    >                       print $0; print NF }'
    -| a::c:d
    -| 4
```    

The field is still there; it just has an empty value, delimited by
the two colons between &lsquo;a&rsquo; and &lsquo;c&rsquo;.
This example shows what happens if you create a new field:

```awk
    $ echo a b c d | awk '{ OFS = ":"; $2 = ""; $6 = "new"
    >                       print $0; print NF }'
    -| a::c:d::new
    -| 6
```    

The intervening field, `$5`, is created with an empty value
(indicated by the second pair of adjacent colons),
and `NF` is updated with the value six.

Decrementing `NF` throws away the values of the fields
after the new value of `NF` and recomputes `$0`.
(d.c.)
Here is an example:

```awk
    $ echo a b c d e f | awk '{ print "NF =", NF;
    >                           NF = 3; print $0 }'
    -| NF = 6
    -| a b c
```    

> CAUTION: Some versions of `awk` don&rsquo;t
> rebuild `$0` when `NF` is decremented.

Finally, there are times when it is convenient to force
`awk` to rebuild the entire record, using the current
values of the fields and `OFS`.  To do this, use the
seemingly innocuous assignment:

```awk
    $1 = $1   # force record to be reconstituted
    print $0  # or whatever else with $0
```    

This forces `awk` to rebuild the record.  It does help
to add a comment, as we&rsquo;ve shown here.

There is a flip side to the relationship between `$0` and
the fields.  Any assignment to `$0` causes the record to be
reparsed into fields using the *current* value of `FS`.
This also applies to any built-in function that updates `$0`,
such as `sub()` and `gsub()`
(see [String Functions](#913-string-manipulation-functions)).

Understanding `$0`

It is important to remember that `$0` is the *full*
record, exactly as it was read from the input.  This includes
any leading or trailing whitespace, and the exact whitespace (or other
characters) that separates the fields.

It is a common error to try to change the field separators
in a record simply by setting `FS` and `OFS`, and then
expecting a plain &lsquo;print&rsquo; or &lsquo;print $0&rsquo; to print the
modified record.

But this does not work, because nothing was done to change the record
itself.  Instead, you must force the record to be rebuilt, typically
with a statement such as &lsquo;$1 = $1&rsquo;, as described earlier.

---

Next: [Constant Size](#46-reading-fixed-width-data), Previous: [Changing Fields](#44-changing-the-contents-of-a-field), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.5 Specifying How Fields Are Separated

| []() | []()
| - | -
| &bull; [Default Field Splitting](#451-whitespace-normally-separates-fields) | How fields are normally separated.
| &bull; [Regexp Field Splitting](#452-using-regular-expressions-to-separate-fields) | Using regexps as the field separator.
| &bull; [Single Character Fields](#453-making-each-character-a-separate-field) | Making each character a separate field.
| &bull; [Command Line Field Separator](#454-setting-fs-from-the-command-line) | Setting `FS` from the command line.
| &bull; [Full Line Fields](#455-making-the-full-line-be-a-single-field) | Making the full line be a single field.
| &bull; [Field Splitting Summary](#456-field-splitting-summary) | Some final points and a summary table.

The *field separator*, which is either a single character or a regular
expression, controls the way `awk` splits an input record into fields.
`awk` scans the input record for character sequences that
match the separator; the fields themselves are the text between the matches.

In the examples that follow, we use the bullet symbol (&bull;) to
represent spaces in the output.
If the field separator is &lsquo;oo&rsquo;, then the following line:

    moo goo gai pan
    

is split into three fields: &lsquo;m&rsquo;, &lsquo;&bull;g&rsquo;, and
&lsquo;&bull;gai&bull;pan&rsquo;.
Note the leading spaces in the values of the second and third fields.

The field separator is represented by the predefined variable `FS`.
Shell programmers take note:  `awk` does *not* use the
name `IFS` that is used by the POSIX-compliant shells (such as
the Unix Bourne shell, `sh`, or Bash).

The value of `FS` can be changed in the `awk` program with the
assignment operator, &lsquo;=&rsquo; (see [Assignment Ops](#623-assignment-expressions)).
Often, the right time to do this is at the beginning of execution
before any input has been processed, so that the very first record
is read with the proper separator.  To do this, use the special
`BEGIN` pattern
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)).
For example, here we set the value of `FS` to the string
`","`:

```awk
    awk 'BEGIN { FS = "," } ; { print $2 }'
```    

Given the input line:

    John Q. Smith, 29 Oak St., Walamazoo, MI 42139
    

this `awk` program extracts and prints the string
&lsquo;&bull;29&bull;Oak&bull;St.&rsquo;.

Sometimes the input data contains separator characters that don&rsquo;t
separate fields the way you thought they would.  For instance, the
person&rsquo;s name in the example we just used might have a title or
suffix attached, such as:

    John Q. Smith, LXIX, 29 Oak St., Walamazoo, MI 42139
    

The same program would extract &lsquo;&bull;LXIX&rsquo; instead of
&lsquo;&bull;29&bull;Oak&bull;St.&rsquo;.
If you were expecting the program to print the
address, you would be surprised.  The moral is to choose your data layout and
separator characters carefully to prevent such problems.
(If the data is not in a form that is easy to process, perhaps you
can massage it first with a separate `awk` program.)

---

Next: [Regexp Field Splitting](#452-using-regular-expressions-to-separate-fields), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.1 Whitespace Normally Separates Fields

Fields are normally separated by whitespace sequences
(spaces, TABs, and newlines), not by single spaces.  Two spaces in a row do not
delimit an empty field.  The default value of the field separator `FS`
is a string containing a single space, `" "`.  If `awk`
interpreted this value in the usual way, each space character would separate
fields, so two spaces in a row would make an empty field between them.
The reason this does not happen is that a single space as the value of
`FS` is a special case&mdash;it is taken to specify the default manner
of delimiting fields.

If `FS` is any other single character, such as `","`, then
each occurrence of that character separates two fields.  Two consecutive
occurrences delimit an empty field.  If the character occurs at the
beginning or the end of the line, that too delimits an empty field.  The
space character is the only single character that does not follow these
rules.

---

Next: [Single Character Fields](#453-making-each-character-a-separate-field), Previous: [Default Field Splitting](#451-whitespace-normally-separates-fields), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.2 Using Regular Expressions to Separate Fields

The previous subsection
discussed the use of single characters or simple strings as the
value of `FS`.
More generally, the value of `FS` may be a string containing any
regular expression.  In this case, each match in the record for the regular
expression separates fields.  For example, the assignment:

```awk
    FS = ", \t"
```    

makes every area of an input line that consists of a comma followed by a
space and a TAB into a field separator.

For a less trivial example of a regular expression, try using
single spaces to separate fields the way single commas are used.
`FS` can be set to `"[ ]"` (left bracket, space, right
bracket).  This regular expression matches a single space and nothing else
(see [Regexp](#3-regular-expressions)).

There is an important difference between the two cases of &lsquo;FS = " "&rsquo;
(a single space) and &lsquo;FS = "[ \t\n]+"&rsquo;
(a regular expression matching one or more spaces, TABs, or newlines).
For both values of `FS`, fields are separated by *runs*
(multiple adjacent occurrences) of spaces, TABs,
and/or newlines.  However, when the value of `FS` is `" "`,
`awk` first strips leading and trailing whitespace from
the record and then decides where the fields are.
For example, the following pipeline prints &lsquo;b&rsquo;:

```awk
    $ echo ' a b c d ' | awk '{ print $2 }'
    -| b
```    

However, this pipeline prints &lsquo;a&rsquo; (note the extra spaces around
each letter):

```awk
    $ echo ' a  b  c  d ' | awk 'BEGIN { FS = "[ \t\n]+" }
    >                                  { print $2 }'
    -| a
```    

In this case, the first field is null, or empty.

The stripping of leading and trailing whitespace also comes into
play whenever `$0` is recomputed.  For instance, study this pipeline:

```awk
    $ echo '   a b c d' | awk '{ print; $2 = $2; print }'
    -|    a b c d
    -| a b c d
```    

The first `print` statement prints the record as it was read,
with leading whitespace intact.  The assignment to `$2` rebuilds
`$0` by concatenating `$1` through `$NF` together,
separated by the value of `OFS` (which is a space by default).
Because the leading whitespace was ignored when finding `$1`,
it is not part of the new `$0`.  Finally, the last `print`
statement prints the new `$0`.

There is an additional subtlety to be aware of when using regular expressions
for field splitting.
It is not well specified in the POSIX standard, or anywhere else, what &lsquo;^&rsquo;
means when splitting fields.  Does the &lsquo;^&rsquo;  match only at the beginning of
the entire record? Or is each field separator a new string?  It turns out that
different `awk` versions answer this question differently, and you
should not rely on any specific behavior in your programs.
(d.c.)

As a point of information, BWK `awk` allows &lsquo;^&rsquo;
to match only at the beginning of the record. `gawk`
also works this way. For example:

```awk
    $ echo 'xxAA  xxBxx  C' |
    > gawk -F '(^x+)|( +)' '{ for (i = 1; i <= NF; i++)
    >                             printf "-->%s<--\n", $i }'
    -| --><--
    -| -->AA<--
    -| -->xxBxx<--
    -| -->C<--
```    

---

Next: [Command Line Field Separator](#454-setting-fs-from-the-command-line), Previous: [Regexp Field Splitting](#452-using-regular-expressions-to-separate-fields), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.3 Making Each Character a Separate Field

There are times when you may want to examine each character
of a record separately.  This can be done in `gawk` by
simply assigning the null string (`""`) to `FS`. (c.e.)
In this case,
each individual character in the record becomes a separate field.
For example:

```awk
    $ echo a b | gawk 'BEGIN { FS = "" }
    >                  {
    >                      for (i = 1; i <= NF; i = i + 1)
    >                          print "Field", i, "is", $i
    >                  }'
    -| Field 1 is a
    -| Field 2 is
    -| Field 3 is b
```    

Traditionally, the behavior of `FS` equal to `""` was not defined.
In this case, most versions of Unix `awk` simply treat the entire record
as only having one field.
(d.c.)
In compatibility mode
(see [Options](#22-command-line-options)),
if `FS` is the null string, then `gawk` also
behaves this way.

---

Next: [Full Line Fields](#455-making-the-full-line-be-a-single-field), Previous: [Single Character Fields](#453-making-each-character-a-separate-field), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.4 Setting `FS` from the Command Line

`FS` can be set on the command line.  Use the -F option to
do so.  For example:

```awk
    awk -F, 'program' input-files
```

sets `FS` to the &lsquo;,&rsquo; character.  Notice that the option uses
an uppercase &lsquo;F&rsquo; instead of a lowercase &lsquo;f&rsquo;. The latter
option (-f) specifies a file containing an `awk` program.

The value used for the argument to -F is processed in exactly the
same way as assignments to the predefined variable `FS`.
Any special characters in the field separator must be escaped
appropriately.  For example, to use a &lsquo;\&rsquo; as the field separator
on the command line, you would have to type:

```awk
    # same as FS = "\\"
    awk -F\\\\ '&hellip;' files &hellip;
```    

Because &lsquo;\&rsquo; is used for quoting in the shell, `awk` sees
&lsquo;-F\\&rsquo;.  Then `awk` processes the &lsquo;\\&rsquo; for escape
characters (see [Escape Sequences](#32-escape-sequences)), finally yielding
a single &lsquo;\&rsquo; to use for the field separator.

As a special case, in compatibility mode
(see [Options](#22-command-line-options)),
if the argument to -F is &lsquo;t&rsquo;, then `FS` is set to
the TAB character.  If you type &lsquo;-F\t&rsquo; at the
shell, without any quotes, the &lsquo;\&rsquo; gets deleted, so `awk`
figures that you really want your fields to be separated with TABs and
not &lsquo;t&rsquo;s.  Use &lsquo;-v FS="t"&rsquo; or &lsquo;-F"[t]"&rsquo; on the command line
if you really do want to separate your fields with &lsquo;t&rsquo;s.
Use &lsquo;-F '\t'&rsquo; when not in compatibility mode to specify that TABs
separate fields.

As an example, let&rsquo;s use an `awk` program file called edu.awk
that contains the pattern `/edu/` and the action &lsquo;print $1&rsquo;:

```awk
    /edu/   { print $1 }
```    

Let&rsquo;s also set `FS` to be the &lsquo;-&rsquo; character and run the
program on the file mail-list.  The following command prints a
list of the names of the people that work at or attend a university, and
the first three digits of their phone numbers:

```awk
    $ awk -F- -f edu.awk mail-list
    -| Fabius       555
    -| Samuel       555
    -| Jean
```    

Note the third line of output.  The third line
in the original file looked like this:

    Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
    

The &lsquo;-&rsquo; as part of the person&rsquo;s name was used as the field
separator, instead of the &lsquo;-&rsquo; in the phone number that was
originally intended.  This demonstrates why you have to be careful in
choosing your field and record separators.

Perhaps the most common use of a single character as the field separator
occurs when processing the Unix system password file.  On many Unix
systems, each user has a separate entry in the system password file, with one
line per user.  The information in these lines is separated by colons.
The first field is the user&rsquo;s login name and the second is the user&rsquo;s
encrypted or shadow password.  (A shadow password is indicated by the
presence of a single &lsquo;x&rsquo; in the second field.)  A password file
entry might look like this:

    arnold:x:2076:10:Arnold Robbins:/home/arnold:/bin/bash
    

The following program searches the system password file and prints
the entries for users whose full name is not indicated:

```awk
    awk -F: '$5 == ""' /etc/passwd
```    

---

Next: [Field Splitting Summary](#456-field-splitting-summary), Previous: [Command Line Field Separator](#454-setting-fs-from-the-command-line), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.5 Making the Full Line Be a Single Field

Occasionally, it&rsquo;s useful to treat the whole input line as a
single field.  This can be done easily and portably simply by
setting `FS` to `"\n"` (a newline):[21](#FOOT21)

```awk
    awk -F'\n' 'program' files &hellip;
```

When you do this, `$1` is the same as `$0`.

Changing `FS` Does Not Affect the Fields

According to the POSIX standard, `awk` is supposed to behave
as if each record is split into fields at the time it is read.
In particular, this means that if you change the value of `FS`
after a record is read, the values of the fields (i.e., how they were split)
should reflect the old value of `FS`, not the new one.

However, many older implementations of `awk` do not work this way.  Instead,
they defer splitting the fields until a field is actually
referenced.  The fields are split
using the *current* value of `FS`!
(d.c.)
This behavior can be difficult
to diagnose. The following example illustrates the difference
between the two methods:

```awk
    sed 1q /etc/passwd | awk '{ FS = ":" ; print $1 }'
```    

which usually prints:

    root
    

on an incorrect implementation of `awk`, while `gawk`
prints the full first line of the file, something like:

    root:x:0:0:Root:/:
    

(The `sed`[22](#FOOT22)
command prints just the first line of /etc/passwd.)

---

Previous: [Full Line Fields](#455-making-the-full-line-be-a-single-field), Up: [Field Separators](#45-specifying-how-fields-are-separated)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.5.6 Field-Splitting Summary

It is important to remember that when you assign a string constant
as the value of `FS`, it undergoes normal `awk` string
processing.  For example, with Unix `awk` and `gawk`,
the assignment &lsquo;FS = "\.."&rsquo; assigns the character string `".."`
to `FS` (the backslash is stripped).  This creates a regexp meaning
&ldquo;fields are separated by occurrences of any two characters.&rdquo;
If instead you want fields to be separated by a literal period followed
by any single character, use &lsquo;FS = "quot;\\.."&rsquo;.

The following list summarizes how fields are split, based on the value
of `FS` (&lsquo;==&rsquo; means &ldquo;is equal to&rdquo;):

<code>FS == " "</code>
Fields are separated by runs of whitespace.  Leading and trailing
whitespace are ignored.  This is the default.

<code>FS == any other single character</code>
Fields are separated by each occurrence of the character.  Multiple
successive occurrences delimit empty fields, as do leading and
trailing occurrences.
The character can even be a regexp metacharacter; it does not need
to be escaped.

<code>FS == regexp</code>
Fields are separated by occurrences of characters that match regexp.
Leading and trailing matches of regexp delimit empty fields.

<code>FS == ""</code>
Each individual character in the record becomes a separate field.
(This is a common extension; it is not specified by the POSIX standard.)

<code>FS</code> and <code>IGNORECASE</code>

The `IGNORECASE` variable
(see [User-modified](#751-built-in-variables-that-control-awk))
affects field splitting *only* when the value of `FS` is a regexp.
It has no effect when `FS` is a single character, even if
that character is a letter.  Thus, in the following code:

```awk
    FS = "c"
    IGNORECASE = 1
    $0 = "aCa"
    print $1
``` 

The output is &lsquo;aCa&rsquo;.  If you really want to split fields on an
alphabetic character while ignoring case, use a regexp that will
do it for you (e.g., &lsquo;FS = "[c]"&rsquo;).  In this case, `IGNORECASE`
will take effect.

---

Next: [Splitting By Content](#47-defining-fields-by-content), Previous: [Field Separators](#45-specifying-how-fields-are-separated), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.6 Reading Fixed-Width Data

This section discusses an advanced
feature of `gawk`.  If you are a novice `awk` user,
you might want to skip it on the first reading.

`gawk` provides a facility for dealing with fixed-width fields
with no distinctive field separator. We discuss this feature in
the following subsections.

| []() | []()
| - | -
| &bull; [Fixed width data](#461-processing-fixed-width-data) | Processing fixed-width data.
| &bull; [Skipping intervening](#462-skipping-intervening-fields) | Skipping intervening fields.
| &bull; [Allowing trailing data](#463-capturing-optional-trailing-data) | Capturing optional trailing data.
| &bull; [Fields with fixed data](#464-field-values-with-fixed-width-data) | Field values with fixed-width data.

---

Next: [Skipping intervening](#462-skipping-intervening-fields), Up: [Constant Size](#46-reading-fixed-width-data)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.6.1 Processing Fixed-Width Data

An example of fixed-width data would be the input for old Fortran programs
where numbers are run together, or the output of programs that did not
anticipate the use of their output as input for other programs.

An example of the latter is a table where all the columns are lined up
by the use of a variable number of spaces and *empty fields are
just spaces*.  Clearly, `awk`&rsquo;s normal field splitting based
on `FS` does not work well in this case.  Although a portable
`awk` program can use a series of `substr()` calls on
`$0` (see [String Functions](#913-string-manipulation-functions)), this is awkward and inefficient
for a large number of fields.

The splitting of an input record into fixed-width fields is specified by
assigning a string containing space-separated numbers to the built-in
variable `FIELDWIDTHS`.  Each number specifies the width of the
field, *including* columns between fields.  If you want to ignore
the columns between fields, you can specify the width as a separate
field that is subsequently ignored.  It is a fatal error to supply a
field width that has a negative value.

The following data is the output of the Unix `w` utility.  It is useful
to illustrate the use of `FIELDWIDTHS`:

     10:06pm  up 21 days, 14:04,  23 users
    User     tty       login  idle   JCPU   PCPU  what
    hzuo     ttyV0     8:58pm            9      5  vi p24.tex
    hzang    ttyV3     6:37pm    50                -csh
    eklye    ttyV5     9:53pm            7      1  em thes.tex
    dportein ttyV6     8:17pm  1:47                -csh
    gierd    ttyD3    10:00pm     1                elm
    dave     ttyD4     9:47pm            4      4  w
    brent    ttyp0    26Jun91  4:46  26:46   4:41  bash
    dave     ttyq4    26Jun9115days     46     46  wnewmail
    

The following program takes this input, converts the idle time to
number of seconds, and prints out the first two fields and the calculated
idle time:

```awk
    BEGIN  { FIELDWIDTHS = "9 6 10 6 7 7 35" }
    NR > 2 {
        idle = $4
        sub(/^ +/, "", idle)   # strip leading spaces
        if (idle == "")
            idle = 0
        if (idle ~ /:/) {      # hh:mm
            split(idle, t, ":")
            idle = t[1] * 60 + t[2]
        }
        if (idle ~ /days/)
            idle *= 24 * 60 * 60
    
        print $1, $2, idle
    }
```    

> NOTE: The preceding program uses a number of `awk` features that
> haven&rsquo;t been introduced yet.

Running the program on the data produces the following results:

    hzuo      ttyV0  0
    hzang     ttyV3  50
    eklye     ttyV5  0
    dportein  ttyV6  107
    gierd     ttyD3  1
    dave      ttyD4  0
    brent     ttyp0  286
    dave      ttyq4  1296000
    

Another (possibly more practical) example of fixed-width input data
is the input from a deck of balloting cards.  In some parts of
the United States, voters mark their choices by punching holes in computer
cards.  These cards are then processed to count the votes for any particular
candidate or on any particular issue.  Because a voter may choose not to
vote on some issue, any column on the card may be empty.  An `awk`
program for processing such data could use the `FIELDWIDTHS` feature
to simplify reading the data.  (Of course, getting `gawk` to run on
a system with card readers is another story!)

---

Next: [Allowing trailing data](#463-capturing-optional-trailing-data), Previous: [Fixed width data](#461-processing-fixed-width-data), Up: [Constant Size](#46-reading-fixed-width-data)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.6.2 Skipping Intervening Fields

Starting in version 4.2, each field width may optionally be
preceded by a colon-separated value specifying the number of characters
to skip before the field starts.  Thus, the preceding program could be
rewritten to specify `FIELDWIDTHS` like so:

```awk
    BEGIN  { FIELDWIDTHS = "8 1:5 4:7 6 1:6 1:6 2:33" }
```    

This strips away some of the white space separating the fields. With such
a change, the program produces the following results:

    hzang    ttyV3 50
    eklye    ttyV5 0
    dportein ttyV6 107
    gierd    ttyD3 1
    dave     ttyD4 0
    brent    ttyp0 286
    dave     ttyq4 1296000
    

---

Next: [Fields with fixed data](#464-field-values-with-fixed-width-data), Previous: [Skipping intervening](#462-skipping-intervening-fields), Up: [Constant Size](#46-reading-fixed-width-data)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.6.3 Capturing Optional Trailing Data

There are times when fixed-width data may be followed by additional data
that has no fixed length.  Such data may or may not be present, but if
it is, it should be possible to get at it from an `awk` program.

Starting with version 4.2, in order to provide a way to say &ldquo;anything
else in the record after the defined fields,&rdquo; `gawk`
allows you to add a final &lsquo;*&rsquo; character to the value of
`FIELDWIDTHS`. There can only be one such character, and it must
be the final non-whitespace character in `FIELDWIDTHS`.
For example:

```awk
    $ cat fw.awkShow the program
    -| BEGIN { FIELDWIDTHS = "2 2 *" }
    -| { print NF, $1, $2, $3 }
    $ cat fw.inShow sample input
    -| 1234abcdefghi
    $ gawk -f fw.awk fw.inRun the program
    -| 3 12 34 abcdefghi
```    

---

Previous: [Allowing trailing data](#463-capturing-optional-trailing-data), Up: [Constant Size](#46-reading-fixed-width-data)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.6.4 Field Values With Fixed-Width Data

So far, so good.  But what happens if there isn&rsquo;t as much data as there
should be based on the contents of `FIELDWIDTHS`? Or, what happens
if there is more data than expected?

For many years, what happens in these cases was not well defined. Starting
with version 4.2, the rules are as follows:

Enough data for some fields
For example, if `FIELDWIDTHS` is set to `"2 3 4"` and the
input record is &lsquo;aabbb&rsquo;.  In this case, `NF` is set to two.

Not enough data for a field
For example, if `FIELDWIDTHS` is set to `"2 3 4"` and the
input record is &lsquo;aab&rsquo;.  In this case, `NF` is set to two and
`$2` has the value `"b"`. The idea is that even though there
aren&rsquo;t as many characters as were expected, there are some, so the data
should be made available to the program.

Too much data
For example, if `FIELDWIDTHS` is set to `"2 3 4"` and the
input record is &lsquo;aabbbccccddd&rsquo;.  In this case, `NF` is set to
three and the extra characters (&lsquo;ddd&rsquo;) are ignored.  If you want
`gawk` to capture the extra characters, supply a final &lsquo;*&rsquo;
in the value of `FIELDWIDTHS`.

Too much data, but with &lsquo;*&rsquo; supplied
For example, if `FIELDWIDTHS` is set to `"2 3 4 *"` and the
input record is &lsquo;aabbbccccddd&rsquo;.  In this case, `NF` is set to
four, and `$4` has the value `"ddd"`.

---

Next: [Testing field creation](#48-checking-how-gawk-is-splitting-records), Previous: [Constant Size](#46-reading-fixed-width-data), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.7 Defining Fields by Content

This section discusses an advanced
feature of `gawk`.  If you are a novice `awk` user,
you might want to skip it on the first reading.

Normally, when using `FS`, `gawk` defines the fields as the
parts of the record that occur in between each field separator. In other
words, `FS` defines what a field *is not*, instead of what a field
*is*.
However, there are times when you really want to define the fields by
what they are, and not by what they are not.

The most notorious such case
is so-called *comma-separated values* (CSV) data. Many spreadsheet programs,
for example, can export their data into text files, where each record is
terminated with a newline, and fields are separated by commas. If
commas only separated the data, there wouldn&rsquo;t be an issue. The problem comes when
one of the fields contains an *embedded* comma.
In such cases, most programs embed the field in double quotes.[23](#FOOT23)
So, we might have data like this:

    Robbins,Arnold,"1234 A Pretty Street, NE",MyTown,MyState,12345-6789,USA
    

The `FPAT` variable offers a solution for cases like this.
The value of `FPAT` should be a string that provides a regular expression.
This regular expression describes the contents of each field.

In the case of CSV data as presented here, each field is either &ldquo;anything that
is not a comma,&rdquo; or &ldquo;a double quote, anything that is not a double quote, and a
closing double quote.&rdquo;  If written as a regular expression constant
(see [Regexp](#3-regular-expressions)),
we would have `/([^,]+)|("[^"]+")/`.
Writing this as a string requires us to escape the double quotes, leading to:

```awk
    FPAT = "([^,]+)|(\"[^\"]+\")"
```    

Putting this to use, here is a simple program to parse the data:

```awk
    BEGIN {
        FPAT = "([^,]+)|(\"[^\"]+\")"
    }
    

    {
        print "NF = ", NF
        for (i = 1; i <= NF; i++) {
            printf("$%d = <%s>\n", i, $i)
        }
    }
```    

When run, we get the following:

    $ gawk -f simple-csv.awk addresses.csv
    NF =  7
    $1 = <Robbins>
    $2 = <Arnold>
    $3 = <"1234 A Pretty Street, NE">
    $4 = <MyTown>
    $5 = <MyState>
    $6 = <12345-6789>
    $7 = <USA>
    

Note the embedded comma in the value of `$3`.

A straightforward improvement when processing CSV data of this sort
would be to remove the quotes when they occur, with something like this:

```awk
    if (substr($i, 1, 1) == "\"") {
        len = length($i)
        $i = substr($i, 2, len - 2)    # Get text within the two quotes
    }
```    

As with `FS`, the `IGNORECASE` variable (see [User-modified](#751-built-in-variables-that-control-awk))
affects field splitting with `FPAT`.

Assigning a value to `FPAT` overrides field splitting
with `FS` and with `FIELDWIDTHS`.

> NOTE: Some programs export CSV data that contains embedded newlines between
> the double quotes.  `gawk` provides no way to deal with this.
> Even though a formal specification for CSV data exists, there isn&rsquo;t much
> more to be done;
> the `FPAT` mechanism provides an elegant solution for the majority
> of cases, and the `gawk` developers are satisfied with that.

As written, the regexp used for `FPAT` requires that each field
contain at least one character.  A straightforward modification
(changing the first &lsquo;+&rsquo; to &lsquo;*&rsquo;) allows fields to be empty:

```awk
    FPAT = "([^,]*)|(\"[^\"]+\")"
```    

Finally, the `patsplit()` function makes the same functionality
available for splitting regular strings (see [String Functions](#913-string-manipulation-functions)).

---

Next: [Multiple Line](#49-multiple-line-records), Previous: [Splitting By Content](#47-defining-fields-by-content), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.8 Checking How `gawk` Is Splitting Records

As we&rsquo;ve seen, `gawk` provides three independent methods to split
input records into fields.  The mechanism used is based on which of the
three variables&mdash;`FS`, `FIELDWIDTHS`, or `FPAT`&mdash;was
last assigned to. In addition, an API input parser may choose to override
the record parsing mechanism; please refer to [Input Parsers](#Input-Parsers) for
further information about this feature.

To restore normal field splitting after using `FIELDWIDTHS`
and/or `FPAT`, simply assign a value to `FS`.
You can use &lsquo;FS = FS&rsquo; to do this,
without having to know the current value of `FS`.

In order to tell which kind of field splitting is in effect,
use `PROCINFO["FS"]` (see [Auto-set](#752-built-in-variables-that-convey-information)).
The value is `"FS"` if regular field splitting is being used,
`"FIELDWIDTHS"` if fixed-width field splitting is being used,
or `"FPAT"` if content-based field splitting is being used:

```awk
    if (PROCINFO["FS"] == "FS")
        regular field splitting &hellip;
    else if (PROCINFO["FS"] == "FIELDWIDTHS")
        fixed-width field splitting &hellip;
    else if (PROCINFO["FS"] == "FPAT")
        content-based field splitting &hellip;
    else
        API input parser field splitting &hellip; (advanced feature)
```

This information is useful when writing a function that needs to
temporarily change `FS` or `FIELDWIDTHS`, read some records,
and then restore the original settings (see [Passwd Functions](#105-reading-the-user-database) for an
example of such a function).

---

Next: [Getline](#410-explicit-input-with-getline), Previous: [Testing field creation](#48-checking-how-gawk-is-splitting-records), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.9 Multiple-Line Records

In some databases, a single line cannot conveniently hold all the
information in one entry.  In such cases, you can use multiline
records.  The first step in doing this is to choose your data format.

One technique is to use an unusual character or string to separate
records.  For example, you could use the formfeed character (written
&lsquo;\f&rsquo; in `awk`, as in C) to separate them, making each record
a page of the file.  To do this, just set the variable `RS` to
`"\f"` (a string containing the formfeed character).  Any
other character could equally well be used, as long as it won&rsquo;t be part
of the data in a record.

Another technique is to have blank lines separate records.  By a special
dispensation, an empty string as the value of `RS` indicates that
records are separated by one or more blank lines.  When `RS` is set
to the empty string, each record always ends at the first blank line
encountered.  The next record doesn&rsquo;t start until the first nonblank
line that follows.  No matter how many blank lines appear in a row, they
all act as one record separator.
(Blank lines must be completely empty; lines that contain only
whitespace do not count.)

You can achieve the same effect as &lsquo;RS = ""&rsquo; by assigning the
string `"\n\n+"` to `RS`. This regexp matches the newline
at the end of the record and one or more blank lines after the record.
In addition, a regular expression always matches the longest possible
sequence when there is a choice
(see [Leftmost Longest](#35-how-much-text-matches)).
So, the next record doesn&rsquo;t start until
the first nonblank line that follows&mdash;no matter how many blank lines
appear in a row, they are considered one record separator.

However, there is an important difference between &lsquo;RS = ""&rsquo; and
&lsquo;RS = "\n\n+"&rsquo;. In the first case, leading newlines in the input
data file are ignored, and if a file ends without extra blank lines
after the last record, the final newline is removed from the record.
In the second case, this special processing is not done.
(d.c.)

Now that the input is separated into records, the second step is to
separate the fields in the records.  One way to do this is to divide each
of the lines into fields in the normal manner.  This happens by default
as the result of a special feature.  When `RS` is set to the empty
string *and*`FS` is set to a single character,
the newline character *always* acts as a field separator.
This is in addition to whatever field separations result from
`FS`.[24](#FOOT24)

The original motivation for this special exception was probably to provide
useful behavior in the default case (i.e., `FS` is equal
to `" "`).  This feature can be a problem if you really don&rsquo;t
want the newline character to separate fields, because there is no way to
prevent it.  However, you can work around this by using the `split()`
function to break up the record manually
(see [String Functions](#913-string-manipulation-functions)).
If you have a single-character field separator, you can work around
the special feature in a different way, by making `FS` into a
regexp for that single character.  For example, if the field
separator is a percent character, instead of
&lsquo;FS = "%"&rsquo;, use &lsquo;FS = "[%]"&rsquo;.

Another way to separate fields is to
put each field on a separate line: to do this, just set the
variable `FS` to the string `"\n"`.
(This single-character separator matches a single newline.)
A practical example of a data file organized this way might be a mailing
list, where blank lines separate the entries.  Consider a mailing
list in a file named addresses, which looks like this:

    Jane Doe
    123 Main Street
    Anywhere, SE 12345-6789
    
    John Smith
    456 Tree-lined Avenue
    Smallville, MW 98765-4321
    &hellip;
    

A simple program to process this file is as follows:

```awk
    # addrs.awk --- simple mailing list program
    
    # Records are separated by blank lines.
    # Each line is one field.
    BEGIN { RS = "" ; FS = "\n" }
    
    {
          print "Name is:", $1
          print "Address is:", $2
          print "City and State are:", $3
          print ""
    }
```    

Running the program produces the following output:

```awk
    $ awk -f addrs.awk addresses
    -| Name is: Jane Doe
    -| Address is: 123 Main Street
    -| City and State are: Anywhere, SE 12345-6789
    -|
    -| Name is: John Smith
    -| Address is: 456 Tree-lined Avenue
    -| City and State are: Smallville, MW 98765-4321
    -|
    &hellip;
```    

See [Labels Program](#1134-printing-mailing-labels) for a more realistic program dealing with
address lists.  The following list summarizes how records are split,
based on the value of
`RS`:

<code>RS == "\n"</code>
Records are separated by the newline character (&lsquo;\n&rsquo;).  In effect,
every line in the data file is a separate record, including blank lines.
This is the default.

<code>RS == any single character</code>
Records are separated by each occurrence of the character.  Multiple
successive occurrences delimit empty records.

<code>RS == ""</code>
Records are separated by runs of blank lines.
When `FS` is a single character, then
the newline character
always serves as a field separator, in addition to whatever value
`FS` may have. Leading and trailing newlines in a file are ignored.

<code>RS == regexp</code>
Records are separated by occurrences of characters that match regexp.
Leading and trailing matches of regexp delimit empty records.
(This is a `gawk` extension; it is not specified by the
POSIX standard.)

If not in compatibility mode (see [Options](#22-command-line-options)), `gawk` sets
`RT` to the input text that matched the value specified by `RS`.
But if the input file ended without any text that matches `RS`,
then `gawk` sets `RT` to the null string.

---

Next: [Read Timeout](#411-reading-input-with-a-timeout), Previous: [Multiple Line](#49-multiple-line-records), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.10 Explicit Input with `getline`

So far we have been getting our input data from `awk`&rsquo;s main
input stream&mdash;either the standard input (usually your keyboard, sometimes
the output from another program) or the
files specified on the command line.  The `awk` language has a
special built-in command called `getline` that
can be used to read input under your explicit control.

The `getline` command is used in several different ways and should
*not* be used by beginners.
The examples that follow the explanation of the `getline` command
include material that has not been covered yet.  Therefore, come back
and study the `getline` command *after* you have reviewed the
rest of
this Web page
and have a good knowledge of how `awk` works.

The `getline` command returns 1 if it finds a record and 0 if
it encounters the end of the file.  If there is some error in getting
a record, such as a file that cannot be opened, then `getline`
returns -1.  In this case, `gawk` sets the variable
`ERRNO` to a string describing the error that occurred.

If `ERRNO` indicates that the I/O operation may be
retried, and `PROCINFO["input", "RETRY"]` is set,
then `getline` returns -2
instead of -1, and further calls to `getline`
may be attempted.  See [Retrying Input](#412-retrying-reads-after-certain-input-errors) for further information about
this feature.

In the following examples, command stands for a string value that
represents a shell command.

> NOTE: When --sandbox is specified (see [Options](#22-command-line-options)),
> reading lines from files, pipes, and coprocesses is disabled.

| []() | []()
| - | -
| &bull; [Plain Getline](#4101-using-getline-with-no-arguments) | Using `getline` with no arguments.
| &bull; [Getline/Variable](#4102-using-getline-into-a-variable) | Using `getline` into a variable.
| &bull; [Getline/File](#4103-using-getline-from-a-file) | Using `getline` from a file.
| &bull; [Getline/Variable/File](#4104-using-getline-into-a-variable-from-a-file) | Using `getline` into a variable from a file.
| &bull; [Getline/Pipe](#4105-using-getline-from-a-pipe) | Using `getline` from a pipe.
| &bull; [Getline/Variable/Pipe](#4106-using-getline-into-a-variable-from-a-pipe) | Using `getline` into a variable from a pipe.
| &bull; [Getline/Coprocess](#4107-using-getline-from-a-coprocess) | Using `getline` from a coprocess.
| &bull; [Getline/Variable/Coprocess](#4108-using-getline-into-a-variable-from-a-coprocess) | Using `getline` into a variable from a coprocess.
| &bull; [Getline Notes](#4109-points-to-remember-about-getline) | Important things to know about `getline`.
| &bull; [Getline Summary](#41010-summary-of-getline-variants) | Summary of `getline` Variants.

---

Next: [Getline/Variable](#4102-using-getline-into-a-variable), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.1 Using `getline` with No Arguments

The `getline` command can be used without arguments to read input
from the current input file.  All it does in this case is read the next
input record and split it up into fields.  This is useful if you&rsquo;ve
finished processing the current record, but want to do some special
processing on the next record *right now*.  For example:

```awk
    # Remove text between /* and */, inclusive
    {
        if ((i = index($0, "/*")) != 0) {
            out = substr($0, 1, i - 1)  # leading part of the string
            rest = substr($0, i + 2)    # ... */ ...
            j = index(rest, "*/")       # is */ in trailing part?
            if (j > 0) {
                rest = substr(rest, j + 2)  # remove comment
            } else {
                while (j == 0) {
                    # get more text
                    if (getline <= 0) {
                        print("unexpected EOF or error:", ERRNO) > "/dev/stderr"
                        exit
                    }
                    # build up the line using string concatenation
                    rest = rest $0
                    j = index(rest, "*/")   # is */ in trailing part?
                    if (j != 0) {
                        rest = substr(rest, j + 2)
                        break
                    }
                }
            }
            # build up the output line using string concatenation
            $0 = out rest
        }
        print $0
    }
``` 

This `awk` program deletes C-style comments (&lsquo;/* &hellip;
*/&rsquo;) from the input.
It uses a number of features we haven&rsquo;t covered yet, including
string concatenation
(see [Concatenation](#622-string-concatenation))
and the `index()` and `substr()` built-in
functions
(see [String Functions](#913-string-manipulation-functions)).
By replacing the &lsquo;print $0&rsquo; with other
statements, you could perform more complicated processing on the
decommented input, such as searching for matches of a regular
expression.  (This program has a subtle problem&mdash;it does not work if one
comment ends and another begins on the same line.)

This form of the `getline` command sets `NF`,
`NR`, `FNR`, `RT`, and the value of `$0`.

> NOTE: The new value of `$0` is used to test
> the patterns of any subsequent rules.  The original value
> of `$0` that triggered the rule that executed `getline`
> is lost.
> By contrast, the `next` statement reads a new record
> but immediately begins processing it normally, starting with the first
> rule in the program.  See [Next Statement](#748-the-next-statement).

---

Next: [Getline/File](#4103-using-getline-from-a-file), Previous: [Plain Getline](#4101-using-getline-with-no-arguments), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.2 Using `getline` into a Variable

You can use &lsquo;getline var&rsquo; to read the next record from
`awk`&rsquo;s input into the variable var.  No other processing is
done.
For example, suppose the next line is a comment or a special string,
and you want to read it without triggering
any rules.  This form of `getline` allows you to read that line
and store it in a variable so that the main
read-a-line-and-check-each-rule loop of `awk` never sees it.
The following example swaps every two lines of input:

```awk
    {
         if ((getline tmp) > 0) {
              print tmp
              print $0
         } else
              print $0
    }
```    

It takes the following list:

    wan
    tew
    free
    phore
    

and produces these results:

    tew
    wan
    phore
    free
    

The `getline` command used in this way sets only the variables
`NR`, `FNR`, and `RT` (and, of course, var).
The record is not
split into fields, so the values of the fields (including `$0`) and
the value of `NF` do not change.

---

Next: [Getline/Variable/File](#4104-using-getline-into-a-variable-from-a-file), Previous: [Getline/Variable](#4102-using-getline-into-a-variable), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.3 Using `getline` from a File

Use &lsquo;getline < file&rsquo; to read the next record from file.
Here, file is a string-valued expression that
specifies the file name.  &lsquo;< file&rsquo; is called a *redirection*
because it directs input to come from a different place.
For example, the following
program reads its input record from the file secondary.input when it
encounters a first field with a value equal to 10 in the current input
file:

```awk
    {
        if ($1 == 10) {
             getline < "secondary.input"
             print
        } else
             print
    }
```    

Because the main input stream is not used, the values of `NR` and
`FNR` are not changed. However, the record it reads is split into fields in
the normal manner, so the values of `$0` and the other fields are
changed, resulting in a new value of `NF`.
`RT` is also set.

According to POSIX, &lsquo;getline < expression&rsquo; is ambiguous if
expression contains unparenthesized operators other than
&lsquo;$&rsquo;; for example, &lsquo;getline < dir "/" file&rsquo; is ambiguous
because the concatenation operator (not discussed yet; see [Concatenation](#622-string-concatenation))
is not parenthesized.  You should write it as &lsquo;getline < (dir "/" file)&rsquo; if
you want your program to be portable to all `awk` implementations.

---

Next: [Getline/Pipe](#4105-using-getline-from-a-pipe), Previous: [Getline/File](#4103-using-getline-from-a-file), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.4 Using `getline` into a Variable from a File

Use &lsquo;getline var < file&rsquo; to read input
from the file
file, and put it in the variable var.  As earlier, file
is a string-valued expression that specifies the file from which to read.

In this version of `getline`, none of the predefined variables are
changed and the record is not split into fields.  The only variable
changed is var.[25](#FOOT25)
For example, the following program copies all the input files to the
output, except for records that say &lsquo;@include filename&rsquo;.
Such a record is replaced by the contents of the file
filename:

```awk
    {
         if (NF == 2 && $1 == "@include") {
              while ((getline line < $2) > 0)
                   print line
              close($2)
         } else
              print
    }
```    

Note here how the name of the extra input file is not built into
the program; it is taken directly from the data, specifically from the second field on
the `@include` line.

The `close()` function is called to ensure that if two identical
`@include` lines appear in the input, the entire specified file is
included twice.
See [Close Files And Pipes](#59-closing-input-and-output-redirections).

One deficiency of this program is that it does not process nested
`@include` statements
(i.e., `@include` statements in included files)
the way a true macro preprocessor would.
See [Igawk Program](#1139-an-easy-way-to-use-library-functions) for a program
that does handle nested `@include` statements.

---

Next: [Getline/Variable/Pipe](#4106-using-getline-into-a-variable-from-a-pipe), Previous: [Getline/Variable/File](#4104-using-getline-into-a-variable-from-a-file), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.5 Using `getline` from a Pipe

> Omniscience has much to recommend it.
> Failing that, attention to details would be useful.

&mdash; *Brian Kernighan*

The output of a command can also be piped into `getline`, using
&lsquo;command | getline&rsquo;.  In
this case, the string command is run as a shell command and its output
is piped into `awk` to be used as input.  This form of `getline`
reads one record at a time from the pipe.
For example, the following program copies its input to its output, except for
lines that begin with &lsquo;@execute&rsquo;, which are replaced by the output
produced by running the rest of the line as a shell command:

```awk
    {
         if ($1 == "@execute") {
              tmp = substr($0, 10)        # Remove "@execute"
              while ((tmp | getline) > 0)
                   print
              close(tmp)
         } else
              print
    }
```    

The `close()` function is called to ensure that if two identical
&lsquo;@execute&rsquo; lines appear in the input, the command is run for
each one.
See [Close Files And Pipes](#59-closing-input-and-output-redirections).
Given the input:

    foo
    bar
    baz
    @execute who
    bletch
    

the program might produce:

    foo
    bar
    baz
    arnold     ttyv0   Jul 13 14:22
    miriam     ttyp0   Jul 13 14:23     (murphy:0)
    bill       ttyp1   Jul 13 14:23     (murphy:0)
    bletch
    

Notice that this program ran the command `who` and printed the result.
(If you try this program yourself, you will of course get different results,
depending upon who is logged in on your system.)

This variation of `getline` splits the record into fields, sets the
value of `NF`, and recomputes the value of `$0`.  The values of
`NR` and `FNR` are not changed.
`RT` is set.

According to POSIX, &lsquo;expression | getline&rsquo; is ambiguous if
expression contains unparenthesized operators other than
&lsquo;$&rsquo;&mdash;for example, &lsquo;"echo " "date" | getline&rsquo; is ambiguous
because the concatenation operator is not parenthesized.  You should
write it as &lsquo;("echo " "date") | getline&rsquo; if you want your program
to be portable to all `awk` implementations.

> NOTE: Unfortunately, `gawk` has not been consistent in its treatment
> of a construct like &lsquo;"echo " "date" | getline&rsquo;.
> Most versions, including the current version, treat it at as
> &lsquo;("echo " "date") | getline&rsquo;.
> (This is also how BWK `awk` behaves.)
> Some versions instead treat it as
> &lsquo;"echo " ("date" | getline)&rsquo;.
> (This is how `mawk` behaves.)
> In short, *always* use explicit parentheses, and then you won&rsquo;t
> have to worry.

---

Next: [Getline/Coprocess](#4107-using-getline-from-a-coprocess), Previous: [Getline/Pipe](#4105-using-getline-from-a-pipe), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.6 Using `getline` into a Variable from a Pipe

When you use &lsquo;command | getline var&rsquo;, the
output of command is sent through a pipe to
`getline` and into the variable var.  For example, the
following program reads the current date and time into the variable
`current_time`, using the `date` utility, and then
prints it:

```awk
    BEGIN {
         "date" | getline current_time
         close("date")
         print "Report printed on " current_time
    }
```    

In this version of `getline`, none of the predefined variables are
changed and the record is not split into fields. However, `RT` is set.

---

Next: [Getline/Variable/Coprocess](#4108-using-getline-into-a-variable-from-a-coprocess), Previous: [Getline/Variable/Pipe](#4106-using-getline-into-a-variable-from-a-pipe), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.7 Using `getline` from a Coprocess

Reading input into `getline` from a pipe is a one-way operation.
The command that is started with &lsquo;command | getline&rsquo; only
sends data *to* your `awk` program.

On occasion, you might want to send data to another program
for processing and then read the results back.
`gawk` allows you to start a *coprocess*, with which two-way
communications are possible.  This is done with the &lsquo;|&&rsquo;
operator.
Typically, you write data to the coprocess first and then
read the results back, as shown in the following:

```awk
    print "some query" |& "db_server"
    "db_server" |& getline
```    

which sends a query to `db_server` and then reads the results.

The values of `NR` and
`FNR` are not changed,
because the main input stream is not used.
However, the record is split into fields in
the normal manner, thus changing the values of `$0`, of the other fields,
and of `NF` and `RT`.

Coprocesses are an advanced feature. They are discussed here only because
this is the section on `getline`.
See [Two-way I/O](#Two_002dway-I_002fO),
where coprocesses are discussed in more detail.

---

Next: [Getline Notes](#4109-points-to-remember-about-getline), Previous: [Getline/Coprocess](#4107-using-getline-from-a-coprocess), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.8 Using `getline` into a Variable from a Coprocess

When you use &lsquo;command |& getline var&rsquo;, the output from
the coprocess command is sent through a two-way pipe to `getline`
and into the variable var.

In this version of `getline`, none of the predefined variables are
changed and the record is not split into fields.  The only variable
changed is var.
However, `RT` is set.

---

Next: [Getline Summary](#41010-summary-of-getline-variants), Previous: [Getline/Variable/Coprocess](#4108-using-getline-into-a-variable-from-a-coprocess), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.9 Points to Remember About `getline`

Here are some miscellaneous points about `getline` that
you should bear in mind:

-  When `getline` changes the value of `$0` and `NF`,
`awk` does *not* automatically jump to the start of the
program and start testing the new record against every pattern.
However, the new record is tested against any subsequent rules.

- 
Some very old `awk` implementations limit the number of pipelines that an `awk`
program may have open to just one.  In `gawk`, there is no such limit.
You can open as many pipelines (and coprocesses) as the underlying operating
system permits.

- 
An interesting side effect occurs if you use `getline` without a
redirection inside a `BEGIN` rule. Because an unredirected `getline`
reads from the command-line data files, the first `getline` command
causes `awk` to set the value of `FILENAME`. Normally,
`FILENAME` does not have a value inside `BEGIN` rules, because you
have not yet started to process the command-line data files.
(d.c.)
(See [BEGIN/END](#714-the-begin-and-end-special-patterns);
also see [Auto-set](#752-built-in-variables-that-convey-information).)

-  Using `FILENAME` with `getline`
(&lsquo;getline < FILENAME&rsquo;)
is likely to be a source of
confusion.  `awk` opens a separate input stream from the
current input file.  However, by not using a variable, `$0`
and `NF` are still updated.  If you&rsquo;re doing this, it&rsquo;s
probably by accident, and you should reconsider what it is you&rsquo;re
trying to accomplish.

- [Getline Summary](#41010-summary-of-getline-variants),
presents a table summarizing the
`getline` variants and which variables they can affect.
It is worth noting that those variants that do not use redirection
can cause `FILENAME` to be updated if they cause
`awk` to start reading a new input file.

- 
If the variable being assigned is an expression with side effects,
different versions of `awk` behave differently upon encountering
end-of-file.  Some versions don&rsquo;t evaluate the expression; many versions
(including `gawk`) do.  Here is an example, courtesy of Duncan Moore:

```awk
    BEGIN {
        system("echo 1 > f")
        while ((getline a[++c] < "f") > 0) { }
        print c
    }
```    

Here, the side effect is the &lsquo;++c&rsquo;.  Is `c` incremented if
end-of-file is encountered before the element in `a` is assigned?

`gawk` treats `getline` like a function call, and evaluates
the expression &lsquo;a[++c]&rsquo; before attempting to read from f.
However, some versions of `awk` only evaluate the expression once they
know that there is a string value to be assigned.

---

Previous: [Getline Notes](#4109-points-to-remember-about-getline), Up: [Getline](#410-explicit-input-with-getline)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 4.10.10 Summary of `getline` Variants

[Table 4.1](#table_002dgetline_002dvariants)
summarizes the eight variants of `getline`,
listing which predefined variables are set by each one,
and whether the variant is standard or a `gawk` extension.
Note: for each variant, `gawk` sets the `RT` predefined variable.

VariantEffect`awk` / `gawk``getline`Sets `$0`, `NF`, `FNR`, `NR`, and `RT``awk``getline`varSets var, `FNR`, `NR`, and `RT``awk``getline <`fileSets `$0`, `NF`, and `RT``awk``getline var < file`Sets var and `RT``awk`command`| getline`Sets `$0`, `NF`, and `RT``awk`command`| getline`varSets var and `RT``awk`command`|& getline`Sets `$0`, `NF`, and `RT``gawk`command`|& getline`varSets var and `RT``gawk`

**Table 4.1: **`getline` variants and what they set

---

Next: [Retrying Input](#412-retrying-reads-after-certain-input-errors), Previous: [Getline](#410-explicit-input-with-getline), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.11 Reading Input with a Timeout

This section describes a feature that is specific to `gawk`.

You may specify a timeout in milliseconds for reading input from the keyboard,
a pipe, or two-way communication, including TCP/IP sockets. This can be done
on a per-input, per-command, or per-connection basis, by setting a special
element in the `PROCINFO` array (see [Auto-set](#752-built-in-variables-that-convey-information)):

```awk
    PROCINFO["input_name", "READ_TIMEOUT"] = timeout in milliseconds
```

When set, this causes `gawk` to time out and return failure
if no data is available to read within the specified timeout period.
For example, a TCP client can decide to give up on receiving
any response from the server after a certain amount of time:

```awk
    Service = "/inet/tcp/0/localhost/daytime"
    PROCINFO[Service, "READ_TIMEOUT"] = 100
    if ((Service |& getline) > 0)
        print $0
    else if (ERRNO != "")
        print ERRNO
```    

Here is how to read interactively from the user[26](#FOOT26) without waiting
for more than five seconds:

```awk
    PROCINFO["/dev/stdin", "READ_TIMEOUT"] = 5000
    while ((getline < "/dev/stdin") > 0)
        print $0
```    

`gawk` terminates the read operation if input does not
arrive after waiting for the timeout period, returns failure,
and sets `ERRNO` to an appropriate string value.
A negative or zero value for the timeout is the same as specifying
no timeout at all.

A timeout can also be set for reading from the keyboard in the implicit
loop that reads input records and matches them against patterns,
like so:

```awk
    $ gawk 'BEGIN { PROCINFO["-", "READ_TIMEOUT"] = 5000 }
    > { print "You entered: " $0 }'gawk
    -| You entered: gawk
```    

In this case, failure to respond within five seconds results in the following
error message:

    error&rarr; gawk: cmd. line:2: (FILENAME=- FNR=1) fatal: error reading input file `-': Connection timed out
    

The timeout can be set or changed at any time, and will take effect on the
next attempt to read from the input device. In the following example,
we start with a timeout value of one second, and progressively
reduce it by one-tenth of a second until we wait indefinitely
for the input to arrive:

```awk
    PROCINFO[Service, "READ_TIMEOUT"] = 1000
    while ((Service |& getline) > 0) {
        print $0
        PROCINFO[Service, "READ_TIMEOUT"] -= 100
    }
```    

> NOTE: You should not assume that the read operation will block
> exactly after the tenth record has been printed. It is possible that
> `gawk` will read and buffer more than one record&rsquo;s
> worth of data the first time. Because of this, changing the value
> of timeout like in the preceding example is not very useful.

If the `PROCINFO` element is not present and the
`GAWK_READ_TIMEOUT` environment variable exists,
`gawk` uses its value to initialize the timeout value.
The exclusive use of the environment variable to specify timeout
has the disadvantage of not being able to control it
on a per-command or per-connection basis.

`gawk` considers a timeout event to be an error even though
the attempt to read from the underlying device may
succeed in a later attempt. This is a limitation, and it also
means that you cannot use this to multiplex input from
two or more sources.  See [Retrying Input](#412-retrying-reads-after-certain-input-errors) for a way to enable 
later I/O attempts to succeed.

Assigning a timeout value prevents read operations from
blocking indefinitely. But bear in mind that there are other ways
`gawk` can stall waiting for an input device to be ready.
A network client can sometimes take a long time to establish
a connection before it can start reading any data,
or the attempt to open a FIFO special file for reading can block
indefinitely until some other process opens it for writing.

---

Next: [Command-line directories](#413-directories-on-the-command-line), Previous: [Read Timeout](#411-reading-input-with-a-timeout), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.12 Retrying Reads After Certain Input Errors

This section describes a feature that is specific to `gawk`.

When `gawk` encounters an error while reading input, by
default `getline` returns -1, and subsequent attempts to
read from that file result in an end-of-file indication.  However, you
may optionally instruct `gawk` to allow I/O to be retried when
certain errors are encountered by setting a special element in
the `PROCINFO` array (see [Auto-set](#752-built-in-variables-that-convey-information)):

```awk
    PROCINFO["input_name", "RETRY"] = 1
```    

When this element exists, `gawk` checks the value of the system
(C language)
`errno` variable when an I/O error occurs.  If `errno` indicates
a subsequent I/O attempt may succeed, `getline` instead returns
-2 and
further calls to `getline` may succeed.  This applies to the `errno`
values `EAGAIN`, `EWOULDBLOCK`, `EINTR`, or `ETIMEDOUT`.

This feature is useful in conjunction with
`PROCINFO["input_name", "READ_TIMEOUT"]` or situations where a file
descriptor has been configured to behave in a non-blocking fashion.

---

Next: [Input Summary](#414-summary), Previous: [Retrying Input](#412-retrying-reads-after-certain-input-errors), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.13 Directories on the Command Line

According to the POSIX standard, files named on the `awk`
command line must be text files; it is a fatal error if they are not.
Most versions of `awk` treat a directory on the command line as
a fatal error.

By default, `gawk` produces a warning for a directory on the
command line, but otherwise ignores it.  This makes it easier to use
shell wildcards with your `awk` program:

    $ gawk -f whizprog.awk *Directories could kill this program

If either of the --posix
or --traditional options is given, then `gawk` reverts
to treating a directory on the command line as a fatal error.

See [Extension Sample Readdir](#Extension-Sample-Readdir) for a way to treat directories
as usable data from an `awk` program.

---

Next: [Input Exercises](#415-exercises), Previous: [Command-line directories](#413-directories-on-the-command-line), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.14 Summary

-  Input is split into records based on the value of `RS`.
The possibilities are as follows:

Value of `RS`Records are split on &hellip;`awk` / `gawk`Any single characterThat character`awk`The empty string (`""`)Runs of two or more newlines`awk`A regexpText that matches the regexp`gawk`
- `FNR` indicates how many records have been read from the current input file;
`NR` indicates how many records have been read in total.

- `gawk` sets `RT` to the text matched by `RS`.

-  After splitting the input into records, `awk` further splits
the records into individual fields, named `$1`, `$2`, and so
on. `$0` is the whole record, and `NF` indicates how many
fields there are.  The default way to split fields is between whitespace
characters.

-  Fields may be referenced using a variable, as in `$NF`.  Fields
may also be assigned values, which causes the value of `$0` to be
recomputed when it is later referenced. Assigning to a field with a number
greater than `NF` creates the field and rebuilds the record, using
`OFS` to separate the fields.  Incrementing `NF` does the same
thing. Decrementing `NF` throws away fields and rebuilds the record.

-  Field splitting is more complicated than record splitting:

Field separator valueFields are split &hellip;`awk` / `gawk``FS == " "`On runs of whitespace`awk``FS == any single character`On that character`awk``FS == regexp`On text matching the regexp`awk``FS == ""`Such that each individual character is a separate field`gawk``FIELDWIDTHS == list of columns`Based on character position`gawk``FPAT == regexp`On the text surrounding text matching the regexp`gawk`
-  Using &lsquo;FS = "\n"&rsquo; causes the entire record to be a single field
(assuming that newlines separate records).

- `FS` may be set from the command line using the -F option.
This can also be done using command-line variable assignment.

-  Use `PROCINFO["FS"]` to see how fields are being split.

-  Use `getline` in its various forms to read additional records
from the default input stream, from a file, or from a pipe or coprocess.

-  Use `PROCINFO[file, "READ_TIMEOUT"]` to cause reads to time out
for file.

-  Directories on the command line are fatal for standard `awk`;
`gawk` ignores them if not in POSIX mode.

---

Previous: [Input Summary](#414-summary), Up: [Reading Files](#4-reading-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

### 4.15 Exercises

1.  Using the `FIELDWIDTHS` variable (see [Constant Size](#46-reading-fixed-width-data)),
write a program to read election data, where each record represents
one voter&rsquo;s votes.  Come up with a way to define which columns are
associated with each ballot item, and print the total votes,
including abstentions, for each item.

2. [Plain Getline](#4101-using-getline-with-no-arguments), presented a program to remove C-style
comments (&lsquo;/* &hellip; */&rsquo;) from the input.  That program
does not work if one comment ends on one line and another one
starts later on the same line.
That can be fixed by making one simple change. What is it?

---

Next: [Expressions](#6-expressions), Previous: [Reading Files](#4-reading-input-files), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 5 Printing Output

One of the most common programming actions is to *print*, or output,
some or all of the input.  Use the `print` statement
for simple output, and the `printf` statement
for fancier formatting.
The `print` statement is not limited when
computing *which* values to print. However, with two exceptions,
you cannot specify *how* to print them&mdash;how many
columns, whether to use exponential notation or not, and so on.
(For the exceptions, see [Output Separators](#53-output-separators) and
[OFMT](#54-controlling-numeric-output-with-print).)
For printing with specifications, you need the `printf` statement
(see [Printf](#55-using-printf-statements-for-fancier-printing)).

Besides basic and formatted printing, this chapter
also covers I/O redirections to files and pipes, introduces
the special file names that `gawk` processes internally,
and discusses the `close()` built-in function.

| []() | []()
| - | -
| &bull; [Print](#51-the-print-statement) | The `print` statement.
| &bull; [Print Examples](#52-print-statement-examples) | Simple examples of `print` statements.
| &bull; [Output Separators](#53-output-separators) | The output separators and how to change them.
| &bull; [OFMT](#54-controlling-numeric-output-with-print) | Controlling Numeric Output With `print`.
| &bull; [Printf](#55-using-printf-statements-for-fancier-printing) | The `printf` statement.
| &bull; [Redirection](#56-redirecting-output-of-print-and-printf) | How to redirect output to multiple files and pipes.
| &bull; [Special FD](#57-special-files-for-standard-preopened-data-streams) | Special files for I/O.
| &bull; [Special Files](#58-special-file-names-in-gawk) | File name interpretation in `gawk`.  `gawk` allows access to inherited file descriptors.
| &bull; [Close Files And Pipes](#59-closing-input-and-output-redirections) | Closing Input and Output Files and Pipes.
| &bull; [Nonfatal](#510-enabling-nonfatal-output) | Enabling Nonfatal Output.
| &bull; [Output Summary](#511-summary) | Output summary.
| &bull; [Output Exercises](#512-exercises) | Exercises.

---

Next: [Print Examples](#52-print-statement-examples), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.1 The `print` Statement

Use the `print` statement to produce output with simple, standardized
formatting.  You specify only the strings or numbers to print, in a
list separated by commas.  They are output, separated by single spaces,
followed by a newline.  The statement looks like this:

```awk
    print item1, item2, &hellip;
```    

The entire list of items may be optionally enclosed in parentheses.  The
parentheses are necessary if any of the item expressions uses the &lsquo;>&rsquo;
relational operator; otherwise it could be confused with an output redirection
(see [Redirection](#56-redirecting-output-of-print-and-printf)).

The items to print can be constant strings or numbers, fields of the
current record (such as `$1`), variables, or any `awk`
expression.  Numeric values are converted to strings and then printed.

The simple statement &lsquo;print&rsquo; with no items is equivalent to
&lsquo;print $0&rsquo;: it prints the entire current record.  To print a blank
line, use &lsquo;print ""&rsquo;.
To print a fixed piece of text, use a string constant, such as
`"Don't Panic"`, as one item.  If you forget to use the
double-quote characters, your text is taken as an `awk`
expression, and you will probably get an error.  Keep in mind that a
space is printed between any two items.

Note that the `print` statement is a statement and not an
expression&mdash;you can&rsquo;t use it in the pattern part of a
pattern&ndash;action statement, for example.

---

Next: [Output Separators](#53-output-separators), Previous: [Print](#51-the-print-statement), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.2 `print` Statement Examples

Each `print` statement makes at least one line of output.  However, it
isn&rsquo;t limited to only one line.  If an item value is a string containing a
newline, the newline is output along with the rest of the string.  A
single `print` statement can make any number of lines this way.

The following is an example of printing a string that contains embedded
newlines
(the &lsquo;\n&rsquo; is an escape sequence, used to represent the newline
character; see [Escape Sequences](#32-escape-sequences)):

```awk
    $ awk 'BEGIN { print "line one\nline two\nline three" }'
    -| line one
    -| line two
    -| line three
```    

The next example, which is run on the inventory-shipped file,
prints the first two fields of each input record, with a space between
them:

```awk
    $ awk '{ print $1, $2 }' inventory-shipped
    -| Jan 13
    -| Feb 15
    -| Mar 15
    &hellip;
```    

A common mistake in using the `print` statement is to omit the comma
between two items.  This often has the effect of making the items run
together in the output, with no space.  The reason for this is that
juxtaposing two string expressions in `awk` means to concatenate
them.  Here is the same program, without the comma:

```awk
    $ awk '{ print $1 $2 }' inventory-shipped
    -| Jan13
    -| Feb15
    -| Mar15
    &hellip;
```    

To someone unfamiliar with the inventory-shipped file, neither
example&rsquo;s output makes much sense.  A heading line at the beginning
would make it clearer.  Let&rsquo;s add some headings to our table of months
(`$1`) and green crates shipped (`$2`).  We do this using
a `BEGIN` rule (see [BEGIN/END](#714-the-begin-and-end-special-patterns)) so that the headings are only
printed once:

```awk
    awk 'BEGIN {  print "Month Crates"
                  print "----- ------" }
               {  print $1, $2 }' inventory-shipped
```    

When run, the program prints the following:

    Month Crates
    ----- ------
    Jan 13
    Feb 15
    Mar 15
    &hellip;
    

The only problem, however, is that the headings and the table data
don&rsquo;t line up!  We can fix this by printing some spaces between the
two fields:

```awk
    awk 'BEGIN { print "Month Crates"
                 print "----- ------" }
               { print $1, "     ", $2 }' inventory-shipped
```    

Lining up columns this way can get pretty
complicated when there are many columns to fix.  Counting spaces for two
or three columns is simple, but any more than this can take up
a lot of time. This is why the `printf` statement was
created (see [Printf](#55-using-printf-statements-for-fancier-printing));
one of its specialties is lining up columns of data.

> NOTE: You can continue either a `print` or
> `printf` statement simply by putting a newline after any comma
> (see [Statements/Lines](#16-awk-statements-versus-lines)).

---

Next: [OFMT](#54-controlling-numeric-output-with-print), Previous: [Print Examples](#52-print-statement-examples), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.3 Output Separators

As mentioned previously, a `print` statement contains a list
of items separated by commas.  In the output, the items are normally
separated by single spaces.  However, this doesn&rsquo;t need to be the case;
a single space is simply the default.  Any string of
characters may be used as the *output field separator* by setting the
predefined variable `OFS`.  The initial value of this variable
is the string `" "` (i.e., a single space).

The output from an entire `print` statement is called an *output
record*.  Each `print` statement outputs one output record, and
then outputs a string called the *output record separator* (or
`ORS`).  The initial value of `ORS` is the string `"\n"`
(i.e., a newline character).  Thus, each `print` statement normally
makes a separate line.

In order to change how output fields and records are separated, assign
new values to the variables `OFS` and `ORS`.  The usual
place to do this is in the `BEGIN` rule
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)), so
that it happens before any input is processed.  It can also be done
with assignments on the command line, before the names of the input
files, or using the -v command-line option
(see [Options](#22-command-line-options)).
The following example prints the first and second fields of each input
record, separated by a semicolon, with a blank line added after each
newline:

```awk
    $ awk 'BEGIN { OFS = ";"; ORS = "\n\n" }
    >            { print $1, $2 }' mail-list
    -| Amelia;555-5553
    -|
    -| Anthony;555-3412
    -|
    -| Becky;555-7685
    -|
    -| Bill;555-1675
    -|
    -| Broderick;555-0542
    -|
    -| Camilla;555-2912
    -|
    -| Fabius;555-1234
    -|
    -| Julie;555-6699
    -|
    -| Martin;555-6480
    -|
    -| Samuel;555-3430
    -|
    -| Jean-Paul;555-2127
    -|
```    

If the value of `ORS` does not contain a newline, the program&rsquo;s output
runs together on a single line.

---

Next: [Printf](#55-using-printf-statements-for-fancier-printing), Previous: [Output Separators](#53-output-separators), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.4 Controlling Numeric Output with `print`

When printing numeric values with the `print` statement,
`awk` internally converts each number to a string of characters
and prints that string.  `awk` uses the `sprintf()` function
to do this conversion
(see [String Functions](#913-string-manipulation-functions)).
For now, it suffices to say that the `sprintf()`
function accepts a *format specification* that tells it how to format
numbers (or strings), and that there are a number of different ways in which
numbers can be formatted.  The different format specifications are discussed
more fully in
[Control Letters](#552-format-control-letters).

The predefined variable `OFMT` contains the format specification
that `print` uses with `sprintf()` when it wants to convert a
number to a string for printing.
The default value of `OFMT` is `"%.6g"`.
The way `print` prints numbers can be changed
by supplying a different format specification
for the value of `OFMT`, as shown in the following example:

```awk
    $ awk 'BEGIN {
    >   OFMT = "%.0f"  # print numbers as integers (rounds)
    >   print 17.23, 17.54 }'
    -| 17 18
```    

According to the POSIX standard, `awk`&rsquo;s behavior is undefined
if `OFMT` contains anything but a floating-point conversion specification.
(d.c.)

---

Next: [Redirection](#56-redirecting-output-of-print-and-printf), Previous: [OFMT](#54-controlling-numeric-output-with-print), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.5 Using `printf` Statements for Fancier Printing

For more precise control over the output format than what is
provided by `print`, use `printf`.
With `printf` you can
specify the width to use for each item, as well as various
formatting choices for numbers (such as what output base to use, whether to
print an exponent, whether to print a sign, and how many digits to print
after the decimal point).

| []() | []()
| - | -
| &bull; [Basic Printf](#551-introduction-to-the-printf-statement) | Syntax of the `printf` statement.
| &bull; [Control Letters](#552-format-control-letters) | Format-control letters.
| &bull; [Format Modifiers](#553-modifiers-for-printf-formats) | Format-specification modifiers.
| &bull; [Printf Examples](#554-examples-using-printf) | Several examples.

---

Next: [Control Letters](#552-format-control-letters), Up: [Printf](#55-using-printf-statements-for-fancier-printing)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.5.1 Introduction to the `printf` Statement

A simple `printf` statement looks like this:

```awk
    printf format, item1, item2, &hellip;
```    

As for `print`, the entire list of arguments may optionally be
enclosed in parentheses. Here too, the parentheses are necessary if any
of the item expressions uses the &lsquo;>&rsquo; relational operator; otherwise,
it can be confused with an output redirection (see [Redirection](#56-redirecting-output-of-print-and-printf)).

The difference between `printf` and `print` is the format
argument.  This is an expression whose value is taken as a string; it
specifies how to output each of the other arguments.  It is called the
*format string*.

The format string is very similar to that in the ISO C library function
`printf()`.  Most of format is text to output verbatim.
Scattered among this text are *format specifiers*&mdash;one per item.
Each format specifier says to output the next item in the argument list
at that place in the format.

The `printf` statement does not automatically append a newline
to its output.  It outputs only what the format string specifies.
So if a newline is needed, you must include one in the format string.
The output separator variables `OFS` and `ORS` have no effect
on `printf` statements. For example:

```awk
    $ awk 'BEGIN {
    >    ORS = "\nOUCH!\n"; OFS = "+"
    >    msg = "Don\47t Panic!"
    >    printf "%s\n", msg
    > }'
    -| Don't Panic!
```    

Here, neither the &lsquo;+&rsquo; nor the &lsquo;OUCH!&rsquo; appears in
the output message.

---

Next: [Format Modifiers](#553-modifiers-for-printf-formats), Previous: [Basic Printf](#551-introduction-to-the-printf-statement), Up: [Printf](#55-using-printf-statements-for-fancier-printing)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.5.2 Format-Control Letters

A format specifier starts with the character &lsquo;%&rsquo; and ends with
a *format-control letter*&mdash;it tells the `printf` statement
how to output one item.  The format-control letter specifies what *kind*
of value to print.  The rest of the format specifier is made up of
optional *modifiers* that control *how* to print the value, such as
the field width.  Here is a list of the format-control letters:

<code>%c</code>
Print a number as a character; thus, &lsquo;printf "%c",
65&rsquo; outputs the letter &lsquo;A&rsquo;. The output for a string value is
the first character of the string.

> NOTE: The POSIX standard says the first character of a string is printed.
> In locales with multibyte characters, `gawk` attempts to
> convert the leading bytes of the string into a valid wide character
> and then to print the multibyte encoding of that character.
> Similarly, when printing a numeric value, `gawk` allows the
> value to be within the numeric range of values that can be held
> in a wide character.
> If the conversion to multibyte encoding fails, `gawk`
> uses the low eight bits of the value as the character to print.
> 
> 
> Other `awk` versions generally restrict themselves to printing
> the first byte of a string or to numeric values within the range of
> a single byte (0&ndash;255).
> (d.c.)

<code>%d</code>, <code>%i</code>
Print a decimal integer.
The two control letters are equivalent.
(The &lsquo;%i&rsquo; specification is for compatibility with ISO C.)

<code>%e</code>, <code>%E</code>
Print a number in scientific (exponential) notation.
For example:

```awk
    printf "%4.3e\n", 1950
```  

prints &lsquo;1.950e+03&rsquo;, with a total of four significant figures, three of
which follow the decimal point.
(The &lsquo;4.3&rsquo; represents two modifiers,
discussed in the next subsection.)
&lsquo;%E&rsquo; uses &lsquo;E&rsquo; instead of &lsquo;e&rsquo; in the output.

<code>%f</code>
Print a number in floating-point notation.
For example:

```awk
    printf "%4.3f", 1950
```    

prints &lsquo;1950.000&rsquo;, with a total of four significant figures, three of
which follow the decimal point.
(The &lsquo;4.3&rsquo; represents two modifiers,
discussed in the next subsection.)

On systems supporting IEEE 754 floating-point format, values
representing negative
infinity are formatted as
&lsquo;-inf&rsquo; or &lsquo;-infinity&rsquo;,
and positive infinity as
&lsquo;inf&rsquo; or &lsquo;infinity&rsquo;.
The special &ldquo;not a number&rdquo; value formats as &lsquo;-nan&rsquo; or &lsquo;nan&rsquo;
(see [Math Definitions](#Math-Definitions)).

<code>%F</code>
Like &lsquo;%f&rsquo;, but the infinity and &ldquo;not a number&rdquo; values are spelled
using uppercase letters.

The &lsquo;%F&rsquo; format is a POSIX extension to ISO C; not all systems
support it.  On those that don&rsquo;t, `gawk` uses &lsquo;%f&rsquo; instead.

<code>%g</code>, <code>%G</code>
Print a number in either scientific notation or in floating-point
notation, whichever uses fewer characters; if the result is printed in
scientific notation, &lsquo;%G&rsquo; uses &lsquo;E&rsquo; instead of &lsquo;e&rsquo;.

<code>%o</code>
Print an unsigned octal integer
(see [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers)).

<code>%s</code>
Print a string.

<code>%u</code>
Print an unsigned decimal integer.
(This format is of marginal use, because all numbers in `awk`
are floating point; it is provided primarily for compatibility with C.)

<code>%x</code>, <code>%X</code>
Print an unsigned hexadecimal integer;
&lsquo;%X&rsquo; uses the letters &lsquo;A&rsquo; through &lsquo;F&rsquo;
instead of &lsquo;a&rsquo; through &lsquo;f&rsquo;
(see [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers)).

<code>%%</code>
Print a single &lsquo;%&rsquo;.
This does not consume an
argument and it ignores any modifiers.

> NOTE: When using the integer format-control letters for values that are
> outside the range of the widest C integer type, `gawk` switches to
> the &lsquo;%g&rsquo; format specifier. If --lint is provided on the
> command line (see [Options](#22-command-line-options)), `gawk`
> warns about this.  Other versions of `awk` may print invalid
> values or do something else entirely.
> (d.c.)

---

Next: [Printf Examples](#554-examples-using-printf), Previous: [Control Letters](#552-format-control-letters), Up: [Printf](#55-using-printf-statements-for-fancier-printing)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.5.3 Modifiers for `printf` Formats

A format specification can also include *modifiers* that can control
how much of the item&rsquo;s value is printed, as well as how much space it gets.
The modifiers come between the &lsquo;%&rsquo; and the format-control letter.
We use the bullet symbol &ldquo;&bull;&rdquo; in the following examples to
represent
spaces in the output. Here are the possible modifiers, in the order in
which they may appear:

<code>N$</code>
An integer constant followed by a &lsquo;$&rsquo; is a *positional specifier*.
Normally, format specifications are applied to arguments in the order
given in the format string.  With a positional specifier, the format
specification is applied to a specific argument, instead of what
would be the next argument in the list.  Positional specifiers begin
counting with one. Thus:

```awk
    printf "%s %s\n", "don't", "panic"
    printf "%2$s %1$s\n", "panic", "don't"
```    

prints the famous friendly message twice.

At first glance, this feature doesn&rsquo;t seem to be of much use.
It is in fact a `gawk` extension, intended for use in translating
messages at runtime.
See [Printf Ordering](#Printf-Ordering),
which describes how and why to use positional specifiers.
For now, we ignore them.

<code>-</code> (Minus)
The minus sign, used before the width modifier (see later on in
this list),
says to left-justify
the argument within its specified width.  Normally, the argument
is printed right-justified in the specified width.  Thus:

```awk
    printf "%-4s", "foo"
```    

prints &lsquo;foo&bull;&rsquo;.

space
For numeric conversions, prefix positive values with a space and
negative values with a minus sign.

<code>+</code>
The plus sign, used before the width modifier (see later on in
this list),
says to always supply a sign for numeric conversions, even if the data
to format is positive. The &lsquo;+&rsquo; overrides the space modifier.

<code>#</code>
Use an &ldquo;alternative form&rdquo; for certain control letters.
For &lsquo;%o&rsquo;, supply a leading zero.
For &lsquo;%x&rsquo; and &lsquo;%X&rsquo;, supply a leading &lsquo;0x&rsquo; or &lsquo;0X&rsquo; for
a nonzero result.
For &lsquo;%e&rsquo;, &lsquo;%E&rsquo;, &lsquo;%f&rsquo;, and &lsquo;%F&rsquo;, the result always
contains a decimal point.
For &lsquo;%g&rsquo; and &lsquo;%G&rsquo;, trailing zeros are not removed from the result.

<code>0</code>
A leading &lsquo;0&rsquo; (zero) acts as a flag indicating that output should be
padded with zeros instead of spaces.
This applies only to the numeric output formats.
This flag only has an effect when the field width is wider than the
value to print.

<code>'</code>
A single quote or apostrophe character is a POSIX extension to ISO C.
It indicates that the integer part of a floating-point value, or the
entire part of an integer decimal value, should have a thousands-separator
character in it.  This only works in locales that support such characters.
For example:

```awk
    $ cat thousands.awkShow source program
    -| BEGIN { printf "%'d\n", 1234567 }
    $ LC_ALL=C gawk -f thousands.awk
    -| 1234567                   Results in "C" locale
    $ LC_ALL=en_US.UTF-8 gawk -f thousands.awk
    -| 1,234,567                 Results in US English UTF locale
```

For more information about locales and internationalization issues,
see [Locales](#66-where-you-are-makes-a-difference).

> NOTE: The &lsquo;'&rsquo; flag is a nice feature, but its use complicates things: it
> becomes difficult to use it in command-line programs.  For information
> on appropriate quoting tricks, see [Quoting](#116-shell-quoting-issues).

width
This is a number specifying the desired minimum width of a field.  Inserting any
number between the &lsquo;%&rsquo; sign and the format-control character forces the
field to expand to this width.  The default way to do this is to
pad with spaces on the left.  For example:

```awk
    printf "%4s", "foo"
```    

prints &lsquo;&bull;foo&rsquo;.

The value of width is a minimum width, not a maximum.  If the item
value requires more than width characters, it can be as wide as
necessary.  Thus, the following:

```awk
    printf "%4s", "foobar"
```    

prints &lsquo;foobar&rsquo;.

Preceding the width with a minus sign causes the output to be
padded with spaces on the right, instead of on the left.

<code>.prec</code>
A period followed by an integer constant
specifies the precision to use when printing.
The meaning of the precision varies by control letter:

<code>%d</code>, <code>%i</code>, <code>%o</code>, <code>%u</code>, <code>%x</code>, <code>%X</code>
Minimum number of digits to print.

<code>%e</code>, <code>%E</code>, <code>%f</code>, <code>%F</code>
Number of digits to the right of the decimal point.

<code>%g</code>, <code>%G</code>
Maximum number of significant digits.

<code>%s</code>
Maximum number of characters from the string that should print.

Thus, the following:

```awk
    printf "%.4s", "foobar"
```    

prints &lsquo;foob&rsquo;.

The C library `printf`&rsquo;s dynamic width and prec
capability (e.g., `"%*.*s"`) is supported.  Instead of
supplying explicit width and/or prec values in the format
string, they are passed in the argument list.  For example:

```awk
    w = 5
    p = 3
    s = "abcdefg"
    printf "%*.*s\n", w, p, s
```    

is exactly equivalent to:

```awk
    s = "abcdefg"
    printf "%5.3s\n", s
```    

Both programs output &lsquo;&bull;&bull;abc&rsquo;.
Earlier versions of `awk` did not support this capability.
If you must use such a version, you may simulate this feature by using
concatenation to build up the format string, like so:

```awk
    w = 5
    p = 3
    s = "abcdefg"
    printf "%" w "." p "s\n", s
```    

This is not particularly easy to read, but it does work.

C programmers may be used to supplying additional modifiers (&lsquo;h&rsquo;,
&lsquo;j&rsquo;, &lsquo;l&rsquo;, &lsquo;L&rsquo;, &lsquo;t&rsquo;, and &lsquo;z&rsquo;) in `printf`
format strings. These are not valid in `awk`.  Most `awk`
implementations silently ignore them.  If --lint is provided
on the command line (see [Options](#22-command-line-options)), `gawk` warns about their
use. If --posix is supplied, their use is a fatal error.

---

Previous: [Format Modifiers](#553-modifiers-for-printf-formats), Up: [Printf](#55-using-printf-statements-for-fancier-printing)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.5.4 Examples Using `printf`

The following simple example shows
how to use `printf` to make an aligned table:

```awk
    awk '{ printf "%-10s %s\n", $1, $2 }' mail-list
```    

This command
prints the names of the people (`$1`) in the file
mail-list as a string of 10 characters that are left-justified.  It also
prints the phone numbers (`$2`) next on the line.  This
produces an aligned two-column table of names and phone numbers,
as shown here:

```awk
    $ awk '{ printf "%-10s %s\n", $1, $2 }' mail-list
    -| Amelia     555-5553
    -| Anthony    555-3412
    -| Becky      555-7685
    -| Bill       555-1675
    -| Broderick  555-0542
    -| Camilla    555-2912
    -| Fabius     555-1234
    -| Julie      555-6699
    -| Martin     555-6480
    -| Samuel     555-3430
    -| Jean-Paul  555-2127
```    

In this case, the phone numbers had to be printed as strings because
the numbers are separated by dashes.  Printing the phone numbers as
numbers would have produced just the first three digits: &lsquo;555&rsquo;.
This would have been pretty confusing.

It wasn&rsquo;t necessary to specify a width for the phone numbers because
they are last on their lines.  They don&rsquo;t need to have spaces
after them.

The table could be made to look even nicer by adding headings to the
tops of the columns.  This is done using a `BEGIN` rule
(see [BEGIN/END](#714-the-begin-and-end-special-patterns))
so that the headers are only printed once, at the beginning of
the `awk` program:

```awk
    awk 'BEGIN { print "Name      Number"
                 print "----      ------" }
               { printf "%-10s %s\n", $1, $2 }' mail-list
```    

The preceding example mixes `print` and `printf` statements in
the same program.  Using just `printf` statements can produce the
same results:

```awk
    awk 'BEGIN { printf "%-10s %s\n", "Name", "Number"
                 printf "%-10s %s\n", "----", "------" }
               { printf "%-10s %s\n", $1, $2 }' mail-list
```    

Printing each column heading with the same format specification
used for the column elements ensures that the headings
are aligned just like the columns.

The fact that the same format specification is used three times can be
emphasized by storing it in a variable, like this:

```awk
    awk 'BEGIN { format = "%-10s %s\n"
                 printf format, "Name", "Number"
                 printf format, "----", "------" }
               { printf format, $1, $2 }' mail-list
```    

---

Next: [Special FD](#57-special-files-for-standard-preopened-data-streams), Previous: [Printf](#55-using-printf-statements-for-fancier-printing), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.6 Redirecting Output of `print` and `printf`

So far, the output from `print` and `printf` has gone
to the standard
output, usually the screen.  Both `print` and `printf` can
also send their output to other places.
This is called *redirection*.

> NOTE: When --sandbox is specified (see [Options](#22-command-line-options)),
> redirecting output to files, pipes, and coprocesses is disabled.

A redirection appears after the `print` or `printf` statement.
Redirections in `awk` are written just like redirections in shell
commands, except that they are written inside the `awk` program.

There are four forms of output redirection: output to a file, output
appended to a file, output through a pipe to another command, and output
to a coprocess.  We show them all for the `print` statement,
but they work identically for `printf`:

`print items > output-file`
This redirection prints the items into the output file named
output-file.  The file name output-file can be any
expression.  Its value is changed to a string and then used as a
file name (see [Expressions](#6-expressions)).

When this type of redirection is used, the output-file is erased
before the first output is written to it.  Subsequent writes to the same
output-file do not erase output-file, but append to it.
(This is different from how you use redirections in shell scripts.)
If output-file does not exist, it is created.  For example, here
is how an `awk` program can write a list of peoples&rsquo; names to one
file named name-list, and a list of phone numbers to another file
named phone-list:

```awk
    $ awk '{ print $2 > "phone-list"
    >        print $1 > "name-list" }' mail-list
    $ cat phone-list
    -| 555-5553
    -| 555-3412
    &hellip;
    $ cat name-list
    -| Amelia
    -| Anthony
    &hellip;
```    

Each output file contains one name or number per line.

`print items >> output-file`
This redirection prints the items into the preexisting output file
named output-file.  The difference between this and the
single-&lsquo;>&rsquo; redirection is that the old contents (if any) of
output-file are not erased.  Instead, the `awk` output is
appended to the file.
If output-file does not exist, then it is created.

`print items | command`
It is possible to send output to another program through a pipe
instead of into a file.   This redirection opens a pipe to
command, and writes the values of items through this pipe
to another process created to execute command.

The redirection argument command is actually an `awk`
expression.  Its value is converted to a string whose contents give
the shell command to be run.  For example, the following produces two
files, one unsorted list of peoples&rsquo; names, and one list sorted in reverse
alphabetical order:

```awk
    awk '{ print $1 > "names.unsorted"
           command = "sort -r > names.sorted"
           print $1 | command }' mail-list
```    

The unsorted list is written with an ordinary redirection, while
the sorted list is written by piping through the `sort` utility.

The next example uses redirection to mail a message to the mailing
list `bug-system`.  This might be useful when trouble is encountered
in an `awk` script run periodically for system maintenance:

```awk
    report = "mail bug-system"
    print("Awk script failed:", $0) | report
    print("at record number", FNR, "of", FILENAME) | report
    close(report)
```    

The `close()` function is called here because it&rsquo;s a good idea to close
the pipe as soon as all the intended output has been sent to it.
See [Close Files And Pipes](#59-closing-input-and-output-redirections)
for more information.

This example also illustrates the use of a variable to represent
a file or command&mdash;it is not necessary to always
use a string constant.  Using a variable is generally a good idea,
because (if you mean to refer to that same file or command)
`awk` requires that the string value be written identically
every time.

`print items |& command`
This redirection prints the items to the input of command.
The difference between this and the
single-&lsquo;|&rsquo; redirection is that the output from command
can be read with `getline`.
Thus, command is a *coprocess*, which works together with
but is subsidiary to the `awk` program.

This feature is a `gawk` extension, and is not available in
POSIX `awk`.
See [Getline/Coprocess](#4107-using-getline-from-a-coprocess),
for a brief discussion.
See [Two-way I/O](#Two_002dway-I_002fO),
for a more complete discussion.

Redirecting output using &lsquo;>&rsquo;, &lsquo;>>&rsquo;, &lsquo;|&rsquo;, or &lsquo;|&&rsquo;
asks the system to open a file, pipe, or coprocess only if the particular
file or command you specify has not already been written
to by your program or if it has been closed since it was last written to.

It is a common error to use &lsquo;>&rsquo; redirection for the first `print`
to a file, and then to use &lsquo;>>&rsquo; for subsequent output:

```awk
    # clear the file
    print "Don't panic" > "guide.txt"
    &hellip;
    # append
    print "Avoid improbability generators" >> "guide.txt"
```    

This is indeed how redirections must be used from the shell.  But in
`awk`, it isn&rsquo;t necessary.  In this kind of case, a program should
use &lsquo;>&rsquo; for all the `print` statements, because the output file
is only opened once. (It happens that if you mix &lsquo;>&rsquo; and &lsquo;>>&rsquo;
output is produced in the expected order. However, mixing the operators
for the same file is definitely poor style, and is confusing to readers
of your program.)

As mentioned earlier
(see [Getline Notes](#4109-points-to-remember-about-getline)),
many
Many
older
`awk` implementations limit the number of pipelines that an `awk`
program may have open to just one!  In `gawk`, there is no such limit.
`gawk` allows a program to
open as many pipelines as the underlying operating system permits.

Piping into `sh`

A particularly powerful way to use redirection is to build command lines
and pipe them into the shell, `sh`.  For example, suppose you
have a list of files brought over from a system where all the file names
are stored in uppercase, and you wish to rename them to have names in
all lowercase.  The following program is both simple and efficient:

```awk
    { printf("mv %s %s\n", $0, tolower($0)) | "sh" }
    
    END { close("sh") }
```    

The `tolower()` function returns its argument string with all
uppercase characters converted to lowercase
(see [String Functions](#913-string-manipulation-functions)).
The program builds up a list of command lines,
using the `mv` utility to rename the files.
It then sends the list to the shell for execution.

See [Shell Quoting](#1029-quoting-strings-to-pass-to-the-shell) for a function that can help in generating
command lines to be fed to the shell.

---

Next: [Special Files](#58-special-file-names-in-gawk), Previous: [Redirection](#56-redirecting-output-of-print-and-printf), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.7 Special Files for Standard Preopened Data Streams

Running programs conventionally have three input and output streams
already available to them for reading and writing.  These are known
as the *standard input*, *standard output*, and *standard
error output*.  These open streams (and any other open files or pipes)
are often referred to by the technical term *file descriptors*.

These streams are, by default, connected to your keyboard and screen, but
they are often redirected with the shell, via the &lsquo;<&rsquo;, &lsquo;<<&rsquo;,
&lsquo;>&rsquo;, &lsquo;>>&rsquo;, &lsquo;>&&rsquo;, and &lsquo;|&rsquo; operators.  Standard error
is typically used for writing error messages; the reason there are two separate
streams, standard output and standard error, is so that they can be
redirected separately.

In traditional implementations of `awk`, the only way to write an error
message to standard error in an `awk` program is as follows:

```awk
    print "Serious error detected!" | "cat 1>&2"
```    

This works by opening a pipeline to a shell command that can access the
standard error stream that it inherits from the `awk` process.
This is far from elegant, and it also requires a
separate process.  So people writing `awk` programs often
don&rsquo;t do this.  Instead, they send the error messages to the
screen, like this:

```awk
    print "Serious error detected!" > "/dev/tty"
```    

(/dev/tty is a special file supplied by the operating system
that is connected to your keyboard and screen. It represents the
&ldquo;terminal,&rdquo;[27](#FOOT27) which on modern systems is a keyboard
and screen, not a serial console.)
This generally has the same effect, but not always: although the
standard error stream is usually the screen, it can be redirected; when
that happens, writing to the screen is not correct.  In fact, if
`awk` is run from a background job, it may not have a
terminal at all.
Then opening /dev/tty fails.

`gawk`, BWK `awk`, and `mawk` provide
special file names for accessing the three standard streams.
If the file name matches one of these special names when `gawk`
(or one of the others) redirects input or output, then it directly uses
the descriptor that the file name stands for.  These special
file names work for all operating systems that `gawk`
has been ported to, not just those that are POSIX-compliant:

<code>/dev/stdin</code>
The standard input (file descriptor 0).

<code>/dev/stdout</code>
The standard output (file descriptor 1).

<code>/dev/stderr</code>
The standard error output (file descriptor 2).

With these facilities,
the proper way to write an error message then becomes:

```awk
    print "Serious error detected!" > "/dev/stderr"
```    

Note the use of quotes around the file name.
Like with any other redirection, the value must be a string.
It is a common error to omit the quotes, which leads
to confusing results.

`gawk` does not treat these file names as special when
in POSIX-compatibility mode. However, because BWK `awk`
supports them, `gawk` does support them even when
invoked with the --traditional option (see [Options](#22-command-line-options)).

---

Next: [Close Files And Pipes](#59-closing-input-and-output-redirections), Previous: [Special FD](#57-special-files-for-standard-preopened-data-streams), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.8 Special File names in `gawk`

Besides access to standard input, standard output, and standard error,
`gawk` provides access to any open file descriptor.
Additionally, there are special file names reserved for
TCP/IP networking.

| []() | []()
| - | -
| &bull; [Other Inherited Files](#581-accessing-other-open-files-with-gawk) | Accessing other open files with `gawk`.
| &bull; [Special Network](#582-special-files-for-network-communications) | Special files for network communications.
| &bull; [Special Caveats](#583-special-file-name-caveats) | Things to watch out for.

---

Next: [Special Network](#582-special-files-for-network-communications), Up: [Special Files](#58-special-file-names-in-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.8.1 Accessing Other Open Files with `gawk`

Besides the `/dev/stdin`, `/dev/stdout`, and `/dev/stderr`
special file names mentioned earlier, `gawk` provides syntax
for accessing any other inherited open file:

<code>/dev/fd/N</code>
The file associated with file descriptor N.  Such a file must
be opened by the program initiating the `awk` execution (typically
the shell).  Unless special pains are taken in the shell from which
`gawk` is invoked, only descriptors 0, 1, and 2 are available.

The file names /dev/stdin, /dev/stdout, and /dev/stderr
are essentially aliases for /dev/fd/0, /dev/fd/1, and
/dev/fd/2, respectively. However, those names are more self-explanatory.

Note that using `close()` on a file name of the
form `"/dev/fd/N"`, for file descriptor numbers
above two, does actually close the given file descriptor.

---

Next: [Special Caveats](#583-special-file-name-caveats), Previous: [Other Inherited Files](#581-accessing-other-open-files-with-gawk), Up: [Special Files](#58-special-file-names-in-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.8.2 Special Files for Network Communications

`gawk` programs
can open a two-way
TCP/IP connection, acting as either a client or a server.
This is done using a special file name of the form:

    /net-type/protocol/local-port/remote-host/remote-port

The net-type is one of &lsquo;inet&rsquo;, &lsquo;inet4&rsquo;, or &lsquo;inet6&rsquo;.
The protocol is one of &lsquo;tcp&rsquo; or &lsquo;udp&rsquo;,
and the other fields represent the other essential pieces of information
for making a networking connection.
These file names are used with the &lsquo;|&&rsquo; operator for communicating
with a coprocess
(see [Two-way I/O](#Two_002dway-I_002fO)).
This is an advanced feature, mentioned here only for completeness.
Full discussion is delayed until
[TCP/IP Networking](#TCP_002fIP-Networking).

---

Previous: [Special Network](#582-special-files-for-network-communications), Up: [Special Files](#58-special-file-names-in-gawk)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 5.8.3 Special File name Caveats

Here are some things to bear in mind when using the
special file names that `gawk` provides:

- 
Recognition of the file names for the three standard preopened
files is disabled only in POSIX mode.

-  Recognition of the other special file names is disabled if `gawk` is in
compatibility mode (either --traditional or --posix;
see [Options](#22-command-line-options)).

- `gawk`*always*
interprets these special file names.
For example, using &lsquo;/dev/fd/4&rsquo;
for output actually writes on file descriptor 4, and not on a new
file descriptor that is `dup()`ed from file descriptor 4.  Most of
the time this does not matter; however, it is important to *not*
close any of the files related to file descriptors 0, 1, and 2.
Doing so results in unpredictable behavior.

---

Next: [Nonfatal](#510-enabling-nonfatal-output), Previous: [Special Files](#58-special-file-names-in-gawk), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.9 Closing Input and Output Redirections

If the same file name or the same shell command is used with `getline`
more than once during the execution of an `awk` program
(see [Getline](#410-explicit-input-with-getline)),
the file is opened (or the command is executed) the first time only.
At that time, the first record of input is read from that file or command.
The next time the same file or command is used with `getline`,
another record is read from it, and so on.

Similarly, when a file or pipe is opened for output, `awk` remembers
the file name or command associated with it, and subsequent
writes to the same file or command are appended to the previous writes.
The file or pipe stays open until `awk` exits.

This implies that special steps are necessary in order to read the same
file again from the beginning, or to rerun a shell command (rather than
reading more output from the same command).  The `close()` function
makes these things possible:

```awk
    close(filename)
```    

or:

```awk
    close(command)
```    

The argument filename or command can be any expression.  Its
value must *exactly* match the string that was used to open the file or
start the command (spaces and other &ldquo;irrelevant&rdquo; characters
included). For example, if you open a pipe with this:

```awk
    "sort -r names" | getline foo
```    

then you must close it with this:

```awk
    close("sort -r names")
```    

Once this function call is executed, the next `getline` from that
file or command, or the next `print` or `printf` to that
file or command, reopens the file or reruns the command.
Because the expression that you use to close a file or pipeline must
exactly match the expression used to open the file or run the command,
it is good practice to use a variable to store the file name or command.
The previous example becomes the following:

```awk
    sortcom = "sort -r names"
    sortcom | getline foo
    

    &hellip;
    close(sortcom)
```    

This helps avoid hard-to-find typographical errors in your `awk`
programs.  Here are some of the reasons for closing an output file:

-  To write a file and read it back later on in the same `awk`
program.  Close the file after writing it, then
begin reading it with `getline`.

-  To write numerous files, successively, in the same `awk`
program.  If the files aren&rsquo;t closed, eventually `awk` may exceed a
system limit on the number of open files in one process.  It is best to
close each one when the program has finished writing it.

-  To make a command finish.  When output is redirected through a pipe,
the command reading the pipe normally continues to try to read input
as long as the pipe is open.  Often this means the command cannot
really do its work until the pipe is closed.  For example, if
output is redirected to the `mail` program, the message is not
actually sent until the pipe is closed.

-  To run the same program a second time, with the same arguments.
This is not the same thing as giving more input to the first run!

For example, suppose a program pipes output to the `mail` program.
If it outputs several lines redirected to this pipe without closing
it, they make a single message of several lines.  By contrast, if the
program closes the pipe after each line of output, then each line makes
a separate message.

If you use more files than the system allows you to have open,
`gawk` attempts to multiplex the available open files among
your data files.  `gawk`&rsquo;s ability to do this depends upon the
facilities of your operating system, so it may not always work.  It is
therefore both good practice and good portability advice to always
use `close()` on your files when you are done with them.
In fact, if you are using a lot of pipes, it is essential that
you close commands when done. For example, consider something like this:

```awk
    {
        &hellip;
        command = ("grep " $1 " /some/file | my_prog -q " $3)
        while ((command | getline) > 0) {
            process output of command
        }
        # need close(command) here
    }
```    

This example creates a new pipeline based on data in *each* record.
Without the call to `close()` indicated in the comment, `awk`
creates child processes to run the commands, until it eventually
runs out of file descriptors for more pipelines.

Even though each command has finished (as indicated by the end-of-file
return status from `getline`), the child process is not
terminated;[28](#FOOT28)
more importantly, the file descriptor for the pipe
is not closed and released until `close()` is called or
`awk` exits.

`close()` silently does nothing if given an argument that
does not represent a file, pipe, or coprocess that was opened with
a redirection.  In such a case, it returns a negative value,
indicating an error. In addition, `gawk` sets `ERRNO`
to a string indicating the error.

Note also that &lsquo;close(FILENAME)&rsquo; has no &ldquo;magic&rdquo; effects on the
implicit loop that reads through the files named on the command line.
It is, more likely, a close of a file that was never opened with a
redirection, so `awk` silently does nothing, except return
a negative value.

When using the &lsquo;|&&rsquo; operator to communicate with a coprocess,
it is occasionally useful to be able to close one end of the two-way
pipe without closing the other.
This is done by supplying a second argument to `close()`.
As in any other call to `close()`,
the first argument is the name of the command or special file used
to start the coprocess.
The second argument should be a string, with either of the values
`"to"` or `"from"`.  Case does not matter.
As this is an advanced feature, discussion is
delayed until
[Two-way I/O](#Two_002dway-I_002fO),
which describes it in more detail and gives an example.

Using `close()`&rsquo;s Return Value

In many older versions of Unix `awk`, the `close()` function
is actually a statement.
(d.c.)
It is a syntax error to try and use the return
value from `close()`:

```awk
    command = "&hellip;"
    command | getline info
    retval = close(command)  # syntax error in many Unix awks
```    

`gawk` treats `close()` as a function.
The return value is -1 if the argument names something
that was never opened with a redirection, or if there is
a system problem closing the file or process.
In these cases, `gawk` sets the predefined variable
`ERRNO` to a string describing the problem.

In `gawk`, starting with version 4.2, when closing a pipe or
coprocess (input or output), the return value is the exit status of the
command, as described in [Table 5.1](#table_002dclose_002dpipe_002dreturn_002dvalues).[29](#FOOT29) Otherwise, it is the return value from the system&rsquo;s `close()`
or `fclose()` C functions when closing input or output files,
respectively.  This value is zero if the close succeeds, or -1
if it fails.

SituationReturn value from `close()`Normal exit of commandCommand&rsquo;s exit statusDeath by signal of command256 + number of murderous signalDeath by signal of command with core dump512 + number of murderous signalSome kind of error-1

**Table 5.1: **Return values from `close()` of a pipe

The POSIX standard is very vague; it says that `close()`
returns zero on success and a nonzero value otherwise.  In general,
different implementations vary in what they report when closing
pipes; thus, the return value cannot be used portably.
(d.c.)
In POSIX mode (see [Options](#22-command-line-options)), `gawk` just returns zero
when closing a pipe.

---

Next: [Output Summary](#511-summary), Previous: [Close Files And Pipes](#59-closing-input-and-output-redirections), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.10 Enabling Nonfatal Output

This section describes a `gawk`-specific feature.

In standard `awk`, output with `print` or `printf`
to a nonexistent file, or some other I/O error (such as filling up the
disk) is a fatal error.

```awk
    $ gawk 'BEGIN { print "hi" > "/no/such/file" }'
    error&rarr; gawk: cmd. line:1: fatal: can't redirect to `/no/such/file' (No
    error&rarr; such file or directory)
```    

`gawk` makes it possible to detect that an error has
occurred, allowing you to possibly recover from the error, or
at least print an error message of your choosing before exiting.
You can do this in one of two ways:

-  For all output files, by assigning any value to `PROCINFO["NONFATAL"]`.

-  On a per-file basis, by assigning any value to
`PROCINFO[filename, "NONFATAL"]`.
Here, filename is the name of the file to which
you wish output to be nonfatal.

Once you have enabled nonfatal output, you must check `ERRNO`
after every relevant `print` or `printf` statement to
see if something went wrong.  It is also a good idea to initialize
`ERRNO` to zero before attempting the output. For example:

```awk
    $ gawk '
    > BEGIN {
    >     PROCINFO["NONFATAL"] = 1
    >     ERRNO = 0
    >     print "hi" > "/no/such/file"
    >     if (ERRNO) {
    >         print("Output failed:", ERRNO) > "/dev/stderr"
    >         exit 1
    >     }
    > }'
    error&rarr; Output failed: No such file or directory
```    

Here, `gawk` did not produce a fatal error; instead
it let the `awk` program code detect the problem and handle it.

This mechanism works also for standard output and standard error.
For standard output, you may use `PROCINFO["-", "NONFATAL"]`
or `PROCINFO["/dev/stdout", "NONFATAL"]`.  For standard error, use
`PROCINFO["/dev/stderr", "NONFATAL"]`.

When attempting to open a TCP/IP socket (see [TCP/IP Networking](#TCP_002fIP-Networking)),
`gawk` tries multiple times. The `GAWK_SOCK_RETRIES`
environment variable (see [Other Environment Variables](#253-other-environment-variables)) allows you to
override `gawk`&rsquo;s builtin default number of attempts.  However,
once nonfatal I/O is enabled for a given socket, `gawk` only
retries once, relying on `awk`-level code to notice that there
was a problem.

---

Next: [Output Exercises](#512-exercises), Previous: [Nonfatal](#510-enabling-nonfatal-output), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.11 Summary

-  The `print` statement prints comma-separated expressions. Each
expression is separated by the value of `OFS` and terminated by
the value of `ORS`.  `OFMT` provides the conversion format
for numeric values for the `print` statement.

-  The `printf` statement provides finer-grained control over output,
with format-control letters for different data types and various flags
that modify the behavior of the format-control letters.

-  Output from both `print` and `printf` may be redirected to
files, pipes, and coprocesses.

- `gawk` provides special file names for access to standard input,
output, and error, and for network communications.

-  Use `close()` to close open file, pipe, and coprocess redirections.
For coprocesses, it is possible to close only one direction of the
communications.

-  Normally errors with `print` or `printf` are fatal.
`gawk` lets you make output errors be nonfatal either for
all files or on a per-file basis. You must then check for errors
after every relevant output statement.

---

Previous: [Output Summary](#511-summary), Up: [Printing](#5-printing-output)   [[Contents](#table-of-contents)][[Index](#Index)]

### 5.12 Exercises

1.  Rewrite the program:

```awk
    awk 'BEGIN { print "Month Crates"
                 print "----- ------" }
               { print $1, "     ", $2 }' inventory-shipped
```    

from [Output Separators](#53-output-separators), by using a new value of `OFS`.

2.  Use the `printf` statement to line up the headings and table data
for the inventory-shipped example that was covered in [Print](#51-the-print-statement).

3.  What happens if you forget the double quotes when redirecting
output, as follows:

```awk
    BEGIN { print "Serious error detected!" > /dev/stderr }
```    

---

Next: [Patterns and Actions](#7-patterns-actions-and-variables), Previous: [Printing](#5-printing-output), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 6 Expressions

Expressions are the basic building blocks of `awk` patterns
and actions.  An expression evaluates to a value that you can print, test,
or pass to a function.  Additionally, an expression
can assign a new value to a variable or a field by using an assignment operator.

An expression can serve as a pattern or action statement on its own.
Most other kinds of
statements contain one or more expressions that specify the data on which to
operate.  As in other languages, expressions in `awk` can include
variables, array references, constants, and function calls, as well as
combinations of these with various operators.

| []() | []()
| - | -
| &bull; [Values](#61-constants-variables-and-conversions) | Constants, Variables, and Regular Expressions.
| &bull; [All Operators](#62-operators-doing-something-with-values) | `gawk`&rsquo;s operators.
| &bull; [Truth Values and Conditions](#63-truth-values-and-conditions) | Testing for true and false.
| &bull; [Function Calls](#64-function-calls) | A function call is an expression.
| &bull; [Precedence](#65-operator-precedence-how-operators-nest) | How various operators nest.
| &bull; [Locales](#66-where-you-are-makes-a-difference) | How the locale affects things.
| &bull; [Expressions Summary](#67-summary) | Expressions summary.

---

Next: [All Operators](#62-operators-doing-something-with-values), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.1 Constants, Variables, and Conversions

Expressions are built up from values and the operations performed
upon them. This section describes the elementary objects
that provide the values used in expressions.

| []() | []()
| - | -
| &bull; [Constants](#611-constant-expressions) | String, numeric and regexp constants.
| &bull; [Using Constant Regexps](#612-using-regular-expression-constants) | When and how to use a regexp constant.
| &bull; [Variables](#613-variables) | Variables give names to values for later use.
| &bull; [Conversion](#614-conversion-of-strings-and-numbers) | The conversion of strings to numbers and vice versa.

---

Next: [Using Constant Regexps](#612-using-regular-expression-constants), Up: [Values](#61-constants-variables-and-conversions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.1 Constant Expressions

The simplest type of expression is the *constant*, which always has
the same value.  There are three types of constants: numeric,
string, and regular expression.

Each is used in the appropriate context when you need a data
value that isn&rsquo;t going to change.  Numeric constants can
have different forms, but are internally stored in an identical manner.

| []() | []()
| - | -
| &bull; [Scalar Constants](#6111-numeric-and-string-constants) | Numeric and string constants.
| &bull; [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers) | What are octal and hex numbers.
| &bull; [Regexp Constants](#6113-regular-expression-constants) | Regular Expression constants.

---

Next: [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers), Up: [Constants](#611-constant-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.1.1 Numeric and String Constants

A *numeric constant* stands for a number.  This number can be an
integer, a decimal fraction, or a number in scientific (exponential)
notation.[30](#FOOT30)
Here are some examples of numeric constants that all
have the same value:

    105
    1.05e+2
    1050e-1
    

A *string constant* consists of a sequence of characters enclosed in
double quotation marks.  For example:

    "parrot"
    

represents the string whose contents are &lsquo;parrot&rsquo;.  Strings in
`gawk` can be of any length, and they can contain any of the possible
eight-bit ASCII characters, including ASCII NUL (character code zero).
Other `awk`
implementations may have difficulty with some character codes.

---

Next: [Regexp Constants](#6113-regular-expression-constants), Previous: [Scalar Constants](#6111-numeric-and-string-constants), Up: [Constants](#611-constant-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.1.2 Octal and Hexadecimal Numbers

In `awk`, all numbers are in decimal (i.e., base 10).  Many other
programming languages allow you to specify numbers in other bases, often
octal (base 8) and hexadecimal (base 16).
In octal, the numbers go 0, 1, 2, 3, 4, 5, 6, 7, 10, 11, 12, and so on.
Just as &lsquo;11&rsquo; in decimal is 1 times 10 plus 1, so
&lsquo;11&rsquo; in octal is 1 times 8 plus 1. This equals 9 in decimal.
In hexadecimal, there are 16 digits. Because the everyday decimal
number system only has ten digits (&lsquo;0&rsquo;&ndash;&lsquo;9&rsquo;), the letters
&lsquo;a&rsquo; through &lsquo;f&rsquo; represent the rest.
(Case in the letters is usually irrelevant; hexadecimal &lsquo;a&rsquo; and &lsquo;A&rsquo;
have the same value.)
Thus, &lsquo;11&rsquo; in
hexadecimal is 1 times 16 plus 1, which equals 17 in decimal.

Just by looking at plain &lsquo;11&rsquo;, you can&rsquo;t tell what base it&rsquo;s in.
So, in C, C++, and other languages derived from C,
there is a special notation to signify the base.
Octal numbers start with a leading &lsquo;0&rsquo;,
and hexadecimal numbers start with a leading &lsquo;0x&rsquo; or &lsquo;0X&rsquo;:

<code>11</code>
Decimal value 11

<code>011</code>
Octal 11, decimal value 9

<code>0x11</code>
Hexadecimal 11, decimal value 17

This example shows the difference:

```awk
    $ gawk 'BEGIN { printf "%d, %d, %d\n", 011, 11, 0x11 }'
    -| 9, 11, 17
```    

Being able to use octal and hexadecimal constants in your programs is most
useful when working with data that cannot be represented conveniently as
characters or as regular numbers, such as binary data of various sorts.

`gawk` allows the use of octal and hexadecimal
constants in your program text.  However, such numbers in the input data
are not treated differently; doing so by default would break old
programs.
(If you really need to do this, use the --non-decimal-data
command-line option;
see [Nondecimal Data](#Nondecimal-Data).)
If you have octal or hexadecimal data,
you can use the `strtonum()` function
(see [String Functions](#913-string-manipulation-functions))
to convert the data into a number.
Most of the time, you will want to use octal or hexadecimal constants
when working with the built-in bit-manipulation functions;
see [Bitwise Functions](#916-bit-manipulation-functions)
for more information.

Unlike in some early C implementations, &lsquo;8&rsquo; and &lsquo;9&rsquo; are not
valid in octal constants.  For example, `gawk` treats &lsquo;018&rsquo;
as decimal 18:

```awk
    $ gawk 'BEGIN { print "021 is", 021 ; print 018 }'
    -| 021 is 17
    -| 18
```    

Octal and hexadecimal source code constants are a `gawk` extension.
If `gawk` is in compatibility mode
(see [Options](#22-command-line-options)),
they are not available.

A Constant&rsquo;s Base Does Not Affect Its Value

Once a numeric constant has
been converted internally into a number,
`gawk` no longer remembers
what the original form of the constant was; the internal value is
always used.  This has particular consequences for conversion of
numbers to strings:

```awk
    $ gawk 'BEGIN { printf "0x11 is <%s>\n", 0x11 }'
    -| 0x11 is <17>
```    

---

Previous: [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers), Up: [Constants](#611-constant-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.1.3 Regular Expression Constants

A *regexp constant* is a regular expression description enclosed in
slashes, such as `/^beginning and end$/`.  Most regexps used in
`awk` programs are constant, but the &lsquo;~&rsquo; and &lsquo;!~&rsquo;
matching operators can also match computed or dynamic regexps
(which are typically just ordinary strings or variables that contain a regexp,
but could be more complex expressions).

---

Next: [Variables](#613-variables), Previous: [Constants](#611-constant-expressions), Up: [Values](#61-constants-variables-and-conversions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.2 Using Regular Expression Constants

Regular expression constants consist of text describing
a regular expression enclosed in slashes (such as `/the +answer/`).
This section describes how such constants work in
POSIX `awk` and `gawk`, and then goes on to describe
*strongly typed regexp constants*, which are a `gawk` extension.

| []() | []()
| - | -
| &bull; [Standard Regexp Constants](#6121-standard-regular-expression-constants)| Regexp constants in standard `awk`.
| &bull; [Strong Regexp Constants](#6122-strongly-typed-regexp-constants)| Strongly typed regexp constants.

---

Next: [Strong Regexp Constants](#6122-strongly-typed-regexp-constants), Up: [Using Constant Regexps](#612-using-regular-expression-constants)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.2.1 Standard Regular Expression Constants

When used on the righthand side of the &lsquo;~&rsquo; or &lsquo;!~&rsquo;
operators, a regexp constant merely stands for the regexp that is to be
matched.
However, regexp constants (such as `/foo/`) may be used like simple expressions.
When a
regexp constant appears by itself, it has the same meaning as if it appeared
in a pattern (i.e., &lsquo;($0 ~ /foo/)&rsquo;).
(d.c.)
See [Expression Patterns](#712-expressions-as-patterns).
This means that the following two code segments:

```awk
    if ($0 ~ /barfly/ || $0 ~ /camelot/)
        print "found"
```    

and:

```awk
    if (/barfly/ || /camelot/)
        print "found"
```    

are exactly equivalent.
One rather bizarre consequence of this rule is that the following
Boolean expression is valid, but does not do what its author probably
intended:

```awk
    # Note that /foo/ is on the left of the ~
    if (/foo/ ~ $1) print "found foo"
```    

This code is &ldquo;obviously&rdquo; testing `$1` for a match against the regexp
`/foo/`.  But in fact, the expression &lsquo;/foo/ ~ $1&rsquo; really means
&lsquo;($0 ~ /foo/) ~ $1&rsquo;.  In other words, first match the input record
against the regexp `/foo/`.  The result is either zero or one,
depending upon the success or failure of the match.  That result
is then matched against the first field in the record.
Because it is unlikely that you would ever really want to make this kind of
test, `gawk` issues a warning when it sees this construct in
a program.
Another consequence of this rule is that the assignment statement:

```awk
    matches = /foo/
```    

assigns either zero or one to the variable `matches`, depending
upon the contents of the current input record.

Constant regular expressions are also used as the first argument for
the `gensub()`, `sub()`, and `gsub()` functions, as the
second argument of the `match()` function,
and as the third argument of the `split()` and `patsplit()` functions
(see [String Functions](#913-string-manipulation-functions)).
Modern implementations of `awk`, including `gawk`, allow
the third argument of `split()` to be a regexp constant, but some
older implementations do not.
(d.c.)
Because some built-in functions accept regexp constants as arguments,
confusion can arise when attempting to use regexp constants as arguments
to user-defined functions (see [User-defined](#92-user-defined-functions)).  For example:

```awk
    function mysub(pat, repl, str, global)
    {
        if (global)
            gsub(pat, repl, str)
        else
            sub(pat, repl, str)
        return str
    }
    

    {
        &hellip;
        text = "hi! hi yourself!"
        mysub(/hi/, "howdy", text, 1)
        &hellip;
    }
```    

In this example, the programmer wants to pass a regexp constant to the
user-defined function `mysub()`, which in turn passes it on to
either `sub()` or `gsub()`.  However, what really happens is that
the `pat` parameter is assigned a value of either one or zero, depending upon whether
or not `$0` matches `/hi/`.
`gawk` issues a warning when it sees a regexp constant used as
a parameter to a user-defined function, because passing a truth value in
this way is probably not what was intended.

---

Previous: [Standard Regexp Constants](#6121-standard-regular-expression-constants), Up: [Using Constant Regexps](#612-using-regular-expression-constants)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.2.2 Strongly Typed Regexp Constants

This section describes a `gawk`-specific feature.

As we saw in the previous section,
regexp constants (`/&hellip;/`) hold a strange position in the
`awk` language. In most contexts, they act like an expression:
&lsquo;$0 ~ /&hellip;/&rsquo;. In other contexts, they denote only a regexp to
be matched. In no case are they really a &ldquo;first class citizen&rdquo; of the
language. That is, you cannot define a scalar variable whose type is
&ldquo;regexp&rdquo; in the same sense that you can define a variable to be a
number or a string:

```awk
    num = 42        Numeric variable
    str = "hi"      String variable
    re = /foo/      Wrong! re is the result of $0 ~ /foo/
```    

For a number of more advanced use cases,
it would be nice to have regexp constants that
are *strongly typed*; in other words, that denote a regexp useful
for matching, and not an expression.

`gawk` provides this feature.  A strongly typed regexp constant
looks almost like a regular regexp constant, except that it is preceded
by an &lsquo;@&rsquo; sign:

```awk
    re = @/foo/     Regexp variable
```

Strongly typed regexp constants *cannot* be used everywhere that a
regular regexp constant can, because this would make the language even more
confusing.  Instead, you may use them only in certain contexts:

-  On the righthand side of the &lsquo;~&rsquo; and &lsquo;!~&rsquo; operators: &lsquo;some_var ~ @/foo/&rsquo;
(see [Regexp Usage](#31-how-to-use-regular-expressions)).

-  In the `case` part of a `switch` statement
(see [Switch Statement](#745-the-switch-statement)).

-  As an argument to one of the built-in functions that accept regexp constants:
`gensub()`,
`gsub()`,
`match()`,
`patsplit()`,
`split()`,
and
`sub()`
(see [String Functions](#913-string-manipulation-functions)).

-  As a parameter in a call to a user-defined function
(see [User-defined](#92-user-defined-functions)).

-  On the righthand side of an assignment to a variable: &lsquo;some_var = @/foo/&rsquo;.
In this case, the type of `some_var` is regexp. Additionally, `some_var`
can be used with &lsquo;~&rsquo; and &lsquo;!~&rsquo;, passed to one of the built-in functions
listed above, or passed as a parameter to a user-defined function.

You may use the `typeof()` built-in function
(see [Type Functions](#917-getting-type-information))
to determine if a variable or function parameter is
a regexp variable.

The true power of this feature comes from the ability to create variables that
have regexp type. Such variables can be passed on to user-defined functions,
without the confusing aspects of computed regular expressions created from
strings or string constants. They may also be passed through indirect function
calls (see [Indirect Calls](#93-indirect-function-calls))
and on to the built-in functions that accept regexp constants.

When used in numeric conversions, strongly typed regexp variables convert
to zero. When used in string conversions, they convert to the string
value of the original regexp text.

---

Next: [Conversion](#614-conversion-of-strings-and-numbers), Previous: [Using Constant Regexps](#612-using-regular-expression-constants), Up: [Values](#61-constants-variables-and-conversions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.3 Variables

*Variables* are ways of storing values at one point in your program for
use later in another part of your program.  They can be manipulated
entirely within the program text, and they can also be assigned values
on the `awk` command line.

| []() | []()
| - | -
| &bull; [Using Variables](#6131-using-variables-in-a-program) | Using variables in your programs.
| &bull; [Assignment Options](#6132-assigning-variables-on-the-command-line) | Setting variables on the command line and a summary of command-line syntax. This is an advanced method of input.

---

Next: [Assignment Options](#6132-assigning-variables-on-the-command-line), Up: [Variables](#613-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.3.1 Using Variables in a Program

Variables let you give names to values and refer to them later.  Variables
have already been used in many of the examples.  The name of a variable
must be a sequence of letters, digits, or underscores, and it may not begin
with a digit.
Here, a *letter* is any one of the 52 upper- and lowercase
English letters.  Other characters that may be defined as letters
in non-English locales are not valid in variable names.
Case is significant in variable names; `a` and `A`
are distinct variables.

A variable name is a valid expression by itself; it represents the
variable&rsquo;s current value.  Variables are given new values with
*assignment operators*, *increment operators*, and
*decrement operators*
(see [Assignment Ops](#623-assignment-expressions)).
In addition, the `sub()` and `gsub()` functions can
change a variable&rsquo;s value, and the `match()`, `split()`,
and `patsplit()` functions can change the contents of their
array parameters (see [String Functions](#913-string-manipulation-functions)).

A few variables have special built-in meanings, such as `FS` (the
field separator) and `NF` (the number of fields in the current input
record).  See [Built-in Variables](#75-predefined-variables) for a list of the predefined variables.
These predefined variables can be used and assigned just like all other
variables, but their values are also used or changed automatically by
`awk`.  All predefined variables&rsquo; names are entirely uppercase.

Variables in `awk` can be assigned either numeric or string values.
The kind of value a variable holds can change over the life of a program.
By default, variables are initialized to the empty string, which
is zero if converted to a number.  There is no need to explicitly
initialize a variable in `awk`,
which is what you would do in C and in most other traditional languages.

---

Previous: [Using Variables](#6131-using-variables-in-a-program), Up: [Variables](#613-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.3.2 Assigning Variables on the Command Line

Any `awk` variable can be set by including a *variable assignment*
among the arguments on the command line when `awk` is invoked
(see [Other Arguments](#23-other-command-line-arguments)).
Such an assignment has the following form:

```awk
    variable=text
```

With it, a variable is set either at the beginning of the
`awk` run or in between input files.
When the assignment is preceded with the -v option,
as in the following:

    -v variable=text

the variable is set at the very beginning, even before the
`BEGIN` rules execute.  The -v option and its assignment
must precede all the file name arguments, as well as the program text.
(See [Options](#22-command-line-options) for more information about
the -v option.)
Otherwise, the variable assignment is performed at a time determined by
its position among the input file arguments&mdash;after the processing of the
preceding input file argument.  For example:

```awk
    awk '{ print $n }' n=4 inventory-shipped n=2 mail-list
```    

prints the value of field number `n` for all input records.  Before
the first file is read, the command line sets the variable `n`
equal to four.  This causes the fourth field to be printed in lines from
inventory-shipped.  After the first file has finished,
but before the second file is started, `n` is set to two, so that the
second field is printed in lines from mail-list:

```awk
    $ awk '{ print $n }' n=4 inventory-shipped n=2 mail-list
    -| 15
    -| 24
    &hellip;
    -| 555-5553
    -| 555-3412
    &hellip;
```    

Command-line arguments are made available for explicit examination by
the `awk` program in the `ARGV` array
(see [ARGC and ARGV](#753-using-argc-and-argv)).
`awk` processes the values of command-line assignments for escape
sequences
(see [Escape Sequences](#32-escape-sequences)).
(d.c.)

---

Previous: [Variables](#613-variables), Up: [Values](#61-constants-variables-and-conversions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.4 Conversion of Strings and Numbers

Number-to-string and string-to-number conversion are generally
straightforward.  There can be subtleties to be aware of;
this section discusses this important facet of `awk`.

| []() | []()
| - | -
| &bull; [Strings And Numbers](#6141-how-awk-converts-between-strings-and-numbers) | How `awk` Converts Between Strings And Numbers.
| &bull; [Locale influences conversions](#6142-locales-can-influence-conversion) | How the locale may affect conversions.

---

Next: [Locale influences conversions](#6142-locales-can-influence-conversion), Up: [Conversion](#614-conversion-of-strings-and-numbers)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.4.1 How `awk` Converts Between Strings and Numbers

Strings are converted to numbers and numbers are converted to strings, if the context
of the `awk` program demands it.  For example, if the value of
either `foo` or `bar` in the expression &lsquo;foo + bar&rsquo;
happens to be a string, it is converted to a number before the addition
is performed.  If numeric values appear in string concatenation, they
are converted to strings.  Consider the following:

```awk
    two = 2; three = 3
    print (two three) + 4
```    

This prints the (numeric) value 27.  The numeric values of
the variables `two` and `three` are converted to strings and
concatenated together.  The resulting string is converted back to the
number 23, to which 4 is then added.

If, for some reason, you need to force a number to be converted to a
string, concatenate that number with the empty string, `""`.
To force a string to be converted to a number, add zero to that string.
A string is converted to a number by interpreting any numeric prefix
of the string as numerals:
`"2.5"` converts to 2.5, `"1e3"` converts to 1,000, and `"25fix"`
has a numeric value of 25.
Strings that can&rsquo;t be interpreted as valid numbers convert to zero.

The exact manner in which numbers are converted into strings is controlled
by the `awk` predefined variable `CONVFMT` (see [Built-in Variables](#75-predefined-variables)).
Numbers are converted using the `sprintf()` function
with `CONVFMT` as the format
specifier
(see [String Functions](#913-string-manipulation-functions)).

`CONVFMT`&rsquo;s default value is `"%.6g"`, which creates a value with
at most six significant digits.  For some applications, you might want to
change it to specify more precision.
On most modern machines,
17 digits is usually enough to capture a floating-point number&rsquo;s
value exactly.[31](#FOOT31)

Strange results can occur if you set `CONVFMT` to a string that doesn&rsquo;t
tell `sprintf()` how to format floating-point numbers in a useful way.
For example, if you forget the &lsquo;%&rsquo; in the format, `awk` converts
all numbers to the same constant string.

As a special case, if a number is an integer, then the result of converting
it to a string is *always* an integer, no matter what the value of
`CONVFMT` may be.  Given the following code fragment:

```awk
    CONVFMT = "%2.2f"
    a = 12
    b = a ""
```

`b` has the value `"12"`, not `"12.00"`.
(d.c.)

Pre-POSIX `awk` Used `OFMT` for String Conversion

Prior to the POSIX standard, `awk` used the value
of `OFMT` for converting numbers to strings.  `OFMT`
specifies the output format to use when printing numbers with `print`.
`CONVFMT` was introduced in order to separate the semantics of
conversion from the semantics of printing.  Both `CONVFMT` and
`OFMT` have the same default value: `"%.6g"`.  In the vast majority
of cases, old `awk` programs do not change their behavior.
See [Print](#51-the-print-statement) for more information on the `print` statement.

---

Previous: [Strings And Numbers](#6141-how-awk-converts-between-strings-and-numbers), Up: [Conversion](#614-conversion-of-strings-and-numbers)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.1.4.2 Locales Can Influence Conversion

Where you are can matter when it comes to converting between numbers and
strings.  The local character set and language&mdash;the *locale*&mdash;can
affect numeric formats.  In particular, for `awk` programs,
it affects the decimal point character and the thousands-separator
character.  The `"C"` locale, and most English-language locales,
use the period character (&lsquo;.&rsquo;) as the decimal point and don&rsquo;t
have a thousands separator.  However, many (if not most) European and
non-English locales use the comma (&lsquo;,&rsquo;) as the decimal point
character. European locales often use either a space or a period as
the thousands separator, if they have one.

The POSIX standard says that `awk` always uses the period as the decimal
point when reading the `awk` program source code, and for
command-line variable assignments (see [Other Arguments](#23-other-command-line-arguments)).  However,
when interpreting input data, for `print` and `printf` output,
and for number-to-string conversion, the local decimal point character
is used.  (d.c.) In all cases, numbers in source code and
in input data cannot have a thousands separator.  Here are some examples
indicating the difference in behavior, on a GNU/Linux system:

```awk
    $ export POSIXLY_CORRECT=1Force POSIX behavior
    $ gawk 'BEGIN { printf "%g\n", 3.1415927 }'
    -| 3.14159
    $ LC_ALL=en_DK.utf-8 gawk 'BEGIN { printf "%g\n", 3.1415927 }'
    -| 3,14159
    $ echo 4,321 | gawk '{ print $1 + 1 }'
    -| 5
    $ echo 4,321 | LC_ALL=en_DK.utf-8 gawk '{ print $1 + 1 }'
    -| 5,321
```    

The `en_DK.utf-8` locale is for English in Denmark, where the comma acts as
the decimal point separator.  In the normal `"C"` locale, `gawk`
treats &lsquo;4,321&rsquo; as 4, while in the Danish locale, it&rsquo;s treated
as the full number including the fractional part, 4.321.

Some earlier versions of `gawk` fully complied with this aspect
of the standard.  However, many users in non-English locales complained
about this behavior, because their data used a period as the decimal
point, so the default behavior was restored to use a period as the
decimal point character.  You can use the --use-lc-numeric
option (see [Options](#22-command-line-options)) to force `gawk` to use the locale&rsquo;s
decimal point character.  (`gawk` also uses the locale&rsquo;s decimal
point character when in POSIX mode, either via --posix or the
`POSIXLY_CORRECT` environment variable, as shown previously.)

[Table 6.1](#table_002dlocale_002daffects) describes the cases in which the locale&rsquo;s decimal
point character is used and when a period is used. Some of these
features have not been described yet.

FeatureDefault--posix or --use-lc-numeric`%'g`Use localeUse locale`%g`Use periodUse localeInputUse periodUse locale`strtonum()`Use periodUse locale

**Table 6.1: **Locale decimal point versus a period

Finally, modern-day formal standards and the IEEE standard floating-point
representation can have an unusual but important effect on the way
`gawk` converts some special string values to numbers.  The details
are presented in [POSIX Floating Point Problems](#POSIX-Floating-Point-Problems).

---

Next: [Truth Values and Conditions](#63-truth-values-and-conditions), Previous: [Values](#61-constants-variables-and-conversions), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.2 Operators: Doing Something with Values

This section introduces the *operators* that make use
of the values provided by constants and variables.

| []() | []()
| - | -
| &bull; [Arithmetic Ops](#621-arithmetic-operators) | Arithmetic operations (&lsquo;+&rsquo;, &lsquo;-&rsquo;, etc.)
| &bull; [Concatenation](#622-string-concatenation) | Concatenating strings.
| &bull; [Assignment Ops](#623-assignment-expressions) | Changing the value of a variable or a field.
| &bull; [Increment Ops](#624-increment-and-decrement-operators) | Incrementing the numeric value of a variable.

---

Next: [Concatenation](#622-string-concatenation), Up: [All Operators](#62-operators-doing-something-with-values)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.2.1 Arithmetic Operators

The `awk` language uses the common arithmetic operators when
evaluating expressions.  All of these arithmetic operators follow normal
precedence rules and work as you would expect them to.

The following example uses a file named grades, which contains
a list of student names as well as three test scores per student (it&rsquo;s
a small class):

    Pat   100 97 58
    Sandy  84 72 93
    Chris  72 92 89
    

This program takes the file grades and prints the average
of the scores:

```awk
    $ awk '{ sum = $2 + $3 + $4 ; avg = sum / 3
    >        print $1, avg }' grades
    -| Pat 85
    -| Sandy 83
    -| Chris 84.3333
```    

The following list provides the arithmetic operators in `awk`,
in order from the highest precedence to the lowest:

<code>x ^ y</code><code>x ** y</code>
Exponentiation; x raised to the y power.  &lsquo;2 ^ 3&rsquo; has
the value eight; the character sequence &lsquo;**&rsquo; is equivalent to
&lsquo;^&rsquo;. (c.e.)

<code>- x</code>
Negation.

<code>+ x</code>
Unary plus; the expression is converted to a number.

<code>x * y</code>
Multiplication.

<code>x / y</code>
Division;  because all numbers in `awk` are floating-point
numbers, the result is *not* rounded to an integer&mdash;&lsquo;3 / 4&rsquo; has
the value 0.75.  (It is a common mistake, especially for C programmers,
to forget that *all* numbers in `awk` are floating point,
and that division of integer-looking constants produces a real number,
not an integer.)

<code>x % y</code>
Remainder; further discussion is provided in the text, just
after this list.

<code>x + y</code>
Addition.

<code>x - y</code>
Subtraction.

Unary plus and minus have the same precedence,
the multiplication operators all have the same precedence, and
addition and subtraction have the same precedence.

When computing the remainder of &lsquo;x % y&rsquo;,
the quotient is rounded toward zero to an integer and
multiplied by y. This result is subtracted from x;
this operation is sometimes known as &ldquo;trunc-mod.&rdquo;  The following
relation always holds:

```awk
    b * int(a / b) + (a % b) == a
``` 

One possibly undesirable effect of this definition of remainder is that
&lsquo;x % y&rsquo; is negative if x is negative.  Thus:

```awk
    -17 % 8 = -1
```    

In other `awk` implementations, the signedness of the remainder
may be machine-dependent.

> NOTE: The POSIX standard only specifies the use of &lsquo;^&rsquo;
> for exponentiation.
> For maximum portability, do not use the &lsquo;**&rsquo; operator.

---

Next: [Assignment Ops](#623-assignment-expressions), Previous: [Arithmetic Ops](#621-arithmetic-operators), Up: [All Operators](#62-operators-doing-something-with-values)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.2.2 String Concatenation

> It seemed like a good idea at the time.

&mdash; *Brian Kernighan*

There is only one string operation: concatenation.  It does not have a
specific operator to represent it.  Instead, concatenation is performed by
writing expressions next to one another, with no operator.  For example:

```awk
    $ awk '{ print "Field number one: " $1 }' mail-list
    -| Field number one: Amelia
    -| Field number one: Anthony
    &hellip;
```    

Without the space in the string constant after the &lsquo;:&rsquo;, the line
runs together.  For example:

```awk
    $ awk '{ print "Field number one:" $1 }' mail-list
    -| Field number one:Amelia
    -| Field number one:Anthony
    &hellip;
```    

Because string concatenation does not have an explicit operator, it is
often necessary to ensure that it happens at the right time by using
parentheses to enclose the items to concatenate.  For example,
you might expect that the
following code fragment concatenates `file` and `name`:

```awk
    file = "file"
    name = "name"
    print "something meaningful" > file name
```    

This produces a syntax error with some versions of Unix
`awk`.[32](#FOOT32)
It is necessary to use the following:

```awk
    print "something meaningful" > (file name)
```    

Parentheses should be used around concatenation in all but the
most common contexts, such as on the righthand side of &lsquo;=&rsquo;.
Be careful about the kinds of expressions used in string concatenation.
In particular, the order of evaluation of expressions used for concatenation
is undefined in the `awk` language.  Consider this example:

```awk
    BEGIN {
        a = "don't"
        print (a " " (a = "panic"))
    }
```    

It is not defined whether the second assignment to `a` happens
before or after the value of `a` is retrieved for producing the
concatenated value.  The result could be either &lsquo;don't panic&rsquo;,
or &lsquo;panic panic&rsquo;.

The precedence of concatenation, when mixed with other operators, is often
counter-intuitive.  Consider this example:

```awk
    $ awk 'BEGIN { print -12 " " -24 }'
    -| -12-24
```    

This &ldquo;obviously&rdquo; is concatenating -12, a space, and -24.
But where did the space disappear to?
The answer lies in the combination of operator precedences and
`awk`&rsquo;s automatic conversion rules.  To get the desired result,
write the program this way:

```awk
    $ awk 'BEGIN { print -12 " " (-24) }'
    -| -12 -24
```    

This forces `awk` to treat the &lsquo;-&rsquo; on the &lsquo;-24&rsquo; as unary.
Otherwise, it&rsquo;s parsed as follows:

```awk
        -12 (" " - 24)
    &rArr; -12 (0 - 24)
    &rArr; -12 (-24)
    &rArr; -12-24
```    

As mentioned earlier,
when mixing concatenation with other operators, *parenthesize*.  Otherwise,
you&rsquo;re never quite sure what you&rsquo;ll get.

---

Next: [Increment Ops](#624-increment-and-decrement-operators), Previous: [Concatenation](#622-string-concatenation), Up: [All Operators](#62-operators-doing-something-with-values)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.2.3 Assignment Expressions

An *assignment* is an expression that stores a (usually different)
value into a variable.  For example, let&rsquo;s assign the value one to the variable
`z`:

```awk
    z = 1
```    

After this expression is executed, the variable `z` has the value one.
Whatever old value `z` had before the assignment is forgotten.

Assignments can also store string values.  For example, the
following stores
the value `"this food is good"` in the variable `message`:

```awk
    thing = "food"
    predicate = "good"
    message = "this " thing " is " predicate
```    

This also illustrates string concatenation.
The &lsquo;=&rsquo; sign is called an *assignment operator*.  It is the
simplest assignment operator because the value of the righthand
operand is stored unchanged.
Most operators (addition, concatenation, and so on) have no effect
except to compute a value.  If the value isn&rsquo;t used, there&rsquo;s no reason to
use the operator.  An assignment operator is different; it does
produce a value, but even if you ignore it, the assignment still
makes itself felt through the alteration of the variable.  We call this
a *side effect*.

The lefthand operand of an assignment need not be a variable
(see [Variables](#613-variables)); it can also be a field
(see [Changing Fields](#44-changing-the-contents-of-a-field)) or
an array element (see [Arrays](#8-arrays-in-awk)).
These are all called *lvalues*,
which means they can appear on the lefthand side of an assignment operator.
The righthand operand may be any expression; it produces the new value
that the assignment stores in the specified variable, field, or array
element. (Such values are called *rvalues*.)

It is important to note that variables do *not* have permanent types.
A variable&rsquo;s type is simply the type of whatever value was last assigned
to it.  In the following program fragment, the variable
`foo` has a numeric value at first, and a string value later on:

```awk
    foo = 1
    print foo
    

    foo = "bar"
    print foo
```    

When the second assignment gives `foo` a string value, the fact that
it previously had a numeric value is forgotten.

String values that do not begin with a digit have a numeric value of
zero. After executing the following code, the value of `foo` is five:

```awk
    foo = "a string"
    foo = foo + 5
```    

> NOTE: Using a variable as a number and then later as a string
> can be confusing and is poor programming style.  The previous two examples
> illustrate how `awk` works, *not* how you should write your
> programs!

An assignment is an expression, so it has a value&mdash;the same value that
is assigned.  Thus, &lsquo;z = 1&rsquo; is an expression with the value one.
One consequence of this is that you can write multiple assignments together,
such as:

```awk
    x = y = z = 5
```    

This example stores the value five in all three variables
(`x`, `y`, and `z`).
It does so because the
value of &lsquo;z = 5&rsquo;, which is five, is stored into `y` and then
the value of &lsquo;y = z = 5&rsquo;, which is five, is stored into `x`.

Assignments may be used anywhere an expression is called for.  For
example, it is valid to write &lsquo;x != (y = 1)&rsquo; to set `y` to one,
and then test whether `x` equals one.  But this style tends to make
programs hard to read; such nesting of assignments should be avoided,
except perhaps in a one-shot program.

Aside from &lsquo;=&rsquo;, there are several other assignment operators that
do arithmetic with the old value of the variable.  For example, the
operator &lsquo;+=&rsquo; computes a new value by adding the righthand value
to the old value of the variable.  Thus, the following assignment adds
five to the value of `foo`:

```awk
    foo += 5
```    

This is equivalent to the following:

```awk
    foo = foo + 5
```    

Use whichever makes the meaning of your program clearer.

There are situations where using &lsquo;+=&rsquo; (or any assignment operator)
is *not* the same as simply repeating the lefthand operand in the
righthand expression.  For example:

```awk
    # Thanks to Pat Rankin for this example
    BEGIN  {
        foo[rand()] += 5
        for (x in foo)
           print x, foo[x]
    

        bar[rand()] = bar[rand()] + 5
        for (x in bar)
           print x, bar[x]
    }
```    

The indices of `bar` are practically guaranteed to be different, because
`rand()` returns different values each time it is called.
(Arrays and the `rand()` function haven&rsquo;t been covered yet.
See [Arrays](#8-arrays-in-awk),
and
see [Numeric Functions](#912-numeric-functions)
for more information.)
This example illustrates an important fact about assignment
operators: the lefthand expression is only evaluated *once*.

It is up to the implementation as to which expression is evaluated
first, the lefthand or the righthand.
Consider this example:

```awk
    i = 1
    a[i += 2] = i + 1
```    

The value of `a[3]` could be either two or four.

[Table 6.2](#table_002dassign_002dops) lists the arithmetic assignment operators.  In each
case, the righthand operand is an expression whose value is converted
to a number.

OperatorEffectlvalue`+=`incrementAdd increment to the value of lvalue.lvalue`-=`decrementSubtract decrement from the value of lvalue.lvalue`*=`coefficientMultiply the value of lvalue by coefficient.lvalue`/=`divisorDivide the value of lvalue by divisor.lvalue`%=`modulusSet lvalue to its remainder by modulus.
lvalue`^=`powerRaise lvalue to the power power.lvalue`**=`powerRaise lvalue to the power power. (c.e.)

**Table 6.2: **Arithmetic assignment operators

> NOTE: Only the &lsquo;^=&rsquo; operator is specified by POSIX.
> For maximum portability, do not use the &lsquo;**=&rsquo; operator.

Syntactic Ambiguities Between &lsquo;/=&rsquo; and Regular Expressions

There is a syntactic ambiguity between the `/=` assignment
operator and regexp constants whose first character is an &lsquo;=&rsquo;.
(d.c.)
This is most notable in some commercial `awk` versions.
For example:

```awk
    $ awk /==/ /dev/null
    error&rarr; awk: syntax error at source line 1
    error&rarr;  context is
    error&rarr;         >>> /= <<<
    error&rarr; awk: bailing out at source line 1
```    

A workaround is:

```awk
    awk '/[=]=/' /dev/null
```    

`gawk` does not have this problem; BWK `awk`
and `mawk` also do not.

---

Previous: [Assignment Ops](#623-assignment-expressions), Up: [All Operators](#62-operators-doing-something-with-values)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.2.4 Increment and Decrement Operators

*Increment* and *decrement operators* increase or decrease the value of
a variable by one.  An assignment operator can do the same thing, so
the increment operators add no power to the `awk` language; however, they
are convenient abbreviations for very common operations.

The operator used for adding one is written &lsquo;++&rsquo;.  It can be used to increment
a variable either before or after taking its value.
To *pre-increment* a variable `v`, write &lsquo;++v&rsquo;.  This adds
one to the value of `v`&mdash;that new value is also the value of the
expression. (The assignment expression &lsquo;v += 1&rsquo; is completely equivalent.)
Writing the &lsquo;++&rsquo; after the variable specifies *post-increment*.  This
increments the variable value just the same; the difference is that the
value of the increment expression itself is the variable&rsquo;s *old*
value.  Thus, if `foo` has the value four, then the expression &lsquo;foo++&rsquo;
has the value four, but it changes the value of `foo` to five.
In other words, the operator returns the old value of the variable,
but with the side effect of incrementing it.

The post-increment &lsquo;foo++&rsquo; is nearly the same as writing &lsquo;(foo
+= 1) - 1&rsquo;.  It is not perfectly equivalent because all numbers in
`awk` are floating point&mdash;in floating point, &lsquo;foo + 1 - 1&rsquo; does
not necessarily equal `foo`.  But the difference is minute as
long as you stick to numbers that are fairly small (less than
1012).

Fields and array elements are incremented
just like variables.  (Use &lsquo;$(i++)&rsquo; when you want to do a field reference
and a variable increment at the same time.  The parentheses are necessary
because of the precedence of the field reference operator &lsquo;$&rsquo;.)

The decrement operator &lsquo;--&rsquo; works just like &lsquo;++&rsquo;, except that
it subtracts one instead of adding it.  As with &lsquo;++&rsquo;, it can be used before
the lvalue to pre-decrement or after it to post-decrement.
Following is a summary of increment and decrement expressions:

<code>++lvalue</code>
Increment lvalue, returning the new value as the
value of the expression.

<code>lvalue++</code>
Increment lvalue, returning the *old* value of lvalue
as the value of the expression.

<code>--lvalue</code>
Decrement lvalue, returning the new value as the
value of the expression.
(This expression is
like &lsquo;++lvalue&rsquo;, but instead of adding, it subtracts.)

<code>lvalue--</code>
Decrement lvalue, returning the *old* value of lvalue
as the value of the expression.
(This expression is
like &lsquo;lvalue++&rsquo;, but instead of adding, it subtracts.)

Operator Evaluation Order

> Doctor, it hurts when I do this!
> 
> Then don&rsquo;t do that!

&mdash; *Groucho Marx*

What happens for something like the following?

```awk
    b = 6
    print b += b++
``` 

Or something even stranger?

```awk
    b = 6
    b += ++b + b++
    print b
```    

In other words, when do the various side effects prescribed by the
postfix operators (&lsquo;b++&rsquo;) take effect?
When side effects happen is *implementation-defined*.
In other words, it is up to the particular version of `awk`.
The result for the first example may be 12 or 13, and for the second, it
may be 22 or 23.

In short, doing things like this is not recommended and definitely
not anything that you can rely upon for portability.
You should avoid such things in your own programs.

---

Next: [Function Calls](#64-function-calls), Previous: [All Operators](#62-operators-doing-something-with-values), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.3 Truth Values and Conditions

In certain contexts, expression values also serve as &ldquo;truth values&rdquo;; i.e.,
they determine what should happen next as the program runs. This
section describes how `awk` defines &ldquo;true&rdquo; and &ldquo;false&rdquo;
and how values are compared.

| []() | []()
| - | -
| &bull; [Truth Values](#631-true-and-false-in-awk) | What is &ldquo;true&rdquo; and what is &ldquo;false&rdquo;.
| &bull; [Typing and Comparison](#6322-comparison-operators) | How variables acquire types and how this affects comparison of numbers and strings with &lsquo;<&rsquo;, etc.
| &bull; [Boolean Ops](#633-boolean-expressions) | Combining comparison expressions using boolean operators &lsquo;||&rsquo; (&ldquo;or&rdquo;), &lsquo;&&&rsquo; (&ldquo;and&rdquo;) and &lsquo;!&rsquo; (&ldquo;not&rdquo;).
| &bull; [Conditional Exp](#634-conditional-expressions) | Conditional expressions select between two subexpressions under control of a third subexpression.

---

Next: [Typing and Comparison](#6322-comparison-operators), Up: [Truth Values and Conditions](#63-truth-values-and-conditions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.1 True and False in `awk`

Many programming languages have a special representation for the concepts
of &ldquo;true&rdquo; and &ldquo;false.&rdquo;  Such languages usually use the special
constants `true` and `false`, or perhaps their uppercase
equivalents.
However, `awk` is different.
It borrows a very simple concept of true and
false from C.  In `awk`, any nonzero numeric value *or* any
nonempty string value is true.  Any other value (zero or the null
string, `""`) is false.  The following program prints &lsquo;A strange
truth value&rsquo; three times:

```awk
    BEGIN {
       if (3.1415927)
           print "A strange truth value"
       if ("Four Score And Seven Years Ago")
           print "A strange truth value"
       if (j = 57)
           print "A strange truth value"
    }
``` 

There is a surprising consequence of the &ldquo;nonzero or non-null&rdquo; rule:
the string constant `"0"` is actually true, because it is non-null.
(d.c.)

---

Next: [Boolean Ops](#633-boolean-expressions), Previous: [Truth Values](#631-true-and-false-in-awk), Up: [Truth Values and Conditions](#63-truth-values-and-conditions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.2 Variable Typing and Comparison Expressions

> The Guide is definitive. Reality is frequently inaccurate.

&mdash; *Douglas Adams, The Hitchhiker&rsquo;s Guide to the Galaxy*

Unlike in other programming languages, in `awk` variables do not have a
fixed type. Instead, they can be either a number or a string, depending
upon the value that is assigned to them.
We look now at how variables are typed, and how `awk`
compares variables.

| []() | []()
| - | -
| &bull; [Variable Typing](#6321-string-type-versus-numeric-type) | String type versus numeric type.
| &bull; [Comparison Operators](#6322-comparison-operators) | The comparison operators.
| &bull; [POSIX String Comparison](#6323-string-comparison-based-on-locale-collating-order) | String comparison with POSIX rules.

---

Next: [Comparison Operators](#6322-comparison-operators), Up: [Typing and Comparison](#6322-comparison-operators)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.2.1 String Type versus Numeric Type

Scalar objects in `awk` (variables, array elements, and fields)
are *dynamically* typed.  This means their type can change as the
program runs, from *untyped* before any use,[33](#FOOT33) to string
or number, and then from string to number or number to string, as the
program progresses.  (`gawk` also provides regexp-typed scalars,
but let&rsquo;s ignore that for now; see [Strong Regexp Constants](#6122-strongly-typed-regexp-constants).)

You can&rsquo;t do much with untyped variables, other than tell that they
are untyped. The following program tests `a` against `""`
and `0`; the test succeeds when `a` has never been assigned
a value.  It also uses the built-in `typeof()` function
(not presented yet; see [Type Functions](#917-getting-type-information)) to show `a`&rsquo;s type:

```awk
    $ gawk 'BEGIN { print (a == "" && a == 0 ?
    > "a is untyped" : "a has a type!") ; print typeof(a) }'
    -| a is untyped
    -| unassigned
``` 

A scalar has numeric type when assigned a numeric value,
such as from a numeric constant, or from another scalar
with numeric type:

```awk
    $ gawk 'BEGIN { a = 42 ; print typeof(a)
    > b = a ; print typeof(b) }'
    number
    number
```    

Similarly, a scalar has string type when assigned a string
value, such as from a string constant, or from another scalar
with string type:

```awk
    $ gawk 'BEGIN { a = "forty two" ; print typeof(a)
    > b = a ; print typeof(b) }'
    string
    string
```    

So far, this is all simple and straightforward.  What happens, though,
when `awk` has to process data from a user?  Let&rsquo;s start with
field data.  What should the following command produce as output?

```awk
    echo hello | awk '{ printf("%s %s < 42\n", $1,
                               ($1 < 42 ? "is" : "is not")) }'
```    

Since &lsquo;hello&rsquo; is alphabetic data, `awk` can only do a string
comparison.  Internally, it converts `42` into `"42"` and compares
the two string values `"hello"` and `"42"`. Here&rsquo;s the result:

```awk
    $ echo hello | awk '{ printf("%s %s < 42\n", $1,
    >                            ($1 < 42 ? "is" : "is not")) }'
    -| hello is not < 42
```    

However, what happens when data from a user *looks like* a number?
On the one hand, in reality, the input data consists of characters, not
binary numeric
values.  But, on the other hand, the data looks numeric, and `awk`
really ought to treat it as such. And indeed, it does:

```awk
    $ echo 37 | awk '{ printf("%s %s < 42\n", $1,
    >                         ($1 < 42 ? "is" : "is not")) }'
    -| 37 is < 42
```    

Here are the rules for when `awk`
treats data as a number, and for when it treats data as a string.

The POSIX standard uses the term *numeric string* for input data that
looks numeric.  The &lsquo;37&rsquo; in the previous example is a numeric string.
So what is the type of a numeric string? Answer: numeric.

The type of a variable is important because the types of two variables
determine how they are compared.
Variable typing follows these definitions and rules:

-  A numeric constant or the result of a numeric operation has the *numeric*
attribute.

-  A string constant or the result of a string operation has the *string*
attribute.

-  Fields, `getline` input, `FILENAME`, `ARGV` elements,
`ENVIRON` elements, and the elements of an array created by
`match()`, `split()`, and `patsplit()` that are numeric
strings have the *strnum* attribute.[34](#FOOT34)
Otherwise, they have
the *string* attribute.  Uninitialized variables also have the
*strnum* attribute.

-  Attributes propagate across assignments but are not changed by
any use.

The last rule is particularly important. In the following program,
`a` has numeric type, even though it is later used in a string
operation:

```awk
    BEGIN {
         a = 12.345
         b = a " is a cute number"
         print b
    }
```    

When two operands are compared, either string comparison or numeric comparison
may be used. This depends upon the attributes of the operands, according to the
following symmetric matrix:

            +----------------------------------------------
            |       STRING          NUMERIC         STRNUM
    --------+----------------------------------------------
            |
    STRING  |       string          string          string
            |
    NUMERIC |       string          numeric         numeric
            |
    STRNUM  |       string          numeric         numeric
    --------+----------------------------------------------
    

The basic idea is that user input that looks numeric&mdash;and *only*
user input&mdash;should be treated as numeric, even though it is actually
made of characters and is therefore also a string.
Thus, for example, the string constant `" +3.14"`,
when it appears in program source code,
is a string&mdash;even though it looks numeric&mdash;and
is *never* treated as a number for comparison
purposes.

In short, when one operand is a &ldquo;pure&rdquo; string, such as a string
constant, then a string comparison is performed.  Otherwise, a
numeric comparison is performed.
(The primary difference between a number and a strnum is that
for strnums `gawk` preserves the original string value that
the scalar had when it came in.)

This point bears additional emphasis:
Input that looks numeric *is* numeric.
All other input is treated as strings.

Thus, the six-character input string &lsquo; +3.14&rsquo; receives the
strnum attribute. In contrast, the eight characters
`" +3.14"` appearing in program text comprise a string constant.
The following examples print &lsquo;1&rsquo; when the comparison between
the two different constants is true, and &lsquo;0&rsquo; otherwise:

```awk
    $ echo ' +3.14' | awk '{ print($0 == " +3.14") }'True
    -| 1
    $ echo ' +3.14' | awk '{ print($0 == "+3.14") }'False
    -| 0
    $ echo ' +3.14' | awk '{ print($0 == "3.14") }'False
    -| 0
    $ echo ' +3.14' | awk '{ print($0 == 3.14) }'True
    -| 1
    $ echo ' +3.14' | awk '{ print($1 == " +3.14") }'False
    -| 0
    $ echo ' +3.14' | awk '{ print($1 == "+3.14") }'True
    -| 1
    $ echo ' +3.14' | awk '{ print($1 == "3.14") }'False
    -| 0
    $ echo ' +3.14' | awk '{ print($1 == 3.14) }'True
    -| 1
```    

You can see the type of an input field (or other user input)
using `typeof()`:

```awk
    $ echo hello 37 | gawk '{ print typeof($1), typeof($2) }'
    -| string strnum
```    

---

Next: [POSIX String Comparison](#6323-string-comparison-based-on-locale-collating-order), Previous: [Variable Typing](#6321-string-type-versus-numeric-type), Up: [Typing and Comparison](#6322-comparison-operators)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.2.2 Comparison Operators

*Comparison expressions* compare strings or numbers for
relationships such as equality.  They are written using *relational
operators*, which are a superset of those in C.
[Table 6.3](#table_002drelational_002dops) describes them.

ExpressionResultx`<`yTrue if x is less than yx`<=`yTrue if x is less than or equal to yx`>`yTrue if x is greater than yx`>=`yTrue if x is greater than or equal to yx`==`yTrue if x is equal to yx`!=`yTrue if x is not equal to yx`~`yTrue if the string x matches the regexp denoted by yx`!~`yTrue if the string x does not match the regexp denoted by ysubscript`in`arrayTrue if the array array has an element with the subscript subscript

**Table 6.3: **Relational operators

Comparison expressions have the value one if true and zero if false.
When comparing operands of mixed types, numeric operands are converted
to strings using the value of `CONVFMT`
(see [Conversion](#614-conversion-of-strings-and-numbers)).

Strings are compared
by comparing the first character of each, then the second character of each,
and so on.  Thus, `"10"` is less than `"9"`.  If there are two
strings where one is a prefix of the other, the shorter string is less than
the longer one.  Thus, `"abc"` is less than `"abcd"`.

It is very easy to accidentally mistype the &lsquo;==&rsquo; operator and
leave off one of the &lsquo;=&rsquo; characters.  The result is still valid
`awk` code, but the program does not do what is intended:

```awk
    if (a = b)   # oops! should be a == b
       &hellip;
    else
       &hellip;
```    

Unless `b` happens to be zero or the null string, the `if`
part of the test always succeeds.  Because the operators are
so similar, this kind of error is very difficult to spot when
scanning the source code.

The following list of expressions illustrates the kinds of comparisons
`awk` performs, as well as what the result of each comparison is:

<code>1.5 <= 2.0</code>
Numeric comparison (true)

<code>"abc" >= "xyz"</code>
String comparison (false)

<code>1.5 != " +2"</code>
String comparison (true)

<code>"1e2" < "3"</code>
String comparison (true)

<code>a = 2; b = "2"</code><code>a == b</code>
String comparison (true)

<code>a = 2; b = " +2"</code><code>a == b</code>
String comparison (false)

In this example:

```awk
    $ echo 1e2 3 | awk '{ print ($1 < $2) ? "true" : "false" }'
    -| false
``` 

the result is &lsquo;false&rsquo; because both `$1` and `$2`
are user input.  They are numeric strings&mdash;therefore both have
the strnum attribute, dictating a numeric comparison.
The purpose of the comparison rules and the use of numeric strings is
to attempt to produce the behavior that is &ldquo;least surprising,&rdquo; while
still &ldquo;doing the right thing.&rdquo;

String comparisons and regular expression comparisons are very different.
For example:

```awk
    x == "foo"
```    

has the value one, or is true if the variable `x`
is precisely &lsquo;foo&rsquo;.  By contrast:

```awk
    x ~ /foo/
```    

has the value one if `x` contains &lsquo;foo&rsquo;, such as
`"Oh, what a fool am I!"`.

The righthand operand of the &lsquo;~&rsquo; and &lsquo;!~&rsquo; operators may be
either a regexp constant (`/`&hellip;`/`) or an ordinary
expression. In the latter case, the value of the expression as a string is used as a
dynamic regexp (see [Regexp Usage](#31-how-to-use-regular-expressions); also
see [Computed Regexps](#36-using-dynamic-regexps)).

A constant regular
expression in slashes by itself is also an expression.
`/regexp/` is an abbreviation for the following comparison expression:

```awk
    $0 ~ /regexp/
```    

One special place where `/foo/` is *not* an abbreviation for
&lsquo;$0 ~ /foo/&rsquo; is when it is the righthand operand of &lsquo;~&rsquo; or
&lsquo;!~&rsquo;.
See [Using Constant Regexps](#612-using-regular-expression-constants),
where this is discussed in more detail.

---

Previous: [Comparison Operators](#6322-comparison-operators), Up: [Typing and Comparison](#6322-comparison-operators)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.2.3 String Comparison Based on Locale Collating Order

The POSIX standard used to say that all string comparisons are
performed based on the locale&rsquo;s *collating order*. This
is the order in which characters sort, as defined by the locale
(for more discussion, see [Locales](#66-where-you-are-makes-a-difference)).  This order is usually very
different from the results obtained when doing straight byte-by-byte
comparison.[35](#FOOT35)

Because this behavior differs considerably from existing practice,
`gawk` only implemented it when in POSIX mode (see [Options](#22-command-line-options)).
Here is an example to illustrate the difference, in an `en_US.UTF-8`
locale:

```awk
    $ gawk 'BEGIN { printf("ABC < abc = %s\n",
    >                     ("ABC" < "abc" ? "TRUE" : "FALSE")) }'
    -| ABC < abc = TRUE
    $ gawk --posix 'BEGIN { printf("ABC < abc = %s\n",
    >                             ("ABC" < "abc" ? "TRUE" : "FALSE")) }'
    -| ABC < abc = FALSE
```    

Fortunately, as of August 2016, comparison based on locale
collating order is no longer required for the `==` and `!=`
operators.[36](#FOOT36) However, comparison based on locales is still
required for `<`, `<=`, `>`, and `>=`.  POSIX thus
recommends as follows:

> Since the `==` operator checks whether strings are identical,
> not whether they collate equally, applications needing to check whether
> strings collate equally can use:
> 
> 
> 
>     a <= b && a >= b
>     

As of version 4.2, `gawk` continues to use locale
collating order for `<`, `<=`, `>`, and `>=` only
in POSIX mode.

---

Next: [Conditional Exp](#634-conditional-expressions), Previous: [Typing and Comparison](#6322-comparison-operators), Up: [Truth Values and Conditions](#63-truth-values-and-conditions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.3 Boolean Expressions

A *Boolean expression* is a combination of comparison expressions or
matching expressions, using the Boolean operators &ldquo;or&rdquo;
(&lsquo;||&rsquo;), &ldquo;and&rdquo; (&lsquo;&&&rsquo;), and &ldquo;not&rdquo; (&lsquo;!&rsquo;), along with
parentheses to control nesting.  The truth value of the Boolean expression is
computed by combining the truth values of the component expressions.
Boolean expressions are also referred to as *logical expressions*.
The terms are equivalent.

Boolean expressions can be used wherever comparison and matching
expressions can be used.  They can be used in `if`, `while`,
`do`, and `for` statements
(see [Statements](#74-control-statements-in-actions)).
They have numeric values (one if true, zero if false) that come into play
if the result of the Boolean expression is stored in a variable or
used in arithmetic.

In addition, every Boolean expression is also a valid pattern, so
you can use one as a pattern to control the execution of rules.
The Boolean operators are:

<code>boolean1 && boolean2</code>
True if both boolean1 and boolean2 are true.  For example,
the following statement prints the current input record if it contains
both &lsquo;edu&rsquo; and &lsquo;li&rsquo;:

```awk
    if ($0 ~ /edu/ && $0 ~ /li/) print
```    

The subexpression boolean2 is evaluated only if boolean1
is true.  This can make a difference when boolean2 contains
expressions that have side effects. In the case of &lsquo;$0 ~ /foo/ &&
($2 == bar++)&rsquo;, the variable `bar` is not incremented if there is
no substring &lsquo;foo&rsquo; in the record.

<code>boolean1 || boolean2</code>
True if at least one of boolean1 or boolean2 is true.
For example, the following statement prints all records in the input
that contain *either* &lsquo;edu&rsquo; or
&lsquo;li&rsquo;:

```awk
    if ($0 ~ /edu/ || $0 ~ /li/) print
```    

The subexpression boolean2 is evaluated only if boolean1
is false.  This can make a difference when boolean2 contains
expressions that have side effects.
(Thus, this test never really distinguishes records that contain both
&lsquo;edu&rsquo; and &lsquo;li&rsquo;&mdash;as soon as &lsquo;edu&rsquo; is matched,
the full test succeeds.)

<code>! boolean</code>
True if boolean is false.  For example,
the following program prints &lsquo;no home!&rsquo; in
the unusual event that the `HOME` environment
variable is not defined:

```awk
    BEGIN { if (! ("HOME" in ENVIRON))
                print "no home!" }
```    

(The `in` operator is described in
[Reference to Elements](#812-referring-to-an-array-element).)

The &lsquo;&&&rsquo; and &lsquo;||&rsquo; operators are called *short-circuit*
operators because of the way they work.  Evaluation of the full expression
is &ldquo;short-circuited&rdquo; if the result can be determined partway through
its evaluation.

Statements that end with &lsquo;&&&rsquo; or &lsquo;||&rsquo; can be continued simply
by putting a newline after them.  But you cannot put a newline in front
of either of these operators without using backslash continuation
(see [Statements/Lines](#16-awk-statements-versus-lines)).

The actual value of an expression using the &lsquo;!&rsquo; operator is
either one or zero, depending upon the truth value of the expression it
is applied to.
The &lsquo;!&rsquo; operator is often useful for changing the sense of a flag
variable from false to true and back again. For example, the following
program is one way to print lines in between special bracketing lines:

```awk
    $1 == "START"   { interested = ! interested; next }
    interested      { print }
    $1 == "END"     { interested = ! interested; next }
```    

The variable `interested`, as with all `awk` variables, starts
out initialized to zero, which is also false.  When a line is seen whose
first field is &lsquo;START&rsquo;, the value of `interested` is toggled
to true, using &lsquo;!&rsquo;. The next rule prints lines as long as
`interested` is true.  When a line is seen whose first field is
&lsquo;END&rsquo;, `interested` is toggled back to false.[37](#FOOT37)

Most commonly, the &lsquo;!&rsquo; operator is used in the conditions of
`if` and `while` statements, where it often makes more
sense to phrase the logic in the negative:

```awk
    if (! some condition || some other condition) {
        &hellip; do whatever processing &hellip;
    }
```    

> NOTE: The `next` statement is discussed in
> [Next Statement](#748-the-next-statement).
> `next` tells `awk` to skip the rest of the rules, get the
> next record, and start processing the rules over again at the top.
> The reason it&rsquo;s there is to avoid printing the bracketing
> &lsquo;START&rsquo; and &lsquo;END&rsquo; lines.

---

Previous: [Boolean Ops](#633-boolean-expressions), Up: [Truth Values and Conditions](#63-truth-values-and-conditions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 6.3.4 Conditional Expressions

A *conditional expression* is a special kind of expression that has
three operands.  It allows you to use one expression&rsquo;s value to select
one of two other expressions.
The conditional expression in `awk` is the same as in the C
language, as shown here:

```awk
    selector ? if-true-exp : if-false-exp
```

There are three subexpressions.  The first, selector, is always
computed first.  If it is &ldquo;true&rdquo; (not zero or not null), then
if-true-exp is computed next, and its value becomes the value of
the whole expression.  Otherwise, if-false-exp is computed next,
and its value becomes the value of the whole expression.
For example, the following expression produces the absolute value of `x`:

```awk
    x >= 0 ? x : -x
```    

Each time the conditional expression is computed, only one of
if-true-exp and if-false-exp is used; the other is ignored.
This is important when the expressions have side effects.  For example,
this conditional expression examines element `i` of either array
`a` or array `b`, and increments `i`:

```awk
    x == y ? a[i++] : b[i++]
```    

This is guaranteed to increment `i` exactly once, because each time
only one of the two increment expressions is executed
and the other is not.
See [Arrays](#8-arrays-in-awk),
for more information about arrays.

As a minor `gawk` extension,
a statement that uses &lsquo;?:&rsquo; can be continued simply
by putting a newline after either character.
However, putting a newline in front
of either character does not work without using backslash continuation
(see [Statements/Lines](#16-awk-statements-versus-lines)).
If --posix is specified
(see [Options](#22-command-line-options)), this extension is disabled.

---

Next: [Precedence](#65-operator-precedence-how-operators-nest), Previous: [Truth Values and Conditions](#63-truth-values-and-conditions), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.4 Function Calls

A *function* is a name for a particular calculation.
This enables you to
ask for it by name at any point in the program.  For
example, the function `sqrt()` computes the square root of a number.

A fixed set of functions are *built in*, which means they are
available in every `awk` program.  The `sqrt()` function is one
of these.  See [Built-in](#91-built-in-functions) for a list of built-in
functions and their descriptions.  In addition, you can define
functions for use in your program.
See [User-defined](#92-user-defined-functions)
for instructions on how to do this.
Finally, `gawk` lets you write functions in C or C++
that may be called from your program (see [Dynamic Extensions](#Dynamic-Extensions)).

The way to use a function is with a *function call* expression,
which consists of the function name followed immediately by a list of
*arguments* in parentheses.  The arguments are expressions that
provide the raw materials for the function&rsquo;s calculations.
When there is more than one argument, they are separated by commas.  If
there are no arguments, just write &lsquo;()&rsquo; after the function name.
The following examples show function calls with and without arguments:

    sqrt(x^2 + y^2)        one argument
    atan2(y, x)            two arguments
    rand()                 no arguments

> CAUTION: Do not put any space between the function name and the opening parenthesis!
> A user-defined function name looks just like the name of a
> variable&mdash;a space would make the expression look like concatenation of
> a variable with an expression inside parentheses.
> With built-in functions, space before the parenthesis is harmless, but
> it is best not to get into the habit of using space to avoid mistakes
> with user-defined functions.

Each function expects a particular number
of arguments.  For example, the `sqrt()` function must be called with
a single argument, the number of which to take the square root:

    sqrt(argument)
    

Some of the built-in functions have one or
more optional arguments.
If those arguments are not supplied, the functions
use a reasonable default value.
See [Built-in](#91-built-in-functions) for full details.  If arguments
are omitted in calls to user-defined functions, then those arguments are
treated as local variables. Such local variables act like the
empty string if referenced where a string value is required,
and like zero if referenced where a numeric value is required
(see [User-defined](#92-user-defined-functions)).

As an advanced feature, `gawk` provides indirect function calls,
which is a way to choose the function to call at runtime, instead of
when you write the source code to your program. We defer discussion of
this feature until later; see [Indirect Calls](#93-indirect-function-calls).

Like every other expression, the function call has a value, often
called the *return value*, which is computed by the function
based on the arguments you give it.  In this example, the return value
of &lsquo;sqrt(argument)&rsquo; is the square root of argument.
The following program reads numbers, one number per line, and prints
the square root of each one:

```awk
    $ awk '{ print "The square root of", $1, "is", sqrt($1) }'1
    -| The square root of 1 is 1
    3
    -| The square root of 3 is 1.73205
    5
    -| The square root of 5 is 2.23607
    Ctrl-d
```

A function can also have side effects, such as assigning
values to certain variables or doing I/O.
This program shows how the `match()` function
(see [String Functions](#913-string-manipulation-functions))
changes the variables `RSTART` and `RLENGTH`:

```awk
    {
        if (match($1, $2))
            print RSTART, RLENGTH
        else
            print "no match"
    }
```    

Here is a sample run:

```awk
    $ awk -f matchit.awkaaccdd  c+
    -| 3 2
    foo     bar
    -| no match
    abcdefg e
    -| 5 1
```    

---

Next: [Locales](#66-where-you-are-makes-a-difference), Previous: [Function Calls](#64-function-calls), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.5 Operator Precedence (How Operators Nest)

*Operator precedence* determines how operators are grouped when
different operators appear close by in one expression.  For example,
&lsquo;*&rsquo; has higher precedence than &lsquo;+&rsquo;; thus, &lsquo;a + b * c&rsquo;
means to multiply `b` and `c`, and then add `a` to the
product (i.e., &lsquo;a + (b * c)&rsquo;).

The normal precedence of the operators can be overruled by using parentheses.
Think of the precedence rules as saying where the
parentheses are assumed to be.  In
fact, it is wise to always use parentheses whenever there is an unusual
combination of operators, because other people who read the program may
not remember what the precedence is in this case.
Even experienced programmers occasionally forget the exact rules,
which leads to mistakes.
Explicit parentheses help prevent
any such mistakes.

When operators of equal precedence are used together, the leftmost
operator groups first, except for the assignment, conditional, and
exponentiation operators, which group in the opposite order.
Thus, &lsquo;a - b + c&rsquo; groups as &lsquo;(a - b) + c&rsquo; and
&lsquo;a = b = c&rsquo; groups as &lsquo;a = (b = c)&rsquo;.

Normally the precedence of prefix unary operators does not matter,
because there is only one way to interpret
them: innermost first.  Thus, &lsquo;$++i&rsquo; means &lsquo;$(++i)&rsquo; and
&lsquo;++$x&rsquo; means &lsquo;++($x)&rsquo;.  However, when another operator follows
the operand, then the precedence of the unary operators can matter.
&lsquo;$x^2&rsquo; means &lsquo;($x)^2&rsquo;, but &lsquo;-x^2&rsquo; means
&lsquo;-(x^2)&rsquo;, because &lsquo;-&rsquo; has lower precedence than &lsquo;^&rsquo;,
whereas &lsquo;$&rsquo; has higher precedence.
Also, operators cannot be combined in a way that violates the
precedence rules; for example, &lsquo;$$0++--&rsquo; is not a valid
expression because the first &lsquo;$&rsquo; has higher precedence than the
&lsquo;++&rsquo;; to avoid the problem the expression can be rewritten as
&lsquo;$($0++)--&rsquo;.

This list presents `awk`&rsquo;s operators, in order of highest
to lowest precedence:

<code>(&hellip;)</code>
Grouping.

<code>$</code>
Field reference.

<code>++ --</code>
Increment, decrement.

<code>^ **</code>
Exponentiation.  These operators group right to left.

<code>+ - !</code>
Unary plus, minus, logical &ldquo;not.&rdquo;

<code>* / %</code>
Multiplication, division, remainder.

<code>+ -</code>
Addition, subtraction.

String concatenation
There is no special symbol for concatenation.
The operands are simply written side by side
(see [Concatenation](#622-string-concatenation)).

<code>< <= == != > >= >> | |&</code>
Relational and redirection.
The relational operators and the redirections have the same precedence
level.  Characters such as &lsquo;>&rsquo; serve both as relationals and as
redirections; the context distinguishes between the two meanings.

Note that the I/O redirection operators in `print` and `printf`
statements belong to the statement level, not to expressions.  The
redirection does not produce an expression that could be the operand of
another operator.  As a result, it does not make sense to use a
redirection operator near another operator of lower precedence without
parentheses.  Such combinations (e.g., &lsquo;print foo > a ? b : c&rsquo;)
result in syntax errors.
The correct way to write this statement is &lsquo;print foo > (a ? b : c)&rsquo;.

<code>~ !~</code>
Matching, nonmatching.

<code>in</code>
Array membership.

<code>&&</code>
Logical &ldquo;and.&rdquo;

<code>||</code>
Logical &ldquo;or.&rdquo;

<code>?:</code>
Conditional.  This operator groups right to left.

<code>= += -= *= /= %= ^= **=</code>
Assignment.  These operators group right to left.

> NOTE: The &lsquo;|&&rsquo;, &lsquo;**&rsquo;, and &lsquo;**=&rsquo; operators are not specified by POSIX.
> For maximum portability, do not use them.

---

Next: [Expressions Summary](#67-summary), Previous: [Precedence](#65-operator-precedence-how-operators-nest), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.6 Where You Are Makes a Difference

Modern systems support the notion of *locales*: a way to tell the
system about the local character set and language.  The ISO C standard
defines a default `"C"` locale, which is an environment that is
typical of what many C programmers are used to.

Once upon a time, the locale setting used to affect regexp matching,
but this is no longer true (see [Ranges and Locales](#Ranges-and-Locales)).

Locales can affect record splitting.  For the normal case of &lsquo;RS =
"\n"&rsquo;, the locale is largely irrelevant.  For other single-character
record separators, setting &lsquo;LC_ALL=C&rsquo; in the environment will
give you much better performance when reading records.  Otherwise,
`gawk` has to make several function calls, *per input
character*, to find the record terminator.

Locales can affect how dates and times are formatted (see [Time Functions](#915-time-functions)).  For example, a common way to abbreviate the date September
4, 2015, in the United States is &ldquo;9/4/15.&rdquo;  In many countries in
Europe, however, it is abbreviated &ldquo;4.9.15.&rdquo;  Thus, the &lsquo;%x&rsquo;
specification in a `"US"` locale might produce &lsquo;9/4/15&rsquo;,
while in a `"EUROPE"` locale, it might produce &lsquo;4.9.15&rsquo;.

According to POSIX, string comparison is also affected by locales (similar
to regular expressions).  The details are presented in [POSIX String Comparison](#6323-string-comparison-based-on-locale-collating-order).

Finally, the locale affects the value of the decimal point character
used when `gawk` parses input data.  This is discussed in detail
in [Conversion](#614-conversion-of-strings-and-numbers).

---

Previous: [Locales](#66-where-you-are-makes-a-difference), Up: [Expressions](#6-expressions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 6.7 Summary

-  Expressions are the basic elements of computation in programs.  They are
built from constants, variables, function calls, and combinations of the
various kinds of values with operators.

- `awk` supplies three kinds of constants: numeric, string, and
regexp.  `gawk` lets you specify numeric constants in octal
and hexadecimal (bases 8 and 16) as well as decimal (base 10).
In certain contexts, a standalone regexp constant such as `/foo/`
has the same meaning as &lsquo;$0 ~ /foo/&rsquo;.

-  Variables hold values between uses in computations. A number of built-in
variables provide information to your `awk` program, and a number
of others let you control how `awk` behaves.

-  Numbers are automatically converted to strings, and strings to numbers,
as needed by `awk`. Numeric values are converted as if they were
formatted with `sprintf()` using the format in `CONVFMT`.
Locales can influence the conversions.

- `awk` provides the usual arithmetic operators (addition,
subtraction, multiplication, division, modulus), and unary plus and minus.
It also provides comparison operators, Boolean operators, an array membership
testing operator, and regexp
matching operators.  String concatenation is accomplished by placing
two expressions next to each other; there is no explicit operator.
The three-operand &lsquo;?:&rsquo; operator provides an &ldquo;if-else&rdquo; test within
expressions.

-  Assignment operators provide convenient shorthands for common arithmetic
operations.

-  In `awk`, a value is considered to be true if it is nonzero
*or* non-null. Otherwise, the value is false.

-  A variable&rsquo;s type is set upon each assignment and may change over its
lifetime.  The type determines how it behaves in comparisons (string
or numeric).

-  Function calls return a value that may be used as part of a larger
expression.  Expressions used to pass parameter values are fully
evaluated before the function is called.  `awk` provides
built-in and user-defined functions; this is described in
[Functions](#9-functions).

-  Operator precedence specifies the order in which operations are performed,
unless explicitly overridden by parentheses.  `awk`&rsquo;s operator
precedence is compatible with that of C.

-  Locales can affect the format of data as output by an `awk`
program, and occasionally the format for data read as input.

---

Next: [Arrays](#8-arrays-in-awk), Previous: [Expressions](#6-expressions), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 7 Patterns, Actions, and Variables

As you have already seen, each `awk` statement consists of
a pattern with an associated action.  This chapter describes how
you build patterns and actions, what kinds of things you can do within
actions, and `awk`&rsquo;s predefined variables.

The pattern&ndash;action rules and the statements available for use
within actions form the core of `awk` programming.
In a sense, everything covered
up to here has been the foundation
that programs are built on top of.  Now it&rsquo;s time to start
building something useful.

| []() | []()
| - | -
| &bull; [Pattern Overview](#71-pattern-elements) | What goes into a pattern.
| &bull; [Using Shell Variables](#72-using-shell-variables-in-programs) | How to use shell variables with `awk`.
| &bull; [Action Overview](#73-actions) | What goes into an action.
| &bull; [Statements](#74-control-statements-in-actions) | Describes the various control statements in detail.
| &bull; [Built-in Variables](#75-predefined-variables) | Summarizes the predefined variables.
| &bull; [Pattern Action Summary](#76-summary) | Patterns and Actions summary.

---

Next: [Using Shell Variables](#72-using-shell-variables-in-programs), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.1 Pattern Elements

| []() | []()
| - | -
| &bull; [Regexp Patterns](#711-regular-expressions-as-patterns) | Using regexps as patterns.
| &bull; [Expression Patterns](#712-expressions-as-patterns) | Any expression can be used as a pattern.
| &bull; [Ranges](#713-specifying-record-ranges-with-patterns) | Pairs of patterns specify record ranges.
| &bull; [BEGIN/END](#714-the-begin-and-end-special-patterns) | Specifying initialization and cleanup rules.
| &bull; [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns) | Two special patterns for advanced control.
| &bull; [Empty](#716-the-empty-pattern) | The empty pattern, which matches every record.

Patterns in `awk` control the execution of rules&mdash;a rule is
executed when its pattern matches the current input record.
The following is a summary of the types of `awk` patterns:

<code>/regular expression/</code>
A regular expression. It matches when the text of the
input record fits the regular expression.
(See [Regexp](#3-regular-expressions).)

<code>expression</code>
A single expression.  It matches when its value
is nonzero (if a number) or non-null (if a string).
(See [Expression Patterns](#712-expressions-as-patterns).)

<code>begpat, endpat</code>
A pair of patterns separated by a comma, specifying a *range* of records.
The range includes both the initial record that matches begpat and
the final record that matches endpat.
(See [Ranges](#713-specifying-record-ranges-with-patterns).)

<code>BEGIN</code><code>END</code>
Special patterns for you to supply startup or cleanup actions for your
`awk` program.
(See [BEGIN/END](#714-the-begin-and-end-special-patterns).)

<code>BEGINFILE</code><code>ENDFILE</code>
Special patterns for you to supply startup or cleanup actions to be
done on a per-file basis.
(See [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns).)

<code>empty</code>
The empty pattern matches every input record.
(See [Empty](#716-the-empty-pattern).)

---

Next: [Expression Patterns](#712-expressions-as-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.1 Regular Expressions as Patterns

Regular expressions are one of the first kinds of patterns presented
in this book.
This kind of pattern is simply a regexp constant in the pattern part of
a rule.  Its  meaning is &lsquo;$0 ~ /pattern/&rsquo;.
The pattern matches when the input record matches the regexp.
For example:

```awk
    /foo|bar|baz/  { buzzwords++ }
    END            { print buzzwords, "buzzwords seen" }
``` 

---

Next: [Ranges](#713-specifying-record-ranges-with-patterns), Previous: [Regexp Patterns](#711-regular-expressions-as-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.2 Expressions as Patterns

Any `awk` expression is valid as an `awk` pattern.
The pattern matches if the expression&rsquo;s value is nonzero (if a
number) or non-null (if a string).
The expression is reevaluated each time the rule is tested against a new
input record.  If the expression uses fields such as `$1`, the
value depends directly on the new input record&rsquo;s text; otherwise, it
depends on only what has happened so far in the execution of the
`awk` program.

Comparison expressions, using the comparison operators described in
[Typing and Comparison](#6322-comparison-operators),
are a very common kind of pattern.
Regexp matching and nonmatching are also very common expressions.
The left operand of the &lsquo;~&rsquo; and &lsquo;!~&rsquo; operators is a string.
The right operand is either a constant regular expression enclosed in
slashes (`/regexp/`), or any expression whose string value
is used as a dynamic regular expression
(see [Computed Regexps](#36-using-dynamic-regexps)).
The following example prints the second field of each input record
whose first field is precisely &lsquo;li&rsquo;:

```awk
    $ awk '$1 == "li" { print $2 }' mail-list
```

(There is no output, because there is no person with the exact name &lsquo;li&rsquo;.)
Contrast this with the following regular expression match, which
accepts any record with a first field that contains &lsquo;li&rsquo;:

```awk
    $ awk '$1 ~ /li/ { print $2 }' mail-list
    -| 555-5553
    -| 555-6699
```    

A regexp constant as a pattern is also a special case of an expression
pattern.  The expression `/li/` has the value one if &lsquo;li&rsquo;
appears in the current input record. Thus, as a pattern, `/li/`
matches any record containing &lsquo;li&rsquo;.

Boolean expressions are also commonly used as patterns.
Whether the pattern
matches an input record depends on whether its subexpressions match.
For example, the following command prints all the records in
mail-list that contain both &lsquo;edu&rsquo; and &lsquo;li&rsquo;:

```awk
    $ awk '/edu/ && /li/' mail-list
    -| Samuel       555-3430     samuel.lanceolis@shu.edu        A
```    

The following command prints all records in
mail-list that contain *either* &lsquo;edu&rsquo; or &lsquo;li&rsquo;
(or both, of course):

```awk
    $ awk '/edu/ || /li/' mail-list
    -| Amelia       555-5553     amelia.zodiacusque@gmail.com    F
    -| Broderick    555-0542     broderick.aliquotiens@yahoo.com R
    -| Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
    -| Julie        555-6699     julie.perscrutabor@skeeve.com   F
    -| Samuel       555-3430     samuel.lanceolis@shu.edu        A
    -| Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
```    

The following command prints all records in
mail-list that do *not* contain the string &lsquo;li&rsquo;:

```awk
    $ awk '! /li/' mail-list
    -| Anthony      555-3412     anthony.asserturo@hotmail.com   A
    -| Becky        555-7685     becky.algebrarum@gmail.com      A
    -| Bill         555-1675     bill.drowning@hotmail.com       A
    -| Camilla      555-2912     camilla.infusarum@skynet.be     R
    -| Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
    

    -| Martin       555-6480     martin.codicibus@hotmail.com    A
    -| Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
```    

The subexpressions of a Boolean operator in a pattern can be constant regular
expressions, comparisons, or any other `awk` expressions.  Range
patterns are not expressions, so they cannot appear inside Boolean
patterns.  Likewise, the special patterns `BEGIN`, `END`,
`BEGINFILE`, and `ENDFILE`,
which never match any input record, are not expressions and cannot
appear inside Boolean patterns.

The precedence of the different operators that can appear in
patterns is described in [Precedence](#65-operator-precedence-how-operators-nest).

---

Next: [BEGIN/END](#714-the-begin-and-end-special-patterns), Previous: [Expression Patterns](#712-expressions-as-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.3 Specifying Record Ranges with Patterns

A *range pattern* is made of two patterns separated by a comma, in
the form &lsquo;begpat, endpat&rsquo;.  It is used to match ranges of
consecutive input records.  The first pattern, begpat, controls
where the range begins, while endpat controls where
the pattern ends.  For example, the following:

```awk
    awk '$1 == "on", $1 == "off"' myfile
```    

prints every record in myfile between &lsquo;on&rsquo;/&lsquo;off&rsquo; pairs, inclusive.

A range pattern starts out by matching begpat against every
input record.  When a record matches begpat, the range pattern is
*turned on*, and the range pattern matches this record as well.  As long as
the range pattern stays turned on, it automatically matches every input
record read.  The range pattern also matches endpat against every
input record; when this succeeds, the range pattern is *turned off* again
for the following record.  Then the range pattern goes back to checking
begpat against each record.

The record that turns on the range pattern and the one that turns it
off both match the range pattern.  If you don&rsquo;t want to operate on
these records, you can write `if` statements in the rule&rsquo;s action
to distinguish them from the records you are interested in.

It is possible for a pattern to be turned on and off by the same
record. If the record satisfies both conditions, then the action is
executed for just that record.
For example, suppose there is text between two identical markers (e.g.,
the &lsquo;%&rsquo; symbol), each on its own line, that should be ignored.
A first attempt would be to
combine a range pattern that describes the delimited text with the
`next` statement
(not discussed yet, see [Next Statement](#748-the-next-statement)).
This causes `awk` to skip any further processing of the current
record and start over again with the next input record. Such a program
looks like this:

```awk
    /^%$/,/^%$/    { next }
                   { print }
```

This program fails because the range pattern is both turned on and turned off
by the first line, which just has a &lsquo;%&rsquo; on it.  To accomplish this task,
write the program in the following manner, using a flag:

```awk
    /^%$/     { skip = ! skip; next }
    skip == 1 { next } # skip lines with `skip' set
```    

In a range pattern, the comma (&lsquo;,&rsquo;) has the lowest precedence of
all the operators (i.e., it is evaluated last).  Thus, the following
program attempts to combine a range pattern with another, simpler test:

```awk
    echo Yes | awk '/1/,/2/ || /Yes/'
```    

The intent of this program is &lsquo;(/1/,/2/) || /Yes/&rsquo;.
However, `awk` interprets this as &lsquo;/1/, (/2/ || /Yes/)&rsquo;.
This cannot be changed or worked around; range patterns do not combine
with other patterns:

```awk
    $ echo Yes | gawk '(/1/,/2/) || /Yes/'
    error&rarr; gawk: cmd. line:1: (/1/,/2/) || /Yes/
    error&rarr; gawk: cmd. line:1:           ^ syntax error
```    

As a minor point of interest, although it is poor style,
POSIX allows you to put a newline after the comma in
a range pattern.  (d.c.)

---

Next: [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns), Previous: [Ranges](#713-specifying-record-ranges-with-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.4 The `BEGIN` and `END` Special Patterns

All the patterns described so far are for matching input records.
The `BEGIN` and `END` special patterns are different.
They supply startup and cleanup actions for `awk` programs.
`BEGIN` and `END` rules must have actions; there is no default
action for these rules because there is no current record when they run.
`BEGIN` and `END` rules are often referred to as
&ldquo;`BEGIN` and `END` blocks&rdquo; by longtime `awk`
programmers.

| []() | []()
| - | -
| &bull; [Using BEGIN/END](#7141-startup-and-cleanup-actions) | How and why to use BEGIN/END rules.
| &bull; [I/O And BEGIN/END](#7142-inputoutput-from-begin-and-end-rules) | I/O issues in BEGIN/END rules.

---

Next: [I/O And BEGIN/END](#7142-inputoutput-from-begin-and-end-rules), Up: [BEGIN/END](#714-the-begin-and-end-special-patterns)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.4.1 Startup and Cleanup Actions

A `BEGIN` rule is executed once only, before the first input record
is read. Likewise, an `END` rule is executed once only, after all the
input is read.  For example:

```awk
    $ awk '
    > BEGIN { print "Analysis of \"li\"" }
    > /li/  { ++n }
    > END   { print "\"li\" appears in", n, "records." }' mail-list
    -| Analysis of "li"
    -| "li" appears in 4 records.
```    

This program finds the number of records in the input file mail-list
that contain the string &lsquo;li&rsquo;.  The `BEGIN` rule prints a title
for the report.  There is no need to use the `BEGIN` rule to
initialize the counter `n` to zero, as `awk` does this
automatically (see [Variables](#613-variables)).
The second rule increments the variable `n` every time a
record containing the pattern &lsquo;li&rsquo; is read.  The `END` rule
prints the value of `n` at the end of the run.

The special patterns `BEGIN` and `END` cannot be used in ranges
or with Boolean operators (indeed, they cannot be used with any operators).
An `awk` program may have multiple `BEGIN` and/or `END`
rules.  They are executed in the order in which they appear: all the `BEGIN`
rules at startup and all the `END` rules at termination.
`BEGIN` and `END` rules may be intermixed with other rules.
This feature was added in the 1987 version of `awk` and is included
in the POSIX standard.
The original (1978) version of `awk`
required the `BEGIN` rule to be placed at the beginning of the
program, the `END` rule to be placed at the end, and only allowed one of
each.
This is no longer required, but it is a good idea to follow this template
in terms of program organization and readability.

Multiple `BEGIN` and `END` rules are useful for writing
library functions, because each library file can have its own `BEGIN` and/or
`END` rule to do its own initialization and/or cleanup.
The order in which library functions are named on the command line
controls the order in which their `BEGIN` and `END` rules are
executed.  Therefore, you have to be careful when writing such rules in
library files so that the order in which they are executed doesn&rsquo;t matter.
See [Options](#22-command-line-options) for more information on
using library functions.
See [Library Functions]((#10-a-library-of-awk-functions)),
for a number of useful library functions.

If an `awk` program has only `BEGIN` rules and no
other rules, then the program exits after the `BEGIN` rules are
run.[38](#FOOT38)  However, if an
`END` rule exists, then the input is read, even if there are
no other rules in the program.  This is necessary in case the `END`
rule checks the `FNR` and `NR` variables.

---

Previous: [Using BEGIN/END](#7141-startup-and-cleanup-actions), Up: [BEGIN/END](#714-the-begin-and-end-special-patterns)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.4.2 Input/Output from `BEGIN` and `END` Rules

There are several (sometimes subtle) points to be aware of when doing I/O
from a `BEGIN` or `END` rule.
The first has to do with the value of `$0` in a `BEGIN`
rule.  Because `BEGIN` rules are executed before any input is read,
there simply is no input record, and therefore no fields, when
executing `BEGIN` rules.  References to `$0` and the fields
yield a null string or zero, depending upon the context.  One way
to give `$0` a real value is to execute a `getline` command
without a variable (see [Getline](#410-explicit-input-with-getline)).
Another way is simply to assign a value to `$0`.

The second point is similar to the first, but from the other direction.
Traditionally, due largely to implementation issues, `$0` and
`NF` were *undefined* inside an `END` rule.
The POSIX standard specifies that `NF` is available in an `END`
rule. It contains the number of fields from the last input record.
Most probably due to an oversight, the standard does not say that `$0`
is also preserved, although logically one would think that it should be.
In fact, all of BWK `awk`, `mawk`, and `gawk`
preserve the value of `$0` for use in `END` rules.  Be aware,
however, that some other implementations and many older versions
of Unix `awk` do not.

The third point follows from the first two.  The meaning of &lsquo;print&rsquo;
inside a `BEGIN` or `END` rule is the same as always:
&lsquo;print $0&rsquo;.  If `$0` is the null string, then this prints an
empty record.  Many longtime `awk` programmers use an unadorned
&lsquo;print&rsquo; in `BEGIN` and `END` rules, to mean &lsquo;print ""&rsquo;,
relying on `$0` being null.  Although one might generally get away with
this in `BEGIN` rules, it is a very bad idea in `END` rules,
at least in `gawk`.  It is also poor style, because if an empty
line is needed in the output, the program should print one explicitly.

Finally, the `next` and `nextfile` statements are not allowed
in a `BEGIN` rule, because the implicit
read-a-record-and-match-against-the-rules loop has not started yet.  Similarly, those statements
are not valid in an `END` rule, because all the input has been read.
(See [Next Statement](#748-the-next-statement) and
see [Nextfile Statement](#749-the-nextfile-statement).)

---

Next: [Empty](#716-the-empty-pattern), Previous: [BEGIN/END](#714-the-begin-and-end-special-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.5 The `BEGINFILE` and `ENDFILE` Special Patterns

This section describes a `gawk`-specific feature.

Two special kinds of rule, `BEGINFILE` and `ENDFILE`, give
you &ldquo;hooks&rdquo; into `gawk`&rsquo;s command-line file processing loop.
As with the `BEGIN` and `END` rules
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)),
all `BEGINFILE` rules in a program are merged, in the order they are
read by `gawk`, and all `ENDFILE` rules are merged as well.

The body of the `BEGINFILE` rules is executed just before
`gawk` reads the first record from a file.  `FILENAME`
is set to the name of the current file, and `FNR` is set to zero.

The `BEGINFILE` rule provides you the opportunity to accomplish two tasks
that would otherwise be difficult or impossible to perform:

-  You can test if the file is readable.  Normally, it is a fatal error if a
file named on the command line cannot be opened for reading.  However,
you can bypass the fatal error and move on to the next file on the
command line.

You do this by checking if the `ERRNO` variable is not the empty
string; if so, then `gawk` was not able to open the file. In
this case, your program can execute the `nextfile` statement
(see [Nextfile Statement](#749-the-nextfile-statement)).  This causes `gawk` to skip
the file entirely.  Otherwise, `gawk` exits with the usual
fatal error.

-  If you have written extensions that modify the record handling (by
inserting an &ldquo;input parser&rdquo;; see [Input Parsers](#Input-Parsers)), you can invoke
them at this point, before `gawk` has started processing the file.
(This is a *very* advanced feature, currently used only by the
[`gawkextlib` project](https://sourceforge.net/projects/gawkextlib).)

The `ENDFILE` rule is called when `gawk` has finished processing
the last record in an input file.  For the last input file,
it will be called before any `END` rules.
The `ENDFILE` rule is executed even for empty input files.

Normally, when an error occurs when reading input in the normal
input-processing loop, the error is fatal.  However, if an `ENDFILE`
rule is present, the error becomes non-fatal, and instead `ERRNO`
is set.  This makes it possible to catch and process I/O errors at the
level of the `awk` program.

The `next` statement (see [Next Statement](#748-the-next-statement)) is not allowed inside
either a `BEGINFILE` or an `ENDFILE` rule.  The `nextfile`
statement is allowed only inside a
`BEGINFILE` rule, not inside an `ENDFILE` rule.

The `getline` statement (see [Getline](#410-explicit-input-with-getline)) is restricted inside
both `BEGINFILE` and `ENDFILE`: only redirected
forms of `getline` are allowed.

`BEGINFILE` and `ENDFILE` are `gawk` extensions.
In most other `awk` implementations, or if `gawk` is in
compatibility mode (see [Options](#22-command-line-options)), they are not special.

---

Previous: [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns), Up: [Pattern Overview](#71-pattern-elements)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.1.6 The Empty Pattern

An empty (i.e., nonexistent) pattern is considered to match *every*
input record.  For example, the program:

```awk
    awk '{ print $1 }' mail-list
```    

prints the first field of every record.

---

Next: [Action Overview](#73-actions), Previous: [Pattern Overview](#71-pattern-elements), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.2 Using Shell Variables in Programs

`awk` programs are often used as components in larger
programs written in shell.
For example, it is very common to use a shell variable to
hold a pattern that the `awk` program searches for.
There are two ways to get the value of the shell variable
into the body of the `awk` program.

A common method is to use shell quoting to substitute
the variable&rsquo;s value into the program inside the script.
For example, consider the following program:

```awk
    printf "Enter search pattern: "
    read pattern
    awk "/$pattern/ "'{ nmatches++ }
         END { print nmatches, "found" }' /path/to/data
```    

The `awk` program consists of two pieces of quoted text
that are concatenated together to form the program.
The first part is double-quoted, which allows substitution of
the `pattern` shell variable inside the quotes.
The second part is single-quoted.

Variable substitution via quoting works, but can potentially be
messy.  It requires a good understanding of the shell&rsquo;s quoting rules
(see [Quoting](#116-shell-quoting-issues)),
and it&rsquo;s often difficult to correctly
match up the quotes when reading the program.

A better method is to use `awk`&rsquo;s variable assignment feature
(see [Assignment Options](#6132-assigning-variables-on-the-command-line))
to assign the shell variable&rsquo;s value to an `awk` variable.
Then use dynamic regexps to match the pattern
(see [Computed Regexps](#36-using-dynamic-regexps)).
The following shows how to redo the
previous example using this technique:

```awk
    printf "Enter search pattern: "
    read pattern
    awk -v pat="$pattern" '$0 ~ pat { nmatches++ }
           END { print nmatches, "found" }' /path/to/data
```    

Now, the `awk` program is just one single-quoted string.
The assignment &lsquo;-v pat="$pattern"&rsquo; still requires double quotes,
in case there is whitespace in the value of `$pattern`.
The `awk` variable `pat` could be named `pattern`
too, but that would be more confusing.  Using a variable also
provides more flexibility, as the variable can be used anywhere inside
the program&mdash;for printing, as an array subscript, or for any other
use&mdash;without requiring the quoting tricks at every point in the program.

---

Next: [Statements](#74-control-statements-in-actions), Previous: [Using Shell Variables](#72-using-shell-variables-in-programs), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.3 Actions

An `awk` program or script consists of a series of
rules and function definitions interspersed.  (Functions are
described later.  See [User-defined](#92-user-defined-functions).)
A rule contains a pattern and an action, either of which (but not
both) may be omitted.  The purpose of the *action* is to tell
`awk` what to do once a match for the pattern is found.  Thus,
in outline, an `awk` program generally looks like this:

```awk
    [pattern]  { action }pattern  [{ action }]
    &hellip;
    function name(args) { &hellip; }
    &hellip;
```    

An action consists of one or more `awk`*statements*, enclosed
in braces (&lsquo;{&hellip;}&rsquo;).  Each statement specifies one
thing to do.  The statements are separated by newlines or semicolons.
The braces around an action must be used even if the action
contains only one statement, or if it contains no statements at
all.  However, if you omit the action entirely, omit the braces as
well.  An omitted action is equivalent to &lsquo;{ print $0 }&rsquo;:

```awk
    /foo/  { }     match foo, do nothing &mdash; empty action
    /foo/          match foo, print the record &mdash; omitted action
```

The following types of statements are supported in `awk`:

<code>Expressions</code>
Call functions or assign values to variables
(see [Expressions](#6-expressions)).  Executing
this kind of statement simply computes the value of the expression.
This is useful when the expression has side effects
(see [Assignment Ops](#623-assignment-expressions)).

<code>Control statements</code>
Specify the control flow of `awk`
programs.  The `awk` language gives you C-like constructs
(`if`, `for`, `while`, and `do`) as well as a few
special ones (see [Statements](#74-control-statements-in-actions)).

<code>Compound statements</code>
Enclose one or more statements in braces.  A compound statement
is used in order to put several statements together in the body of an
`if`, `while`, `do`, or `for` statement.

<code>Input statements</code>
Use the `getline` command
(see [Getline](#410-explicit-input-with-getline)).
Also supplied in `awk` are the `next`
statement (see [Next Statement](#748-the-next-statement))
and the `nextfile` statement
(see [Nextfile Statement](#749-the-nextfile-statement)).

<code>Output statements</code>
Such as `print` and `printf`.
See [Printing](#5-printing-output).

<code>Deletion statements</code>
For deleting array elements.
See [Delete](#84-the-delete-statement).

---

Next: [Built-in Variables](#75-predefined-variables), Previous: [Action Overview](#73-actions), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.4 Control Statements in Actions

*Control statements*, such as `if`, `while`, and so on,
control the flow of execution in `awk` programs.  Most of `awk`&rsquo;s
control statements are patterned after similar statements in C.

All the control statements start with special keywords, such as `if`
and `while`, to distinguish them from simple expressions.
Many control statements contain other statements.  For example, the
`if` statement contains another statement that may or may not be
executed.  The contained statement is called the *body*.
To include more than one statement in the body, group them into a
single *compound statement* with braces, separating them with
newlines or semicolons.

| []() | []()
| - | -
| &bull; [If Statement](#741-the-if-else-statement) | Conditionally execute some `awk` statements.
| &bull; [While Statement](#742-the-while-statement) | Loop until some condition is satisfied.
| &bull; [Do Statement](#743-the-do-while-statement) | Do specified action while looping until some condition is satisfied.
| &bull; [For Statement](#744-the-for-statement) | Another looping statement, that provides initialization and increment clauses.
| &bull; [Switch Statement](#745-the-switch-statement) | Switch/case evaluation for conditional execution of statements based on a value.
| &bull; [Break Statement](#746-the-break-statement) | Immediately exit the innermost enclosing loop.
| &bull; [Continue Statement](#747-the-continue-statement) | Skip to the end of the innermost enclosing loop.
| &bull; [Next Statement](#748-the-next-statement) | Stop processing the current input record.
| &bull; [Nextfile Statement](#749-the-nextfile-statement) | Stop processing the current file.
| &bull; [Exit Statement](#7410-the-exit-statement) | Stop execution of `awk`.

---

Next: [While Statement](#742-the-while-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.1 The `if`-`else` Statement

The `if`-`else` statement is `awk`&rsquo;s decision-making
statement.  It looks like this:

```awk
    if (condition) then-body [else else-body]
``` 

The condition is an expression that controls what the rest of the
statement does.  If the condition is true, then-body is
executed; otherwise, else-body is executed.
The `else` part of the statement is
optional.  The condition is considered false if its value is zero or
the null string; otherwise, the condition is true.
Refer to the following:

```awk
    if (x % 2 == 0)
        print "x is even"
    else
        print "x is odd"
```    

In this example, if the expression &lsquo;x % 2 == 0&rsquo; is true (i.e.,
if the value of `x` is evenly divisible by two), then the first
`print` statement is executed; otherwise, the second `print`
statement is executed.
If the `else` keyword appears on the same line as then-body and
then-body is not a compound statement (i.e., not surrounded by
braces), then a semicolon must separate then-body from
the `else`.
To illustrate this, the previous example can be rewritten as:

```awk
    if (x % 2 == 0) print "x is even"; else
            print "x is odd"
```    

If the &lsquo;;&rsquo; is left out, `awk` can&rsquo;t interpret the statement and
it produces a syntax error.  Don&rsquo;t actually write programs this way,
because a human reader might fail to see the `else` if it is not
the first thing on its line.

---

Next: [Do Statement](#743-the-do-while-statement), Previous: [If Statement](#741-the-if-else-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.2 The `while` Statement

In programming, a *loop* is a part of a program that can
be executed two or more times in succession.
The `while` statement is the simplest looping statement in
`awk`.  It repeatedly executes a statement as long as a condition is
true.  For example:

```awk
    while (condition)
      body
```

body is a statement called the *body* of the loop,
and condition is an expression that controls how long the loop
keeps running.
The first thing the `while` statement does is test the condition.
If the condition is true, it executes the statement body.
After body has been executed,
condition is tested again, and if it is still true, body
executes again.  This process repeats until the condition is no longer
true.  If the condition is initially false, the body of the loop
never executes and `awk` continues with the statement following
the loop.
This example prints the first three fields of each record, one per line:

```awk
    awk '
    {
        i = 1
        while (i <= 3) {
            print $i
            i++
        }
    }' inventory-shipped
```    

The body of this loop is a compound statement enclosed in braces,
containing two statements.
The loop works in the following manner: first, the value of `i` is set to one.
Then, the `while` statement tests whether `i` is less than or equal to
three.  This is true when `i` equals one, so the `i`th
field is printed.  Then the &lsquo;i++&rsquo; increments the value of `i`
and the loop repeats.  The loop terminates when `i` reaches four.

A newline is not required between the condition and the
body; however, using one makes the program clearer unless the body is a
compound statement or else is very simple.  The newline after the open brace
that begins the compound statement is not required either, but the
program is harder to read without it.

---

Next: [For Statement](#744-the-for-statement), Previous: [While Statement](#742-the-while-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.3 The `do`-`while` Statement

The `do` loop is a variation of the `while` looping statement.
The `do` loop executes the body once and then repeats the
body as long as the condition is true.  It looks like this:

```awk
    do
      body
    while (condition)
```    

Even if the condition is false at the start, the body
executes at least once (and only once, unless executing body
makes condition true).  Contrast this with the corresponding
`while` statement:

```awk
    while (condition)
        body
```

This statement does not execute the body even once if the
condition is false to begin with.  The following is an example of
a `do` statement:

```awk
    {
        i = 1
        do {
            print $0
            i++
        } while (i <= 10)
    }
```    

This program prints each input record 10 times.  However, it isn&rsquo;t a very
realistic example, because in this case an ordinary `while` would do
just as well.  This situation reflects actual experience; only
occasionally is there a real use for a `do` statement.

---

Next: [Switch Statement](#745-the-switch-statement), Previous: [Do Statement](#743-the-do-while-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.4 The `for` Statement

The `for` statement makes it more convenient to count iterations of a
loop.  The general form of the `for` statement looks like this:

    for (initialization; condition; increment)
      body

The initialization, condition, and increment parts are
arbitrary `awk` expressions, and body stands for any
`awk` statement.

The `for` statement starts by executing initialization.
Then, as long
as the condition is true, it repeatedly executes body and then
increment.  Typically, initialization sets a variable to
either zero or one, increment adds one to it, and condition
compares it against the desired number of iterations.
For example:

    awk '
    {
        for (i = 1; i <= 3; i++)
            print $i
    }' inventory-shipped
    

This prints the first three fields of each input record, with one
input field per output line.

It isn&rsquo;t possible to
set more than one variable in the
initialization part without using a multiple assignment statement
such as &lsquo;x = y = 0&rsquo;. This makes sense only if all the initial values
are equal.  (But it is possible to initialize additional variables by writing
their assignments as separate statements preceding the `for` loop.)

The same is true of the increment part. Incrementing additional
variables requires separate statements at the end of the loop.
The C compound expression, using C&rsquo;s comma operator, is useful in
this context, but it is not supported in `awk`.

Most often, increment is an increment expression, as in the previous
example.  But this is not required; it can be any expression
whatsoever.  For example, the following statement prints all the powers of two
between 1 and 100:

    for (i = 1; i <= 100; i *= 2)
        print i
    

If there is nothing to be done, any of the three expressions in the
parentheses following the `for` keyword may be omitted.  Thus,
&lsquo;for (; x > 0;)&rsquo; is equivalent to &lsquo;while (x > 0)&rsquo;.  If the
condition is omitted, it is treated as true, effectively
yielding an *infinite loop* (i.e., a loop that never terminates).

In most cases, a `for` loop is an abbreviation for a `while`
loop, as shown here:

    initialization
    while (condition) {
      bodyincrement
    }
    

The only exception is when the `continue` statement
(see [Continue Statement](#747-the-continue-statement)) is used
inside the loop. Changing a `for` statement to a `while`
statement in this way can change the effect of the `continue`
statement inside the loop.

The `awk` language has a `for` statement in addition to a
`while` statement because a `for` loop is often both less work to
type and more natural to think of.  Counting the number of iterations is
very common in loops.  It can be easier to think of this counting as part
of looping rather than as something to do inside the loop.

There is an alternative version of the `for` loop, for iterating over
all the indices of an array:

    for (i in array)
        do something with array[i]
    

See [Scanning an Array](#815-scanning-all-elements-of-an-array)
for more information on this version of the `for` loop.

---

Next: [Break Statement](#746-the-break-statement), Previous: [For Statement](#744-the-for-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.5 The `switch` Statement

This section describes a `gawk`-specific feature.
If `gawk` is in compatibility mode (see [Options](#22-command-line-options)),
it is not available.

The `switch` statement allows the evaluation of an expression and
the execution of statements based on a `case` match. Case statements
are checked for a match in the order they are defined.  If no suitable
`case` is found, the `default` section is executed, if supplied.

Each `case` contains a single constant, be it numeric, string, or
regexp.  The `switch` expression is evaluated, and then each
`case`&rsquo;s constant is compared against the result in turn. The type of constant
determines the comparison: numeric or string do the usual comparisons.
A regexp constant does a regular expression match against the string
value of the original expression.  The general form of the `switch`
statement looks like this:

    switch (expression) {
    case value or regular expression:
        case-body
    default:
        default-body
    }
    

Control flow in
the `switch` statement works as it does in C. Once a match to a given
case is made, the case statement bodies execute until a `break`,
`continue`, `next`, `nextfile`, or `exit` is encountered,
or the end of the `switch` statement itself. For example:

    while ((c = getopt(ARGC, ARGV, "aksx")) != -1) {
        switch (c) {
        case "a":
            # report size of all files
            all_files = TRUE;
            break
        case "k":
            BLOCK_SIZE = 1024       # 1K block size
            break
        case "s":
            # do sums only
            sum_only = TRUE
            break
        case "x":
            # don't cross filesystems
            fts_flags = or(fts_flags, FTS_XDEV)
            break
        case "?":
        default:
            usage()
            break
        }
    }
    

Note that if none of the statements specified here halt execution
of a matched `case` statement, execution falls through to the
next `case` until execution halts. In this example, the
`case` for `"?"` falls through to the `default`
case, which is to call a function named `usage()`.
(The `getopt()` function being called here is
described in [Getopt Function](#104-processing-command-line-options).)

---

Next: [Continue Statement](#747-the-continue-statement), Previous: [Switch Statement](#745-the-switch-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.6 The `break` Statement

The `break` statement jumps out of the innermost `for`,
`while`, or `do` loop that encloses it.  The following example
finds the smallest divisor of any integer, and also identifies prime
numbers:

    # find smallest divisor of num
    {
        num = $1
        for (divisor = 2; divisor * divisor <= num; divisor++) {
            if (num % divisor == 0)
                break
        }
    

        if (num % divisor == 0)
            printf "Smallest divisor of %d is %d\n", num, divisor
        else
            printf "%d is prime\n", num
    }
    

When the remainder is zero in the first `if` statement, `awk`
immediately *breaks out* of the containing `for` loop.  This means
that `awk` proceeds immediately to the statement following the loop
and continues processing.  (This is very different from the `exit`
statement, which stops the entire `awk` program.
See [Exit Statement](#7410-the-exit-statement).)

The following program illustrates how the condition of a `for`
or `while` statement could be replaced with a `break` inside
an `if`:

    # find smallest divisor of num
    {
        num = $1
        for (divisor = 2; ; divisor++) {
            if (num % divisor == 0) {
                printf "Smallest divisor of %d is %d\n", num, divisor
                break
            }
            if (divisor * divisor > num) {
                printf "%d is prime\n", num
                break
            }
        }
    }
    

The `break` statement is also used to break out of the
`switch` statement.
This is discussed in [Switch Statement](#745-the-switch-statement).

The `break` statement has no meaning when
used outside the body of a loop or `switch`.
However, although it was never documented,
historical implementations of `awk` treated the `break`
statement outside of a loop as if it were a `next` statement
(see [Next Statement](#748-the-next-statement)).
(d.c.)
Recent versions of BWK `awk` no longer allow this usage,
nor does `gawk`.

---

Next: [Next Statement](#748-the-next-statement), Previous: [Break Statement](#746-the-break-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.7 The `continue` Statement

Similar to `break`, the `continue` statement is used only inside
`for`, `while`, and `do` loops.  It skips
over the rest of the loop body, causing the next cycle around the loop
to begin immediately.  Contrast this with `break`, which jumps out
of the loop altogether.

The `continue` statement in a `for` loop directs `awk` to
skip the rest of the body of the loop and resume execution with the
increment-expression of the `for` statement.  The following program
illustrates this fact:

    BEGIN {
         for (x = 0; x <= 20; x++) {
             if (x == 5)
                 continue
             printf "%d ", x
         }
         print ""
    }
    

This program prints all the numbers from 0 to 20&mdash;except for 5, for
which the `printf` is skipped.  Because the increment &lsquo;x++&rsquo;
is not skipped, `x` does not remain stuck at 5.  Contrast the
`for` loop from the previous example with the following `while` loop:

    BEGIN {
         x = 0
         while (x <= 20) {
             if (x == 5)
                 continue
             printf "%d ", x
             x++
         }
         print ""
    }
    

This program loops forever once `x` reaches 5, because
the increment (&lsquo;x++&rsquo;) is never reached.

The `continue` statement has no special meaning with respect to the
`switch` statement, nor does it have any meaning when used outside the
body of a loop.  Historical versions of `awk` treated a `continue`
statement outside a loop the same way they treated a `break`
statement outside a loop: as if it were a `next`
statement
(see [Next Statement](#748-the-next-statement)).
(d.c.)
Recent versions of BWK `awk` no longer work this way, nor
does `gawk`.

---

Next: [Nextfile Statement](#749-the-nextfile-statement), Previous: [Continue Statement](#747-the-continue-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.8 The `next` Statement

The `next` statement forces `awk` to immediately stop processing
the current record and go on to the next record.  This means that no
further rules are executed for the current record, and the rest of the
current rule&rsquo;s action isn&rsquo;t executed.

Contrast this with the effect of the `getline` function
(see [Getline](#410-explicit-input-with-getline)).  That also causes
`awk` to read the next record immediately, but it does not alter the
flow of control in any way (i.e., the rest of the current action executes
with a new input record).

At the highest level, `awk` program execution is a loop that reads
an input record and then tests each rule&rsquo;s pattern against it.  If you
think of this loop as a `for` statement whose body contains the
rules, then the `next` statement is analogous to a `continue`
statement. It skips to the end of the body of this implicit loop and
executes the increment (which reads another record).

For example, suppose an `awk` program works only on records
with four fields, and it shouldn&rsquo;t fail when given bad input.  To avoid
complicating the rest of the program, write a &ldquo;weed out&rdquo; rule near
the beginning, in the following manner:

    NF != 4 {
        printf("%s:%d: skipped: NF != 4\n", FILENAME, FNR) > "/dev/stderr"
        next
    }
    

Because of the `next` statement,
the program&rsquo;s subsequent rules won&rsquo;t see the bad record.  The error
message is redirected to the standard error output stream, as error
messages should be.
For more detail, see
[Special Files](#58-special-file-names-in-gawk).

If the `next` statement causes the end of the input to be reached,
then the code in any `END` rules is executed.
See [BEGIN/END](#714-the-begin-and-end-special-patterns).

The `next` statement is not allowed inside `BEGINFILE` and
`ENDFILE` rules. See [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns).

According to the POSIX standard, the behavior is undefined if the
`next` statement is used in a `BEGIN` or `END` rule.
`gawk` treats it as a syntax error.  Although POSIX does not disallow it,
most other `awk` implementations don&rsquo;t allow the `next`
statement inside function bodies (see [User-defined](#92-user-defined-functions)).  Just as with any
other `next` statement, a `next` statement inside a function
body reads the next record and starts processing it with the first rule
in the program.

---

Next: [Exit Statement](#7410-the-exit-statement), Previous: [Next Statement](#748-the-next-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.9 The `nextfile` Statement

The `nextfile` statement
is similar to the `next` statement.
However, instead of abandoning processing of the current record, the
`nextfile` statement instructs `awk` to stop processing the
current data file.

Upon execution of the `nextfile` statement,
`FILENAME` is
updated to the name of the next data file listed on the command line,
`FNR` is reset to one,
and processing
starts over with the first rule in the program.
If the `nextfile` statement causes the end of the input to be reached,
then the code in any `END` rules is executed. An exception to this is
when `nextfile` is invoked during execution of any statement in an
`END` rule; in this case, it causes the program to stop immediately.
See [BEGIN/END](#714-the-begin-and-end-special-patterns).

The `nextfile` statement is useful when there are many data files
to process but it isn&rsquo;t necessary to process every record in every file.
Without `nextfile`,
in order to move on to the next data file, a program
would have to continue scanning the unwanted records.  The `nextfile`
statement accomplishes this much more efficiently.

In `gawk`, execution of `nextfile` causes additional things
to happen: any `ENDFILE` rules are executed if `gawk` is
not currently in an `END` or `BEGINFILE` rule, `ARGIND` is
incremented, and any `BEGINFILE` rules are executed.  (`ARGIND`
hasn&rsquo;t been introduced yet. See [Built-in Variables](#75-predefined-variables).)

With `gawk`, `nextfile` is useful inside a `BEGINFILE`
rule to skip over a file that would otherwise cause `gawk`
to exit with a fatal error. In this case, `ENDFILE` rules are not
executed. See [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns).

Although it might seem that &lsquo;close(FILENAME)&rsquo; would accomplish
the same as `nextfile`, this isn&rsquo;t true.  `close()` is
reserved for closing files, pipes, and coprocesses that are
opened with redirections.  It is not related to the main processing that
`awk` does with the files listed in `ARGV`.

> NOTE: For many years, `nextfile` was a
> common extension. In September 2012, it was accepted for
> inclusion into the POSIX standard.
> See [the Austin Group website](http://austingroupbugs.net/view.php?id=607).

The current version of BWK `awk` and `mawk`
also support `nextfile`.  However, they don&rsquo;t allow the
`nextfile` statement inside function bodies (see [User-defined](#92-user-defined-functions)).
`gawk` does; a `nextfile` inside a function body reads the
first record from the next file and starts processing it with the first
rule in the program, just as any other `nextfile` statement.

---

Previous: [Nextfile Statement](#749-the-nextfile-statement), Up: [Statements](#74-control-statements-in-actions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.4.10 The `exit` Statement

The `exit` statement causes `awk` to immediately stop
executing the current rule and to stop processing input; any remaining input
is ignored.  The `exit` statement is written as follows:

    exit [return code]
    

When an `exit` statement is executed from a `BEGIN` rule, the
program stops processing everything immediately.  No input records are
read.  However, if an `END` rule is present,
as part of executing the `exit` statement,
the `END` rule is executed
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)).
If `exit` is used in the body of an `END` rule, it causes
the program to stop immediately.

An `exit` statement that is not part of a `BEGIN` or `END`
rule stops the execution of any further automatic rules for the current
record, skips reading any remaining input records, and executes the
`END` rule if there is one.  `gawk` also skips
any `ENDFILE` rules; they do not execute.

In such a case,
if you don&rsquo;t want the `END` rule to do its job, set a variable
to a nonzero value before the `exit` statement and check that variable in
the `END` rule.
See [Assert Function](#1022-assertions)
for an example that does this.

If an argument is supplied to `exit`, its value is used as the exit
status code for the `awk` process.  If no argument is supplied,
`exit` causes `awk` to return a &ldquo;success&rdquo; status.
In the case where an argument
is supplied to a first `exit` statement, and then `exit` is
called a second time from an `END` rule with no argument,
`awk` uses the previously supplied exit value.  (d.c.)
See [Exit Status](#26-gawkrsquos-exit-status) for more information.

For example, suppose an error condition occurs that is difficult or
impossible to handle.  Conventionally, programs report this by
exiting with a nonzero status.  An `awk` program can do this
using an `exit` statement with a nonzero argument, as shown
in the following example:

    BEGIN {
        if (("date" | getline date_now) <= 0) {
            print "Can't get system date" > "/dev/stderr"
            exit 1
        }
    

        print "current date is", date_now
        close("date")
    }
    

> NOTE: For full portability, exit values should be between zero and 126, inclusive.
> Negative values, and values of 127 or greater, may not produce consistent
> results across different operating systems.

---

Next: [Pattern Action Summary](#76-summary), Previous: [Statements](#74-control-statements-in-actions), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.5 Predefined Variables

Most `awk` variables are available to use for your own
purposes; they never change unless your program assigns values to
them, and they never affect anything unless your program examines them.
However, a few variables in `awk` have special built-in meanings.
`awk` examines some of these automatically, so that they enable you
to tell `awk` how to do certain things.  Others are set
automatically by `awk`, so that they carry information from the
internal workings of `awk` to your program.

This section documents all of `gawk`&rsquo;s predefined variables,
most of which are also documented in the chapters describing
their areas of activity.

&bull; [User-modified](#751-built-in-variables-that-control-awk):  Built-in variables that you change to control
                                `awk`.
&bull; [Auto-set](#752-built-in-variables-that-convey-information):  Built-in variables where `awk` gives
                                you information.
&bull; [ARGC and ARGV](#753-using-argc-and-argv):  Ways to use `ARGC` and `ARGV`.

---

Next: [Auto-set](#752-built-in-variables-that-convey-information), Up: [Built-in Variables](#75-predefined-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.5.1 Built-in Variables That Control `awk`

The following is an alphabetical list of variables that you can change to
control how `awk` does certain things.

The variables that are specific to `gawk` are marked with a pound
sign (&lsquo;#&rsquo;).  These variables are `gawk` extensions.  In other
`awk` implementations or if `gawk` is in compatibility
mode (see [Options](#22-command-line-options)), they are not special.  (Any exceptions are noted
in the description of each variable.)

`BINMODE #`
On non-POSIX systems, this variable specifies use of binary mode
for all I/O.  Numeric values of one, two, or three specify that input
files, output files, or all files, respectively, should use binary I/O.
A numeric value less than zero is treated as zero, and a numeric value
greater than three is treated as three.  Alternatively, string values
of `"r"` or `"w"` specify that input files and output files,
respectively, should use binary I/O.  A string value of `"rw"` or
`"wr"` indicates that all files should use binary I/O.  Any other
string value is treated the same as `"rw"`, but causes `gawk`
to generate a warning message.  `BINMODE` is described in more
detail in [PC Using](#PC-Using).  `mawk` (see [Other Versions](#Other-Versions))
also supports this variable, but only using numeric values.

``CONVFMT``
A string that controls the conversion of numbers to
strings (see [Conversion](#614-conversion-of-strings-and-numbers)).
It works by being passed, in effect, as the first argument to the
`sprintf()` function
(see [String Functions](#913-string-manipulation-functions)).
Its default value is `"%.6g"`.
`CONVFMT` was introduced by the POSIX standard.

`FIELDWIDTHS #`
A space-separated list of columns that tells `gawk`
how to split input with fixed columnar boundaries.
Starting in version 4.2, each field width may optionally be
preceded by a colon-separated value specifying the number of characters to skip
before the field starts.
Assigning a value to `FIELDWIDTHS`
overrides the use of `FS` and `FPAT` for field splitting.
See [Constant Size](#46-reading-fixed-width-data) for more information.

`FPAT #`
A regular expression (as a string) that tells `gawk`
to create the fields based on text that matches the regular expression.
Assigning a value to `FPAT`
overrides the use of `FS` and `FIELDWIDTHS` for field splitting.
See [Splitting By Content](#47-defining-fields-by-content) for more information.

`FS`
The input field separator (see [Field Separators](#45-specifying-how-fields-are-separated)).
The value is a single-character string or a multicharacter regular
expression that matches the separations between fields in an input
record.  If the value is the null string (`""`), then each
character in the record becomes a separate field.
(This behavior is a `gawk` extension. POSIX `awk` does not
specify the behavior when `FS` is the null string.
Nonetheless, some other versions of `awk` also treat
`""` specially.)

The default value is `" "`, a string consisting of a single
space.  As a special exception, this value means that any sequence of
spaces, TABs, and/or newlines is a single separator.  It also causes
spaces, TABs, and newlines at the beginning and end of a record to
be ignored.

You can set the value of `FS` on the command line using the
-F option:

    awk -F, 'program' input-files

If `gawk` is using `FIELDWIDTHS` or `FPAT`
for field splitting,
assigning a value to `FS` causes `gawk` to return to
the normal, `FS`-based field splitting. An easy way to do this
is to simply say &lsquo;FS = FS&rsquo;, perhaps with an explanatory comment.

`IGNORECASE #`
If `IGNORECASE` is nonzero or non-null, then all string comparisons
and all regular expression matching are case-independent.
This applies to
regexp matching with &lsquo;~&rsquo; and &lsquo;!~&rsquo;,
the `gensub()`, `gsub()`, `index()`, `match()`,
`patsplit()`, `split()`, and `sub()` functions,
record termination with `RS`, and field splitting with
`FS` and `FPAT`.
However, the value of `IGNORECASE` does *not* affect array subscripting
and it does not affect field splitting when using a single-character
field separator.
See [Case-sensitivity](#38-case-sensitivity-in-matching).

`LINT #`
When this variable is true (nonzero or non-null), `gawk`
behaves as if the --lint command-line option is in effect
(see [Options](#22-command-line-options)).
With a value of `"fatal"`, lint warnings become fatal errors.
With a value of `"invalid"`, only warnings about things that are
actually invalid are issued. (This is not fully implemented yet.)
Any other true value prints nonfatal warnings.
Assigning a false value to `LINT` turns off the lint warnings.

This variable is a `gawk` extension.  It is not special
in other `awk` implementations.  Unlike with the other special variables,
changing `LINT` does affect the production of lint warnings,
even if `gawk` is in compatibility mode.  Much as
the --lint and --traditional options independently
control different aspects of `gawk`&rsquo;s behavior, the control
of lint warnings during program execution is independent of the flavor
of `awk` being executed.

`OFMT`
A string that controls conversion of numbers to
strings (see [Conversion](#614-conversion-of-strings-and-numbers)) for
printing with the `print` statement.  It works by being passed
as the first argument to the `sprintf()` function
(see [String Functions](#913-string-manipulation-functions)).
Its default value is `"%.6g"`.  Earlier versions of `awk`
used `OFMT` to specify the format for converting numbers to
strings in general expressions; this is now done by `CONVFMT`.

`OFS`
The output field separator (see [Output Separators](#53-output-separators)).  It is
output between the fields printed by a `print` statement.  Its
default value is `" "`, a string consisting of a single space.

`ORS`
The output record separator.  It is output at the end of every
`print` statement.  Its default value is `"\n"`, the newline
character.  (See [Output Separators](#53-output-separators).)

`PREC #`
The working precision of arbitrary-precision floating-point numbers,
53 bits by default (see [Setting precision](#Setting-precision)).

`ROUNDMODE #`
The rounding mode to use for arbitrary-precision arithmetic on
numbers, by default `"N"` (`roundTiesToEven` in
the IEEE 754 standard; see [Setting the rounding mode](#Setting-the-rounding-mode)).

``RS``
The input record separator.  Its default value is a string
containing a single newline character, which means that an input record
consists of a single line of text.
It can also be the null string, in which case records are separated by
runs of blank lines.
If it is a regexp, records are separated by
matches of the regexp in the input text.
(See [Records](#41-how-input-is-split-into-records).)

The ability for `RS` to be a regular expression
is a `gawk` extension.
In most other `awk` implementations,
or if `gawk` is in compatibility mode
(see [Options](#22-command-line-options)),
just the first character of `RS`&rsquo;s value is used.

``SUBSEP``
The subscript separator.  It has the default value of
`"\034"` and is used to separate the parts of the indices of a
multidimensional array.  Thus, the expression &lsquo;foo["A", "B"]&rsquo;
really accesses `foo["A\034B"]`
(see [Multidimensional](#85-multidimensional-arrays)).

`TEXTDOMAIN #`
Used for internationalization of programs at the
`awk` level.  It sets the default text domain for specially
marked string constants in the source text, as well as for the
`dcgettext()`, `dcngettext()`, and `bindtextdomain()` functions
(see [Internationalization](#Internationalization)).
The default value of `TEXTDOMAIN` is `"messages"`.

---

Next: [ARGC and ARGV](#753-using-argc-and-argv), Previous: [User-modified](#751-built-in-variables-that-control-awk), Up: [Built-in Variables](#75-predefined-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.5.2 Built-in Variables That Convey Information

The following is an alphabetical list of variables that `awk`
sets automatically on certain occasions in order to provide
information to your program.

The variables that are specific to `gawk` are marked with a pound
sign (&lsquo;#&rsquo;).  These variables are `gawk` extensions.  In other
`awk` implementations or if `gawk` is in compatibility
mode (see [Options](#22-command-line-options)), they are not special:

`ARGC`, `ARGV`
The command-line arguments available to `awk` programs are stored in
an array called `ARGV`.  `ARGC` is the number of command-line
arguments present.  See [Other Arguments](#23-other-command-line-arguments).
Unlike most `awk` arrays,
`ARGV` is indexed from 0 to `ARGC` - 1.
In the following example:

    $ awk 'BEGIN {
    >         for (i = 0; i < ARGC; i++)
    >             print ARGV[i]
    >      }' inventory-shipped mail-list
    -| awk
    -| inventory-shipped
    -| mail-list
    

`ARGV[0]` contains &lsquo;awk&rsquo;, `ARGV[1]`
contains &lsquo;inventory-shipped&rsquo;, and `ARGV[2]` contains
&lsquo;mail-list&rsquo;.  The value of `ARGC` is three, one more than the
index of the last element in `ARGV`, because the elements are numbered
from zero.

The names `ARGC` and `ARGV`, as well as the convention of indexing
the array from 0 to `ARGC` - 1, are derived from the C language&rsquo;s
method of accessing command-line arguments.

The value of `ARGV[0]` can vary from system to system.
Also, you should note that the program text is *not* included in
`ARGV`, nor are any of `awk`&rsquo;s command-line options.
See [ARGC and ARGV](#753-using-argc-and-argv) for information
about how `awk` uses these variables.
(d.c.)

`ARGIND #`
The index in `ARGV` of the current file being processed.
Every time `gawk` opens a new data file for processing, it sets
`ARGIND` to the index in `ARGV` of the file name.
When `gawk` is processing the input files,
&lsquo;FILENAME == ARGV[ARGIND]&rsquo; is always true.

This variable is useful in file processing; it allows you to tell how far
along you are in the list of data files as well as to distinguish between
successive instances of the same file name on the command line.

While you can change the value of `ARGIND` within your `awk`
program, `gawk` automatically sets it to a new value when it
opens the next file.

`ENVIRON`
An associative array containing the values of the environment.  The array
indices are the environment variable names; the elements are the values of
the particular environment variables.  For example,
`ENVIRON["HOME"]` might be `/home/arnold`.

For POSIX `awk`, changing this array does not affect the
environment passed on to any programs that `awk` may spawn via
redirection or the `system()` function.

However, beginning with version 4.2, if not in POSIX
compatibility mode, `gawk` does update its own environment when
`ENVIRON` is changed, thus changing the environment seen by programs
that it creates.  You should therefore be especially careful if you
modify `ENVIRON["PATH"]`, which is the search path for finding
executable programs.

This can also affect the running `gawk` program, since some of the
built-in functions may pay attention to certain environment variables.
The most notable instance of this is `mktime()` (see [Time Functions](#915-time-functions)), which pays attention the value of the `TZ` environment
variable on many systems.

Some operating systems may not have environment variables.
On such systems, the `ENVIRON` array is empty (except for
`ENVIRON["AWKPATH"]` and
`ENVIRON["AWKLIBPATH"]`;
see [AWKPATH Variable](#251-the-awkpath-environment-variable) and
see [AWKLIBPATH Variable](#252-the-awklibpath-environment-variable)).

`ERRNO #`
If a system error occurs during a redirection for `getline`, during
a read for `getline`, or during a `close()` operation, then
`ERRNO` contains a string describing the error.

In addition, `gawk` clears `ERRNO` before opening each
command-line input file. This enables checking if the file is readable
inside a `BEGINFILE` pattern (see [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns)).

Otherwise, `ERRNO` works similarly to the C variable `errno`.
Except for the case just mentioned, `gawk`*never* clears
it (sets it to zero or `""`).  Thus, you should only expect its
value to be meaningful when an I/O operation returns a failure value,
such as `getline` returning -1.  You are, of course, free
to clear it yourself before doing an I/O operation.

If the value of `ERRNO` corresponds to a system error in the C
`errno` variable, then `PROCINFO["errno"]` will be set to the value
of `errno`.  For non-system errors, `PROCINFO["errno"]` will
be zero.

`FILENAME`
The name of the current input file.  When no data files are listed
on the command line, `awk` reads from the standard input and
`FILENAME` is set to `"-"`.  `FILENAME` changes each
time a new file is read (see [Reading Files](#4-reading-input-files)).  Inside a `BEGIN`
rule, the value of `FILENAME` is `""`, because there are no input
files being processed yet.[39](#FOOT39) (d.c.) Note, though,
that using `getline` (see [Getline](#410-explicit-input-with-getline)) inside a `BEGIN` rule
can give `FILENAME` a value.

`FNR`
The current record number in the current file.  `awk` increments
`FNR` each time it reads a new record (see [Records](#41-how-input-is-split-into-records)).
`awk` resets `FNR` to zero each time it starts a new
input file.

`NF`
The number of fields in the current input record.
`NF` is set each time a new record is read, when a new field is
created, or when `$0` changes (see [Fields](#42-examining-fields)).

Unlike most of the variables described in this subsection,
assigning a value to `NF` has the potential to affect
`awk`&rsquo;s internal workings.  In particular, assignments
to `NF` can be used to create fields in or remove fields from the
current record. See [Changing Fields](#44-changing-the-contents-of-a-field).

`FUNCTAB #`
An array whose indices and corresponding values are the names of all
the built-in, user-defined, and extension functions in the program.

> NOTE: Attempting to use the `delete` statement with the `FUNCTAB`
> array causes a fatal error.  Any attempt to assign to an element of
> `FUNCTAB` also causes a fatal error.

`NR`
The number of input records `awk` has processed since
the beginning of the program&rsquo;s execution
(see [Records](#41-how-input-is-split-into-records)).
`awk` increments `NR` each time it reads a new record.

`PROCINFO #`
The elements of this array provide access to information about the
running `awk` program.
The following elements (listed alphabetically)
are guaranteed to be available:

`PROCINFO["argv"]`
The `PROCINFO["argv"]` array contains all of the command-line arguments
(after glob expansion and redirection processing on platforms where that must
be done manually by the program) with subscripts ranging from 0 through
`argc` - 1.  For example, `PROCINFO["argv"][0]` will contain
the name by which `gawk` was invoked.  Here is an example of how this
feature may be used:

    gawk '
    BEGIN {
            for (i = 0; i < length(PROCINFO["argv"]); i++)
                    print i, PROCINFO["argv"][i]
    }'
    

Please note that this differs from the standard `ARGV` array which does
not include command-line arguments that have already been processed by
`gawk` (see [ARGC and ARGV](#753-using-argc-and-argv)).

`PROCINFO["egid"]`
The value of the `getegid()` system call.

`PROCINFO["errno"]`
The value of the C `errno` variable when `ERRNO` is set to
the associated error message.

`PROCINFO["euid"]`
The value of the `geteuid()` system call.

`PROCINFO["FS"]`
This is
`"FS"` if field splitting with `FS` is in effect,
`"FIELDWIDTHS"` if field splitting with `FIELDWIDTHS` is in effect,
`"FPAT"` if field matching with `FPAT` is in effect,
or `"API"` if field splitting is controlled by an API input parser.

`PROCINFO["gid"]`
The value of the `getgid()` system call.

`PROCINFO["identifiers"]`
A subarray, indexed by the names of all identifiers used in the text of
the `awk` program.  An *identifier* is simply the name of a variable
(be it scalar or array), built-in function, user-defined function, or
extension function.  For each identifier, the value of the element is
one of the following:

`"array"`
The identifier is an array.

`"builtin"`
The identifier is a built-in function.

`"extension"`
The identifier is an extension function loaded via
`@load` or -l.

`"scalar"`
The identifier is a scalar.

`"untyped"`
The identifier is untyped (could be used as a scalar or an array;
`gawk` doesn&rsquo;t know yet).

`"user"`
The identifier is a user-defined function.

The values indicate what `gawk` knows about the identifiers
after it has finished parsing the program; they are *not* updated
while the program runs.

`PROCINFO["pgrpid"]`
The process group ID of the current process.

`PROCINFO["pid"]`
The process ID of the current process.

`PROCINFO["ppid"]`
The parent process ID of the current process.

`PROCINFO["strftime"]`
The default time format string for `strftime()`.
Assigning a new value to this element changes the default.
See [Time Functions](#915-time-functions).

`PROCINFO["uid"]`
The value of the `getuid()` system call.

`PROCINFO["version"]`
The version of `gawk`.

The following additional elements in the array
are available to provide information about the MPFR and GMP libraries
if your version of `gawk` supports arbitrary-precision arithmetic
(see [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)):

`PROCINFO["gmp_version"]`
The version of the GNU MP library.

`PROCINFO["mpfr_version"]`
The version of the GNU MPFR library.

`PROCINFO["prec_max"]`
The maximum precision supported by MPFR.

`PROCINFO["prec_min"]`
The minimum precision required by MPFR.

The following additional elements in the array are available to provide
information about the version of the extension API, if your version
of `gawk` supports dynamic loading of extension functions
(see [Dynamic Extensions](#Dynamic-Extensions)):

`PROCINFO["api_major"]`
The major version of the extension API.

`PROCINFO["api_minor"]`
The minor version of the extension API.

On some systems, there may be elements in the array, `&ququot;group1"`
through `"groupN"` for some N. N is the number of
supplementary groups that the process has.  Use the `in` operator
to test for these elements
(see [Reference to Elements](#812-referring-to-an-array-element)).

The following elements allow you to change `gawk`&rsquo;s behavior:

`PROCINFO["NONFATAL"]`
If this element exists, then I/O errors for all redirections become nonfatal.
See [Nonfatal](#510-enabling-nonfatal-output).

`PROCINFO["name", "NONFATAL"]`
Make I/O errors for name be nonfatal.
See [Nonfatal](#510-enabling-nonfatal-output).

`PROCINFO["command", "pty"]`
For two-way communication to command, use a pseudo-tty instead
of setting up a two-way pipe.
See [Two-way I/O](#Two_002dway-I_002fO) for more information.

`PROCINFO["input_name", "READ_TIMEOUT"]`
Set a timeout for reading from input redirection input_name.
See [Read Timeout](#411-reading-input-with-a-timeout) for more information.

`PROCINFO["input_name", "RETRY"]`
If an I/O error that may be retried occurs when reading data from
input_name, and this array entry exists, then `getline` returns
-2 instead of following the default behavior of returning -1
and configuring input_name to return no further data.  An I/O error
that may be retried is one where `errno` has the value `EAGAIN`,
`EWOULDBLOCK`, `EINTR`, or `ETIMEDOUT`.  This may be useful
in conjunction with `PROCINFO["input_name", "READ_TIMEOUT"]`
or situations where a file descriptor has been configured to behave in
a non-blocking fashion.
See [Retrying Input](#412-retrying-reads-after-certain-input-errors) for more information.

`PROCINFO["sorted_in"]`
If this element exists in `PROCINFO`, its value controls the
order in which array indices will be processed by
&lsquo;for (indx in array)&rsquo; loops.
This is an advanced feature, so we defer the
full description until later; see
[Scanning an Array](#815-scanning-all-elements-of-an-array).

`RLENGTH`
The length of the substring matched by the
`match()` function
(see [String Functions](#913-string-manipulation-functions)).
`RLENGTH` is set by invoking the `match()` function.  Its value
is the length of the matched string, or -1 if no match is found.

`RSTART`
The start index in characters of the substring that is matched by the
`match()` function
(see [String Functions](#913-string-manipulation-functions)).
`RSTART` is set by invoking the `match()` function.  Its value
is the position of the string where the matched substring starts, or zero
if no match was found.

`RT #`
The input text that matched the text denoted by `RS`,
the record separator.  It is set every time a record is read.

`SYMTAB #`
An array whose indices are the names of all defined global variables and
arrays in the program.  `SYMTAB` makes `gawk`&rsquo;s symbol table
visible to the `awk` programmer.  It is built as `gawk`
parses the program and is complete before the program starts to run.

The array may be used for indirect access to read or write the value of
a variable:

    foo = 5
    SYMTAB["foo"] = 4
    print foo    # prints 4
    

The `isarray()` function (see [Type Functions](#917-getting-type-information)) may be used to test
if an element in `SYMTAB` is an array.
Also, you may not use the `delete` statement with the
`SYMTAB` array.

You may use an index for `SYMTAB` that is not a predefined identifier:

    SYMTAB["xxx"] = 5
    print SYMTAB["xxx"]
    

This works as expected: in this case `SYMTAB` acts just like
a regular array.  The only difference is that you can&rsquo;t then delete
`SYMTAB["xxx"]`.

The `SYMTAB` array is more interesting than it looks. Andrew Schorr
points out that it effectively gives `awk` data pointers. Consider his
example:

    # Indirect multiply of any variable by amount, return result
    
    function multiply(variable, amount)
    {
        return SYMTAB[variable] *= amount
    }
    

You would use it like this:

    BEGIN {
        answer = 10.5
        multiply("answer", 4)
        print "The answer is", answer
    }
    

When run, this produces:

    $ gawk -f answer.awk
    -| The answer is 42
    

> NOTE: In order to avoid severe time-travel paradoxes,[40](#FOOT40) neither `FUNCTAB` nor `SYMTAB`
> is available as an element within the `SYMTAB` array.

Changing `NR` and `FNR`

`awk` increments `NR` and `FNR`
each time it reads a record, instead of setting them to the absolute
value of the number of records read.  This means that a program can
change these variables and their new values are incremented for
each record.
(d.c.)
The following example shows this:

    $ echo '1
    > 2
    > 3
    > 4' | awk 'NR == 2 { NR = 17 }
    > { print NR }'
    -| 1
    -| 17
    -| 18
    -| 19
    

Before `FNR` was added to the `awk` language
(see [V7/SVR3.1](#V7_002fSVR3_002e1)),
many `awk` programs used this feature to track the number of
records in a file by resetting `NR` to zero when `FILENAME`
changed.

---

Previous: [Auto-set](#752-built-in-variables-that-convey-information), Up: [Built-in Variables](#75-predefined-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 7.5.3 Using `ARGC` and `ARGV`

[Auto-set](#752-built-in-variables-that-convey-information)
presented the following program describing the information contained in `ARGC`
and `ARGV`:

    $ awk 'BEGIN {
    >        for (i = 0; i < ARGC; i++)
    >            print ARGV[i]
    >      }' inventory-shipped mail-list
    -| awk
    -| inventory-shipped
    -| mail-list
    

In this example, `ARGV[0]` contains &lsquo;awk&rsquo;, `ARGV[1]`
contains &lsquo;inventory-shipped&rsquo;, and `ARGV[2]` contains
&lsquo;mail-list&rsquo;.
Notice that the `awk` program is not entered in `ARGV`.  The
other command-line options, with their arguments, are also not
entered.  This includes variable assignments done with the -v
option (see [Options](#22-command-line-options)).
Normal variable assignments on the command line *are*
treated as arguments and do show up in the `ARGV` array.
Given the following program in a file named showargs.awk:

    BEGIN {
        printf "A=%d, B=%d\n", A, B
        for (i = 0; i < ARGC; i++)
            printf "\tARGV[%d] = %s\n", i, ARGV[i]
    }
    END   { printf "A=%d, B=%d\n", A, B }
    

Running it produces the following:

    $ awk -v A=1 -f showargs.awk B=2 /dev/null
    -| A=1, B=0
    -|        ARGV[0] = awk
    -|        ARGV[1] = B=2
    -|        ARGV[2] = /dev/null
    -| A=1, B=2
    

A program can alter `ARGC` and the elements of `ARGV`.
Each time `awk` reaches the end of an input file, it uses the next
element of `ARGV` as the name of the next input file.  By storing a
different string there, a program can change which files are read.
Use `"-"` to represent the standard input.  Storing
additional elements and incrementing `ARGC` causes
additional files to be read.

If the value of `ARGC` is decreased, that eliminates input files
from the end of the list.  By recording the old value of `ARGC`
elsewhere, a program can treat the eliminated arguments as
something other than file names.

To eliminate a file from the middle of the list, store the null string
(`""`) into `ARGV` in place of the file&rsquo;s name.  As a
special feature, `awk` ignores file names that have been
replaced with the null string.
Another option is to
use the `delete` statement to remove elements from
`ARGV` (see [Delete](#84-the-delete-statement)).

All of these actions are typically done in the `BEGIN` rule,
before actual processing of the input begins.
See [Split Program](#1124-splitting-a-large-file-into-pieces) and
see [Tee Program](#1125-duplicating-output-into-multiple-files)
for examples
of each way of removing elements from `ARGV`.

To actually get options into an `awk` program,
end the `awk` options with -- and then supply
the `awk` program&rsquo;s options, in the following manner:

    awk -f myprog.awk -- -v -q file1 file2 &hellip;
    

The following fragment processes `ARGV` in order to examine, and
then remove, the previously mentioned command-line options:

    BEGIN {
        for (i = 1; i < ARGC; i++) {
            if (ARGV[i] == "-v")
                verbose = 1
            else if (ARGV[i] == "-q")
                debug = 1
            else if (ARGV[i] ~ /^-./) {
                e = sprintf("%s: unrecognized option -- %c",
                        ARGV[0], substr(ARGV[i], 2, 1))
                print e > "/dev/stderr"
            } else
                break
            delete ARGV[i]
        }
    }
    

Ending the `awk` options with -- isn&rsquo;t
necessary in `gawk`. Unless --posix has
been specified, `gawk` silently puts any unrecognized options
into `ARGV` for the `awk` program to deal with.  As soon
as it sees an unknown option, `gawk` stops looking for other
options that it might otherwise recognize.  The previous command line with
`gawk` would be:

    gawk -f myprog.awk -q -v file1 file2 &hellip;
    

Because -q is not a valid `gawk` option, it and the
following -v are passed on to the `awk` program.
(See [Getopt Function](#104-processing-command-line-options) for an `awk` library function that
parses command-line options.)

When designing your program, you should choose options that don&rsquo;t
conflict with `gawk`&rsquo;s, because it will process any options
that it accepts before passing the rest of the command line on to
your program.  Using &lsquo;#!&rsquo; with the -E option may help
(see [Executable Scripts](#114-executable-awk-programs)
and
see [Options](#22-command-line-options)).

---

Previous: [Built-in Variables](#75-predefined-variables), Up: [Patterns and Actions](#7-patterns-actions-and-variables)   [[Contents](#table-of-contents)][[Index](#Index)]

### 7.6 Summary

-  Pattern&ndash;action pairs make up the basic elements of an `awk`
program.  Patterns are either normal expressions, range expressions,
or regexp constants; one of the special keywords `BEGIN`, `END`,
`BEGINFILE`, or `ENDFILE`; or empty.  The action executes if
the current record matches the pattern.  Empty (missing) patterns match
all records.

-  I/O from `BEGIN` and `END` rules has certain constraints.
This is also true, only more so, for `BEGINFILE` and `ENDFILE`
rules.  The latter two give you &ldquo;hooks&rdquo; into `gawk`&rsquo;s file
processing, allowing you to recover from a file that otherwise would
cause a fatal error (such as a file that cannot be opened).

-  Shell variables can be used in `awk` programs by careful
use of shell quoting.  It is easier to pass a shell variable into
`awk` by using the -v option and an `awk`
variable.

-  Actions consist of statements enclosed in curly braces. Statements
are built up from expressions, control statements, compound statements,
input and output statements, and deletion statements.

-  The control statements in `awk` are `if`-`else`,
`while`, `for`, and `do`-`while`.  `gawk`
adds the `switch` statement.  There are two flavors of `for`
statement: one for performing general looping, and the other for iterating
through an array.

- `break` and `continue` let you exit early or start the next
iteration of a loop (or get out of a `switch`).

- `next` and `nextfile` let you read the next record and start
over at the top of your program or skip to the next input file and
start over, respectively.

-  The `exit` statement terminates your program. When executed
from an action (or function body), it transfers control to the
`END` statements. From an `END` statement body, it exits
immediately.  You may pass an optional numeric value to be used
as `awk`&rsquo;s exit status.

-  Some predefined variables provide control over `awk`, mainly for I/O.
Other variables convey information from `awk` to your program.

- `ARGC` and `ARGV` make the command-line arguments available
to your program. Manipulating them from a `BEGIN` rule lets you
control how `awk` will process the provided data files.

---

Next: [Functions](#9-functions), Previous: [Patterns and Actions](#7-patterns-actions-and-variables), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 8 Arrays in `awk`

An *array* is a table of values called *elements*.  The
elements of an array are distinguished by their *indices*.  Indices
may be either numbers or strings.

This chapter describes how arrays work in `awk`,
how to use array elements, how to scan through every element in an array,
and how to remove array elements.
It also describes how `awk` simulates multidimensional
arrays, as well as some of the less obvious points about array usage.
The chapter moves on to discuss `gawk`&rsquo;s facility
for sorting arrays, and ends with a brief description of `gawk`&rsquo;s
ability to support true arrays of arrays.

&bull; [Array Basics](#81-the-basics-of-arrays):  The basics of arrays.
&bull; [Numeric Array Subscripts](#82-using-numbers-to-subscript-arrays):  How to use numbers as subscripts in
                                `awk`.
&bull; [Uninitialized Subscripts](#83-using-uninitialized-variables-as-subscripts):  Using Uninitialized variables as subscripts.
&bull; [Delete](#84-the-delete-statement):  The `delete` statement removes an element
                                from an array.
&bull; [Multidimensional](#85-multidimensional-arrays):  Emulating multidimensional arrays in
                                `awk`.
&bull; [Arrays of Arrays](#86-arrays-of-arrays):  True multidimensional arrays.
&bull; [Arrays Summary](#87-summary):  Summary of arrays.

---

Next: [Numeric Array Subscripts](#82-using-numbers-to-subscript-arrays), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.1 The Basics of Arrays

This section presents the basics: working with elements
in arrays one at a time, and traversing all of the elements in
an array.

&bull; [Array Intro](#811-introduction-to-arrays):  Introduction to Arrays
&bull; [Reference to Elements](#812-referring-to-an-array-element):  How to examine one element of an array.
&bull; [Assigning Elements](#813-assigning-array-elements):  How to change an element of an array.
&bull; [Array Example](#814-basic-array-example):  Basic Example of an Array
&bull; [Scanning an Array](#815-scanning-all-elements-of-an-array):  A variation of the `for` statement. It
                                loops through the indices of an array&rsquo;s
                                existing elements.
&bull; [Controlling Scanning](#816-using-predefined-array-scanning-orders-with-gawk):  Controlling the order in which arrays are
                                scanned.

---

Next: [Reference to Elements](#812-referring-to-an-array-element), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.1 Introduction to Arrays

> Doing linear scans over an associative array is like trying to club someone
> to death with a loaded Uzi.

&mdash; *Larry Wall*

The `awk` language provides one-dimensional arrays
for storing groups of related strings or numbers.
Every `awk` array must have a name.  Array names have the same
syntax as variable names; any valid variable name would also be a valid
array name.  But one name cannot be used in both ways (as an array and
as a variable) in the same `awk` program.

Arrays in `awk` superficially resemble arrays in other programming
languages, but there are fundamental differences.  In `awk`, it
isn&rsquo;t necessary to specify the size of an array before starting to use it.
Additionally, any number or string, not just consecutive integers,
may be used as an array index.

In most other languages, arrays must be *declared* before use,
including a specification of
how many elements or components they contain.  In such languages, the
declaration causes a contiguous block of memory to be allocated for that
many elements.  Usually, an index in the array must be a nonnegative integer.
For example, the index zero specifies the first element in the array, which is
actually stored at the beginning of the block of memory.  Index one
specifies the second element, which is stored in memory right after the
first element, and so on.  It is impossible to add more elements to the
array, because it has room only for as many elements as given in
the declaration.
(Some languages allow arbitrary starting and ending
indices&mdash;e.g., &lsquo;15 .. 27&rsquo;&mdash;but the size of the array is still fixed when
the array is declared.)

A contiguous array of four elements might look like
[Figure 8.1](#figure_002darray_002delements),
conceptually, if the element values are eight, `"foo"`,
`""`, and 30.

![A Contiguous Array](array-elements.png)

**Figure 8.1: **A contiguous array

Only the values are stored; the indices are implicit from the order of
the values. Here, eight is the value at index zero, because eight appears in the
position with zero elements before it.

Arrays in `awk` are different&mdash;they are *associative*.  This means
that each array is a collection of pairs&mdash;an index and its corresponding
array element value:

IndexValue`3``30``1``"foo"``0``8``2``""`
The pairs are shown in jumbled order because their order is
irrelevant.[41](#FOOT41)

One advantage of associative arrays is that new pairs can be added
at any time.  For example, suppose a tenth element is added to the array
whose value is `"number ten"`.  The result is:

IndexValue`10``"number ten"``3``30``1``"foo"``0``8``2``""`

Now the array is *sparse*, which just means some indices are missing.
It has elements 0&ndash;3 and 10, but doesn&rsquo;t have elements 4, 5, 6, 7, 8, or 9.

Another consequence of associative arrays is that the indices don&rsquo;t
have to be nonnegative integers.  Any number, or even a string, can be
an index.  For example, the following is an array that translates words from
English to French:

IndexValue`"dog"``"chien"``"cat"``"chat"``"one"``"un"``1``"un"`
Here we decided to translate the number one in both spelled-out and
numeric form&mdash;thus illustrating that a single array can have both
numbers and strings as indices.
(In fact, array subscripts are always strings.
There are some subtleties to how numbers work when used as
array subscripts; this is discussed in more detail in
[Numeric Array Subscripts](#82-using-numbers-to-subscript-arrays).)
Here, the number `1` isn&rsquo;t double-quoted, because `awk`
automatically converts it to a string.

The value of `IGNORECASE` has no effect upon array subscripting.
The identical string value used to store an array element must be used
to retrieve it.
When `awk` creates an array (e.g., with the `split()`
built-in function),
that array&rsquo;s indices are consecutive integers starting at one.
(See [String Functions](#913-string-manipulation-functions).)

`awk`&rsquo;s arrays are efficient&mdash;the time to access an element
is independent of the number of elements in the array.

---

Next: [Assigning Elements](#813-assigning-array-elements), Previous: [Array Intro](#811-introduction-to-arrays), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.2 Referring to an Array Element

The principal way to use an array is to refer to one of its elements.
An *array reference* is an expression as follows:

    array[index-expression]
    

Here, array is the name of an array.  The expression index-expression is
the index of the desired element of the array.

The value of the array reference is the current value of that array
element.  For example, `foo[4.3]` is an expression referencing the element
of array `foo` at index &lsquo;4.3&rsquo;.

A reference to an array element that has no recorded value yields a value of
`""`, the null string.  This includes elements
that have not been assigned any value as well as elements that have been
deleted (see [Delete](#84-the-delete-statement)).

> NOTE: A reference to an element that does not exist *automatically* creates
> that array element, with the null string as its value.  (In some cases,
> this is unfortunate, because it might waste memory inside `awk`.)
> 
> 
> Novice `awk` programmers often make the mistake of checking if
> an element exists by checking if the value is empty:
> 
> 
> 
>     # Check if "foo" exists in a:         Incorrect!
>     if (a["foo"] != "") &hellip;
>     
> 
> 
> 
> This is incorrect for two reasons. First, it *creates*`a["foo"]`
> if it didn&rsquo;t exist before! Second, it is valid (if a bit unusual) to set
> an array element equal to the empty string.

To determine whether an element exists in an array at a certain index, use
the following expression:

    indx in array

This expression tests whether the particular index indx exists,
without the side effect of creating that element if it is not present.
The expression has the value one (true) if `array[indx]`
exists and zero (false) if it does not exist.
(We use indx here, because &lsquo;index&rsquo; is the name of a built-in
function.)
For example, this statement tests whether the array `frequencies`
contains the index &lsquo;2&rsquo;:

    if (2 in frequencies)
        print "Subscript 2 is present."
    

Note that this is *not* a test of whether the array
`frequencies` contains an element whose *value* is two.
There is no way to do that except to scan all the elements.  Also, this
*does not* create `frequencies[2]`, while the following
(incorrect) alternative does:

    if (frequencies[2] != "")
        print "Subscript 2 is present."
    

---

Next: [Array Example](#814-basic-array-example), Previous: [Reference to Elements](#812-referring-to-an-array-element), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.3 Assigning Array Elements

Array elements can be assigned values just like
`awk` variables:

    array[index-expression] = value

array is the name of an array.  The expression
index-expression is the index of the element of the array that is
assigned a value.  The expression value is the value to
assign to that element of the array.

---

Next: [Scanning an Array](#815-scanning-all-elements-of-an-array), Previous: [Assigning Elements](#813-assigning-array-elements), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.4 Basic Array Example

The following program takes a list of lines, each beginning with a line
number, and prints them out in order of line number.  The line numbers
are not in order when they are first read&mdash;instead, they
are scrambled.  This program sorts the lines by making an array using
the line numbers as subscripts.  The program then prints out the lines
in sorted order of their numbers.  It is a very simple program and gets
confused upon encountering repeated numbers, gaps, or lines that don&rsquo;t
begin with a number:

    {
        if ($1 > max)
            max = $1
        arr[$1] = $0
    }
    
    END {
        for (x = 1; x <= max; x++)
            print arr[x]
    }
    

The first rule keeps track of the largest line number seen so far;
it also stores each line into the array `arr`, at an index that
is the line&rsquo;s number.
The second rule runs after all the input has been read, to print out
all the lines.
When this program is run with the following input:

    5  I am the Five man
    2  Who are you?  The new number two!
    4  . . . And four on the floor
    1  Who is number one?
    3  I three you.
    

Its output is:

    1  Who is number one?
    2  Who are you?  The new number two!
    3  I three you.
    4  . . . And four on the floor
    5  I am the Five man
    

If a line number is repeated, the last line with a given number overrides
the others.
Gaps in the line numbers can be handled with an easy improvement to the
program&rsquo;s `END` rule, as follows:

    END {
        for (x = 1; x <= max; x++)
            if (x in arr)
                print arr[x]
    }
    

---

Next: [Controlling Scanning](#816-using-predefined-array-scanning-orders-with-gawk), Previous: [Array Example](#814-basic-array-example), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.5 Scanning All Elements of an Array

In programs that use arrays, it is often necessary to use a loop that
executes once for each element of an array.  In other languages, where
arrays are contiguous and indices are limited to nonnegative integers,
this is easy: all the valid indices can be found by counting from
the lowest index up to the highest.  This technique won&rsquo;t do the job
in `awk`, because any number or string can be an array index.
So `awk` has a special kind of `for` statement for scanning
an array:

    for (var in array)
        body

This loop executes body once for each index in array that the
program has previously used, with the variable var set to that index.

The following program uses this form of the `for` statement.  The
first rule scans the input records and notes which words appear (at
least once) in the input, by storing a one into the array `used` with
the word as the index.  The second rule scans the elements of `used` to
find all the distinct words that appear in the input.  It prints each
word that is more than 10 characters long and also prints the number of
such words.
See [String Functions](#913-string-manipulation-functions)
for more information on the built-in function `length()`.

    # Record a 1 for each word that is used at least once
    {
        for (i = 1; i <= NF; i++)
            used[$i] = 1
    }
    

    # Find number of distinct words more than 10 characters long
    END {
        for (x in used) {
            if (length(x) > 10) {
                ++num_long_words
                print x
            }
        }
        print num_long_words, "words longer than 10 characters"
    }
    

See [Word Sorting](#1135-generating-word-usage-counts)
for a more detailed example of this type.

The order in which elements of the array are accessed by this statement
is determined by the internal arrangement of the array elements within
`awk` and in standard `awk` cannot be controlled
or changed.  This can lead to problems if new elements are added to
array by statements in the loop body; it is not predictable whether
the `for` loop will reach them.  Similarly, changing var inside
the loop may produce strange results.  It is best to avoid such things.

As a point of information, `gawk` sets up the list of elements
to be iterated over before the loop starts, and does not change it.
But not all `awk` versions do so. Consider this program, named
loopcheck.awk:

    BEGIN {
        a["here"] = "here"
        a["is"] = "is"
        a["a"] = "a"
        a["loop"] = "loop"
        for (i in a) {
            j++
            a[j] = j
            print i
        }
    }
    

Here is what happens when run with `gawk` (and `mawk`):

    $ gawk -f loopcheck.awk
    -| here
    -| loop
    -| a
    -| is
    

Contrast this to BWK `awk`:

    $ nawk -f loopcheck.awk
    -| loop
    -| here
    -| is
    -| a
    -| 1
    

---

Previous: [Scanning an Array](#815-scanning-all-elements-of-an-array), Up: [Array Basics](#81-the-basics-of-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.1.6 Using Predefined Array Scanning Orders with `gawk`

This subsection describes a feature that is specific to `gawk`.

By default, when a `for` loop traverses an array, the order
is undefined, meaning that the `awk` implementation
determines the order in which the array is traversed.
This order is usually based on the internal implementation of arrays
and will vary from one version of `awk` to the next.

Often, though, you may wish to do something simple, such as
&ldquo;traverse the array by comparing the indices in ascending order,&rdquo;
or &ldquo;traverse the array by comparing the values in descending order.&rdquo;
`gawk` provides two mechanisms that give you this control:

-  Set `PROCINFO["sorted_in"]` to one of a set of predefined values.
We describe this now.

-  Set `PROCINFO["sorted_in"]` to the name of a user-defined function
to use for comparison of array elements. This advanced feature
is described later in [Array Sorting](#Array-Sorting).

The following special values for `PROCINFO["sorted_in"]` are available:

`"@unsorted"`
Array elements are processed in arbitrary order, which is the default
`awk` behavior.

`"@ind_str_asc"`
Order by indices in ascending order compared as strings; this is the most basic sort.
(Internally, array indices are always strings, so with &lsquo;a[2*5] = 1&rsquo;
the index is `"10"` rather than numeric 10.)

`"@ind_num_asc"`
Order by indices in ascending order but force them to be treated as numbers in the process.
Any index with a non-numeric value will end up positioned as if it were zero.

`"@val_type_asc"`
Order by element values in ascending order (rather than by indices).
Ordering is by the type assigned to the element
(see [Typing and Comparison](#6322-comparison-operators)).
All numeric values come before all string values,
which in turn come before all subarrays.
(Subarrays have not been described yet;
see [Arrays of Arrays](#86-arrays-of-arrays).)

`"@val_str_asc"`
Order by element values in ascending order (rather than by indices).  Scalar values are
compared as strings.  Subarrays, if present, come out last.

`"@val_num_asc"`
Order by element values in ascending order (rather than by indices).  Scalar values are
compared as numbers.  Subarrays, if present, come out last.
When numeric values are equal, the string values are used to provide
an ordering: this guarantees consistent results across different
versions of the C `qsort()` function,[42](#FOOT42) which `gawk` uses internally
to perform the sorting.

`"@ind_str_desc"`
Like `"@ind_str_asc"`, but the
string indices are ordered from high to low.

`"@ind_num_desc"`
Like `"@ind_num_asc"`, but the
numeric indices are ordered from high to low.

`"@val_type_desc"`
Like `"@val_type_asc"`, but the
element values, based on type, are ordered from high to low.
Subarrays, if present, come out first.

`"@val_str_desc"`
Like `"@val_str_asc"`, but the
element values, treated as strings, are ordered from high to low.
Subarrays, if present, come out first.

`"@val_num_desc"`
Like `"@val_num_asc"`, but the
element values, treated as numbers, are ordered from high to low.
Subarrays, if present, come out first.

The array traversal order is determined before the `for` loop
starts to run. Changing `PROCINFO["sorted_in"]` in the loop body
does not affect the loop.
For example:

    $ gawk '
    > BEGIN {
    >    a[4] = 4
    >    a[3] = 3
    >    for (i in a)
    >        print i, a[i]
    > }'
    -| 4 4
    -| 3 3
    $ gawk '
    > BEGIN {
    >    PROCINFO["sorted_in"] = "@ind_str_asc"
    >    a[4] = 4
    >    a[3] = 3
    >    for (i in a)
    >        print i, a[i]
    > }'
    -| 3 3
    -| 4 4
    

When sorting an array by element values, if a value happens to be
a subarray then it is considered to be greater than any string or
numeric value, regardless of what the subarray itself contains,
and all subarrays are treated as being equal to each other.  Their
order relative to each other is determined by their index strings.

Here are some additional things to bear in mind about sorted
array traversal:

-  The value of `PROCINFO["sorted_in"]` is global. That is, it affects
all array traversal `for` loops.  If you need to change it within your
own code, you should see if it&rsquo;s defined and save and restore the value:

    &hellip;
    if ("sorted_in" in PROCINFO) {
        save_sorted = PROCINFO["sorted_in"]
        PROCINFO["sorted_in"] = "@val_str_desc" # or whatever
    }
    &hellip;
    if (save_sorted)
        PROCINFO["sorted_in"] = save_sorted
    

-  As already mentioned, the default array traversal order is represented by
`"@unsorted"`.  You can also get the default behavior by assigning
the null string to `PROCINFO["sorted_in"]` or by just deleting the
`"sorted_in"` element from the `PROCINFO` array with
the `delete` statement.
(The `delete` statement hasn&rsquo;t been described yet; see [Delete](#84-the-delete-statement).)

In addition, `gawk` provides built-in functions for
sorting arrays; see [Array Sorting Functions](#Array-Sorting-Functions).

---

Next: [Uninitialized Subscripts](#83-using-uninitialized-variables-as-subscripts), Previous: [Array Basics](#81-the-basics-of-arrays), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.2 Using Numbers to Subscript Arrays

An important aspect to remember about arrays is that *array subscripts
are always strings*.  When a numeric value is used as a subscript,
it is converted to a string value before being used for subscripting
(see [Conversion](#614-conversion-of-strings-and-numbers)).
This means that the value of the predefined variable `CONVFMT` can
affect how your program accesses elements of an array.  For example:

    xyz = 12.153
    data[xyz] = 1
    CONVFMT = "%2.2f"
    if (xyz in data)
        printf "%s is in data\n", xyz
    else
        printf "%s is not in data\n", xyz
    

This prints &lsquo;12.15 is not in data&rsquo;.  The first statement gives
`xyz` a numeric value.  Assigning to
`data[xyz]` subscripts `data` with the string value `"12.153"`
(using the default conversion value of `CONVFMT`, `"%.6g"`).
Thus, the array element `data["12.153"]` is assigned the value one.
The program then changes
the value of `CONVFMT`.  The test &lsquo;(xyz in data)&rsquo; generates a new
string value from `xyz`&mdash;this time `"12.15"`&mdash;because the value of
`CONVFMT` only allows two significant digits.  This test fails,
because `"12.15"` is different from `"12.153"`.

According to the rules for conversions
(see [Conversion](#614-conversion-of-strings-and-numbers)), integer
values always convert to strings as integers, no matter what the
value of `CONVFMT` may happen to be.  So the usual case of
the following works:

    for (i = 1; i <= maxsub; i++)
        do something with array[i]
    

The &ldquo;integer values always convert to strings as integers&rdquo; rule
has an additional consequence for array indexing.
Octal and hexadecimal constants
(see [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers))
are converted internally into numbers, and their original form
is forgotten.  This means, for example, that `array[17]`,
`array[021]`, and `array[0x11]` all refer to the same element!

As with many things in `awk`, the majority of the time
things work as you would expect them to.  But it is useful to have a precise
knowledge of the actual rules, as they can sometimes have a subtle
effect on your programs.

---

Next: [Delete](#84-the-delete-statement), Previous: [Numeric Array Subscripts](#82-using-numbers-to-subscript-arrays), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.3 Using Uninitialized Variables as Subscripts

Suppose it&rsquo;s necessary to write a program
to print the input data in reverse order.
A reasonable attempt to do so (with some test
data) might look like this:

    $ echo 'line 1
    > line 2
    > line 3' | awk '{ l[lines] = $0; ++lines }
    > END {
    >     for (i = lines - 1; i >= 0; i--)
    >        print l[i]
    > }'
    -| line 3
    -| line 2
    

Unfortunately, the very first line of input data did not appear in the
output!

Upon first glance, we would think that this program should have worked.
The variable `lines`
is uninitialized, and uninitialized variables have the numeric value zero.
So, `awk` should have printed the value of `l[0]`.

The issue here is that subscripts for `awk` arrays are *always*
strings. Uninitialized variables, when used as strings, have the
value `""`, not zero.  Thus, &lsquo;line 1&rsquo; ends up stored in
`l[""]`.
The following version of the program works correctly:

    { l[lines++] = $0 }
    END {
        for (i = lines - 1; i >= 0; i--)
           print l[i]
    }
    

Here, the &lsquo;++&rsquo; forces `lines` to be numeric, thus making
the &ldquo;old value&rdquo; numeric zero. This is then converted to `"0"`
as the array subscript.

Even though it is somewhat unusual, the null string
(`""`) is a valid array subscript.
(d.c.)
`gawk` warns about the use of the null string as a subscript
if --lint is provided
on the command line (see [Options](#22-command-line-options)).

---

Next: [Multidimensional](#85-multidimensional-arrays), Previous: [Uninitialized Subscripts](#83-using-uninitialized-variables-as-subscripts), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.4 The `delete` Statement

To remove an individual element of an array, use the `delete`
statement:

    delete array[index-expression]
    

Once an array element has been deleted, any value the element once
had is no longer available. It is as if the element had never
been referred to or been given a value.
The following is an example of deleting elements in an array:

    for (i in frequencies)
        delete frequencies[i]
    

This example removes all the elements from the array `frequencies`.
Once an element is deleted, a subsequent `for` statement to scan the array
does not report that element and using the `in` operator to check for
the presence of that element returns zero (i.e., false):

    delete foo[4]
    if (4 in foo)
        print "This will never be printed"
    

It is important to note that deleting an element is *not* the
same as assigning it a null value (the empty string, `""`).
For example:

    foo[4] = ""
    if (4 in foo)
      print "This is printed, even though foo[4] is empty"
    

It is not an error to delete an element that does not exist.
However, if --lint is provided on the command line
(see [Options](#22-command-line-options)),
`gawk` issues a warning message when an element that
is not in the array is deleted.

All the elements of an array may be deleted with a single statement
by leaving off the subscript in the `delete` statement,
as follows:

    delete array

Using this version of the `delete` statement is about three times
more efficient than the equivalent loop that deletes each element one
at a time.

This form of the `delete` statement is also supported
by BWK `awk` and `mawk`, as well as
by a number of other implementations.

> NOTE: For many years, using `delete` without a subscript was a common
> extension.  In September 2012, it was accepted for inclusion into the
> POSIX standard.  See [the Austin Group website](http://austingroupbugs.net/view.php?id=544).

The following statement provides a portable but nonobvious way to clear
out an array:[43](#FOOT43)

    split("", array)
    

The `split()` function
(see [String Functions](#913-string-manipulation-functions))
clears out the target array first. This call asks it to split
apart the null string. Because there is no data to split out, the
function simply clears the array and then returns.

> CAUTION: Deleting all the elements from an array does not change its type; you cannot
> clear an array and then use the array&rsquo;s name as a scalar
> (i.e., a regular variable). For example, the following does not work:
> 
> 
> 
>     a[1] = 3
>     delete a
>     a = 3
>     

---

Next: [Arrays of Arrays](#86-arrays-of-arrays), Previous: [Delete](#84-the-delete-statement), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.5 Multidimensional Arrays
&bull; [Multiscanning](#851-scanning-multidimensional-arrays):  Scanning multidimensional arrays.

A *multidimensional array* is an array in which an element is identified
by a sequence of indices instead of a single index.  For example, a
two-dimensional array requires two indices.  The usual way (in many
languages, including `awk`) to refer to an element of a
two-dimensional array named `grid` is with
`grid[x,y]`.

Multidimensional arrays are supported in `awk` through
concatenation of indices into one string.
`awk` converts the indices into strings
(see [Conversion](#614-conversion-of-strings-and-numbers)) and
concatenates them together, with a separator between them.  This creates
a single string that describes the values of the separate indices.  The
combined string is used as a single index into an ordinary,
one-dimensional array.  The separator used is the value of the built-in
variable `SUBSEP`.

For example, suppose we evaluate the expression &lsquo;foo[5,12] = "value"&rsquo;
when the value of `SUBSEP` is `"@"`.  The numbers 5 and 12 are
converted to strings and
concatenated with an &lsquo;@&rsquo; between them, yielding `"5@12"`; thus,
the array element `foo["5@12"]` is set to `"value"`.

Once the element&rsquo;s value is stored, `awk` has no record of whether
it was stored with a single index or a sequence of indices.  The two
expressions &lsquo;foo[5,12]&rsquo; and &lsquo;foo[5 SUBSEP 12]&rsquo; are always
equivalent.

The default value of `SUBSEP` is the string `"\034"`,
which contains a nonprinting character that is unlikely to appear in an
`awk` program or in most input data.
The usefulness of choosing an unlikely character comes from the fact
that index values that contain a string matching `SUBSEP` can lead to
combined strings that are ambiguous.  Suppose that `SUBSEP` is
`"@"`; then &lsquo;foo["a@b", "c"]&rsquo; and &lsquo;foo["a", "b@c"]&rsquo; are indistinguishable because both are actually
stored as &lsquo;foo["a@b@c"]&rsquo;.

To test whether a particular index sequence exists in a
multidimensional array, use the same operator (`in`) that is
used for single-dimensional arrays.  Write the whole sequence of indices
in parentheses, separated by commas, as the left operand:

    if ((subscript1, subscript2, &hellip;) in array)
        &hellip;
    

Here is an example that treats its input as a two-dimensional array of
fields; it rotates this array 90 degrees clockwise and prints the
result.  It assumes that all lines have the same number of
elements:

    {
         if (max_nf < NF)
              max_nf = NF
         max_nr = NR
         for (x = 1; x <= NF; x++)
              vector[x, NR] = $x
    }
    
    END {
         for (x = 1; x <= max_nf; x++) {
              for (y = max_nr; y >= 1; --y)
                   printf("%s ", vector[x, y])
              printf("\n")
         }
    }
    

When given the input:

    1 2 3 4 5 6
    2 3 4 5 6 1
    3 4 5 6 1 2
    4 5 6 1 2 3
    

the program produces the following output:

    4 3 2 1
    5 4 3 2
    6 5 4 3
    1 6 5 4
    2 1 6 5
    3 2 1 6
    

---

Up: [Multidimensional](#85-multidimensional-arrays)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 8.5.1 Scanning Multidimensional Arrays

There is no special `for` statement for scanning a
&ldquo;multidimensional&rdquo; array. There cannot be one, because, in truth,
`awk` does not have
multidimensional arrays or elements&mdash;there is only a
multidimensional *way of accessing* an array.

However, if your program has an array that is always accessed as
multidimensional, you can get the effect of scanning it by combining
the scanning `for` statement
(see [Scanning an Array](#815-scanning-all-elements-of-an-array)) with the
built-in `split()` function
(see [String Functions](#913-string-manipulation-functions)).
It works in the following manner:

    for (combined in array) {
        split(combined, separate, SUBSEP)
        &hellip;
    }
    

This sets the variable `combined` to
each concatenated combined index in the array, and splits it
into the individual indices by breaking it apart where the value of
`SUBSEP` appears.  The individual indices then become the elements of
the array `separate`.

Thus, if a value is previously stored in `array[1, "foo"]`, then
an element with index `"1\034foo"` exists in `array`.  (Recall
that the default value of `SUBSEP` is the character with code 034.)
Sooner or later, the `for` statement finds that index and does an
iteration with the variable `combined` set to `"1\034foo"`.
Then the `split()` function is called as follows:

    split("1\034foo", separate, "\034")
    

The result is to set `separate[1]` to `"1"` and
`separate[2]` to `"foo"`.  Presto! The original sequence of
separate indices is recovered.

---

Next: [Arrays Summary](#87-summary), Previous: [Multidimensional](#85-multidimensional-arrays), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.6 Arrays of Arrays

`gawk` goes beyond standard `awk`&rsquo;s multidimensional
array access and provides true arrays of
arrays. Elements of a subarray are referred to by their own indices
enclosed in square brackets, just like the elements of the main array.
For example, the following creates a two-element subarray at index `1`
of the main array `a`:

    a[1][1] = 1
    a[1][2] = 2
    

This simulates a true two-dimensional array. Each subarray element can
contain another subarray as a value, which in turn can hold other arrays
as well. In this way, you can create arrays of three or more dimensions.
The indices can be any `awk` expressions, including scalars
separated by commas (i.e., a regular `awk` simulated
multidimensional subscript). So the following is valid in
`gawk`:

    a[1][3][1, "name"] = "barney"
    

Each subarray and the main array can be of different length. In fact, the
elements of an array or its subarray do not all have to have the same
type. This means that the main array and any of its subarrays can be
nonrectangular, or jagged in structure. You can assign a scalar value to
the index `4` of the main array `a`, even though `a[1]`
is itself an array and not a scalar:

    a[4] = "An element in a jagged array"
    

The terms *dimension*, *row*, and *column* are
meaningless when applied
to such an array, but we will use &ldquo;dimension&rdquo; henceforth to imply the
maximum number of indices needed to refer to an existing element. The
type of any element that has already been assigned cannot be changed
by assigning a value of a different type. You have to first delete the
current element, which effectively makes `gawk` forget about
the element at that index:

    delete a[4]
    a[4][5][6][7] = "An element in a four-dimensional array"
    

This removes the scalar value from index `4` and then inserts a
three-level nested subarray
containing a scalar. You can also
delete an entire subarray or subarray of subarrays:

    delete a[4][5]
    a[4][5] = "An element in subarray a[4]"
    

But recall that you can not delete the main array `a` and then use it
as a scalar.

The built-in functions that take array arguments can also be used
with subarrays. For example, the following code fragment uses `length()`
(see [String Functions](#913-string-manipulation-functions))
to determine the number of elements in the main array `a` and
its subarrays:

    print length(a), length(a[1]), length(a[1][3])
    

This results in the following output for our main array `a`:

    2, 3, 1
    

The &lsquo;subscript in array&rsquo; expression
(see [Reference to Elements](#812-referring-to-an-array-element)) works similarly for both
regular `awk`-style
arrays and arrays of arrays. For example, the tests &lsquo;1 in a&rsquo;,
&lsquo;3 in a[1]&rsquo;, and &lsquo;(1, "name") in a[1][3]&rsquo; all evaluate to
one (true) for our array `a`.

The &lsquo;for (item in array)&rsquo; statement (see [Scanning an Array](#815-scanning-all-elements-of-an-array))
can be nested to scan all the
elements of an array of arrays if it is rectangular in structure. In order
to print the contents (scalar values) of a two-dimensional array of arrays
(i.e., in which each first-level element is itself an
array, not necessarily of the same length),
you could use the following code:

    for (i in array)
        for (j in array[i])
            print array[i][j]
    

The `isarray()` function (see [Type Functions](#917-getting-type-information))
lets you test if an array element is itself an array:

    for (i in array) {
        if (isarray(array[i]) {
            for (j in array[i]) {
                print array[i][j]
            }
        }
        else
            print array[i]
    }
    

If the structure of a jagged array of arrays is known in advance,
you can often devise workarounds using control statements. For example,
the following code prints the elements of our main array `a`:

    for (i in a) {
        for (j in a[i]) {
            if (j == 3) {
                for (k in a[i][j])
                    print a[i][j][k]
    

            } else
                print a[i][j]
        }
    }
    

See [Walking Arrays](#107-traversing-arrays-of-arrays) for a user-defined function that &ldquo;walks&rdquo; an
arbitrarily dimensioned array of arrays.

Recall that a reference to an uninitialized array element yields a value
of `""`, the null string. This has one important implication when you
intend to use a subarray as an argument to a function, as illustrated by
the following example:

    $ gawk 'BEGIN { split("a b c d", b[1]); print b[1][1] }'
    error&rarr; gawk: cmd. line:1: fatal: split: second argument is not an array
    

The way to work around this is to first force `b[1]` to be an array by
creating an arbitrary index:

    $ gawk 'BEGIN { b[1][1] = ""; split("a b c d", b[1]); print b[1][1] }'
    -| a
    

---

Previous: [Arrays of Arrays](#86-arrays-of-arrays), Up: [Arrays](#8-arrays-in-awk)   [[Contents](#table-of-contents)][[Index](#Index)]

### 8.7 Summary

-  Standard `awk` provides one-dimensional associative arrays
(arrays indexed by string values).  All arrays are associative; numeric
indices are converted automatically to strings.

-  Array elements are referenced as `array[indx]`.
Referencing an element creates it if it did not exist previously.

-  The proper way to see if an array has an element with a given index
is to use the `in` operator: &lsquo;indx in array&rsquo;.

-  Use &lsquo;for (indx in array) &hellip;&rsquo; to scan through all the
individual elements of an array. In the body of the loop, indx takes
on the value of each element&rsquo;s index in turn.

-  The order in which a &lsquo;for (indx in array)&rsquo; loop
traverses an array is undefined in POSIX `awk` and varies among
implementations.  `gawk` lets you control the order by assigning
special predefined values to `PROCINFO["sorted_in"]`.

-  Use &lsquo;delete array[indx]&rsquo; to delete an individual element.
To delete all of the elements in an array,
use &lsquo;delete array&rsquo;.
This latter feature has been a common extension for many
years and is now standard, but may not be supported by all commercial
versions of `awk`.

-  Standard `awk` simulates multidimensional arrays by separating
subscript values with commas.  The values are concatenated into a
single string, separated by the value of `SUBSEP`.  The fact
that such a subscript was created in this way is not retained; thus,
changing `SUBSEP` may have unexpected consequences.  You can use
&lsquo;(sub1, sub2, &hellip;) in array&rsquo; to see if such
a multidimensional subscript exists in array.

- `gawk` provides true arrays of arrays. You use a separate
set of square brackets for each dimension in such an array:
`data[row][col]`, for example. Array elements may thus be either
scalar values (number or string) or other arrays.

-  Use the `isarray()` built-in function to determine if an array
element is itself a subarray.

---

Next: [Library Functions]((#10-a-library-of-awk-functions)), Previous: [Arrays](#8-arrays-in-awk), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 9 Functions

This chapter describes `awk`&rsquo;s built-in functions,
which fall into three categories: numeric, string, and I/O.
`gawk` provides additional groups of functions
to work with values that represent time, do
bit manipulation, sort arrays,
provide type information, and internationalize and localize programs.

Besides the built-in functions, `awk` has provisions for
writing new functions that the rest of a program can use.
The second half of this chapter describes these
*user-defined* functions.
Finally, we explore indirect function calls, a `gawk`-specific
extension that lets you determine at runtime what function is to
be called.

&bull; [Built-in](#91-built-in-functions):  Summarizes the built-in functions.
&bull; [User-defined](#92-user-defined-functions):  Describes User-defined functions in detail.
&bull; [Indirect Calls](#93-indirect-function-calls):  Choosing the function to call at runtime.
&bull; [Functions Summary](#94-summary):  Summary of functions.

---

Next: [User-defined](#92-user-defined-functions), Up: [Functions](#9-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 9.1 Built-in Functions

*Built-in* functions are always available for your `awk`
program to call.  This section defines all the built-in functions
in `awk`; some of these are mentioned in other sections
but are summarized here for your convenience.

&bull; [Calling Built-in](#911-calling-built-in-functions):  How to call built-in functions.
&bull; [Numeric Functions](#912-numeric-functions):  Functions that work with numbers, including
                                `int()`, `sin()` and `rand()`.
&bull; [String Functions](#913-string-manipulation-functions):  Functions for string manipulation, such as
                                `split()`, `match()` and
                                `sprintf()`.
&bull; [I/O Functions](#914-inputoutput-functions):  Functions for files and shell commands.
&bull; [Time Functions](#915-time-functions):  Functions for dealing with timestamps.
&bull; [Bitwise Functions](#916-bit-manipulation-functions):  Functions for bitwise operations.
&bull; [Type Functions](#917-getting-type-information):  Functions for type information.
&bull; [I18N Functions](#918-string-translation-functions):  Functions for string translation.

---

Next: [Numeric Functions](#912-numeric-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.1 Calling Built-in Functions

To call one of `awk`&rsquo;s built-in functions, write the name of
the function followed
by arguments in parentheses.  For example, &lsquo;atan2(y + z, 1)&rsquo;
is a call to the function `atan2()` and has two arguments.

Whitespace is ignored between the built-in function name and the
opening parenthesis, but nonetheless it is good practice to avoid using whitespace
there.  User-defined functions do not permit whitespace in this way, and
it is easier to avoid mistakes by following a simple
convention that always works&mdash;no whitespace after a function name.

Each built-in function accepts a certain number of arguments.
In some cases, arguments can be omitted. The defaults for omitted
arguments vary from function to function and are described under the
individual functions.  In some `awk` implementations, extra
arguments given to built-in functions are ignored.  However, in `gawk`,
it is a fatal error to give extra arguments to a built-in function.

When a function is called, expressions that create the function&rsquo;s actual
parameters are evaluated completely before the call is performed.
For example, in the following code fragment:

    i = 4
    j = sqrt(i++)
    

the variable `i` is incremented to the value five before `sqrt()`
is called with a value of four for its actual parameter.
The order of evaluation of the expressions used for the function&rsquo;s
parameters is undefined.  Thus, avoid writing programs that
assume that parameters are evaluated from left to right or from
right to left.  For example:

    i = 5
    j = atan2(++i, i *= 2)
    

If the order of evaluation is left to right, then `i` first becomes
six, and then 12, and `atan2()` is called with the two arguments six
and 12.  But if the order of evaluation is right to left, `i`
first becomes 10, then 11, and `atan2()` is called with the
two arguments 11 and 10.

---

Next: [String Functions](#913-string-manipulation-functions), Previous: [Calling Built-in](#911-calling-built-in-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.2 Numeric Functions

The following list describes all of
the built-in functions that work with numbers.
Optional parameters are enclosed in square brackets ([ ]):
`atan2(y, x)`
Return the arctangent of `y / x` in radians.
You can use &lsquo;pi = atan2(0, -1)&rsquo; to retrieve the value of
pi.

`cos(x)`
Return the cosine of x, with x in radians.

`exp(x)`
Return the exponential of x (`e ^ x`) or report
an error if x is out of range.  The range of values x can have
depends on your machine&rsquo;s floating-point representation.

`int(x)`
Return the nearest integer to x, located between x and zero and
truncated toward zero.
For example, `int(3)` is 3, `int(3.9)` is 3, `int(-3.9)`
is -3, and `int(-3)` is -3 as well.

`log(x)`
Return the natural logarithm of x, if x is positive;
otherwise, return `NaN` (&ldquo;not a number&rdquo;) on IEEE 754 systems.
Additionally, `gawk` prints a warning message when `x`
is negative.

`rand()`
Return a random number.  The values of `rand()` are
uniformly distributed between zero and one.
The value could be zero but is never one.[44](#FOOT44)

Often random integers are needed instead.  Following is a user-defined function
that can be used to obtain a random nonnegative integer less than n:

    function randint(n)
    {
        return int(n * rand())
    }
    

The multiplication produces a random number greater than or equal to
zero and less than `n`.  Using `int()`, this result is made into
an integer between zero and `n` - 1, inclusive.

The following example uses a similar function to produce random integers
between one and n.  This program prints a new random number for
each input record:

    # Function to roll a simulated die.
    function roll(n) { return 1 + int(rand() * n) }
    
    # Roll 3 six-sided dice and
    # print total number of points.
    {
        printf("%d points\n", roll(6) + roll(6) + roll(6))
    }
    

> CAUTION: In most `awk` implementations, including `gawk`,
> `rand()` starts generating numbers from the same
> starting number, or *seed*, each time you run `awk`.[45](#FOOT45)  Thus,
> a program generates the same results each time you run it.
> The numbers are random within one `awk` run but predictable
> from run to run.  This is convenient for debugging, but if you want
> a program to do different things each time it is used, you must change
> the seed to a value that is different in each run.  To do this,
> use `srand()`.

`sin(x)`
Return the sine of x, with x in radians.

`sqrt(x)`
Return the positive square root of x.
`gawk` prints a warning message
if x is negative.  Thus, `sqrt(4)` is 2.

`srand(`[x]`)`
Set the starting point, or seed,
for generating random numbers to the value x.

Each seed value leads to a particular sequence of random
numbers.[46](#FOOT46)
Thus, if the seed is set to the same value a second time,
the same sequence of random numbers is produced again.

> CAUTION: Different `awk` implementations use different random-number
> generators internally.  Don&rsquo;t expect the same `awk` program
> to produce the same series of random numbers when executed by
> different versions of `awk`.

If the argument x is omitted, as in &lsquo;srand()&rsquo;, then the current
date and time of day are used for a seed.  This is the way to get random
numbers that are truly unpredictable.

The return value of `srand()` is the previous seed.  This makes it
easy to keep track of the seeds in case you need to consistently reproduce
sequences of random numbers.

POSIX does not specify the initial seed; it differs among `awk`
implementations.

---

Next: [I/O Functions](#914-inputoutput-functions), Previous: [Numeric Functions](#912-numeric-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.3 String-Manipulation Functions

The functions in this section look at or change the text of one
or more strings.

`gawk` understands locales (see [Locales](#66-where-you-are-makes-a-difference)) and does all
string processing in terms of *characters*, not *bytes*.
This distinction is particularly important to understand for locales
where one character may be represented by multiple bytes.  Thus, for
example, `length()` returns the number of characters in a string,
and not the number of bytes used to represent those characters. Similarly,
`index()` works with character indices, and not byte indices.

> CAUTION: A number of functions deal with indices into strings.  For these
> functions, the first character of a string is at position (index) one.
> This is different from C and the languages descended from it, where the
> first character is at position zero.  You need to remember this when
> doing index calculations, particularly if you are used to C.

In the following list, optional parameters are enclosed in square brackets ([ ]).
Several functions perform string substitution; the full discussion is
provided in the description of the `sub()` function, which comes
toward the end, because the list is presented alphabetically.

Those functions that are specific to `gawk` are marked with a
pound sign (&lsquo;#&rsquo;).  They are not available in compatibility mode
(see [Options](#22-command-line-options)):

&bull; [Gory Details](#9131-more-about-lsquorsquo-and-lsquorsquo-with-sub-gsub-and-gensub):  More than you want to know about &lsquo;\&rsquo; and
                                &lsquo;&&rsquo; with `sub()`, `gsub()`, and
                                `gensub()`.
`asort(`source [`,`dest [`,`how ] ]`) #``asorti(`source [`,`dest [`,`how ] ]`) #`
These two functions are similar in behavior, so they are described
together.

> NOTE: The following description ignores the third argument, how, as it
> requires understanding features that we have not discussed yet.  Thus,
> the discussion here is a deliberate simplification.  (We do provide all
> the details later on; see [Array Sorting Functions](#Array-Sorting-Functions) for the full story.)

Both functions return the number of elements in the array source.
For `asort()`, `gawk` sorts the values of source
and replaces the indices of the sorted values of source with
sequential integers starting with one.  If the optional array dest
is specified, then source is duplicated into dest.  dest
is then sorted, leaving the indices of source unchanged.

When comparing strings, `IGNORECASE` affects the sorting
(see [Array Sorting Functions](#Array-Sorting-Functions)).  If the
source array contains subarrays as values (see [Arrays of Arrays](#86-arrays-of-arrays)), they will come last, after all scalar values.
Subarrays are *not* recursively sorted.

For example, if the contents of `a` are as follows:

    a["last"] = "de"
    a["first"] = "sac"
    a["middle"] = "cul"
    

A call to `asort()`:

    asort(a)
    

results in the following contents of `a`:

    a[1] = "cul"
    a[2] = "de"
    a[3] = "sac"
    

The `asorti()` function works similarly to `asort()`; however,
the *indices* are sorted, instead of the values. Thus, in the
previous example, starting with the same initial set of indices and
values in `a`, calling &lsquo;asorti(a)&rsquo; would yield:

    a[1] = "first"
    a[2] = "last"
    a[3] = "middle"
    

`gensub(regexp, replacement, how` [`, target`]`) #`
Search the target string target for matches of the regular
expression regexp.  If how is a string beginning with
&lsquo;g&rsquo; or &lsquo;G&rsquo; (short for &ldquo;global&rdquo;), then replace all matches of regexp with
replacement.  Otherwise, how is treated as a number indicating
which match of regexp to replace. If no target is supplied,
use `$0`.  It returns the modified string as the result
of the function and the original target string is *not* changed.

`gensub()` is a general substitution function.  Its purpose is
to provide more features than the standard `sub()` and `gsub()`
functions.

`gensub()` provides an additional feature that is not available
in `sub()` or `gsub()`: the ability to specify components of a
regexp in the replacement text.  This is done by using parentheses in
the regexp to mark the components and then specifying &lsquo;\N&rsquo;
in the replacement text, where N is a digit from 1 to 9.
For example:

    $ gawk '
    > BEGIN {
    >      a = "abc def"
    >      b = gensub(/(.+) (.+)/, "\\2 \\1", "g", a)
    >      print b
    > }'
    -| def abc
    

As with `sub()`, you must type two backslashes in order
to get one into the string.
In the replacement text, the sequence &lsquo;\0&rsquo; represents the entire
matched text, as does the character &lsquo;&&rsquo;.

The following example shows how you can use the third argument to control
which match of the regexp should be changed:

    $ echo a b c a b c |
    > gawk '{ print gensub(/a/, "AA", 2) }'
    -| a b c AA b c
    

In this case, `$0` is the default target string.
`gensub()` returns the new string as its result, which is
passed directly to `print` for printing.

If the how argument is a string that does not begin with &lsquo;g&rsquo; or
&lsquo;G&rsquo;, or if it is a number that is less than or equal to zero, only one
substitution is performed.  If how is zero, `gawk` issues
a warning message.

If regexp does not match target, `gensub()`&rsquo;s return value
is the original unchanged value of target.

`gsub(regexp, replacement` [`, target`]`)`
Search target for
*all* of the longest, leftmost, *nonoverlapping* matching
substrings it can find and replace them with replacement.
The &lsquo;g&rsquo; in `gsub()` stands for
&ldquo;global,&rdquo; which means replace everywhere.  For example:

    { gsub(/Britain/, "United Kingdom"); print }
    

replaces all occurrences of the string &lsquo;Britain&rsquo; with &lsquo;United
Kingdom&rsquo; for all input records.

The `gsub()` function returns the number of substitutions made.  If
the variable to search and alter (target) is
omitted, then the entire input record (`$0`) is used.
As in `sub()`, the characters &lsquo;&&rsquo; and &lsquo;\&rsquo; are special,
and the third argument must be assignable.

`index(in, find)`
Search the string in for the first occurrence of the string
find, and return the position in characters where that occurrence
begins in the string in.  Consider the following example:

    $ awk 'BEGIN { print index("peanut", "an") }'
    -| 3
    

If find is not found, `index()` returns zero.

With BWK `awk` and `gawk`,
it is a fatal error to use a regexp constant for find.
Other implementations allow it, simply treating the regexp
constant as an expression meaning &lsquo;$0 ~ /regexp/&rsquo;. (d.c.)

`length(`[string]`)`
Return the number of characters in string.  If
string is a number, the length of the digit string representing
that number is returned.  For example, `length("abcde")` is five.  By
contrast, `length(15 * 35)` works out to three. In this example,
15 * 35 = 525,
and 525 is then converted to the string `"525"`, which has
three characters.

If no argument is supplied, `length()` returns the length of `$0`.

> NOTE: In older versions of `awk`, the `length()` function could
> be called
> without any parentheses.  Doing so is considered poor practice,
> although the 2008 POSIX standard explicitly allows it, to
> support historical practice.  For programs to be maximally portable,
> always supply the parentheses.

If `length()` is called with a variable that has not been used,
`gawk` forces the variable to be a scalar.  Other
implementations of `awk` leave the variable without a type.
(d.c.)
Consider:

    $ gawk 'BEGIN { print length(x) ; x[1] = 1 }'
    -| 0
    error&rarr; gawk: fatal: attempt to use scalar `x' as array
    
    $ nawk 'BEGIN { print length(x) ; x[1] = 1 }'
    -| 0
    

If --lint has
been specified on the command line, `gawk` issues a
warning about this.

With `gawk` and several other `awk` implementations, when given an
array argument, the `length()` function returns the number of elements
in the array. (c.e.)
This is less useful than it might seem at first, as the
array is not guaranteed to be indexed from one to the number of elements
in it.
If --lint is provided on the command line
(see [Options](#22-command-line-options)),
`gawk` warns that passing an array argument is not portable.
If --posix is supplied, using an array argument is a fatal error
(see [Arrays](#8-arrays-in-awk)).

`match(string, regexp` [`, array`]`)`
Search string for the
longest, leftmost substring matched by the regular expression
regexp and return the character position (index)
at which that substring begins (one, if it starts at the beginning of
string).  If no match is found, return zero.

The regexp argument may be either a regexp constant
(`/`&hellip;`/`) or a string constant (`"`&hellip;`"`).
In the latter case, the string is treated as a regexp to be matched.
See [Computed Regexps](#36-using-dynamic-regexps) for a
discussion of the difference between the two forms, and the
implications for writing your program correctly.

The order of the first two arguments is the opposite of most other string
functions that work with regular expressions, such as
`sub()` and `gsub()`.  It might help to remember that
for `match()`, the order is the same as for the &lsquo;~&rsquo; operator:
&lsquo;string ~ regexp&rsquo;.

The `match()` function sets the predefined variable `RSTART` to
the index.  It also sets the predefined variable `RLENGTH` to the
length in characters of the matched substring.  If no match is found,
`RSTART` is set to zero, and `RLENGTH` to -1.

For example:

    {
        if ($1 == "FIND")
            regex = $2
        else {
            where = match($0, regex)
            if (where != 0)
                print "Match of", regex, "found at", where, "in", $0
           }
    }
    

This program looks for lines that match the regular expression stored in
the variable `regex`.  This regular expression can be changed.  If the
first word on a line is &lsquo;FIND&rsquo;, `regex` is changed to be the
second word on that line.  Therefore, if given:

    FIND ru+n
    My program runs
    but not very quickly
    FIND Melvin
    JF+KM
    This line is property of Reality Engineering Co.
    Melvin was here.
    

`awk` prints:

    Match of ru+n found at 12 in My program runs
    Match of Melvin found at 1 in Melvin was here.
    

If array is present, it is cleared, and then the zeroth element
of array is set to the entire portion of string
matched by regexp.  If regexp contains parentheses,
the integer-indexed elements of array are set to contain the
portion of string matching the corresponding parenthesized
subexpression.
For example:

    $ echo foooobazbarrrrr |
    > gawk '{ match($0, /(fo+).+(bar*)/, arr)
    >         print arr[1], arr[2] }'
    -| foooo barrrrr
    

In addition,
multidimensional subscripts are available providing
the start index and length of each matched subexpression:

    $ echo foooobazbarrrrr |
    > gawk '{ match($0, /(fo+).+(bar*)/, arr)
    >           print arr[1], arr[2]
    >           print arr[1, "start"], arr[1, "length"]
    >           print arr[2, "start"], arr[2, "length"]
    > }'
    -| foooo barrrrr
    -| 1 5
    -| 9 7
    

There may not be subscripts for the start and index for every parenthesized
subexpression, because they may not all have matched text; thus, they
should be tested for with the `in` operator
(see [Reference to Elements](#812-referring-to-an-array-element)).

The array argument to `match()` is a
`gawk` extension.  In compatibility mode
(see [Options](#22-command-line-options)),
using a third argument is a fatal error.

`patsplit(string, array` [`, fieldpat` [`, seps` ] ]`) #`
Divide
string into pieces (or &ldquo;fields&rdquo;) defined by fieldpat
and store the pieces in array and the separator strings in the
seps array.  The first piece is stored in
`array[1]`, the second piece in `array[2]`, and so
forth.  The third argument, fieldpat, is
a regexp describing the fields in string (just as `FPAT` is
a regexp describing the fields in input records).
It may be either a regexp constant or a string.
If fieldpat is omitted, the value of `FPAT` is used.
`patsplit()` returns the number of elements created.
`seps[i]` is
the possibly null separator string
after `array[i]`.
The possibly null leading separator will be in `seps[0]`.
So a non-null string with n fields will have n+1 separators.
A null string will not have neither fields nor separators.

The `patsplit()` function splits strings into pieces in a
manner similar to the way input lines are split into fields using `FPAT`
(see [Splitting By Content](#47-defining-fields-by-content)).

Before splitting the string, `patsplit()` deletes any previously existing
elements in the arrays array and seps.

`split(string, array` [`, fieldsep` [`, seps` ] ]`)`
Divide string into pieces separated by fieldsep
and store the pieces in array and the separator strings in the
seps array.  The first piece is stored in
`array[1]`, the second piece in `array[2]`, and so
forth.  The string value of the third argument, fieldsep, is
a regexp describing where to split string (much as `FS` can
be a regexp describing where to split input records).
If fieldsep is omitted, the value of `FS` is used.
`split()` returns the number of elements created.
seps is a `gawk` extension, with `seps[i]`
being the separator string
between `array[i]` and `array[i+1]`.
If fieldsep is a single
space, then any leading whitespace goes into `seps[0]` and
any trailing
whitespace goes into `seps[n]`, where n is the
return value of
`split()` (i.e., the number of elements in array).

The `split()` function splits strings into pieces in a
manner similar to the way input lines are split into fields.  For example:

    split("cul-de-sac", a, "-", seps)
    

splits the string `"cul-de-sac"` into three fields using &lsquo;-&rsquo; as the
separator.  It sets the contents of the array `a` as follows:

    a[1] = "cul"
    a[2] = "de"
    a[3] = "sac"
    

and sets the contents of the array `seps` as follows:

    seps[1] = "-"
    seps[2] = "-"
    

The value returned by this call to `split()` is three.

As with input field-splitting, when the value of fieldsep is
`" "`, leading and trailing whitespace is ignored in values assigned to
the elements of
array but not in seps, and the elements
are separated by runs of whitespace.
Also, as with input field splitting, if fieldsep is the null string, each
individual character in the string is split into its own array element.
(c.e.)

Note, however, that `RS` has no effect on the way `split()`
works. Even though &lsquo;RS = ""&rsquo; causes the newline character to also be an input
field separator, this does not affect how `split()` splits strings.

Modern implementations of `awk`, including `gawk`, allow
the third argument to be a regexp constant (`/`&hellip;`/`)
as well as a string.  (d.c.)
The POSIX standard allows this as well.
See [Computed Regexps](#36-using-dynamic-regexps) for a
discussion of the difference between using a string constant or a regexp constant,
and the implications for writing your program correctly.

Before splitting the string, `split()` deletes any previously existing
elements in the arrays array and seps.

If string is null, the array has no elements. (So this is a portable
way to delete an entire array with one statement.
See [Delete](#84-the-delete-statement).)

If string does not match fieldsep at all (but is not null),
array has one element only. The value of that element is the original
string.

In POSIX mode (see [Options](#22-command-line-options)), the fourth argument is not allowed.

`sprintf(format, expression1, &hellip;)`
Return (without printing) the string that `printf` would
have printed out with the same arguments
(see [Printf](#55-using-printf-statements-for-fancier-printing)).
For example:

    pival = sprintf("pi = %.2f (approx.)", 22/7)
    

assigns the string &lsquo;pi = 3.14 (approx.)&rsquo; to the variable `pival`.

`strtonum(str) #`
Examine str and return its numeric value.  If str
begins with a leading &lsquo;0&rsquo;, `strtonum()` assumes that str
is an octal number.  If str begins with a leading &lsquo;0x&rsquo; or
&lsquo;0X&rsquo;, `strtonum()` assumes that str is a hexadecimal number.
For example:

    $ echo 0x11 |
    > gawk '{ printf "%d\n", strtonum($1) }'
    -| 17
    

Using the `strtonum()` function is *not* the same as adding zero
to a string value; the automatic coercion of strings to numbers
works only for decimal data, not for octal or hexadecimal.[47](#FOOT47)

Note also that `strtonum()` uses the current locale&rsquo;s decimal point
for recognizing numbers (see [Locales](#66-where-you-are-makes-a-difference)).

`sub(regexp, replacement` [`, target`]`)`
Search target, which is treated as a string, for the
leftmost, longest substring matched by the regular expression regexp.
Modify the entire string
by replacing the matched text with replacement.
The modified string becomes the new value of target.
Return the number of substitutions made (zero or one).

The regexp argument may be either a regexp constant
(`/`&hellip;`/`) or a string constant (`"`&hellip;`"`).
In the latter case, the string is treated as a regexp to be matched.
See [Computed Regexps](#36-using-dynamic-regexps) for a
discussion of the difference between the two forms, and the
implications for writing your program correctly.

This function is peculiar because target is not simply
used to compute a value, and not just any expression will do&mdash;it
must be a variable, field, or array element so that `sub()` can
store a modified value there.  If this argument is omitted, then the
default is to use and alter `$0`.[48](#FOOT48)
For example:

    str = "water, water, everywhere"
    sub(/at/, "ith", str)
    

sets `str` to &lsquo;wither, water, everywhere&rsquo;, by replacing the
leftmost longest occurrence of &lsquo;at&rsquo; with &lsquo;ith&rsquo;.

If the special character &lsquo;&&rsquo; appears in replacement, it
stands for the precise substring that was matched by regexp.  (If
the regexp can match more than one string, then this precise substring
may vary.)  For example:

    { sub(/candidate/, "& and his wife"); print }
    

changes the first occurrence of &lsquo;candidate&rsquo; to &lsquo;candidate
and his wife&rsquo; on each input line.
Here is another example:

    $ awk 'BEGIN {
    >         str = "daabaaa"
    >         sub(/a+/, "C&C", str)
    >         print str
    > }'
    -| dCaaCbaaa
    

This shows how &lsquo;&&rsquo; can represent a nonconstant string and also
illustrates the &ldquo;leftmost, longest&rdquo; rule in regexp matching
(see [Leftmost Longest](#35-how-much-text-matches)).

The effect of this special character (&lsquo;&&rsquo;) can be turned off by putting a
backslash before it in the string.  As usual, to insert one backslash in
the string, you must write two backslashes.  Therefore, write &lsquo;\\&&rsquo;
in a string constant to include a literal &lsquo;&&rsquo; in the replacement.
For example, the following shows how to replace the first &lsquo;|&rsquo; on each line with
an &lsquo;&&rsquo;:

    { sub(/\|/, "\\&"); print }
    

As mentioned, the third argument to `sub()` must
be a variable, field, or array element.
Some versions of `awk` allow the third argument to
be an expression that is not an lvalue.  In such a case, `sub()`
still searches for the pattern and returns zero or one, but the result of
the substitution (if any) is thrown away because there is no place
to put it.  Such versions of `awk` accept expressions
like the following:

    sub(/USA/, "United States", "the USA and Canada")
    

For historical compatibility, `gawk` accepts such erroneous code.
However, using any other nonchangeable
object as the third parameter causes a fatal error and your program
will not run.

Finally, if the regexp is not a regexp constant, it is converted into a
string, and then the value of that string is treated as the regexp to match.

`substr(string, start` [`, length` ]`)`
Return a length-character-long substring of string,
starting at character number start.  The first character of a
string is character number one.[49](#FOOT49)
For example, `substr("washington", 5, 3)` returns `"ing"`.

If length is not present, `substr()` returns the whole suffix of
string that begins at character number start.  For example,
`substr("washington", 5)` returns `"ington"`.  The whole
suffix is also returned
if length is greater than the number of characters remaining
in the string, counting from character start.

If start is less than one, `substr()` treats it as
if it was one. (POSIX doesn&rsquo;t specify what to do in this case:
BWK `awk` acts this way, and therefore `gawk`
does too.)
If start is greater than the number of characters
in the string, `substr()` returns the null string.
Similarly, if length is present but less than or equal to zero,
the null string is returned.

The string returned by `substr()`*cannot* be
assigned.  Thus, it is a mistake to attempt to change a portion of
a string, as shown in the following example:

    string = "abcdef"
    # try to get "abCDEf", won't work
    substr(string, 3, 3) = "CDE"
    

It is also a mistake to use `substr()` as the third argument
of `sub()` or `gsub()`:

    gsub(/xyz/, "pdq", substr($0, 5, 20))  # WRONG
    

(Some commercial versions of `awk` treat
`substr()` as assignable, but doing so is not portable.)

If you need to replace bits and pieces of a string, combine `substr()`
with string concatenation, in the following manner:

    string = "abcdef"
    &hellip;
    string = substr(string, 1, 2) "CDE" substr(string, 6)
    

`tolower(string)`
Return a copy of string, with each uppercase character
in the string replaced with its corresponding lowercase character.
Nonalphabetic characters are left unchanged.  For example,
`tolower("MiXeD cAsE 123")` returns `"mixed case 123"`.

`toupper(string)`
Return a copy of string, with each lowercase character
in the string replaced with its corresponding uppercase character.
Nonalphabetic characters are left unchanged.  For example,
`toupper("MiXeD cAsE 123")` returns `"MIXED CASE 123"`.

Matching the Null String

In `awk`, the &lsquo;*&rsquo; operator can match the null string.
This is particularly important for the `sub()`, `gsub()`,
and `gensub()` functions.  For example:

    $ echo abc | awk '{ gsub(/m*/, "X"); print }'
    -| XaXbXcX
    

Although this makes a certain amount of sense, it can be surprising.

---

Up: [String Functions](#913-string-manipulation-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.3.1 More about &lsquo;\&rsquo; and &lsquo;&&rsquo; with `sub()`, `gsub()`, and `gensub()`

> CAUTION: This subsubsection has been reported to cause headaches.
> You might want to skip it upon first reading.

When using `sub()`, `gsub()`, or `gensub()`, and trying to get literal
backslashes and ampersands into the replacement text, you need to remember
that there are several levels of *escape processing* going on.

First, there is the *lexical* level, which is when `awk` reads
your program
and builds an internal copy of it to execute.
Then there is the runtime level, which is when `awk` actually scans the
replacement string to determine what to generate.

At both levels, `awk` looks for a defined set of characters that
can come after a backslash.  At the lexical level, it looks for the
escape sequences listed in [Escape Sequences](#32-escape-sequences).
Thus, for every &lsquo;\&rsquo; that `awk` processes at the runtime
level, you must type two backslashes at the lexical level.
When a character that is not valid for an escape sequence follows the
&lsquo;\&rsquo;, BWK `awk` and `gawk` both simply remove the initial
&lsquo;\&rsquo; and put the next character into the string. Thus, for
example, `"a\qb"` is treated as `"aqb"`.

At the runtime level, the various functions handle sequences of
&lsquo;\&rsquo; and &lsquo;&&rsquo; differently.  The situation is (sadly) somewhat complex.
Historically, the `sub()` and `gsub()` functions treated the
two-character sequence &lsquo;\&&rsquo; specially; this sequence was replaced in
the generated text with a single &lsquo;&&rsquo;.  Any other &lsquo;\&rsquo; within
the replacement string that did not precede an &lsquo;&&rsquo; was passed
through unchanged.  This is illustrated in [Table 9.1](#table_002dsub_002descapes).

     You type         sub() sees          sub() generates
     &mdash;&mdash;&ndash;         &mdash;&mdash;&mdash;-          &mdash;&mdash;&mdash;&mdash;&mdash;
         \&&            The matched text
        \\&\&            A literal &lsquo;&&rsquo;
       \\\&\&            A literal &lsquo;&&rsquo;
      \\\\&\\&            A literal &lsquo;\&&rsquo;
     \\\\\&\\&            A literal &lsquo;\&&rsquo;
    \\\\\\&\\\&            A literal &lsquo;\\&&rsquo;
        \\q\q            A literal &lsquo;\q&rsquo;
    

**Table 9.1: **Historical escape sequence processing for `sub()` and `gsub()`

This table shows the lexical-level processing, where
an odd number of backslashes becomes an even number at the runtime level,
as well as the runtime processing done by `sub()`.
(For the sake of simplicity, the rest of the following tables only show the
case of even numbers of backslashes entered at the lexical level.)

The problem with the historical approach is that there is no way to get
a literal &lsquo;\&rsquo; followed by the matched text.

Several editions of the POSIX standard attempted to fix this problem
but weren&rsquo;t successful. The details are irrelevant at this point in time.

At one point, the `gawk` maintainer submitted
proposed text for a revised standard that
reverts to rules that correspond more closely to the original existing
practice. The proposed rules have special cases that make it possible
to produce a &lsquo;\&rsquo; preceding the matched text.
This is shown in
[Table 9.2](#table_002dsub_002dproposed).

     You type         sub() sees         sub() generates
     &mdash;&mdash;&ndash;         &mdash;&mdash;&mdash;-         &mdash;&mdash;&mdash;&mdash;&mdash;
    \\\\\\&\\\&            A literal &lsquo;\&&rsquo;
      \\\\&\\&            A literal &lsquo;\&rsquo;rsquo;, followed by the matched text
        \\&\&            A literal &lsquo;&&rsquo;
        \\q\q            A literal &lsquo;\q&rsquo;
       \\\\\\\\

**Table 9.2: **`gawk` rules for `sub()` and backslash

In a nutshell, at the runtime level, there are now three special sequences
of characters (&lsquo;\\\&&rsquo;, &lsquo;\\&&rsquo;, and &lsquo;\&&rsquo;) whereas historically
there was only one.  However, as in the historical case, any &lsquo;\&rsquo; that
is not part of one of these three sequences is not special and appears
in the output literally.

`gawk` 3.0 and 3.1 follow these rules for `sub()` and
`gsub()`.  The POSIX standard took much longer to be revised than
was expected.  In addition, the `gawk` maintainer&rsquo;s proposal was
lost during the standardization process.  The final rules are
somewhat simpler.  The results are similar except for one case.

The POSIX rules state that &lsquo;\&&rsquo; in the replacement string produces
a literal &lsquo;&&rsquo;, &lsquo;\\&rsquo; produces a literal &lsquo;\&rsquo;, and &lsquo;\&rsquo; followed
by anything else is not special; the &lsquo;\&rsquo; is placed straight into the output.
These rules are presented in [Table 9.3](#table_002dposix_002dsub).

     You type         sub() sees         sub() generates
     &mdash;&mdash;&ndash;         &mdash;&mdash;&mdash;-         &mdash;&mdash;&mdash;&mdash;&mdash;
    \\\\\\&\\\&            A literal &lsquo;\&&rsquo;
      \\\\&\\&            A literal &lsquo;\&rsquo;, followed by the matched text
        \\&\&            A literal &lsquo;&&rsquo;
        \\q\q            A literal &lsquo;\q&rsquo;
       \\\\\\\

**Table 9.3: **POSIX rules for `sub()` and `gsub()`

The only case where the difference is noticeable is the last one: &lsquo;\\\\&rsquo;
is seen as &lsquo;\\&rsquo; and produces &lsquo;\&rsquo; instead of &lsquo;\\&rsquo;.

Starting with version 3.1.4, `gawk` followed the POSIX rules
when --posix was specified (see [Options](#22-command-line-options)). Otherwise,
it continued to follow the proposed rules, as
that had been its behavior for many years.

When version 4.0.0 was released, the `gawk` maintainer
made the POSIX rules the default, breaking well over a decade&rsquo;s worth
of backward compatibility.[50](#FOOT50) Needless to say, this was a bad idea,
and as of version 4.0.1, `gawk` resumed its historical
behavior, and only follows the POSIX rules when --posix is given.

The rules for `gensub()` are considerably simpler. At the runtime
level, whenever `gawk` sees a &lsquo;\&rsquo;, if the following character
is a digit, then the text that matched the corresponding parenthesized
subexpression is placed in the generated output.  Otherwise,
no matter what character follows the &lsquo;\&rsquo;, it
appears in the generated text and the &lsquo;\&rsquo; does not,
as shown in [Table 9.4](#table_002dgensub_002descapes).

      You type          gensub() sees         gensub() generates
      &mdash;&mdash;&ndash;          &mdash;&mdash;&mdash;&mdash;-         &mdash;&mdash;&mdash;&mdash;&mdash;&mdash;
          &&            The matched text
        \\&\&            A literal &lsquo;&&rsquo;
       \\\\\\            A literal &lsquo;\&rsquo;
      \\\\&\\&            A literal &lsquo;\&rsquo;, then the matched text
    \\\\\\&\\\&            A literal &lsquo;\&&rsquo;
        \\q\q            A literal &lsquo;q&rsquo;
    

**Table 9.4: **Escape sequence processing for `gensub()`

Because of the complexity of the lexical- and runtime-level processing
and the special cases for `sub()` and `gsub()`,
we recommend the use of `gawk` and `gensub()` when you have
to do substitutions.

---

Next: [Time Functions](#915-time-functions), Previous: [String Functions](#913-string-manipulation-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.4 Input/Output Functions

The following functions relate to input/output (I/O).
Optional parameters are enclosed in square brackets ([ ]):

`close(`filename [`,`how]`)`
Close the file filename for input or output. Alternatively, the
argument may be a shell command that was used for creating a coprocess, or
for redirecting to or from a pipe; then the coprocess or pipe is closed.
See [Close Files And Pipes](#59-closing-input-and-output-redirections)
for more information.

When closing a coprocess, it is occasionally useful to first close
one end of the two-way pipe and then to close the other.  This is done
by providing a second argument to `close()`.  This second argument
(how)
should be one of the two string values `"to"` or `"from"`,
indicating which end of the pipe to close.  Case in the string does
not matter.
See [Two-way I/O](#Two_002dway-I_002fO),
which discusses this feature in more detail and gives an example.

Note that the second argument to `close()` is a `gawk`
extension; it is not available in compatibility mode (see [Options](#22-command-line-options)).

`fflush(`[filename]`)`
Flush any buffered output associated with filename, which is either a
file opened for writing or a shell command for redirecting output to
a pipe or coprocess.

Many utility programs *buffer* their output (i.e., they save information
to write to a disk file or the screen in memory until there is enough
for it to be worthwhile to send the data to the output device).
This is often more efficient than writing
every little bit of information as soon as it is ready.  However, sometimes
it is necessary to force a program to *flush* its buffers (i.e.,
write the information to its destination, even if a buffer is not full).
This is the purpose of the `fflush()` function&mdash;`gawk` also
buffers its output, and the `fflush()` function forces
`gawk` to flush its buffers.

Brian Kernighan added `fflush()` to his `awk` in April
1992.  For two decades, it was a common extension.  In December
2012, it was accepted for inclusion into the POSIX standard.
See [the Austin Group website](http://austingroupbugs.net/view.php?id=634).

POSIX standardizes `fflush()` as follows: if there
is no argument, or if the argument is the null string (`""`),
then `awk` flushes the buffers for *all* open output files
and pipes.

> NOTE: Prior to version 4.0.2, `gawk`
> would flush only the standard output if there was no argument,
> and flush all output files and pipes if the argument was the null
> string. This was changed in order to be compatible with Brian
> Kernighan&rsquo;s `awk`, in the hope that standardizing this
> feature in POSIX would then be easier (which indeed proved to be the case).
> 
> 
> With `gawk`,
> you can use &lsquo;fflush("/dev/stdout")&rsquo; if you wish to flush
> only the standard output.

`fflush()` returns zero if the buffer is successfully flushed;
otherwise, it returns a nonzero value. (`gawk` returns -1.)
In the case where all buffers are flushed, the return value is zero
only if all buffers were flushed successfully.  Otherwise, it is
-1, and `gawk` warns about the problem filename.

`gawk` also issues a warning message if you attempt to flush
a file or pipe that was opened for reading (such as with `getline`),
or if filename is not an open file, pipe, or coprocess.
In such a case, `fflush()` returns -1, as well.

Interactive Versus Noninteractive Buffering

As a side point, buffering issues can be even more confusing if
your program is *interactive* (i.e., communicating
with a user sitting at a keyboard).[51](#FOOT51)

Interactive programs generally *line buffer* their output (i.e., they
write out every line).  Noninteractive programs wait until they have
a full buffer, which may be many lines of output.
Here is an example of the difference:

    $ awk '{ print $1 + $2 }'1 1
    -| 2
    2 3
    -| 5
    Ctrl-d

Each line of output is printed immediately. Compare that behavior
with this example:

    $ awk '{ print $1 + $2 }' | cat1 12 3Ctrl-d
    -| 2
    -| 5
    

Here, no output is printed until after the Ctrl-d is typed, because
it is all buffered and sent down the pipe to `cat` in one shot.

`system(command)`
Execute the operating system
command command and then return to the `awk` program.
Return command&rsquo;s exit status (see further on).

For example, if the following fragment of code is put in your `awk`
program:

    END {
         system("date | mail -s 'awk run done' root")
    }
    

the system administrator is sent mail when the `awk` program
finishes processing input and begins its end-of-input processing.

Note that redirecting `print` or `printf` into a pipe is often
enough to accomplish your task.  If you need to run many commands, it
is more efficient to simply print them down a pipeline to the shell:

    while (more stuff to do)
        print command | "/bin/sh"
    close("/bin/sh")
    

However, if your `awk`
program is interactive, `system()` is useful for running large
self-contained programs, such as a shell or an editor.
Some operating systems cannot implement the `system()` function.
`system()` causes a fatal error if it is not supported.

> NOTE: When --sandbox is specified, the `system()` function is disabled
> (see [Options](#22-command-line-options)).

On POSIX systems, a command&rsquo;s exit status is a 16-bit number. The exit
value passed to the C `exit()` function is held in the high-order
eight bits. The low-order bits indicate if the process was killed by a
signal (bit 7) and if so, the guilty signal number (bits 0&ndash;6).

Traditionally, `awk`&rsquo;s `system()` function has simply
returned the exit status value divided by 256. In the normal case this
gives the exit status but in the case of death-by-signal it yields
a fractional floating-point value.[52](#FOOT52) POSIX states that `awk`&rsquo;s
`system()` should return the full 16-bit value.

`gawk` steers a middle ground.
The return values are summarized in [Table 9.5](#table_002dsystem_002dreturn_002dvalues).

SituationReturn value from `system()`--traditionalC `system()`&rsquo;s value divided by 256--posixC `system()`&rsquo;s valueNormal exit of commandCommand&rsquo;s exit statusDeath by signal of command256 + number of murderous signalDeath by signal of command with core dump512 + number of murderous signalSome kind of error-1

**Table 9.5: **Return values from `system()`

Controlling Output Buffering with `system()`

The `fflush()` function provides explicit control over output buffering for
individual files and pipes.  However, its use is not portable to many older
`awk` implementations.  An alternative method to flush output
buffers is to call `system()` with a null string as its argument:

    system("")   # flush output
    

`gawk` treats this use of the `system()` function as a special
case and is smart enough not to run a shell (or other command
interpreter) with the empty command.  Therefore, with `gawk`, this
idiom is not only useful, it is also efficient.  Although this method should work
with other `awk` implementations, it does not necessarily avoid
starting an unnecessary shell.  (Other implementations may only
flush the buffer associated with the standard output and not necessarily
all buffered output.)

If you think about what a programmer expects, it makes sense that
`system()` should flush any pending output.  The following program:

    BEGIN {
         print "first print"
         system("echo system echo")
         print "second print"
    }
    

must print:

    first print
    system echo
    second print
    

and not:

    system echo
    first print
    second print
    

If `awk` did not flush its buffers before calling `system()`,
you would see the latter (undesirable) output.

---

Next: [Bitwise Functions](#916-bit-manipulation-functions), Previous: [I/O Functions](#914-inputoutput-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.5 Time Functions

`awk` programs are commonly used to process log files
containing timestamp information, indicating when a
particular log record was written.  Many programs log their timestamps
in the form returned by the `time()` system call, which is the
number of seconds since a particular epoch.  On POSIX-compliant systems,
it is the number of seconds since
1970-01-01 00:00:00 UTC, not counting leap
seconds.[53](#FOOT53)
All known POSIX-compliant systems support timestamps from 0 through
231 - 1,
which is sufficient to represent times through
2038-01-19 03:14:07 UTC.  Many systems support a wider range of timestamps,
including negative timestamps that represent times before the
epoch.

In order to make it easier to process such log files and to produce
useful reports, `gawk` provides the following functions for
working with timestamps.  They are `gawk` extensions; they are
not specified in the POSIX standard.[54](#FOOT54)
However, recent versions
of `mawk` (see [Other Versions](#Other-Versions)) also support these functions.
Optional parameters are enclosed in square brackets ([ ]):

`mktime(datespec` [`, utc-flag` ]`)`
Turn datespec into a timestamp in the same form
as is returned by `systime()`.  It is similar to the function of the
same name in ISO C.  The argument, datespec, is a string of the form
`"YYYY MM DD HH MM SS [DST]"`.
The string consists of six or seven numbers representing, respectively,
the full year including century, the month from 1 to 12, the day of the month
from 1 to 31, the hour of the day from 0 to 23, the minute from 0 to
59, the second from 0 to 60,[55](#FOOT55)
and an optional daylight-savings flag.

The values of these numbers need not be within the ranges specified;
for example, an hour of -1 means 1 hour before midnight.
The origin-zero Gregorian calendar is assumed, with year 0 preceding
year 1 and year -1 preceding year 0.
If utc-flag is present and is either nonzero or non-null, the time
is assumed to be in the UTC time zone; otherwise, the
time is assumed to be in the local time zone.
If the DST daylight-savings flag is positive, the time is assumed to be
daylight savings time; if zero, the time is assumed to be standard
time; and if negative (the default), `mktime()` attempts to determine
whether daylight savings time is in effect for the specified time.

If datespec does not contain enough elements or if the resulting time
is out of range, `mktime()` returns -1.

`strftime(`[format [`,`timestamp [`,`utc-flag] ] ]`)`
Format the time specified by timestamp
based on the contents of the format string and return the result.
It is similar to the function of the same name in ISO C.
If utc-flag is present and is either nonzero or non-null, the value
is formatted as UTC (Coordinated Universal Time, formerly GMT or Greenwich
Mean Time). Otherwise, the value is formatted for the local time zone.
The timestamp is in the same format as the value returned by the
`systime()` function.  If no timestamp argument is supplied,
`gawk` uses the current time of day as the timestamp.
Without a format argument, `strftime()` uses
the value of `PROCINFO["strftime"]` as the format string
(see [Built-in Variables](#75-predefined-variables)).
The default string value is
`"%a %b %e %H:%M:%S %Z %Y"`.  This format string produces
output that is equivalent to that of the `date` utility.
You can assign a new value to `PROCINFO["strftime"]` to
change the default format; see the following list for the various format directives.

`systime()`
Return the current time as the number of seconds since
the system epoch.  On POSIX systems, this is the number of seconds
since 1970-01-01 00:00:00 UTC, not counting leap seconds.
It may be a different number on other systems.

The `systime()` function allows you to compare a timestamp from a
log file with the current time of day.  In particular, it is easy to
determine how long ago a particular record was logged.  It also allows
you to produce log records using the &ldquo;seconds since the epoch&rdquo; format.

The `mktime()` function allows you to convert a textual representation
of a date and time into a timestamp.   This makes it easy to do before/after
comparisons of dates and times, particularly when dealing with date and
time data coming from an external source, such as a log file.

The `strftime()` function allows you to easily turn a timestamp
into human-readable information.  It is similar in nature to the `sprintf()`
function
(see [String Functions](#913-string-manipulation-functions)),
in that it copies nonformat specification characters verbatim to the
returned string, while substituting date and time values for format
specifications in the format string.

`strftime()` is guaranteed by the 1999 ISO C
standard[56](#FOOT56)
to support the following date format specifications:

`%a`
The locale&rsquo;s abbreviated weekday name.

`%A`
The locale&rsquo;s full weekday name.

`%b`
The locale&rsquo;s abbreviated month name.

`%B`
The locale&rsquo;s full month name.

`%c`
The locale&rsquo;s &ldquo;appropriate&rdquo; date and time representation.
(This is &lsquo;%A %B %d %T %Y&rsquo; in the `"C"` locale.)

`%C`
The century part of the current year.
This is the year divided by 100 and truncated to the next
lower integer.

`%d`
The day of the month as a decimal number (01&ndash;31).

`%D`
Equivalent to specifying &lsquo;%m/%d/%y&rsquo;.

`%e`
The day of the month, padded with a space if it is only one digit.

`%F`
Equivalent to specifying &lsquo;%Y-%m-%d&rsquo;.
This is the ISO 8601 date format.

`%g`
The year modulo 100 of the ISO 8601 week number, as a decimal number (00&ndash;99).
For example, January 1, 2012, is in week 53 of 2011. Thus, the year
of its ISO 8601 week number is 2011, even though its year is 2012.
Similarly, December 31, 2012, is in week 1 of 2013. Thus, the year
of its ISO week number is 2013, even though its year is 2012.

`%G`
The full year of the ISO week number, as a decimal number.

`%h`
Equivalent to &lsquo;%b&rsquo;.

`%H`
The hour (24-hour clock) as a decimal number (00&ndash;23).

`%I`
The hour (12-hour clock) as a decimal number (01&ndash;12).

`%j`
The day of the year as a decimal number (001&ndash;366).

`%m`
The month as a decimal number (01&ndash;12).

`%M`
The minute as a decimal number (00&ndash;59).

`%n`
A newline character (ASCII LF).

`%p`
The locale&rsquo;s equivalent of the AM/PM designations associated
with a 12-hour clock.

`%r`
The locale&rsquo;s 12-hour clock time.
(This is &lsquo;%I:%M:%S %p&rsquo; in the `"C"` locale.)

`%R`
Equivalent to specifying &lsquo;%H:%M&rsquo;.

`%S`
The second as a decimal number (00&ndash;60).

`%t`
A TAB character.

`%T`
Equivalent to specifying &lsquo;%H:%M:%S&rsquo;.

`%u`
The weekday as a decimal number (1&ndash;7).  Monday is day one.

`%U`
The week number of the year (with the first Sunday as the first day of week one)
as a decimal number (00&ndash;53).

`%V`
The week number of the year (with the first Monday as the first
day of week one) as a decimal number (01&ndash;53).
The method for determining the week number is as specified by ISO 8601.
(To wit: if the week containing January 1 has four or more days in the
new year, then it is week one; otherwise it is the last week
[52 or 53] of the previous year and the next week is week one.)

`%w`
The weekday as a decimal number (0&ndash;6).  Sunday is day zero.

`%W`
The week number of the year (with the first Monday as the first day of week one)
as a decimal number (00&ndash;53).

`%x`
The locale&rsquo;s &ldquo;appropriate&rdquo; date representation.
(This is &lsquo;%A %B %d %Y&rsquo; in the `"C"` locale.)

`%X`
The locale&rsquo;s &ldquo;appropriate&rdquo; time representation.
(This is &lsquo;%T&rsquo; in the `"C"` locale.)

`%y`
The year modulo 100 as a decimal number (00&ndash;99).

`%Y`
The full year as a decimal number (e.g., 2015).

`%z`
The time zone offset in a &lsquo;+HHMM&rsquo; format (e.g., the format
necessary to produce RFC 822/RFC 1036 date headers).

`%Z`
The time zone name or abbreviation; no characters if
no time zone is determinable.

`%Ec %EC %Ex %EX %Ey %EY %Od %Oe %OH``%OI %Om %OM %OS %Ou %OU %OV %Ow %OW %Oy`
&ldquo;Alternative representations&rdquo; for the specifications
that use only the second letter (&lsquo;%c&rsquo;, &lsquo;%C&rsquo;,
and so on).[57](#FOOT57)
(These facilitate compliance with the POSIX `date` utility.)

`%%`
A literal &lsquo;%&rsquo;.

If a conversion specifier is not one of those just listed, the behavior is
undefined.[58](#FOOT58)

For systems that are not yet fully standards-compliant,
`gawk` supplies a copy of
`strftime()` from the GNU C Library.
It supports all of the just-listed format specifications.
If that version is
used to compile `gawk` (see [Installation](#Installation)),
then the following additional format specifications are available:

`%k`
The hour (24-hour clock) as a decimal number (0&ndash;23).
Single-digit numbers are padded with a space.

`%l`
The hour (12-hour clock) as a decimal number (1&ndash;12).
Single-digit numbers are padded with a space.

`%s`
The time as a decimal timestamp in seconds since the epoch.

Additionally, the alternative representations are recognized but their
normal representations are used.

The following example is an `awk` implementation of the POSIX
`date` utility.  Normally, the `date` utility prints the
current date and time of day in a well-known format.  However, if you
provide an argument to it that begins with a &lsquo;+&rsquo;, `date`
copies nonformat specifier characters to the standard output and
interprets the current time according to the format specifiers in
the string.  For example:

    $ date '+Today is %A, %B %d, %Y.'
    -| Today is Monday, September 22, 2014.
    

Here is the `gawk` version of the `date` utility.
It has a shell &ldquo;wrapper&rdquo; to handle the -u option,
which requires that `date` run as if the time zone
is set to UTC:

    #! /bin/sh
    #
    # date --- approximate the POSIX 'date' command
    
    case $1 in
    -u)  TZ=UTC0     # use UTC
         export TZ
         shift ;;
    esac
    
    gawk 'BEGIN  {
        format = PROCINFO["strftime"]
        exitval = 0
    
        if (ARGC > 2)
            exitval = 1
        else if (ARGC == 2) {
            format = ARGV[1]
            if (format ~ /^\+/)
                format = substr(format, 2)   # remove leading +
        }
        print strftime(format)
        exit exitval
    }' "$@"
    

---

Next: [Type Functions](#917-getting-type-information), Previous: [Time Functions](#915-time-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.6 Bit-Manipulation Functions

> I can explain it for you, but I can&rsquo;t understand it for you.

&mdash; *Anonymous*

Many languages provide the ability to perform *bitwise* operations
on two integer numbers.  In other words, the operation is performed on
each successive pair of bits in the operands.
Three common operations are bitwise AND, OR, and XOR.
The operations are described in [Table 9.6](#table_002dbitwise_002dops).

                    Bit operator
              |  AND  |   OR  |  XOR
              |---+---+---+---+---+---
    Operands  | 0 | 1 | 0 | 1 | 0 | 1
    ----------+---+---+---+---+---+---
        0     | 0   0 | 0   1 | 0   1
        1     | 0   1 | 1   1 | 1   0
    

**Table 9.6: **Bitwise operations

As you can see, the result of an AND operation is 1 only when *both*
bits are 1.
The result of an OR operation is 1 if *either* bit is 1.
The result of an XOR operation is 1 if either bit is 1,
but not both.
The next operation is the *complement*; the complement of 1 is 0 and
the complement of 0 is 1. Thus, this operation &ldquo;flips&rdquo; all the bits
of a given value.

Finally, two other common operations are to shift the bits left or right.
For example, if you have a bit string &lsquo;10111001&rsquo; and you shift it
right by three bits, you end up with &lsquo;00010111&rsquo;.[59](#FOOT59)
If you start over again with &lsquo;10111001&rsquo; and shift it left by three
bits, you end up with &lsquo;11001000&rsquo;.  The following list describes
`gawk`&rsquo;s built-in functions that implement the bitwise operations.
Optional parameters are enclosed in square brackets ([ ]):

``and(`v1`,`v2 [`,` &hellip;]`)``
Return the bitwise AND of the arguments. There must be at least two.

``compl(val)``
Return the bitwise complement of val.

``lshift(val, count)``
Return the value of val, shifted left by count bits.

``or(`v1`,`v2 [`,` &hellip;]`)``
Return the bitwise OR of the arguments. There must be at least two.

``rshift(val, count)``
Return the value of val, shifted right by count bits.

``xor(`v1`,`v2 [`,` &hellip;]`)``
Return the bitwise XOR of the arguments. There must be at least two.

> CAUTION: Beginning with `gawk` version 4.2, negative
> operands are not allowed for any of these functions. A negative
> operand produces a fatal error.  See the sidebar
> &ldquo;Beware The Smoke and Mirrors!&rdquo; for more information as to why.

Here is a user-defined function (see [User-defined](#92-user-defined-functions))
that illustrates the use of these functions:

    # bits2str --- turn an integer into readable ones and zeros
    
    function bits2str(bits,        data, mask)
    {
        if (bits == 0)
            return "0"
    
        mask = 1
        for (; bits != 0; bits = rshift(bits, 1))
            data = (and(bits, mask) ? "1" : "0") data
    
        while ((length(data) % 8) != 0)
            data = "0" data
    
        return data
    }
    

    
    
    BEGIN {
        printf "123 = %s\n", bits2str(123)
        printf "0123 = %s\n", bits2str(0123)
        printf "0x99 = %s\n", bits2str(0x99)
        comp = compl(0x99)
        printf "compl(0x99) = %#x = %s\n", comp, bits2str(comp)
        shift = lshift(0x99, 2)
        printf "lshift(0x99, 2) = %#x = %s\n", shift, bits2str(shift)
        shift = rshift(0x99, 2)
        printf "rshift(0x99, 2) = %#x = %s\n", shift, bits2str(shift)
    }
    

This program produces the following output when run:

    $ gawk -f testbits.awk
    -| 123 = 01111011
    -| 0123 = 01010011
    -| 0x99 = 10011001
    -| compl(0x99) = 0x3fffffffffff66 =
    -| 00111111111111111111111111111111111111111111111101100110
    -| lshift(0x99, 2) = 0x264 = 0000001001100100
    -| rshift(0x99, 2) = 0x26 = 00100110
    

The `bits2str()` function turns a binary number into a string.
Initializing `mask` to one creates
a binary value where the rightmost bit
is set to one.  Using this mask,
the function repeatedly checks the rightmost bit.
ANDing the mask with the value indicates whether the
rightmost bit is one or not. If so, a `"1"` is concatenated onto the front
of the string.
Otherwise, a `"0"` is added.
The value is then shifted right by one bit and the loop continues
until there are no more one bits.

If the initial value is zero, it returns a simple `"0"`.
Otherwise, at the end, it pads the value with zeros to represent multiples
of 8-bit quantities. This is typical in modern computers.

The main code in the `BEGIN` rule shows the difference between the
decimal and octal values for the same numbers
(see [Nondecimal-numbers](#6112-octal-and-hexadecimal-numbers)),
and then demonstrates the
results of the `compl()`, `lshift()`, and `rshift()` functions.

Beware The Smoke and Mirrors!

It other languages, bitwise operations are performed on integer values,
not floating-point values.  As a general statement, such operations work
best when performed on unsigned integers.

`gawk` attempts to treat the arguments to the bitwise functions
as unsigned integers.  For this reason, negative arguments produce a
fatal error.

In normal operation, for all of these functions, first the
double-precision floating-point value is converted to the widest C
unsigned integer type, then the bitwise operation is performed.  If the
result cannot be represented exactly as a C `double`, leading
nonzero bits are removed one by one until it can be represented exactly.
The result is then converted back into a C `double`.[60](#FOOT60)

However, when using arbitrary precision arithmetic with the -M
option (see [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)), the results may differ.
This is particularly noticeable with the `compl()` function:

    $ gawk 'BEGIN { print compl(42) }'
    -| 9007199254740949
    $ gawk -M 'BEGIN { print compl(42) }'
    -| -43
    

What&rsquo;s going on becomes clear when printing the results
in hexadecimal:

    $ gawk 'BEGIN { printf "%#x\n", compl(42) }'
    -| 0x1fffffffffffd5
    $ gawk -M 'BEGIN { printf "%#x\n", compl(42) }'
    -| 0xffffffffffffffd5
    

When using the -M option, under the hood, `gawk` uses
GNU MP arbitrary precision integers which have at least 64 bits of precision.
When not using -M, `gawk` stores integral values in
regular double-precision floating point, which only maintain 53 bits of
precision.  Furthermore, the GNU MP library treats (or at least seems to treat)
the leading bit as a sign bit; thus the result with -M in this case is
a negative number.

In short, using `gawk` for any but the simplest kind of bitwise
operations is probably a bad idea; caveat emptor!

---

Next: [I18N Functions](#918-string-translation-functions), Previous: [Bitwise Functions](#916-bit-manipulation-functions), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.7 Getting Type Information

`gawk` provides two functions that let you distinguish
the type of a variable.
This is necessary for writing code
that traverses every element of an array of arrays
(see [Arrays of Arrays](#86-arrays-of-arrays)), and in other contexts.

`isarray(x)`
Return a true value if x is an array. Otherwise, return false.

`typeof(x)`
Return one of the following strings, depending upon the type of x:

`"array"`
x is an array.

`"regexp"`
x is a strongly typed regexp (see [Strong Regexp Constants](#6122-strongly-typed-regexp-constants)).

`"number"`
x is a number.

`"string"`
x is a string.

`"strnum"`
x is a number that started life as user input, such as a field or
the result of calling `split()`. (I.e., x has the strnum
attribute; see [Variable Typing](#6321-string-type-versus-numeric-type).)

`"unassigned"`
x is a scalar variable that has not been assigned a value yet.
For example:

    BEGIN {
        # creates a[1] but it has no assigned value
        a[1]
        print typeof(a[1])  # unassigned
    }
    

`"untyped"`
x has not yet been used yet at all; it can become a scalar or an
array.  The typing could even conceivably differ from run to run of
the same program! For example:

    BEGIN {
        print "initially, typeof(v) = ", typeof(v)
    
        if ("FOO" in ENVIRON)
            make_scalar(v)
        else
            make_array(v)
    
        print "typeof(v) =", typeof(v)
    }
    
    function make_scalar(p,    l) { l = p }
    
    function make_array(p) { p[1] = 1 }
    

`isarray()` is meant for use in two circumstances. The first is when
traversing a multidimensional array: you can test if an element is itself
an array or not.  The second is inside the body of a user-defined function
(not discussed yet; see [User-defined](#92-user-defined-functions)), to test if a parameter is an
array or not.

> NOTE: Using `isarray()` at the global level to test
> variables makes no sense. Because you are the one writing the program, you
> are supposed to know if your variables are arrays or not. And in fact,
> due to the way `gawk` works, if you pass the name of a variable
> that has not been previously used to `isarray()`, `gawk`
> ends up turning it into a scalar.

The `typeof()` function is general; it allows you to determine
if a variable or function parameter is a scalar, an array, or a strongly
typed regexp.

---

Previous: [Type Functions](#917-getting-type-information), Up: [Built-in](#91-built-in-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.1.8 String-Translation Functions

`gawk` provides facilities for internationalizing `awk` programs.
These include the functions described in the following list.
The descriptions here are purposely brief.
See [Internationalization](#Internationalization),
for the full story.
Optional parameters are enclosed in square brackets ([ ]):

`bindtextdomain(directory` [`,`domain]`)`
Set the directory in which
`gawk` will look for message translation files, in case they
will not or cannot be placed in the &ldquo;standard&rdquo; locations
(e.g., during testing).
It returns the directory in which domain is &ldquo;bound.&rdquo;

The default domain is the value of `TEXTDOMAIN`.
If directory is the null string (`""`), then
`bindtextdomain()` returns the current binding for the
given domain.

`dcgettext(string` [`,`domain [`,`category] ]`)`
Return the translation of string in
text domain domain for locale category category.
The default value for domain is the current value of `TEXTDOMAIN`.
The default value for category is `"LC_MESSAGES"`.

`dcngettext(string1, string2, number` [`,`domain [`,`category] ]`)`
Return the plural form used for number of the
translation of string1 and string2 in text domain
domain for locale category category. string1 is the
English singular variant of a message, and string2 is the English plural
variant of the same message.
The default value for domain is the current value of `TEXTDOMAIN`.
The default value for category is `"LC_MESSAGES"`.

---

Next: [Indirect Calls](#93-indirect-function-calls), Previous: [Built-in](#91-built-in-functions), Up: [Functions](#9-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 9.2 User-Defined Functions

Complicated `awk` programs can often be simplified by defining
your own functions.  User-defined functions can be called just like
built-in ones (see [Function Calls](#64-function-calls)), but it is up to you to define
them (i.e., to tell `awk` what they should do).

&bull; [Definition Syntax](#921-function-definition-syntax):  How to write definitions and what they mean.
&bull; [Function Example](#922-function-definition-examples):  An example function definition and what it
                                does.
&bull; [Function Caveats](#923-calling-user-defined-functions):  Things to watch out for.
&bull; [Return Statement](#924-the-return-statement):  Specifying the value a function returns.
&bull; [Dynamic Typing](#925-functions-and-their-effects-on-variable-typing):  How variable types can change at runtime.

---

Next: [Function Example](#922-function-definition-examples), Up: [User-defined](#92-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.1 Function Definition Syntax

> It&rsquo;s entirely fair to say that the awk syntax for local
> variable definitions is appallingly awful.

&mdash; *Brian Kernighan*

Definitions of functions can appear anywhere between the rules of an
`awk` program.  Thus, the general form of an `awk` program is
extended to include sequences of rules *and* user-defined function
definitions.
There is no need to put the definition of a function
before all uses of the function.  This is because `awk` reads the
entire program before starting to execute any of it.

The definition of a function named name looks like this:

    functionname([parameter-list]){body-of-function}

Here, name is the name of the function to define.  A valid function
name is like a valid variable name: a sequence of letters, digits, and
underscores that doesn&rsquo;t start with a digit.
Here too, only the 52 upper- and lowercase English letters may
be used in a function name.
Within a single `awk` program, any particular name can only be
used as a variable, array, or function.

parameter-list is an optional list of the function&rsquo;s arguments and local
variable names, separated by commas.  When the function is called,
the argument names are used to hold the argument values given in
the call.

A function cannot have two parameters with the same name, nor may it
have a parameter with the same name as the function itself.

> CAUTION: According to the POSIX standard, function parameters
> cannot have the same name as one of the special predefined variables
> (see [Built-in Variables](#75-predefined-variables)), nor may a function parameter have the
> same name as another function.
> 
> 
> Not all versions of `awk` enforce
> these restrictions.
> `gawk` always enforces the first restriction.
> With --posix (see [Options](#22-command-line-options)),
> it also enforces the second restriction.

Local variables act like the empty string if referenced where a string
value is required, and like zero if referenced where a numeric value
is required. This is the same as the behavior of regular variables that have never been
assigned a value.  (There is more to understand about local variables;
see [Dynamic Typing](#925-functions-and-their-effects-on-variable-typing).)

The body-of-function consists of `awk` statements.  It is the
most important part of the definition, because it says what the function
should actually *do*.  The argument names exist to give the body a
way to talk about the arguments; local variables exist to give the body
places to keep temporary values.

Argument names are not distinguished syntactically from local variable
names. Instead, the number of arguments supplied when the function is
called determines how many argument variables there are.  Thus, if three
argument values are given, the first three names in parameter-list
are arguments and the rest are local variables.

It follows that if the number of arguments is not the same in all calls
to the function, some of the names in parameter-list may be
arguments on some occasions and local variables on others.  Another
way to think of this is that omitted arguments default to the
null string.

Usually when you write a function, you know how many names you intend to
use for arguments and how many you intend to use as local variables.  It is
conventional to place some extra space between the arguments and
the local variables, in order to document how your function is supposed to be used.

During execution of the function body, the arguments and local variable
values hide, or *shadow*, any variables of the same names used in the
rest of the program.  The shadowed variables are not accessible in the
function definition, because there is no way to name them while their
names have been taken away for the arguments and local variables.  All other variables
used in the `awk` program can be referenced or set normally in the
function&rsquo;s body.

The arguments and local variables last only as long as the function body
is executing.  Once the body finishes, you can once again access the
variables that were shadowed while the function was running.

The function body can contain expressions that call functions.  They
can even call this function, either directly or by way of another
function.  When this happens, we say the function is *recursive*.
The act of a function calling itself is called *recursion*.

All the built-in functions return a value to their caller.
User-defined functions can do so also, using the `return` statement,
which is described in detail in [Return Statement](#924-the-return-statement).
Many of the subsequent examples in this section use
the `return` statement.

In many `awk` implementations, including `gawk`,
the keyword `function` may be
abbreviated `func`. (c.e.)
However, POSIX only specifies the use of
the keyword `function`.  This actually has some practical implications.
If `gawk` is in POSIX-compatibility mode
(see [Options](#22-command-line-options)), then the following
statement does *not* define a function:

    func foo() { a = sqrt($1) ; print a }
    

Instead, it defines a rule that, for each record, concatenates the value
of the variable &lsquo;func&rsquo; with the return value of the function &lsquo;foo&rsquo;.
If the resulting string is non-null, the action is executed.
This is probably not what is desired.  (`awk` accepts this input as
syntactically valid, because functions may be used before they are defined
in `awk` programs.[61](#FOOT61))

To ensure that your `awk` programs are portable, always use the
keyword `function` when defining a function.

---

Next: [Function Caveats](#923-calling-user-defined-functions), Previous: [Definition Syntax](#921-function-definition-syntax), Up: [User-defined](#92-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.2 Function Definition Examples

Here is an example of a user-defined function, called `myprint()`, that
takes a number and prints it in a specific format:

    function myprint(num)
    {
         printf "%6.3g\n", num
    }
    

To illustrate, here is an `awk` rule that uses our `myprint()`
function:

    $3 > 0     { myprint($3) }
    

This program prints, in our special format, all the third fields that
contain a positive number in our input.  Therefore, when given the following input:

     1.2   3.4    5.6   7.8
     9.10 11.12 -13.14 15.16
    17.18 19.20  21.22 23.24
    

this program, using our function to format the results, prints:

       5.6
      21.2
    

This function deletes all the elements in an array (recall that the
extra whitespace signifies the start of the local variable list):

    function delarray(a,    i)
    {
        for (i in a)
            delete a[i]
    }
    

When working with arrays, it is often necessary to delete all the elements
in an array and start over with a new list of elements
(see [Delete](#84-the-delete-statement)).
Instead of having
to repeat this loop everywhere that you need to clear out
an array, your program can just call `delarray()`.
(This guarantees portability.  The use of &lsquo;delete array&rsquo; to delete
the contents of an entire array is a relatively recent[62](#FOOT62)
addition to the POSIX standard.)

The following is an example of a recursive function.  It takes a string
as an input parameter and returns the string in reverse order.
Recursive functions must always have a test that stops the recursion.
In this case, the recursion terminates when the input string is
already empty:

    function rev(str)
    {
        if (str == "")
            return ""
    
        return (rev(substr(str, 2)) substr(str, 1, 1))
    }
    

If this function is in a file named rev.awk, it can be tested
this way:

    $ echo "Don't Panic!" |
    > gawk -e '{ print rev($0) }' -f rev.awk
    -| !cinaP t'noD
    

The C `ctime()` function takes a timestamp and returns it as a string,
formatted in a well-known fashion.
The following example uses the built-in `strftime()` function
(see [Time Functions](#915-time-functions))
to create an `awk` version of `ctime()`:

    # ctime.awk
    #
    # awk version of C ctime(3) function
    
    function ctime(ts,    format)
    {
        format = "%a %b %e %H:%M:%S %Z %Y"
    
        if (ts == 0)
            ts = systime()       # use current time as default
        return strftime(format, ts)
    }
    

You might think that `ctime()` could use `PROCINFO["strftime"]`
for its format string. That would be a mistake, because `ctime()` is
supposed to return the time formatted in a standard fashion, and user-level
code could have changed `PROCINFO["strftime"]`.

---

Next: [Return Statement](#924-the-return-statement), Previous: [Function Example](#922-function-definition-examples), Up: [User-defined](#92-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.3 Calling User-Defined Functions

*Calling a function* means causing the function to run and do its job.
A function call is an expression and its value is the value returned by
the function.

&bull; [Calling A Function](#9231-writing-a-function-call):  Don&rsquo;t use spaces.
&bull; [Variable Scope](#9232-controlling-variable-scope):  Controlling variable scope.
&bull; [Pass By Value/Reference](#9233-passing-function-arguments-by-value-or-by-reference):  Passing parameters.

---

Next: [Variable Scope](#9232-controlling-variable-scope), Up: [Function Caveats](#923-calling-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.3.1 Writing a Function Call

A function call consists of the function name followed by the arguments
in parentheses.  `awk` expressions are what you write in the
call for the arguments.  Each time the call is executed, these
expressions are evaluated, and the values become the actual arguments.  For
example, here is a call to `foo()` with three arguments (the first
being a string concatenation):

    foo(x y, "lose", 4 * z)
    

> CAUTION: Whitespace characters (spaces and TABs) are not allowed
> between the function name and the opening parenthesis of the argument list.
> If you write whitespace by mistake, `awk` might think that you mean
> to concatenate a variable with an expression in parentheses.  However, it
> notices that you used a function name and not a variable name, and reports
> an error.

---

Next: [Pass By Value/Reference](#9233-passing-function-arguments-by-value-or-by-reference), Previous: [Calling A Function](#9231-writing-a-function-call), Up: [Function Caveats](#923-calling-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.3.2 Controlling Variable Scope

Unlike in many languages,
there is no way to make a variable local to a `{` &hellip; `}` block in
`awk`, but you can make a variable local to a function. It is
good practice to do so whenever a variable is needed only in that
function.

To make a variable local to a function, simply declare the variable as
an argument after the actual function arguments
(see [Definition Syntax](#921-function-definition-syntax)).
Look at the following example, where variable
`i` is a global variable used by both functions `foo()` and
`bar()`:

    function bar()
    {
        for (i = 0; i < 3; i++)
            print "bar's i=" i
    }
    
    function foo(j)
    {
        i = j + 1
        print "foo's i=" i
        bar()
        print "foo's i=" i
    }
    
    BEGIN {
          i = 10
          print "top's i=" i
          foo(0)
          print "top's i=" i
    }
    

Running this script produces the following, because the `i` in
functions `foo()` and `bar()` and at the top level refer to the same
variable instance:

    top's i=10
    foo's i=1
    bar's i=0
    bar's i=1
    bar's i=2
    foo's i=3
    top's i=3
    

If you want `i` to be local to both `foo()` and `bar()`, do as
follows (the extra space before `i` is a coding convention to
indicate that `i` is a local variable, not an argument):

    function bar(    i)
    {
        for (i = 0; i < 3; i++)
            print "bar's i=" i
    }
    
    function foo(j,    i)
    {
        i = j + 1
        print "foo's i=" i
        bar()
        print "foo's i=" i
    }
    
    BEGIN {
          i = 10
          print "top's i=" i
          foo(0)
          print "top's i=" i
    }
    

Running the corrected script produces the following:

    top's i=10
    foo's i=1
    bar's i=0
    bar's i=1
    bar's i=2
    foo's i=1
    top's i=10
    

Besides scalar values (strings and numbers), you may also have
local arrays.  By using a parameter name as an array, `awk`
treats it as an array, and it is local to the function.
In addition, recursive calls create new arrays.
Consider this example:

    function some_func(p1,      a)
    {
        if (p1++ > 3)
            return
    

    
    
        a[p1] = p1
    
        some_func(p1)
    
        printf("At level %d, index %d %s found in a\n",
             p1, (p1 - 1), (p1 - 1) in a ? "is" : "is not")
        printf("At level %d, index %d %s found in a\n",
             p1, p1, p1 in a ? "is" : "is not")
        print ""
    }
    
    BEGIN {
        some_func(1)
    }
    

When run, this program produces the following output:

    At level 4, index 3 is not found in a
    At level 4, index 4 is found in a
    
    At level 3, index 2 is not found in a
    At level 3, index 3 is found in a
    
    At level 2, index 1 is not found in a
    At level 2, index 2 is found in a
    

---

Previous: [Variable Scope](#9232-controlling-variable-scope), Up: [Function Caveats](#923-calling-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.3.3 Passing Function Arguments by Value Or by Reference

In `awk`, when you declare a function, there is no way to
declare explicitly whether the arguments are passed *by value* or
*by reference*.

Instead, the passing convention is determined at runtime when
the function is called, according to the following rule:
if the argument is an array variable, then it is passed by reference.
Otherwise, the argument is passed by value.

Passing an argument by value means that when a function is called, it
is given a *copy* of the value of this argument.
The caller may use a variable as the expression for the argument, but
the called function does not know this&mdash;it only knows what value the
argument had.  For example, if you write the following code:

    foo = "bar"
    z = myfunc(foo)
    

then you should not think of the argument to `myfunc()` as being
&ldquo;the variable `foo`.&rdquo;  Instead, think of the argument as the
string value `"bar"`.
If the function `myfunc()` alters the values of its local variables,
this has no effect on any other variables.  Thus, if `myfunc()`
does this:

    function myfunc(str)
    {
       print str
       str = "zzz"
       print str
    }
    

to change its first argument variable `str`, it does *not*
change the value of `foo` in the caller.  The role of `foo` in
calling `myfunc()` ended when its value (`"bar"`) was computed.
If `str` also exists outside of `myfunc()`, the function body
cannot alter this outer value, because it is shadowed during the
execution of `myfunc()` and cannot be seen or changed from there.

However, when arrays are the parameters to functions, they are *not*
copied.  Instead, the array itself is made available for direct manipulation
by the function.  This is usually termed *call by reference*.
Changes made to an array parameter inside the body of a function *are*
visible outside that function.

> NOTE: Changing an array parameter inside a function
> can be very dangerous if you do not watch what you are doing.
> For example:
> 
> 
> 
>     function changeit(array, ind, nvalue)
>     {
>          array[ind] = nvalue
>     }
>     
>     BEGIN {
>         a[1] = 1; a[2] = 2; a[3] = 3
>         changeit(a, 2, "two")
>         printf "a[1] = %s, a[2] = %s, a[3] = %s\n",
>                 a[1], a[2], a[3]
>     }
>     
> 
> 
> 
> prints &lsquo;a[1] = 1, a[2] = two, a[3] = 3&rsquo;, because
> `changeit()` stores `"two"` in the second element of `a`.

Some `awk` implementations allow you to call a function that
has not been defined. They only report a problem at runtime, when the
program actually tries to call the function. For example:

    BEGIN {
        if (0)
            foo()
        else
            bar()
    }
    function bar() { &hellip; }
    # note that `foo' is not defined
    

Because the &lsquo;if&rsquo; statement will never be true, it is not really a
problem that `foo()` has not been defined.  Usually, though, it is a
problem if a program calls an undefined function.

If --lint is specified
(see [Options](#22-command-line-options)),
`gawk` reports calls to undefined functions.

Some `awk` implementations generate a runtime
error if you use either the `next` statement
or the `nextfile` statement
(see [Next Statement](#748-the-next-statement), and
see [Nextfile Statement](#749-the-nextfile-statement))
inside a user-defined function.
`gawk` does not have this limitation.

---

Next: [Dynamic Typing](#925-functions-and-their-effects-on-variable-typing), Previous: [Function Caveats](#923-calling-user-defined-functions), Up: [User-defined](#92-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.4 The `return` Statement

As seen in several earlier examples,
the body of a user-defined function can contain a `return` statement.
This statement returns control to the calling part of the `awk` program.  It
can also be used to return a value for use in the rest of the `awk`
program.  It looks like this:

    return [expression]
    

The expression part is optional.
Due most likely to an oversight, POSIX does not define what the return
value is if you omit the expression.  Technically speaking, this
makes the returned value undefined, and therefore, unpredictable.
In practice, though, all versions of `awk` simply return the
null string, which acts like zero if used in a numeric context.

A `return` statement without an expression is assumed at the end of
every function definition.  So, if control reaches the end of the function
body, then technically the function returns an unpredictable value.
In practice, it returns the empty string.  `awk`
does *not* warn you if you use the return value of such a function.

Sometimes, you want to write a function for what it does, not for
what it returns.  Such a function corresponds to a `void` function
in C, C++, or Java, or to a `procedure` in Ada.  Thus, it may be appropriate to not
return any value; simply bear in mind that you should not be using the
return value of such a function.

The following is an example of a user-defined function that returns a value
for the largest number among the elements of an array:

    function maxelt(vec,   i, ret)
    {
         for (i in vec) {
              if (ret == "" || vec[i] > ret)
                   ret = vec[i]
         }
         return ret
    }
    

You call `maxelt()` with one argument, which is an array name.  The local
variables `i` and `ret` are not intended to be arguments;
there is nothing to stop you from passing more than one argument
to `maxelt()` but the results would be strange.  The extra space before
`i` in the function parameter list indicates that `i` and
`ret` are local variables.
You should follow this convention when defining functions.

The following program uses the `maxelt()` function.  It loads an
array, calls `maxelt()`, and then reports the maximum number in that
array:

    function maxelt(vec,   i, ret)
    {
         for (i in vec) {
              if (ret == "" || vec[i] > ret)
                   ret = vec[i]
         }
         return ret
    }
    
    

    # Load all fields of each record into nums.
    {
         for(i = 1; i <= NF; i++)
              nums[NR, i] = $i
    }
    

    
    
    END {
         print maxelt(nums)
    }
    

Given the following input:

     1 5 23 8 16
    44 3 5 2 8 26
    256 291 1396 2962 100
    -6 467 998 1101
    99385 11 0 225
    

the program reports (predictably) that 99,385 is the largest value
in the array.

---

Previous: [Return Statement](#924-the-return-statement), Up: [User-defined](#92-user-defined-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 9.2.5 Functions and Their Effects on Variable Typing

`awk` is a very fluid language.
It is possible that `awk` can&rsquo;t tell if an identifier
represents a scalar variable or an array until runtime.
Here is an annotated sample program:

    function foo(a)
    {
        a[1] = 1   # parameter is an array
    }
    
    BEGIN {
        b = 1
        foo(b)  # invalid: fatal type mismatch
    
        foo(x)  # x uninitialized, becomes an array dynamically
        x = 1   # now not allowed, runtime error
    }
    

In this example, the first call to `foo()` generates
a fatal error, so `awk` will not report the second
error. If you comment out that call, though, then `awk`
does report the second error.

Usually, such things aren&rsquo;t a big issue, but it&rsquo;s worth
being aware of them.

---

Next: [Functions Summary](#94-summary), Previous: [User-defined](#92-user-defined-functions), Up: [Functions](#9-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 9.3 Indirect Function Calls

This section describes an advanced, `gawk`-specific extension.

Often, you may wish to defer the choice of function to call until runtime.
For example, you may have different kinds of records, each of which
should be processed differently.

Normally, you would have to use a series of `if`-`else`
statements to decide which function to call.  By using *indirect*
function calls, you can specify the name of the function to call as a
string variable, and then call the function.  Let&rsquo;s look at an example.

Suppose you have a file with your test scores for the classes you
are taking, and
you wish to get the sum and the average of
your test scores.
The first field is the class name. The following fields
are the functions to call to process the data, up to a &ldquo;marker&rdquo;
field &lsquo;data:&rsquo;.  Following the marker, to the end of the record,
are the various numeric test scores.

Here is the initial file:

    Biology_101 sum average data: 87.0 92.4 78.5 94.9
    Chemistry_305 sum average data: 75.2 98.3 94.7 88.2
    English_401 sum average data: 100.0 95.6 87.1 93.4
    

To process the data, you might write initially:

    {
        class = $1
        for (i = 2; $i != "data:"; i++) {
            if ($i == "sum")
                sum()   # processes the whole record
            else if ($i == "average")
                average()
            &hellip;           # and so on
        }
    }
    

This style of programming works, but can be awkward.  With *indirect*
function calls, you tell `gawk` to use the *value* of a
variable as the *name* of the function to call.

The syntax is similar to that of a regular function call: an identifier
immediately followed by an opening parenthesis, any arguments, and then
a closing parenthesis, with the addition of a leading &lsquo;@&rsquo;
character:

    the_func = "sum"
    result = @the_func()   # calls the sum() function
    

Here is a full program that processes the previously shown data,
using indirect function calls:

    # indirectcall.awk --- Demonstrate indirect function calls
    
    # average --- return the average of the values in fields $first - $last
    
    function average(first, last,   sum, i)
    {
        sum = 0;
        for (i = first; i <= last; i++)
            sum += $i
    
        return sum / (last - first + 1)
    }
    
    # sum --- return the sum of the values in fields $first - $last
    
    function sum(first, last,   ret, i)
    {
        ret = 0;
        for (i = first; i <= last; i++)
            ret += $i
    
        return ret
    }
    

These two functions expect to work on fields; thus, the parameters
`first` and `last` indicate where in the fields to start and end.
Otherwise, they perform the expected computations and are not unusual:

    # For each record, print the class name and the requested statistics
    {
        class_name = $1
        gsub(/_/, " ", class_name)  # Replace _ with spaces
    
        # find start
        for (i = 1; i <= NF; i++) {
            if ($i == "data:") {
                start = i + 1
                break
            }
        }
    
        printf("%s:\n", class_name)
        for (i = 2; $i != "data:"; i++) {
            the_function = $i
            printf("\t%s: <%s>\n", $i, @the_function(start, NF) "")
        }
        print ""
    }
    

This is the main processing for each record. It prints the class name (with
underscores replaced with spaces). It then finds the start of the actual data,
saving it in `start`.
The last part of the code loops through each function name (from `$2` up to
the marker, &lsquo;data:&rsquo;), calling the function named by the field. The indirect
function call itself occurs as a parameter in the call to `printf`.
(The `printf` format string uses &lsquo;%s&rsquo; as the format specifier so that we
can use functions that return strings, as well as numbers. Note that the result
from the indirect call is concatenated with the empty string, in order to force
it to be a string value.)

Here is the result of running the program:

    $ gawk -f indirectcall.awk class_data1
    -| Biology 101:
    -|     sum: <352.8>
    -|     average: <88.2>
    -|
    -| Chemistry 305:
    -|     sum: <356.4>
    -|     average: <89.1>
    -|
    -| English 401:
    -|     sum: <376.1>
    -|     average: <94.025>
    

The ability to use indirect function calls is more powerful than you may
think at first.  The C and C++ languages provide &ldquo;function pointers,&rdquo; which
are a mechanism for calling a function chosen at runtime.  One of the most
well-known uses of this ability is the C `qsort()` function, which sorts
an array using the famous &ldquo;quicksort&rdquo; algorithm
(see [the Wikipedia article](https://en.wikipedia.org/wiki/Quicksort)
for more information).  To use this function, you supply a pointer to a comparison
function.  This mechanism allows you to sort arbitrary data in an arbitrary
fashion.

We can do something similar using `gawk`, like this:

    # quicksort.awk --- Quicksort algorithm, with user-supplied
    #                   comparison function
    
    # quicksort --- C.A.R. Hoare's quicksort algorithm. See Wikipedia
    #               or almost any algorithms or computer science text.
    
    function quicksort(data, left, right, less_than,    i, last)
    {
        if (left >= right)  # do nothing if array contains fewer
            return          # than two elements
    
        quicksort_swap(data, left, int((left + right) / 2))
        last = left
        for (i = left + 1; i <= right; i++)
            if (@less_than(data[i], data[left]))
                quicksort_swap(data, ++last, i)
        quicksort_swap(data, left, last)
        quicksort(data, left, last - 1, less_than)
        quicksort(data, last + 1, right, less_than)
    }
    
    # quicksort_swap --- helper function for quicksort, should really be inline
    
    function quicksort_swap(data, i, j,      temp)
    {
        temp = data[i]
        data[i] = data[j]
        data[j] = temp
    }
    

The `quicksort()` function receives the `data` array, the starting and ending
indices to sort (`left` and `right`), and the name of a function that
performs a &ldquo;less than&rdquo; comparison.  It then implements the quicksort algorithm.

To make use of the sorting function, we return to our previous example. The
first thing to do is write some comparison functions:

    # num_lt --- do a numeric less than comparison
    
    function num_lt(left, right)
    {
        return ((left + 0) < (right + 0))
    }
    

    
    
    # num_ge --- do a numeric greater than or equal to comparison
    
    function num_ge(left, right)
    {
        return ((left + 0) >= (right + 0))
    }
    

The `num_ge()` function is needed to perform a descending sort; when used
to perform a &ldquo;less than&rdquo; test, it actually does the opposite (greater than
or equal to), which yields data sorted in descending order.

Next comes a sorting function.  It is parameterized with the starting and
ending field numbers and the comparison function. It builds an array with
the data and calls `quicksort()` appropriately, and then formats the
results as a single string:

    # do_sort --- sort the data according to `compare'
    #             and return it as a string
    
    function do_sort(first, last, compare,      data, i, retval)
    {
        delete data
        for (i = 1; first <= last; first++) {
            data[i] = $first
            i++
        }
    
        quicksort(data, 1, i-1, compare)
    
        retval = data[1]
        for (i = 2; i in data; i++)
            retval = retval " " data[i]
    
        return retval
    }
    

Finally, the two sorting functions call `do_sort()`, passing in the
names of the two comparison functions:

    # sort --- sort the data in ascending order and return it as a string
    
    function sort(first, last)
    {
        return do_sort(first, last, "num_lt")
    }
    

    # rsort --- sort the data in descending order and return it as a string
    
    function rsort(first, last)
    {
        return do_sort(first, last, "num_ge")
    }
    

Here is an extended version of the data file:

    Biology_101 sum average sort rsort data: 87.0 92.4 78.5 94.9
    Chemistry_305 sum average sort rsort data: 75.2 98.3 94.7 88.2
    English_401 sum average sort rsort data: 100.0 95.6 87.1 93.4
    

Finally, here are the results when the enhanced program is run:

    $ gawk -f quicksort.awk -f indirectcall.awk class_data2
    -| Biology 101:
    -|     sum: <352.8>
    -|     average: <88.2>
    -|     sort: <78.5 87.0 92.4 94.9>
    -|     rsort: <94.9 92.4 87.0 78.5>
    -|
    -| Chemistry 305:
    -|     sum: <356.4>
    -|     average: <89.1>
    -|     sort: <75.2 88.2 94.7 98.3>
    -|     rsort: <98.3 94.7 88.2 75.2>
    -|
    -| English 401:
    -|     sum: <376.1>
    -|     average: <94.025>
    -|     sort: <87.1 93.4 95.6 100.0>
    -|     rsort: <100.0 95.6 93.4 87.1>
    

Another example where indirect functions calls are useful can be found in
processing arrays. This is described in [Walking Arrays](#107-traversing-arrays-of-arrays).

Remember that you must supply a leading &lsquo;@&rsquo; in front of an indirect function call.

Starting with version 4.1.2 of `gawk`, indirect function
calls may also be used with built-in functions and with extension functions
(see [Dynamic Extensions](#Dynamic-Extensions)). There are some limitations when calling
built-in functions indirectly, as follows.

-  You cannot pass a regular expression constant to a built-in function
through an indirect function call.[63](#FOOT63) This applies to the `sub()`,
`gsub()`, `gensub()`, `match()`, `split()` and
`patsplit()` functions.

-  If calling `sub()` or `gsub()`, you may only pass two arguments,
since those functions are unusual in that they update their third argument.
This means that `$0` will be updated.

`gawk` does its best to make indirect function calls efficient.
For example, in the following case:

    for (i = 1; i <= n; i++)
        @the_func()
    

`gawk` looks up the actual function to call only once.

---

Previous: [Indirect Calls](#93-indirect-function-calls), Up: [Functions](#9-functions)   [[Contents](#table-of-contents)][[Index](#Index)]

### 9.4 Summary

- `awk` provides built-in functions and lets you define your own
functions.

-  POSIX `awk` provides three kinds of built-in functions: numeric,
string, and I/O.  `gawk` provides functions that sort arrays, work
with values representing time, do bit manipulation, determine variable
type (array versus scalar), and internationalize and localize programs.
`gawk` also provides several extensions to some of standard
functions, typically in the form of additional arguments.

-  Functions accept zero or more arguments and return a value.  The
expressions that provide the argument values are completely evaluated
before the function is called. Order of evaluation is not defined.
The return value can be ignored.

-  The handling of backslash in `sub()` and `gsub()` is not simple.
It is more straightforward in `gawk`&rsquo;s `gensub()` function,
but that function still requires care in its use.

-  User-defined functions provide important capabilities but come with
some syntactic inelegancies. In a function call, there cannot be any
space between the function name and the opening left parenthesis of the
argument list.  Also, there is no provision for local variables, so the
convention is to add extra parameters, and to separate them visually
from the real parameters by extra whitespace.

-  User-defined functions may call other user-defined (and built-in)
functions and may call themselves recursively. Function parameters
&ldquo;hide&rdquo; any global variables of the same names.
You cannot use the name of a reserved variable (such as `ARGC`)
as the name of a parameter in user-defined functions.

-  Scalar values are passed to user-defined functions by value. Array
parameters are passed by reference; any changes made by the function to
array parameters are thus visible after the function has returned.

-  Use the `return` statement to return from a user-defined function.
An optional expression becomes the function&rsquo;s return value.  Only scalar
values may be returned by a function.

-  If a variable that has never been used is passed to a user-defined
function, how that function treats the variable can set its nature:
either scalar or array.

- `gawk` provides indirect function calls using a special syntax.
By setting a variable to the name of a function, you can
determine at runtime what function will be called at that point in the
program. This is equivalent to function pointers in C and C++.

---

Next: [Sample Programs](#11-practical-awk-programs), Previous: [Functions](#9-functions), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

# Part II:
Problem Solving with `awk`

---

## 10 A Library of `awk` Functions

[User-defined](#92-user-defined-functions) describes how to write
your own `awk` functions.  Writing functions is important, because
it allows you to encapsulate algorithms and program tasks in a single
place.  It simplifies programming, making program development more
manageable and making programs more readable.

In their seminal 1976 book, Software Tools,[64](#FOOT64) Brian Kernighan
and P.J. Plauger wrote:

> Good Programming is not learned from generalities, but by seeing how
> significant programs can be made clean, easy to read, easy to maintain and
> modify, human-engineered, efficient and reliable, by the application of
> common sense and good programming practices.  Careful study and imitation
> of good programs leads to better writing.

In fact, they felt this idea was so important that they placed this
statement on the cover of their book.  Because we believe strongly
that their statement is correct, this chapter and [Sample Programs](#11-practical-awk-programs), provide a good-sized body of code for you to read and, we hope,
to learn from.

This chapter presents a library of useful `awk` functions.
Many of the sample programs presented later in this Web page
use these functions.
The functions are presented here in a progression from simple to complex.

[Extract Program](#1137-extracting-programs-from-texinfo-source-files)
presents a program that you can use to extract the source code for
these example library functions and programs from the Texinfo source
for this Web page.
(This has already been done as part of the `gawk` distribution.)

If you have written one or more useful, general-purpose `awk` functions
and would like to contribute them to the `awk` user community, see
[How To Contribute](#how-to-contribute), for more information.

The programs in this chapter and in
[Sample Programs](#11-practical-awk-programs),
freely use `gawk`-specific features.
Rewriting these programs for different implementations of `awk`
is pretty straightforward:

-  Diagnostic error messages are sent to /dev/stderr.
Use &lsquo;| "cat 1>&2"&rsquo; instead of &lsquo;> "/dev/stderr"&rsquo; if your system
does not have a /dev/stderr, or if you cannot use `gawk`.

-  A number of programs use `nextfile`
(see [Nextfile Statement](#749-the-nextfile-statement))
to skip any remaining input in the input file.

- 
Finally, some of the programs choose to ignore upper- and lowercase
distinctions in their input. They do so by assigning one to `IGNORECASE`.
You can achieve almost the same effect[65](#FOOT65) by adding the following rule to the
beginning of the program:

    # ignore case
    { $0 = tolower($0) }
    

Also, verify that all regexp and string constants used in
comparisons use only lowercase letters.

&bull; [Library Names](#101-naming-library-function-global-variables):  How to best name private global variables in
                                library functions.
&bull; [General Functions](#102-general-programming):  Functions that are of general use.
&bull; [Data File Management](#103-data-file-management):  Functions for managing command-line data
                                files.
&bull; [Getopt Function](#104-processing-command-line-options):  A function for processing command-line
                                arguments.
&bull; [Passwd Functions](#105-reading-the-user-database):  Functions for getting user information.
&bull; [Group Functions](#106-reading-the-group-database):  Functions for getting group information.
&bull; [Walking Arrays](#107-traversing-arrays-of-arrays):  A function to walk arrays of arrays.
&bull; [Library Functions Summary]((#10-a-library-of-awk-functions)-Summary):  Summary of library functions.
&bull; [Library Exercises](#109-exercises):  Exercises.

---

Next: [General Functions](#102-general-programming), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.1 Naming Library Function Global Variables

Due to the way the `awk` language evolved, variables are either
*global* (usable by the entire program) or *local* (usable just by
a specific function).  There is no intermediate state analogous to
`static` variables in C.

Library functions often need to have global variables that they can use to
preserve state information between calls to the function&mdash;for example,
`getopt()`&rsquo;s variable `_opti`
(see [Getopt Function](#104-processing-command-line-options)).
Such variables are called *private*, as the only functions that need to
use them are the ones in the library.

When writing a library function, you should try to choose names for your
private variables that will not conflict with any variables used by
either another library function or a user&rsquo;s main program.  For example, a
name like `i` or `j` is not a good choice, because user programs
often use variable names like these for their own purposes.

The example programs shown in this chapter all start the names of their
private variables with an underscore (&lsquo;_&rsquo;).  Users generally don&rsquo;t use
leading underscores in their variable names, so this convention immediately
decreases the chances that the variable names will be accidentally shared
with the user&rsquo;s program.

In addition, several of the library functions use a prefix that helps
indicate what function or set of functions use the variables&mdash;for example,
`_pw_byname()` in the user database routines
(see [Passwd Functions](#105-reading-the-user-database)).
This convention is recommended, as it even further decreases the
chance of inadvertent conflict among variable names.  Note that this
convention is used equally well for variable names and for private
function names.[66](#FOOT66)

As a final note on variable naming, if a function makes global variables
available for use by a main program, it is a good convention to start those
variables&rsquo; names with a capital letter&mdash;for
example, `getopt()`&rsquo;s `Opterr` and `Optind` variables
(see [Getopt Function](#104-processing-command-line-options)).
The leading capital letter indicates that it is global, while the fact that
the variable name is not all capital letters indicates that the variable is
not one of `awk`&rsquo;s predefined variables, such as `FS`.

It is also important that *all* variables in library
functions that do not need to save state are, in fact, declared
local.[67](#FOOT67) If this is not done, the variables
could accidentally be used in the user&rsquo;s program, leading to bugs that
are very difficult to track down:

    function lib_func(x, y,    l1, l2)
    {
        &hellip;
        # some_var should be local but by oversight is not
        use variable some_var
        &hellip;
    }
    

A different convention, common in the Tcl community, is to use a single
associative array to hold the values needed by the library function(s), or
&ldquo;package.&rdquo;  This significantly decreases the number of actual global names
in use.  For example, the functions described in
[Passwd Functions](#105-reading-the-user-database)
might have used array elements `PW_data["inited"]`, `PW_data["total"]`,
`PW_data["count"]`, and `PW_data["awklib"]`, instead of
`_pw_inited`, `_pw_awklib`, `_pw_total`,
and `_pw_count`.

The conventions presented in this section are exactly
that: conventions. You are not required to write your programs this
way&mdash;we merely recommend that you do so.

---

Next: [Data File Management](#103-data-file-management), Previous: [Library Names](#101-naming-library-function-global-variables), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.2 General Programming

This section presents a number of functions that are of general
programming use.

&bull; [Strtonum Function](#1021-converting-strings-to-numbers):  A replacement for the built-in
                                `strtonum()` function.
&bull; [Assert Function](#1022-assertions):  A function for assertions in `awk`
                                programs.
&bull; [Round Function](#1023-rounding-numbers):  A function for rounding if `sprintf()`
                                does not do it correctly.
&bull; [Cliff Random Function](#1024-the-cliff-random-number-generator):  The Cliff Random Number Generator.
&bull; [Ordinal Functions](#1025-translating-between-characters-and-numbers):  Functions for using characters as numbers and
                                vice versa.
&bull; [Join Function](#1026-merging-an-array-into-a-string):  A function to join an array into a string.
&bull; [Getlocaltime Function](#1027-managing-the-time-of-day):  A function to get formatted times.
&bull; [Readfile Function](#1028-reading-a-whole-file-at-once):  A function to read an entire file at once.
&bull; [Shell Quoting](#1029-quoting-strings-to-pass-to-the-shell):  A function to quote strings for the shell.

---

Next: [Assert Function](#1022-assertions), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.1 Converting Strings to Numbers

The `strtonum()` function (see [String Functions](#913-string-manipulation-functions))
is a `gawk` extension.  The following function
provides an implementation for other versions of `awk`:

    # mystrtonum --- convert string to number
    
    function mystrtonum(str,        ret, n, i, k, c)
    {
        if (str ~ /^0[0-7]*$/) {
            # octal
            n = length(str)
            ret = 0
            for (i = 1; i <= n; i++) {
                c = substr(str, i, 1)
                # index() returns 0 if c not in string,
                # includes c == "0"
                k = index("1234567", c)
    
                ret = ret * 8 + k
            }
        } else if (str ~ /^0[xX][[:xdigit:]]+$/) {
            # hexadecimal
            str = substr(str, 3)    # lop off leading 0x
            n = length(str)
            ret = 0
            for (i = 1; i <= n; i++) {
                c = substr(str, i, 1)
                c = tolower(c)
                # index() returns 0 if c not in string,
                # includes c == "0"
                k = index("123456789abcdef", c)
    
                ret = ret * 16 + k
            }
        } else if (str ~ \
      /^[-+]?([0-9]+([.][0-9]*([Ee][0-9]+)?)?|([.][0-9]+([Ee][-+]?[0-9]+)?))$/) {
            # decimal number, possibly floating point
            ret = str + 0
        } else
            ret = "NOT-A-NUMBER"
    
        return ret
    }
    
    # BEGIN {     # gawk test harness
    #     a[1] = "25"
    #     a[2] = ".31"
    #     a[3] = "0123"
    #     a[4] = "0xdeadBEEF"
    #     a[5] = "123.45"
    #     a[6] = "1.e3"
    #     a[7] = "1.32"
    #     a[8] = "1.32E2"
    #
    #     for (i = 1; i in a; i++)
    #         print a[i], strtonum(a[i]), mystrtonum(a[i])
    # }
    

The function first looks for C-style octal numbers (base 8).
If the input string matches a regular expression describing octal
numbers, then `mystrtonum()` loops through each character in the
string.  It sets `k` to the index in `"1234567"` of the current
octal digit.
The return value will either be the same number as the digit, or zero
if the character is not there, which will be true for a &lsquo;0&rsquo;.
This is safe, because the regexp test in the `if` ensures that
only octal values are converted.

Similar logic applies to the code that checks for and converts a
hexadecimal value, which starts with &lsquo;0x&rsquo; or &lsquo;0X&rsquo;.
The use of `tolower()` simplifies the computation for finding
the correct numeric value for each hexadecimal digit.

Finally, if the string matches the (rather complicated) regexp for a
regular decimal integer or floating-point number, the computation
&lsquo;ret = str + 0&rsquo; lets `awk` convert the value to a
number.

A commented-out test program is included, so that the function can
be tested with `gawk` and the results compared to the built-in
`strtonum()` function.

---

Next: [Round Function](#1023-rounding-numbers), Previous: [Strtonum Function](#1021-converting-strings-to-numbers), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.2 Assertions

When writing large programs, it is often useful to know
that a condition or set of conditions is true.  Before proceeding with a
particular computation, you make a statement about what you believe to be
the case.  Such a statement is known as an
*assertion*.  The C language provides an `<assert.h>` header file
and corresponding `assert()` macro that a programmer can use to make
assertions.  If an assertion fails, the `assert()` macro arranges to
print a diagnostic message describing the condition that should have
been true but was not, and then it kills the program.  In C, using
`assert()` looks this:

    #include <assert.h>
    
    int myfunc(int a, double b)
    {
         assert(a <= 5 && b >= 17.1);
         &hellip;
    }
    

If the assertion fails, the program prints a message similar to this:

    prog.c:5: assertion failed: a <= 5 && b >= 17.1
    

The C language makes it possible to turn the condition into a string for use
in printing the diagnostic message.  This is not possible in `awk`, so
this `assert()` function also requires a string version of the condition
that is being tested.
Following is the function:

    # assert --- assert that a condition is true. Otherwise, exit.
    
    function assert(condition, string)
    {
        if (! condition) {
            printf("%s:%d: assertion failed: %s\n",
                FILENAME, FNR, string) > "/dev/stderr"
            _assert_exit = 1
            exit 1
        }
    }
    
    

    END {
        if (_assert_exit)
            exit 1
    }
    

The `assert()` function tests the `condition` parameter. If it
is false, it prints a message to standard error, using the `string`
parameter to describe the failed condition.  It then sets the variable
`_assert_exit` to one and executes the `exit` statement.
The `exit` statement jumps to the `END` rule. If the `END`
rule finds `_assert_exit` to be true, it exits immediately.

The purpose of the test in the `END` rule is to
keep any other `END` rules from running.  When an assertion fails, the
program should exit immediately.
If no assertions fail, then `_assert_exit` is still
false when the `END` rule is run normally, and the rest of the
program&rsquo;s `END` rules execute.
For all of this to work correctly, assert.awk must be the
first source file read by `awk`.
The function can be used in a program in the following way:

    function myfunc(a, b)
    {
         assert(a <= 5 && b >= 17.1, "a <= 5 && b >= 17.1")
         &hellip;
    }
    

If the assertion fails, you see a message similar to the following:

    mydata:1357: assertion failed: a <= 5 && b >= 17.1
    

There is a small problem with this version of `assert()`.
An `END` rule is automatically added
to the program calling `assert()`.  Normally, if a program consists
of just a `BEGIN` rule, the input files and/or standard input are
not read. However, now that the program has an `END` rule, `awk`
attempts to read the input data files or standard input
(see [Using BEGIN/END](#7141-startup-and-cleanup-actions)),
most likely causing the program to hang as it waits for input.

There is a simple workaround to this:
make sure that such a `BEGIN` rule always ends
with an `exit` statement.

---

Next: [Cliff Random Function](#1024-the-cliff-random-number-generator), Previous: [Assert Function](#1022-assertions), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.3 Rounding Numbers

The way `printf` and `sprintf()`
(see [Printf](#55-using-printf-statements-for-fancier-printing))
perform rounding often depends upon the system&rsquo;s C `sprintf()`
subroutine.  On many machines, `sprintf()` rounding is *unbiased*,
which means it doesn&rsquo;t always round a trailing .5 up, contrary
to naive expectations.  In unbiased rounding, .5 rounds to even,
rather than always up, so 1.5 rounds to 2 but 4.5 rounds to 4.  This means
that if you are using a format that does rounding (e.g., `"%.0f"`),
you should check what your system does.  The following function does
traditional rounding; it might be useful if your `awk`&rsquo;s `printf`
does unbiased rounding:

    # round.awk --- do normal rounding
    
    function round(x,   ival, aval, fraction)
    {
       ival = int(x)    # integer part, int() truncates
    
       # see if fractional part
       if (ival == x)   # no fraction
          return ival   # ensure no decimals
    
       if (x < 0) {
          aval = -x     # absolute value
          ival = int(aval)
          fraction = aval - ival
          if (fraction >= .5)
             return int(x) - 1   # -2.5 --> -3
          else
             return int(x)       # -2.3 --> -2
       } else {
          fraction = x - ival
          if (fraction >= .5)
             return ival + 1
          else
             return ival
       }
    }
    

    # test harness
    # { print $0, round($0) }
    

---

Next: [Ordinal Functions](#1025-translating-between-characters-and-numbers), Previous: [Round Function](#1023-rounding-numbers), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.4 The Cliff Random Number Generator

The
[Cliff random number generator](http://mathworld.wolfram.com/CliffRandomNumberGenerator.html)
is a very simple random number generator that &ldquo;passes the noise sphere test
for randomness by showing no structure.&rdquo;
It is easily programmed, in less than 10 lines of `awk` code:

    # cliff_rand.awk --- generate Cliff random numbers
    
    BEGIN { _cliff_seed = 0.1 }
    
    function cliff_rand()
    {
        _cliff_seed = (100 * log(_cliff_seed)) % 1
        if (_cliff_seed < 0)
            _cliff_seed = - _cliff_seed
        return _cliff_seed
    }
    

This algorithm requires an initial &ldquo;seed&rdquo; of 0.1.  Each new value
uses the current seed as input for the calculation.
If the built-in `rand()` function
(see [Numeric Functions](#912-numeric-functions))
isn&rsquo;t random enough, you might try using this function instead.

---

Next: [Join Function](#1026-merging-an-array-into-a-string), Previous: [Cliff Random Function](#1024-the-cliff-random-number-generator), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.5 Translating Between Characters and Numbers

One commercial implementation of `awk` supplies a built-in function,
`ord()`, which takes a character and returns the numeric value for that
character in the machine&rsquo;s character set.  If the string passed to
`ord()` has more than one character, only the first one is used.

The inverse of this function is `chr()` (from the function of the same
name in Pascal), which takes a number and returns the corresponding character.
Both functions are written very nicely in `awk`; there is no real
reason to build them into the `awk` interpreter:

    # ord.awk --- do ord and chr
    
    # Global identifiers:
    #    _ord_:        numerical values indexed by characters
    #    _ord_init:    function to initialize _ord_
    
    BEGIN    { _ord_init() }
    
    function _ord_init(    low, high, i, t)
    {
        low = sprintf("%c", 7) # BEL is ascii 7
        if (low == "\a") {    # regular ascii
            low = 0
            high = 127
        } else if (sprintf("%c", 128 + 7) == "\a") {
            # ascii, mark parity
            low = 128
            high = 255
        } else {        # ebcdic(!)
            low = 0
            high = 255
        }
    
        for (i = low; i <= high; i++) {
            t = sprintf("%c", i)
            _ord_[t] = i
        }
    }
    

Some explanation of the numbers used by `_ord_init()` is worthwhile.
The most prominent character set in use today is ASCII.[68](#FOOT68)
Although an
8-bit byte can hold 256 distinct values (from 0 to 255), ASCII only
defines characters that use the values from 0 to 127.[69](#FOOT69)
In the now distant past,
at least one minicomputer manufacturer
used ASCII, but with mark parity, meaning that the leftmost bit in the byte
is always 1.  This means that on those systems, characters
have numeric values from 128 to 255.
Finally, large mainframe systems use the EBCDIC character set, which
uses all 256 values.
There are other character sets in use on some older systems, but
they are not really worth worrying about:

    function ord(str,    c)
    {
        # only first character is of interest
        c = substr(str, 1, 1)
        return _ord_[c]
    }
    
    function chr(c)
    {
        # force c to be numeric by adding 0
        return sprintf("%c", c + 0)
    }
    
    #### test code ####
    # BEGIN {
    #    for (;;) {
    #        printf("enter a character: ")
    #        if (getline var <= 0)
    #            break
    #        printf("ord(%s) = %d\n", var, ord(var))
    #    }
    # }
    

An obvious improvement to these functions is to move the code for the
`_ord_init` function into the body of the `BEGIN` rule.  It was
written this way initially for ease of development.
There is a &ldquo;test program&rdquo; in a `BEGIN` rule, to test the
function.  It is commented out for production use.

---

Next: [Getlocaltime Function](#1027-managing-the-time-of-day), Previous: [Ordinal Functions](#1025-translating-between-characters-and-numbers), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.6 Merging an Array into a String

When doing string processing, it is often useful to be able to join
all the strings in an array into one long string.  The following function,
`join()`, accomplishes this task.  It is used later in several of
the application programs
(see [Sample Programs](#11-practical-awk-programs)).

Good function design is important; this function needs to be general, but it
should also have a reasonable default behavior.  It is called with an array
as well as the beginning and ending indices of the elements in the array to be
merged.  This assumes that the array indices are numeric&mdash;a reasonable
assumption, as the array was likely created with `split()`
(see [String Functions](#913-string-manipulation-functions)):

    # join.awk --- join an array into a string
    
    function join(array, start, end, sep,    result, i)
    {
        if (sep == "")
           sep = " "
        else if (sep == SUBSEP) # magic value
           sep = ""
        result = array[start]
        for (i = start + 1; i <= end; i++)
            result = result sep array[i]
        return result
    }
    

An optional additional argument is the separator to use when joining the
strings back together.  If the caller supplies a nonempty value,
`join()` uses it; if it is not supplied, it has a null
value.  In this case, `join()` uses a single space as a default
separator for the strings.  If the value is equal to `SUBSEP`,
then `join()` joins the strings with no separator between them.
`SUBSEP` serves as a &ldquo;magic&rdquo; value to indicate that there should
be no separation between the component strings.[70](#FOOT70)

---

Next: [Readfile Function](#1028-reading-a-whole-file-at-once), Previous: [Join Function](#1026-merging-an-array-into-a-string), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.7 Managing the Time of Day

The `systime()` and `strftime()` functions described in
[Time Functions](#915-time-functions)
provide the minimum functionality necessary for dealing with the time of day
in human-readable form.  Although `strftime()` is extensive, the control
formats are not necessarily easy to remember or intuitively obvious when
reading a program.

The following function, `getlocaltime()`, populates a user-supplied array
with preformatted time information.  It returns a string with the current
time formatted in the same way as the `date` utility:

    # getlocaltime.awk --- get the time of day in a usable format
    
    # Returns a string in the format of output of date(1)
    # Populates the array argument time with individual values:
    #    time["second"]       -- seconds (0 - 59)
    #    time["minute"]       -- minutes (0 - 59)
    #    time["hour"]         -- hours (0 - 23)
    #    time["althour"]      -- hours (0 - 12)
    #    time["monthday"]     -- day of month (1 - 31)
    #    time["month"]        -- month of year (1 - 12)
    #    time["monthname"]    -- name of the month
    #    time["shortmonth"]   -- short name of the month
    #    time["year"]         -- year modulo 100 (0 - 99)
    #    time["fullyear"]     -- full year
    #    time["weekday"]      -- day of week (Sunday = 0)
    #    time["altweekday"]   -- day of week (Monday = 0)
    #    time["dayname"]      -- name of weekday
    #    time["shortdayname"] -- short name of weekday
    #    time["yearday"]      -- day of year (0 - 365)
    #    time["timezone"]     -- abbreviation of timezone name
    #    time["ampm"]         -- AM or PM designation
    #    time["weeknum"]      -- week number, Sunday first day
    #    time["altweeknum"]   -- week number, Monday first day
    
    function getlocaltime(time,    ret, now, i)
    {
        # get time once, avoids unnecessary system calls
        now = systime()
    
        # return date(1)-style output
        ret = strftime("%a %b %e %H:%M:%S %Z %Y", now)
    
        # clear out target array
        delete time
    
        # fill in values, force numeric values to be
        # numeric by adding 0
        time["second"]       = strftime("%S", now) + 0
        time["minute"]       = strftime("%M", now) + 0
        time["hour"]         = strftime("%H", now) + 0
        time["althour"]      = strftime("%I", now) + 0
        time["monthday"]     = strftime("%d", now) + 0
        time["month"]        = strftime("%m", now) + 0
        time["monthname"]    = strftime("%B", now)
        time["shortmonth"]   = strftime("%b", now)
        time["year"]         = strftime("%y", now) + 0
        time["fullyear"]     = strftime("%Y", now) + 0
        time["weekday"]      = strftime("%w", now) + 0
        time["altweekday"]   = strftime("%u", now) + 0
        time["dayname"]      = strftime("%A", now)
        time["shortdayname"] = strftime("%a", now)
        time["yearday"]      = strftime("%j", now) + 0
        time["timezone"]     = strftime("%Z", now)
        time["ampm"]         = strftime("%p", now)
        time["weeknum"]      = strftime("%U", now) + 0
        time["altweeknum"]   = strftime("%W", now) + 0
    
        return ret
    }
    

The string indices are easier to use and read than the various formats
required by `strftime()`.  The `alarm` program presented in
[Alarm Program](#1132-an-alarm-clock-program)
uses this function.
A more general design for the `getlocaltime()` function would have
allowed the user to supply an optional timestamp value to use instead
of the current time.

---

Next: [Shell Quoting](#1029-quoting-strings-to-pass-to-the-shell), Previous: [Getlocaltime Function](#1027-managing-the-time-of-day), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.8 Reading a Whole File at Once

Often, it is convenient to have the entire contents of a file available
in memory as a single string. A straightforward but naive way to
do that might be as follows:

    function readfile1(file,    tmp, contents)
    {
        if ((getline tmp < file) < 0)
            return
    
        contents = tmp RT
        while ((getline tmp < file) > 0)
            contents = contents tmp RT
    
        close(file)
        return contents
    }
    

This function reads from `file` one record at a time, building
up the full contents of the file in the local variable `contents`.
It works, but is not necessarily efficient.

The following function, based on a suggestion by Denis Shirokov,
reads the entire contents of the named file in one shot:

    # readfile.awk --- read an entire file at once
    
    function readfile(file,     tmp, save_rs)
    {
        save_rs = RS
        RS = "^$"
        getline tmp < file
        close(file)
        RS = save_rs
    
        return tmp
    }
    

It works by setting `RS` to &lsquo;^$&rsquo;, a regular expression that
will never match if the file has contents.  `gawk` reads data from
the file into `tmp`, attempting to match `RS`.  The match fails
after each read, but fails quickly, such that `gawk` fills
`tmp` with the entire contents of the file.
(See [Records](#41-how-input-is-split-into-records) for information on `RT` and `RS`.)

In the case that `file` is empty, the return value is the null
string.  Thus, calling code may use something like:

    contents = readfile("/some/path")
    if (length(contents) == 0)
        # file was empty &hellip;
    

This tests the result to see if it is empty or not. An equivalent
test would be &lsquo;contents == ""&rsquo;.

See [Extension Sample Readfile](#Extension-Sample-Readfile) for an extension function that
also reads an entire file into memory.

---

Previous: [Readfile Function](#1028-reading-a-whole-file-at-once), Up: [General Functions](#102-general-programming)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.2.9 Quoting Strings to Pass to the Shell

Michael Brennan offers the following programming pattern,
which he uses frequently:

    #! /bin/sh
    
    awkp='
       &hellip;
       '
    
    input_program | awk "$awkp" | /bin/sh
    

For example, a program of his named `flac-edit` has this form:

    $ flac-edit -song="Whoope! That's Great" file.flac

It generates the following output, which is to be piped to
the shell (/bin/sh):

    chmod +w file.flac
    metaflac --remove-tag=TITLE file.flac
    LANG=en_US.88591 metaflac --set-tag=TITLE='Whoope! That'"'"'s Great' file.flac
    chmod -w file.flac
    

Note the need for shell quoting.  The function `shell_quote()`
does it.  `SINGLE` is the one-character string `"'"` and
`QSINGLE` is the three-character string `"\"'\""`:

    # shell_quote --- quote an argument for passing to the shell
    
    function shell_quote(s,             # parameter
        SINGLE, QSINGLE, i, X, n, ret)  # locals
    {
        if (s == "")
            return "\"\""
    
        SINGLE = "\x27"  # single quote
        QSINGLE = "\"\x27\""
        n = split(s, X, SINGLE)
    
        ret = SINGLE X[1] SINGLE
        for (i = 2; i <= n; i++)
            ret = ret QSINGLE SINGLE X[i] SINGLE
    
        return ret
    }
    

---

Next: [Getopt Function](#104-processing-command-line-options), Previous: [General Functions](#102-general-programming), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.3 Data file Management

This section presents functions that are useful for managing
command-line data files.

&bull; [Filetrans Function](#1031-noting-data-file-boundaries):  A function for handling data file transitions.
&bull; [Rewind Function](#1032-rereading-the-current-file):  A function for rereading the current file.
&bull; [File Checking](#1033-checking-for-readable-data-files):  Checking that data files are readable.
&bull; [Empty Files](#1034-checking-for-zero-length-files):  Checking for zero-length files.
&bull; [Ignoring Assigns](#1035-treating-assignments-as-file-names):  Treating assignments as file names.

---

Next: [Rewind Function](#1032-rereading-the-current-file), Up: [Data File Management](#103-data-file-management)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.3.1 Noting Data file Boundaries

The `BEGIN` and `END` rules are each executed exactly once, at
the beginning and end of your `awk` program, respectively
(see [BEGIN/END](#714-the-begin-and-end-special-patterns)).
We (the `gawk` authors) once had a user who mistakenly thought that the
`BEGIN` rules were executed at the beginning of each data file and the
`END` rules were executed at the end of each data file.

When informed
that this was not the case, the user requested that we add new special
patterns to `gawk`, named `BEGIN_FILE` and `END_FILE`, that
would have the desired behavior.  He even supplied us the code to do so.

Adding these special patterns to `gawk` wasn&rsquo;t necessary;
the job can be done cleanly in `awk` itself, as illustrated
by the following library program.
It arranges to call two user-supplied functions, `beginfile()` and
`endfile()`, at the beginning and end of each data file.
Besides solving the problem in only nine(!) lines of code, it does so
*portably*; this works with any implementation of `awk`:

    # transfile.awk
    #
    # Give the user a hook for filename transitions
    #
    # The user must supply functions beginfile() and endfile()
    # that each take the name of the file being started or
    # finished, respectively.
    
    FILENAME != _oldfilename {
        if (_oldfilename != "")
            endfile(_oldfilename)
        _oldfilename = FILENAME
        beginfile(FILENAME)
    }
    
    END { endfile(FILENAME) }
    

This file must be loaded before the user&rsquo;s &ldquo;main&rdquo; program, so that the
rule it supplies is executed first.

This rule relies on `awk`&rsquo;s `FILENAME` variable, which
automatically changes for each new data file.  The current file name is
saved in a private variable, `_oldfilename`.  If `FILENAME` does
not equal `_oldfilename`, then a new data file is being processed and
it is necessary to call `endfile()` for the old file.  Because
`endfile()` should only be called if a file has been processed, the
program first checks to make sure that `_oldfilename` is not the null
string.  The program then assigns the current file name to
`_oldfilename` and calls `beginfile()` for the file.
Because, like all `awk` variables, `_oldfilename` is
initialized to the null string, this rule executes correctly even for the
first data file.

The program also supplies an `END` rule to do the final processing for
the last file.  Because this `END` rule comes before any `END` rules
supplied in the &ldquo;main&rdquo; program, `endfile()` is called first.  Once
again, the value of multiple `BEGIN` and `END` rules should be clear.

If the same data file occurs twice in a row on the command line, then
`endfile()` and `beginfile()` are not executed at the end of the
first pass and at the beginning of the second pass.
The following version solves the problem:

    # ftrans.awk --- handle datafile transitions
    #
    # user supplies beginfile() and endfile() functions
    
    FNR == 1 {
        if (_filename_ != "")
            endfile(_filename_)
        _filename_ = FILENAME
        beginfile(FILENAME)
    }
    
    END { endfile(_filename_) }
    

[Wc Program](#1127-counting-things)
shows how this library function can be used and
how it simplifies writing the main program.

So Why Does `gawk` Have `BEGINFILE` and `ENDFILE`?

You are probably wondering, if `beginfile()` and `endfile()`
functions can do the job, why does `gawk` have
`BEGINFILE` and `ENDFILE` patterns?

Good question.  Normally, if `awk` cannot open a file, this
causes an immediate fatal error.  In this case, there is no way for a
user-defined function to deal with the problem, as the mechanism for
calling it relies on the file being open and at the first record.  Thus,
the main reason for `BEGINFILE` is to give you a &ldquo;hook&rdquo; to catch
files that cannot be processed.  `ENDFILE` exists for symmetry,
and because it provides an easy way to do per-file cleanup processing.
For more information, refer to [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns).

---

Next: [File Checking](#1033-checking-for-readable-data-files), Previous: [Filetrans Function](#1031-noting-data-file-boundaries), Up: [Data File Management](#103-data-file-management)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.3.2 Rereading the Current File

Another request for a new built-in function was for a
function that would make it possible to reread the current file.
The requesting user didn&rsquo;t want to have to use `getline`
(see [Getline](#410-explicit-input-with-getline))
inside a loop.

However, as long as you are not in the `END` rule, it is
quite easy to arrange to immediately close the current input file
and then start over with it from the top.
For lack of a better name, we&rsquo;ll call the function `rewind()`:

    # rewind.awk --- rewind the current file and start over
    
    function rewind(    i)
    {
        # shift remaining arguments up
        for (i = ARGC; i > ARGIND; i--)
            ARGV[i] = ARGV[i-1]
    
        # make sure gawk knows to keep going
        ARGC++
    
        # make current file next to get done
        ARGV[ARGIND+1] = FILENAME
    
        # do it
        nextfile
    }
    

The `rewind()` function relies on the `ARGIND` variable
(see [Auto-set](#752-built-in-variables-that-convey-information)), which is specific to `gawk`.  It also
relies on the `nextfile` keyword (see [Nextfile Statement](#749-the-nextfile-statement)).
Because of this, you should not call it from an `ENDFILE` rule.
(This isn&rsquo;t necessary anyway, because `gawk` goes to the next
file as soon as an `ENDFILE` rule finishes!)

You need to be careful calling `rewind()`.  You can end up
causing infinite recursion if you don&rsquo;t pay attention. Here is an
example use:

    $ cat data
    -| a
    -| b
    -| c
    -| d
    -| e
    
    $ cat test.awk
    -| FNR == 3 && ! rewound {
    -|    rewound = 1
    -|    rewind()
    -| }
    -| 
    -| { print FILENAME, FNR, $0 }
    
    $ gawk -f rewind.awk -f test.awk data 
    -| data 1 a
    -| data 2 b
    -| data 1 a
    -| data 2 b
    -| data 3 c
    

    -| data 4 d
    -| data 5 e
    

---

Next: [Empty Files](#1034-checking-for-zero-length-files), Previous: [Rewind Function](#1032-rereading-the-current-file), Up: [Data File Management](#103-data-file-management)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.3.3 Checking for Readable Data files

Normally, if you give `awk` a data file that isn&rsquo;t readable,
it stops with a fatal error.  There are times when you might want to
just ignore such files and keep going.[71](#FOOT71) You can do this by prepending
the following program to your `awk` program:

    # readable.awk --- library file to skip over unreadable files
    
    BEGIN {
        for (i = 1; i < ARGC; i++) {
            if (ARGV[i] ~ /^[a-zA-Z_][a-zA-Z0-9_]*=.*/ \
                || ARGV[i] == "-" || ARGV[i] == "/dev/stdin")
                continue    # assignment or standard input
            else if ((getline junk < ARGV[i]) < 0) # unreadable
                delete ARGV[i]
            else
                close(ARGV[i])
        }
    }
    

This works, because the `getline` won&rsquo;t be fatal.
Removing the element from `ARGV` with `delete`
skips the file (because it&rsquo;s no longer in the list).
See also [ARGC and ARGV](#753-using-argc-and-argv).

Because `awk` variable names only allow the English letters,
the regular expression check purposely does not use character classes
such as &lsquo;[:alpha:]&rsquo; and &lsquo;[:alnum:]&rsquo;
(see [Bracket Expressions](#34-using-bracket-expressions)).

---

Next: [Ignoring Assigns](#1035-treating-assignments-as-file-names), Previous: [File Checking](#1033-checking-for-readable-data-files), Up: [Data File Management](#103-data-file-management)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.3.4 Checking for Zero-Length Files

All known `awk` implementations silently skip over zero-length files.
This is a by-product of `awk`&rsquo;s implicit
read-a-record-and-match-against-the-rules loop: when `awk`
tries to read a record from an empty file, it immediately receives an
end-of-file indication, closes the file, and proceeds on to the next
command-line data file, *without* executing any user-level
`awk` program code.

Using `gawk`&rsquo;s `ARGIND` variable
(see [Built-in Variables](#75-predefined-variables)), it is possible to detect when an empty
data file has been skipped.  Similar to the library file presented
in [Filetrans Function](#1031-noting-data-file-boundaries), the following library file calls a function named
`zerofile()` that the user must provide.  The arguments passed are
the file name and the position in `ARGV` where it was found:

    # zerofile.awk --- library file to process empty input files
    
    BEGIN { Argind = 0 }
    
    ARGIND > Argind + 1 {
        for (Argind++; Argind < ARGIND; Argind++)
            zerofile(ARGV[Argind], Argind)
    }
    
    ARGIND != Argind { Argind = ARGIND }
    
    END {
        if (ARGIND > Argind)
            for (Argind++; Argind <= ARGIND; Argind++)
                zerofile(ARGV[Argind], Argind)
    }
    

The user-level variable `Argind` allows the `awk` program
to track its progress through `ARGV`.  Whenever the program detects
that `ARGIND` is greater than &lsquo;Argind + 1&rsquo;, it means that one or
more empty files were skipped.  The action then calls `zerofile()` for
each such file, incrementing `Argind` along the way.

The &lsquo;Argind != ARGIND&rsquo; rule simply keeps `Argind` up to date
in the normal case.

Finally, the `END` rule catches the case of any empty files at
the end of the command-line arguments.  Note that the test in the
condition of the `for` loop uses the &lsquo;<=&rsquo; operator,
not &lsquo;<&rsquo;.

---

Previous: [Empty Files](#1034-checking-for-zero-length-files), Up: [Data File Management](#103-data-file-management)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 10.3.5 Treating Assignments as File names

Occasionally, you might not want `awk` to process command-line
variable assignments
(see [Assignment Options](#6132-assigning-variables-on-the-command-line)).
In particular, if you have a file name that contains an &lsquo;=&rsquo; character,
`awk` treats the file name as an assignment and does not process it.

Some users have suggested an additional command-line option for `gawk`
to disable command-line assignments.  However, some simple programming with
a library file does the trick:

    # noassign.awk --- library file to avoid the need for a
    # special option that disables command-line assignments
    
    function disable_assigns(argc, argv,    i)
    {
        for (i = 1; i < argc; i++)
            if (argv[i] ~ /^[a-zA-Z_][a-zA-Z0-9_]*=.*/)
                argv[i] = ("./" argv[i])
    }
    
    BEGIN {
        if (No_command_assign)
            disable_assigns(ARGC, ARGV)
    }
    

You then run your program this way:

    awk -v No_command_assign=1 -f noassign.awk -f yourprog.awk *
    

The function works by looping through the arguments.
It prepends &lsquo;./&rsquo; to
any argument that matches the form
of a variable assignment, turning that argument into a file name.

The use of `No_command_assign` allows you to disable command-line
assignments at invocation time, by giving the variable a true value.
When not set, it is initially zero (i.e., false), so the command-line arguments
are left alone.

---

Next: [Passwd Functions](#105-reading-the-user-database), Previous: [Data File Management](#103-data-file-management), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.4 Processing Command-Line Options

Most utilities on POSIX-compatible systems take options on
the command line that can be used to change the way a program behaves.
`awk` is an example of such a program
(see [Options](#22-command-line-options)).
Often, options take *arguments* (i.e., data that the program needs to
correctly obey the command-line option).  For example, `awk`&rsquo;s
-F option requires a string to use as the field separator.
The first occurrence on the command line of either -- or a
string that does not begin with &lsquo;-&rsquo; ends the options.

Modern Unix systems provide a C function named `getopt()` for processing
command-line arguments.  The programmer provides a string describing the
one-letter options. If an option requires an argument, it is followed in the
string with a colon.  `getopt()` is also passed the
count and values of the command-line arguments and is called in a loop.
`getopt()` processes the command-line arguments for option letters.
Each time around the loop, it returns a single character representing the
next option letter that it finds, or &lsquo;?&rsquo; if it finds an invalid option.
When it returns -1, there are no options left on the command line.

When using `getopt()`, options that do not take arguments can be
grouped together.  Furthermore, options that take arguments require that the
argument be present.  The argument can immediately follow the option letter,
or it can be a separate command-line argument.

Given a hypothetical program that takes
three command-line options, -a, -b, and -c, where
-b requires an argument, all of the following are valid ways of
invoking the program:

    prog -a -b foo -c data1 data2 data3
    prog -ac -bfoo -- data1 data2 data3
    prog -acbfoo data1 data2 data3
    

Notice that when the argument is grouped with its option, the rest of
the argument is considered to be the option&rsquo;s argument.
In this example, -acbfoo indicates that all of the
-a, -b, and -c options were supplied,
and that &lsquo;foo&rsquo; is the argument to the -b option.

`getopt()` provides four external variables that the programmer can use:

`optind`
The index in the argument value array (`argv`) where the first
nonoption command-line argument can be found.

`optarg`
The string value of the argument to an option.

`opterr`
Usually `getopt()` prints an error message when it finds an invalid
option.  Setting `opterr` to zero disables this feature.  (An
application might want to print its own error message.)

`optopt`
The letter representing the command-line option.

The following C fragment shows how `getopt()` might process command-line
arguments for `awk`:

    int
    main(int argc, char *argv[])
    {
        &hellip;
        /* print our own message */
        opterr = 0;
        while ((c = getopt(argc, argv, "v:f:F:W:")) != -1) {
            switch (c) {
            case 'f':    /* file */
                &hellip;
                break;
            case 'F':    /* field separator */
                &hellip;
                break;
            case 'v':    /* variable assignment */
                &hellip;
                break;
            case 'W':    /* extension */
                &hellip;
                break;
            case '?':
            default:
                usage();
                break;
            }
        }
        &hellip;
    }
    

As a side point, `gawk` actually uses the GNU `getopt_long()`
function to process both normal and GNU-style long options
(see [Options](#22-command-line-options)).

The abstraction provided by `getopt()` is very useful and is quite
handy in `awk` programs as well.  Following is an `awk`
version of `getopt()`.  This function highlights one of the
greatest weaknesses in `awk`, which is that it is very poor at
manipulating single characters.  Repeated calls to `substr()` are
necessary for accessing individual characters
(see [String Functions](#913-string-manipulation-functions)).[72](#FOOT72)

The discussion that follows walks through the code a bit at a time:

    # getopt.awk --- Do C library getopt(3) function in awk
    
    # External variables:
    #    Optind -- index in ARGV of first nonoption argument
    #    Optarg -- string value of argument to current option
    #    Opterr -- if nonzero, print our own diagnostic
    #    Optopt -- current option letter
    
    # Returns:
    #    -1     at end of options
    #    "?"    for unrecognized option
    #    <c>    a character representing the current option
    
    # Private Data:
    #    _opti  -- index in multiflag option, e.g., -abc
    

The function starts out with comments presenting
a list of the global variables it uses,
what the return values are, what they mean, and any global variables that
are &ldquo;private&rdquo; to this library function.  Such documentation is essential
for any program, and particularly for library functions.

The `getopt()` function first checks that it was indeed called with
a string of options (the `options` parameter).  If `options`
has a zero length, `getopt()` immediately returns -1:

    function getopt(argc, argv, options,    thisopt, i)
    {
        if (length(options) == 0)    # no options given
            return -1
    
    

        if (argv[Optind] == "--") {  # all done
            Optind++
            _opti = 0
            return -1
    

        } else if (argv[Optind] !~ /^-[^:[:space:]]/) {
            _opti = 0
            return -1
        }
    

The next thing to check for is the end of the options.  A --
ends the command-line options, as does any command-line argument that
does not begin with a &lsquo;-&rsquo;.  `Optind` is used to step through
the array of command-line arguments; it retains its value across calls
to `getopt()`, because it is a global variable.

The regular expression that is used, `/^-[^:[:space:]/`,
checks for a &lsquo;-&rsquo; followed by anything
that is not whitespace and not a colon.
If the current command-line argument does not match this pattern,
it is not an option, and it ends option processing. Continuing on:

        if (_opti == 0)
            _opti = 2
        thisopt = substr(argv[Optind], _opti, 1)
        Optopt = thisopt
        i = index(options, thisopt)
        if (i == 0) {
            if (Opterr)
                printf("%c -- invalid option\n", thisopt) > "/dev/stderr"
            if (_opti >= length(argv[Optind])) {
                Optind++
                _opti = 0
            } else
                _opti++
            return "?"
        }
    

The `_opti` variable tracks the position in the current command-line
argument (`argv[Optind]`).  If multiple options are
grouped together with one &lsquo;-&rsquo; (e.g., -abx), it is necessary
to return them to the user one at a time.

If `_opti` is equal to zero, it is set to two, which is the index in
the string of the next character to look at (we skip the &lsquo;-&rsquo;, which
is at position one).  The variable `thisopt` holds the character,
obtained with `substr()`.  It is saved in `Optopt` for the main
program to use.

If `thisopt` is not in the `options` string, then it is an
invalid option.  If `Opterr` is nonzero, `getopt()` prints an error
message on the standard error that is similar to the message from the C
version of `getopt()`.

Because the option is invalid, it is necessary to skip it and move on to the
next option character.  If `_opti` is greater than or equal to the
length of the current command-line argument, it is necessary to move on
to the next argument, so `Optind` is incremented and `_opti` is reset
to zero. Otherwise, `Optind` is left alone and `_opti` is merely
incremented.

In any case, because the option is invalid, `getopt()` returns `"?"`.
The main program can examine `Optopt` if it needs to know what the
invalid option letter actually is. Continuing on:

        if (substr(options, i + 1, 1) == ":") {
            # get option argument
            if (length(substr(argv[Optind], _opti + 1)) > 0)
                Optarg = substr(argv[Optind], _opti + 1)
            else
                Optarg = argv[++Optind]
            _opti = 0
        } else
            Optarg = ""
    

If the option requires an argument, the option letter is followed by a colon
in the `options` string.  If there are remaining characters in the
current command-line argument (`argv[Optind]`), then the rest of that
string is assigned to `Optarg`.  Otherwise, the next command-line
argument is used (&lsquo;-xFOO&rsquo; versus &lsquo;-x FOO&rsquo;). In either case,
`_opti` is reset to zero, because there are no more characters left to
examine in the current command-line argument. Continuing:

        if (_opti == 0 || _opti >= length(argv[Optind])) {
            Optind++
            _opti = 0
        } else
            _opti++
        return thisopt
    }
    

Finally, if `_opti` is either zero or greater than the length of the
current command-line argument, it means this element in `argv` is
through being processed, so `Optind` is incremented to point to the
next element in `argv`.  If neither condition is true, then only
`_opti` is incremented, so that the next option letter can be processed
on the next call to `getopt()`.

The `BEGIN` rule initializes both `Opterr` and `Optind` to one.
`Opterr` is set to one, because the default behavior is for `getopt()`
to print a diagnostic message upon seeing an invalid option.  `Optind`
is set to one, because there&rsquo;s no reason to look at the program name, which is
in `ARGV[0]`:

    BEGIN {
        Opterr = 1    # default is to diagnose
        Optind = 1    # skip ARGV[0]
    
        # test program
        if (_getopt_test) {
            while ((_go_c = getopt(ARGC, ARGV, "ab:cd")) != -1)
                printf("c = <%c>, Optarg = <%s>\n",
                                           _go_c, Optarg)
            printf("non-option arguments:\n")
            for (; Optind < ARGC; Optind++)
                printf("\tARGV[%d] = <%s>\n",
                                        Optind, ARGV[Optind])
        }
    }
    

The rest of the `BEGIN` rule is a simple test program.  Here are the
results of two sample runs of the test program:

    $ awk -f getopt.awk -v _getopt_test=1 -- -a -cbARG bax -x
    -| c = <a>, Optarg = <>
    -| c = <c>, Optarg = <>
    -| c = <b>, Optarg = <ARG>
    -| non-option arguments:
    -|         ARGV[3] = <bax>
    -|         ARGV[4] = <-x>
    
    $ awk -f getopt.awk -v _getopt_test=1 -- -a -x -- xyz abc
    -| c = <a>, Optarg = <>
    error&rarr; x -- invalid option
    -| c = <?>, Optarg = <>
    -| non-option arguments:
    -|         ARGV[4] = <xyz>
    -|         ARGV[5] = <abc>
    

In both runs, the first -- terminates the arguments to
`awk`, so that it does not try to interpret the -a,
etc., as its own options.

> NOTE: After `getopt()` is through,
> user-level code must clear out all the elements of `ARGV` from 1
> to `Optind`, so that `awk` does not try to process the
> command-line options as file names.

Using &lsquo;#!&rsquo; with the -E option may help avoid
conflicts between your program&rsquo;s options and `gawk`&rsquo;s options,
as -E causes `gawk` to abandon processing of
further options
(see [Executable Scripts](#114-executable-awk-programs) and
see [Options](#22-command-line-options)).

Several of the sample programs presented in
[Sample Programs](#11-practical-awk-programs),
use `getopt()` to process their arguments.

---

Next: [Group Functions](#106-reading-the-group-database), Previous: [Getopt Function](#104-processing-command-line-options), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.5 Reading the User Database

The `PROCINFO` array
(see [Built-in Variables](#75-predefined-variables))
provides access to the current user&rsquo;s real and effective user and group ID
numbers, and, if available, the user&rsquo;s supplementary group set.
However, because these are numbers, they do not provide very useful
information to the average user.  There needs to be some way to find the
user information associated with the user and group ID numbers.  This
section presents a suite of functions for retrieving information from the
user database.  See [Group Functions](#106-reading-the-group-database)
for a similar suite that retrieves information from the group database.

The POSIX standard does not define the file where user information is
kept.  Instead, it provides the `<pwd.h>` header file
and several C language subroutines for obtaining user information.
The primary function is `getpwent()`, for &ldquo;get password entry.&rdquo;
The &ldquo;password&rdquo; comes from the original user database file,
/etc/passwd, which stores user information along with the
encrypted passwords (hence the name).

Although an `awk` program could simply read /etc/passwd
directly, this file may not contain complete information about the
system&rsquo;s set of users.[73](#FOOT73) To be sure you are able to
produce a readable and complete version of the user database, it is necessary
to write a small C program that calls `getpwent()`.  `getpwent()`
is defined as returning a pointer to a `struct passwd`.  Each time it
is called, it returns the next entry in the database.  When there are
no more entries, it returns `NULL`, the null pointer.  When this
happens, the C program should call `endpwent()` to close the database.
Following is `pwcat`, a C program that &ldquo;cats&rdquo; the password database:

    /*
     * pwcat.c
     *
     * Generate a printable version of the password database.
     */
    #include <stdio.h>
    #include <pwd.h>
    
    int
    main(int argc, char **argv)
    {
        struct passwd *p;
    
        while ((p = getpwent()) != NULL)
            printf("%s:%s:%ld:%ld:%s:%s:%s\n",
                p->pw_name, p->pw_passwd, (long) p->pw_uid,
                (long) p->pw_gid, p->pw_gecos, p->pw_dir, p->pw_shell);
    
        endpwent();
        return 0;
    }
    

If you don&rsquo;t understand C, don&rsquo;t worry about it.
The output from `pwcat` is the user database, in the traditional
/etc/passwd format of colon-separated fields.  The fields are:

Login name
The user&rsquo;s login name.

Encrypted password
The user&rsquo;s encrypted password.  This may not be available on some systems.

User-ID
The user&rsquo;s numeric user ID number.
(On some systems, it&rsquo;s a C `long`, and not an `int`.  Thus,
we cast it to `long` for all cases.)

Group-ID
The user&rsquo;s numeric group ID number.
(Similar comments about `long` versus `int` apply here.)

Full name
The user&rsquo;s full name, and perhaps other information associated with the
user.

Home directory
The user&rsquo;s login (or &ldquo;home&rdquo;) directory (familiar to shell programmers as
`$HOME`).

Login shell
The program that is run when the user logs in.  This is usually a
shell, such as Bash.

A few lines representative of `pwcat`&rsquo;s output are as follows:

    $ pwcat
    -| root:x:0:1:Operator:/:/bin/sh
    -| nobody:*:65534:65534::/:
    -| daemon:*:1:1::/:
    -| sys:*:2:2::/:/bin/csh
    -| bin:*:3:3::/bin:
    -| arnold:xyzzy:2076:10:Arnold Robbins:/home/arnold:/bin/sh
    -| miriam:yxaay:112:10:Miriam Robbins:/home/miriam:/bin/sh
    -| andy:abcca2:113:10:Andy Jacobs:/home/andy:/bin/sh
    &hellip;
    

With that introduction, following is a group of functions for getting user
information.  There are several functions here, corresponding to the C
functions of the same names:

    # passwd.awk --- access password file information
    
    BEGIN {
        # tailor this to suit your system
        _pw_awklib = "/usr/local/libexec/awk/"
    }
    
    function _pw_init(    oldfs, oldrs, olddol0, pwcat, using_fw, using_fpat)
    {
        if (_pw_inited)
            return
    
        oldfs = FS
        oldrs = RS
        olddol0 = $0
        using_fw = (PROCINFO["FS"] == "FIELDWIDTHS")
        using_fpat = (PROCINFO["FS"] == "FPAT")
        FS = ":"
        RS = "\n"
    
        pwcat = _pw_awklib "pwcat"
        while ((pwcat | getline) > 0) {
            _pw_byname[$1] = $0
            _pw_byuid[$3] = $0
            _pw_bycount[++_pw_total] = $0
        }
        close(pwcat)
        _pw_count = 0
        _pw_inited = 1
        FS = oldfs
        if (using_fw)
            FIELDWIDTHS = FIELDWIDTHS
        else if (using_fpat)
            FPAT = FPAT
        RS = oldrs
        $0 = olddol0
    }
    

The `BEGIN` rule sets a private variable to the directory where
`pwcat` is stored.  Because it is used to help out an `awk` library
routine, we have chosen to put it in /usr/local/libexec/awk;
however, you might want it to be in a different directory on your system.

The function `_pw_init()` fills three copies of the user information
into three associative arrays.  The arrays are indexed by username
(`_pw_byname`), by user ID number (`_pw_byuid`), and by order of
occurrence (`_pw_bycount`).
The variable `_pw_inited` is used for efficiency, as `_pw_init()`
needs to be called only once.

Because this function uses `getline` to read information from
`pwcat`, it first saves the values of `FS`, `RS`, and `$0`.
It notes in the variable `using_fw` whether field splitting
with `FIELDWIDTHS` is in effect or not.
Doing so is necessary, as these functions could be called
from anywhere within a user&rsquo;s program, and the user may have his
or her own way of splitting records and fields.
This makes it possible to restore the correct
field-splitting mechanism later.  The test can only be true for
`gawk`.  It is false if using `FS` or `FPAT`,
or on some other `awk` implementation.

The code that checks for using `FPAT`, using `using_fpat`
and `PROCINFO["FS"]`, is similar.

The main part of the function uses a loop to read database lines, split
the lines into fields, and then store the lines into each array as necessary.
When the loop is done, `_pw_init()` cleans up by closing the pipeline,
setting `_pw_inited` to one, and restoring `FS`
(and `FIELDWIDTHS` or `FPAT`
if necessary), `RS`, and `$0`.
The use of `_pw_count` is explained shortly.

The `getpwnam()` function takes a username as a string argument. If that
user is in the database, it returns the appropriate line. Otherwise, it
relies on the array reference to a nonexistent
element to create the element with the null string as its value:

    function getpwnam(name)
    {
        _pw_init()
        return _pw_byname[name]
    }
    

Similarly, the `getpwuid()` function takes a user ID number
argument. If that user number is in the database, it returns the
appropriate line. Otherwise, it returns the null string:

    function getpwuid(uid)
    {
        _pw_init()
        return _pw_byuid[uid]
    }
    

The `getpwent()` function simply steps through the database, one entry at
a time.  It uses `_pw_count` to track its current position in the
`_pw_bycount` array:

    function getpwent()
    {
        _pw_init()
        if (_pw_count < _pw_total)
            return _pw_bycount[++_pw_count]
        return ""
    }
    

The `endpwent()` function resets `_pw_count` to zero, so that
subsequent calls to `getpwent()` start over again:

    function endpwent()
    {
        _pw_count = 0
    }
    

A conscious design decision in this suite is that each subroutine calls
`_pw_init()` to initialize the database arrays.
The overhead of running
a separate process to generate the user database, and the I/O to scan it,
are only incurred if the user&rsquo;s main program actually calls one of these
functions.  If this library file is loaded along with a user&rsquo;s program, but
none of the routines are ever called, then there is no extra runtime overhead.
(The alternative is move the body of `_pw_init()` into a
`BEGIN` rule, which always runs `pwcat`.  This simplifies the
code but runs an extra process that may never be needed.)

In turn, calling `_pw_init()` is not too expensive, because the
`_pw_inited` variable keeps the program from reading the data more than
once.  If you are worried about squeezing every last cycle out of your
`awk` program, the check of `_pw_inited` could be moved out of
`_pw_init()` and duplicated in all the other functions.  In practice,
this is not necessary, as most `awk` programs are I/O-bound,
and such a change would clutter up the code.

The `id` program in [Id Program](#1123-printing-out-user-information)
uses these functions.

---

Next: [Walking Arrays](#107-traversing-arrays-of-arrays), Previous: [Passwd Functions](#105-reading-the-user-database), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.6 Reading the Group Database

Much of the discussion presented in
[Passwd Functions](#105-reading-the-user-database)
applies to the group database as well.  Although there has traditionally
been a well-known file (/etc/group) in a well-known format, the POSIX
standard only provides a set of C library routines
(`<grp.h>` and `getgrent()`)
for accessing the information.
Even though this file may exist, it may not have
complete information.  Therefore, as with the user database, it is necessary
to have a small C program that generates the group database as its output.
`grcat`, a C program that &ldquo;cats&rdquo; the group database,
is as follows:

    /*
     * grcat.c
     *
     * Generate a printable version of the group database.
     */
    #include <stdio.h>
    #include <grp.h>
    
    int
    main(int argc, char **argv)
    {
        struct group *g;
        int i;
    
        while ((g = getgrent()) != NULL) {
            printf("%s:%s:%ld:", g->gr_name, g->gr_passwd,
                                         (long) g->gr_gid);
            for (i = 0; g->gr_mem[i] != NULL; i++) {
                printf("%s", g->gr_mem[i]);
    

                if (g->gr_mem[i+1] != NULL)
                    putchar(',');
            }
    

            putchar('\n');
        }
        endgrent();
        return 0;
    }
    

Each line in the group database represents one group.  The fields are
separated with colons and represent the following information:

Group Name
The group&rsquo;s name.

Group Password
The group&rsquo;s encrypted password. In practice, this field is never used;
it is usually empty or set to &lsquo;*&rsquo;.

Group ID Number
The group&rsquo;s numeric group ID number;
the association of name to number must be unique within the file.
(On some systems it&rsquo;s a C `long`, and not an `int`.  Thus,
we cast it to `long` for all cases.)

Group Member List
A comma-separated list of usernames.  These users are members of the group.
Modern Unix systems allow users to be members of several groups
simultaneously.  If your system does, then there are elements
`"group1"` through `"groupN"` in `PROCINFO`
for those group ID numbers.
(Note that `PROCINFO` is a `gawk` extension;
see [Built-in Variables](#75-predefined-variables).)

Here is what running `grcat` might produce:

    $ grcat
    -| wheel:*:0:arnold
    -| nogroup:*:65534:
    -| daemon:*:1:
    -| kmem:*:2:
    -| staff:*:10:arnold,miriam,andy
    -| other:*:20:
    &hellip;
    

Here are the functions for obtaining information from the group database.
There are several, modeled after the C library functions of the same names:

    # group.awk --- functions for dealing with the group file
    
    BEGIN {
        # Change to suit your system
        _gr_awklib = "/usr/local/libexec/awk/"
    }
    
    function _gr_init(    oldfs, oldrs, olddol0, grcat,
                                 using_fw, using_fpat, n, a, i)
    {
        if (_gr_inited)
            return
    
        oldfs = FS
        oldrs = RS
        olddol0 = $0
        using_fw = (PROCINFO["FS"] == "FIELDWIDTHS")
        using_fpat = (PROCINFO["FS"] == "FPAT")
        FS = ":"
        RS = "\n"
    
        grcat = _gr_awklib "grcat"
        while ((grcat | getline) > 0) {
            if ($1 in _gr_byname)
                _gr_byname[$1] = _gr_byname[$1] "," $4
            else
                _gr_byname[$1] = $0
            if ($3 in _gr_bygid)
                _gr_bygid[$3] = _gr_bygid[$3] "," $4
            else
                _gr_bygid[$3] = $0
    
            n = split($4, a, "[ \t]*,[ \t]*")
            for (i = 1; i <= n; i++)
                if (a[i] in _gr_groupsbyuser)
                    _gr_groupsbyuser[a[i]] = _gr_groupsbyuser[a[i]] " " $1
                else
                    _gr_groupsbyuser[a[i]] = $1
    
            _gr_bycount[++_gr_count] = $0
        }
        close(grcat)
        _gr_count = 0
        _gr_inited++
        FS = oldfs
        if (using_fw)
            FIELDWIDTHS = FIELDWIDTHS
        else if (using_fpat)
            FPAT = FPAT
        RS = oldrs
        $0 = olddol0
    }
    

The `BEGIN` rule sets a private variable to the directory where
`grcat` is stored.  Because it is used to help out an `awk` library
routine, we have chosen to put it in /usr/local/libexec/awk.  You might
want it to be in a different directory on your system.

These routines follow the same general outline as the user database routines
(see [Passwd Functions](#105-reading-the-user-database)).
The `_gr_inited` variable is used to
ensure that the database is scanned no more than once.
The `_gr_init()` function first saves `FS`,
`RS`, and
`$0`, and then sets `FS` and `RS` to the correct values for
scanning the group information.
It also takes care to note whether `FIELDWIDTHS` or `FPAT`
is being used, and to restore the appropriate field-splitting mechanism.

The group information is stored in several associative arrays.
The arrays are indexed by group name (`_gr_byname`), by group ID number
(`_gr_bygid`), and by position in the database (`_gr_bycount`).
There is an additional array indexed by username (`_gr_groupsbyuser`),
which is a space-separated list of groups to which each user belongs.

Unlike in the user database, it is possible to have multiple records in the
database for the same group.  This is common when a group has a large number
of members.  A pair of such entries might look like the following:

    tvpeople:*:101:johnny,jay,arsenio
    tvpeople:*:101:david,conan,tom,joan
    

For this reason, `_gr_init()` looks to see if a group name or
group ID number is already seen.  If so, the usernames are
simply concatenated onto the previous list of users.[74](#FOOT74)

Finally, `_gr_init()` closes the pipeline to `grcat`, restores
`FS` (and `FIELDWIDTHS` or `FPAT`, if necessary), `RS`, and `$0`,
initializes `_gr_count` to zero
(it is used later), and makes `_gr_inited` nonzero.

The `getgrnam()` function takes a group name as its argument, and if that
group exists, it is returned.
Otherwise, it
relies on the array reference to a nonexistent
element to create the element with the null string as its value:

    function getgrnam(group)
    {
        _gr_init()
        return _gr_byname[group]
    }
    

The `getgrgid()` function is similar; it takes a numeric group ID and
looks up the information associated with that group ID:

    function getgrgid(gid)
    {
        _gr_init()
        return _gr_bygid[gid]
    }
    

The `getgruser()` function does not have a C counterpart. It takes a
username and returns the list of groups that have the user as a member:

    function getgruser(user)
    {
        _gr_init()
        return _gr_groupsbyuser[user]
    }
    

The `getgrent()` function steps through the database one entry at a time.
It uses `_gr_count` to track its position in the list:

    function getgrent()
    {
        _gr_init()
        if (++_gr_count in _gr_bycount)
            return _gr_bycount[_gr_count]
    

        return ""
    }
    

The `endgrent()` function resets `_gr_count` to zero so that `getgrent()` can
start over again:

    function endgrent()
    {
        _gr_count = 0
    }
    

As with the user database routines, each function calls `_gr_init()` to
initialize the arrays.  Doing so only incurs the extra overhead of running
`grcat` if these functions are used (as opposed to moving the body of
`_gr_init()` into a `BEGIN` rule).

Most of the work is in scanning the database and building the various
associative arrays.  The functions that the user calls are themselves very
simple, relying on `awk`&rsquo;s associative arrays to do work.

The `id` program in [Id Program](#1123-printing-out-user-information)
uses these functions.

---

Next: [Library Functions Summary]((#10-a-library-of-awk-functions)-Summary), Previous: [Group Functions](#106-reading-the-group-database), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.7 Traversing Arrays of Arrays

[Arrays of Arrays](#86-arrays-of-arrays) described how `gawk`
provides arrays of arrays.  In particular, any element of
an array may be either a scalar or another array. The
`isarray()` function (see [Type Functions](#917-getting-type-information))
lets you distinguish an array
from a scalar.
The following function, `walk_array()`, recursively traverses
an array, printing the element indices and values.
You call it with the array and a string representing the name
of the array:

    function walk_array(arr, name,      i)
    {
        for (i in arr) {
            if (isarray(arr[i]))
                walk_array(arr[i], (name "[" i "]"))
            else
                printf("%s[%s] = %s\n", name, i, arr[i])
        }
    }
    

It works by looping over each element of the array. If any given
element is itself an array, the function calls itself recursively,
passing the subarray and a new string representing the current index.
Otherwise, the function simply prints the element&rsquo;s name, index, and value.
Here is a main program to demonstrate:

    BEGIN {
        a[1] = 1
        a[2][1] = 21
        a[2][2] = 22
        a[3] = 3
        a[4][1][1] = 411
        a[4][2] = 42
    
        walk_array(a, "a")
    }
    

When run, the program produces the following output:

    $ gawk -f walk_array.awk
    -| a[1] = 1
    -| a[2][1] = 21
    -| a[2][2] = 22
    -| a[3] = 3
    -| a[4][1][1] = 411
    -| a[4][2] = 42
    

The function just presented simply prints the
name and value of each scalar array element. However, it is easy to
generalize it, by passing in the name of a function to call
when walking an array. The modified function looks like this:

    function process_array(arr, name, process, do_arrays,   i, new_name)
    {
        for (i in arr) {
            new_name = (name "[" i "]")
            if (isarray(arr[i])) {
                if (do_arrays)
                    @process(new_name, arr[i])
                process_array(arr[i], new_name, process, do_arrays)
            } else
                @process(new_name, arr[i])
        }
    }
    

The arguments are as follows:

`arr`
The array.

`name`
The name of the array (a string).

`process`
The name of the function to call.

`do_arrays`
If this is true, the function can handle elements that are subarrays.

If subarrays are to be processed, that is done before walking them further.

When run with the following scaffolding, the function produces the same
results as does the earlier version of `walk_array()`:

    BEGIN {
        a[1] = 1
        a[2][1] = 21
        a[2][2] = 22
        a[3] = 3
        a[4][1][1] = 411
        a[4][2] = 42
    
        process_array(a, "a", "do_print", 0)
    }
    
    function do_print(name, element)
    {
        printf "%s = %s\n", name, element
    }
    

---

Next: [Library Exercises](#109-exercises), Previous: [Walking Arrays](#107-traversing-arrays-of-arrays), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.8 Summary

-  Reading programs is an excellent way to learn Good Programming.
The functions and programs provided in this chapter and the next
are intended to serve that purpose.

-  When writing general-purpose library functions, put some thought into how
to name any global variables so that they won&rsquo;t conflict with variables
from a user&rsquo;s program.

-  The functions presented here fit into the following categories:

General problems
Number-to-string conversion, testing assertions, rounding, random number
generation, converting characters to numbers, joining strings, getting
easily usable time-of-day information, and reading a whole file in
one shot

Managing data files
Noting data file boundaries, rereading the current file, checking for
readable files, checking for zero-length files, and treating assignments
as file names

Processing command-line options
An `awk` version of the standard C `getopt()` function

Reading the user and group databases
Two sets of routines that parallel the C library versions

Traversing arrays of arrays
Two functions that traverse an array of arrays to any depth

---

Previous: [Library Functions Summary]((#10-a-library-of-awk-functions)-Summary), Up: [Library Functions]((#10-a-library-of-awk-functions))   [[Contents](#table-of-contents)][[Index](#Index)]

### 10.9 Exercises

1.  In [Empty Files](#1034-checking-for-zero-length-files), we presented the zerofile.awk program,
which made use of `gawk`&rsquo;s `ARGIND` variable.  Can this
problem be solved without relying on `ARGIND`?  If so, how?

2.  As a related challenge, revise that code to handle the case where
an intervening value in `ARGV` is a variable assignment.

---

Next: [Advanced Features](#Advanced-Features), Previous: [Library Functions]((#10-a-library-of-awk-functions)), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]

## 11 Practical `awk` Programs

[Library Functions]((#10-a-library-of-awk-functions)),
presents the idea that reading programs in a language contributes to
learning that language.  This chapter continues that theme,
presenting a potpourri of `awk` programs for your reading
enjoyment.
There are three sections.
The first describes how to run the programs presented
in this chapter.

The second presents `awk`
versions of several common POSIX utilities.
These are programs that you are hopefully already familiar with,
and therefore whose problems are understood.
By reimplementing these programs in `awk`,
you can focus on the `awk`-related aspects of solving
the programming problems.

The third is a grab bag of interesting programs.
These solve a number of different data-manipulation and management
problems.  Many of the programs are short, which emphasizes `awk`&rsquo;s
ability to do a lot in just a few lines of code.

Many of these programs use library functions presented in
[Library Functions]((#10-a-library-of-awk-functions)).

&bull; [Running Examples](#111-one-shot-throwaway-awk-programs):  How to run these examples.
&bull; [Clones](#112-running-awk-without-input-files):  Clones of common utilities.
&bull; [Miscellaneous Programs](#113-running-long-programs):  Some interesting `awk` programs.
&bull; [Programs Summary](#114-summary):  Summary of programs.
&bull; [Programs Exercises](#115-exercises):  Exercises.

---

Next: [Clones](#112-running-awk-without-input-files), Up: [Sample Programs](#11-practical-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

### 11.1 Running the Example Programs

To run a given program, you would typically do something like this:

    awk -f program -- optionsfiles

Here, program is the name of the `awk` program (such as
cut.awk), options are any command-line options for the
program that start with a &lsquo;-&rsquo;, and files are the actual data files.

If your system supports the &lsquo;#!&rsquo; executable interpreter mechanism
(see [Executable Scripts](#114-executable-awk-programs)),
you can instead run your program directly:

    cut.awk -c1-8 myfiles > results
    

If your `awk` is not `gawk`, you may instead need to use this:

    cut.awk -- -c1-8 myfiles > results
    

---

Next: [Miscellaneous Programs](#113-running-long-programs), Previous: [Running Examples](#111-one-shot-throwaway-awk-programs), Up: [Sample Programs](#11-practical-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

### 11.2 Reinventing Wheels for Fun and Profit

This section presents a number of POSIX utilities implemented in
`awk`.  Reinventing these programs in `awk` is often enjoyable,
because the algorithms can be very clearly expressed, and the code is usually
very concise and simple.  This is true because `awk` does so much for you.

It should be noted that these programs are not necessarily intended to
replace the installed versions on your system.
Nor may all of these programs be fully compliant with the most recent
POSIX standard.  This is not a problem; their
purpose is to illustrate `awk` language programming for &ldquo;real-world&rdquo;
tasks.

The programs are presented in alphabetical order.

&bull; [Cut Program](#1121-cutting-out-fields-and-columns):  The `cut` utility.
&bull; [Egrep Program](#1122-searching-for-regular-expressions-in-files):  The `egrep` utility.
&bull; [Id Program](#1123-printing-out-user-information):  The `id` utility.
&bull; [Split Program](#1124-splitting-a-large-file-into-pieces):  The `split` utility.
&bull; [Tee Program](#1125-duplicating-output-into-multiple-files):  The `tee` utility.
&bull; [Uniq Program](#1126-printing-nonduplicated-lines-of-text):  The `uniq` utility.
&bull; [Wc Program](#1127-counting-things):  The `wc` utility.

---

Next: [Egrep Program](#1122-searching-for-regular-expressions-in-files), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.1 Cutting Out Fields and Columns

The `cut` utility selects, or &ldquo;cuts,&rdquo; characters or fields
from its standard input and sends them to its standard output.
Fields are separated by TABs by default,
but you may supply a command-line option to change the field
*delimiter* (i.e., the field-separator character). `cut`&rsquo;s
definition of fields is less general than `awk`&rsquo;s.

A common use of `cut` might be to pull out just the login names of
logged-on users from the output of `who`.  For example, the following
pipeline generates a sorted, unique list of the logged-on users:

    who | cut -c1-8 | sort | uniq
    

The options for `cut` are:

`-c list`
Use list as the list of characters to cut out.  Items within the list
may be separated by commas, and ranges of characters can be separated with
dashes.  The list &lsquo;1-8,15,22-35&rsquo; specifies characters 1 through
8, 15, and 22 through 35.

`-f list`
Use list as the list of fields to cut out.

`-d delim`
Use delim as the field-separator character instead of the TAB
character.

`-s`
Suppress printing of lines that do not contain the field delimiter.

The `awk` implementation of `cut` uses the `getopt()` library
function (see [Getopt Function](#104-processing-command-line-options))
and the `join()` library function
(see [Join Function](#1026-merging-an-array-into-a-string)).

The program begins with a comment describing the options, the library
functions needed, and a `usage()` function that prints out a usage
message and exits.  `usage()` is called if invalid arguments are
supplied:

    # cut.awk --- implement cut in awk
    
    # Options:
    #    -f list     Cut fields
    #    -d c        Field delimiter character
    #    -c list     Cut characters
    #
    #    -s          Suppress lines without the delimiter
    #
    # Requires getopt() and join() library functions
    
    

    function usage()
    {
        print("usage: cut [-f list] [-d c] [-s] [files...]") > "/dev/stderr"
        print("usage: cut [-c list] [files...]") > "/dev/stderr"
        exit 1
    }
    

Next comes a `BEGIN` rule that parses the command-line options.
It sets `FS` to a single TAB character, because that is `cut`&rsquo;s
default field separator. The rule then sets the output field separator to be the
same as the input field separator.  A loop using `getopt()` steps
through the command-line options.  Exactly one of the variables
`by_fields` or `by_chars` is set to true, to indicate that
processing should be done by fields or by characters, respectively.
When cutting by characters, the output field separator is set to the null
string:

    BEGIN {
        FS = "\t"    # default
        OFS = FS
        while ((c = getopt(ARGC, ARGV, "sf:c:d:")) != -1) {
            if (c == "f") {
                by_fields = 1
                fieldlist = Optarg
            } else if (c == "c") {
                by_chars = 1
                fieldlist = Optarg
                OFS = ""
            } else if (c == "d") {
                if (length(Optarg) > 1) {
                    printf("cut: using first character of %s" \
                           " for delimiter\n", Optarg) > "/dev/stderr"
                    Optarg = substr(Optarg, 1, 1)
                }
                fs = FS = Optarg
                OFS = FS
                if (FS == " ")    # defeat awk semantics
                    FS = "[ ]"
            } else if (c == "s")
                suppress = 1
            else
                usage()
        }
    
        # Clear out options
        for (i = 1; i < Optind; i++)
            ARGV[i] = ""
    

The code must take
special care when the field delimiter is a space.  Using
a single space (`" "`) for the value of `FS` is
incorrect&mdash;`awk` would separate fields with runs of spaces,
TABs, and/or newlines, and we want them to be separated with individual
spaces.
To this end, we save the original space character in the variable
`fs` for later use; after setting `FS` to `"[ ]"` we can&rsquo;t
use it directly to see if the field delimiter character is in the string.

Also remember that after `getopt()` is through
(as described in [Getopt Function](#104-processing-command-line-options)),
we have to
clear out all the elements of `ARGV` from 1 to `Optind`,
so that `awk` does not try to process the command-line options
as file names.

After dealing with the command-line options, the program verifies that the
options make sense.  Only one or the other of -c and -f
should be used, and both require a field list.  Then the program calls
either `set_fieldlist()` or `set_charlist()` to pull apart the
list of fields or characters:

        if (by_fields && by_chars)
            usage()
    
        if (by_fields == 0 && by_chars == 0)
            by_fields = 1    # default
    
    

        if (fieldlist == "") {
            print "cut: needs list for -c or -f" > "/dev/stderr"
            exit 1
        }
    

    
    
        if (by_fields)
            set_fieldlist()
        else
            set_charlist()
    }
    

`set_fieldlist()` splits the field list apart at the commas
into an array.  Then, for each element of the array, it looks to
see if the element is actually a range, and if so, splits it apart.
The function checks the range
to make sure that the first number is smaller than the second.
Each number in the list is added to the `flist` array, which
simply lists the fields that will be printed.  Normal field splitting
is used.  The program lets `awk` handle the job of doing the
field splitting:

    function set_fieldlist(        n, m, i, j, k, f, g)
    {
        n = split(fieldlist, f, ",")
        j = 1    # index in flist
        for (i = 1; i <= n; i++) {
            if (index(f[i], "-") != 0) { # a range
                m = split(f[i], g, "-")
    

                if (m != 2 || g[1] >= g[2]) {
                    printf("cut: bad field list: %s\n",
                                      f[i]) > "/dev/stderr"
                    exit 1
                }
    

                for (k = g[1]; k <= g[2]; k++)
                    flist[j++] = k
            } else
                flist[j++] = f[i]
        }
        nfields = j - 1
    }
    

The `set_charlist()` function is more complicated than
`set_fieldlist()`.
The idea here is to use `gawk`&rsquo;s `FIELDWIDTHS` variable
(see [Constant Size](#46-reading-fixed-width-data)),
which describes constant-width input.  When using a character list, that is
exactly what we have.

Setting up `FIELDWIDTHS` is more complicated than simply listing the
fields that need to be printed.  We have to keep track of the fields to
print and also the intervening characters that have to be skipped.
For example, suppose you wanted characters 1 through 8, 15, and
22 through 35.  You would use &lsquo;-c 1-8,15,22-35&rsquo;.  The necessary value
for `FIELDWIDTHS` is `"8 6 1 6 14"`.  This yields five
fields, and the fields to print
are `$1`, `$3`, and `$5`.
The intermediate fields are *filler*,
which is stuff in between the desired data.
`flist` lists the fields to print, and `t` tracks the
complete field list, including filler fields:

    function set_charlist(    field, i, j, f, g, n, m, t,
                              filler, last, len)
    {
        field = 1   # count total fields
        n = split(fieldlist, f, ",")
        j = 1       # index in flist
        for (i = 1; i <= n; i++) {
            if (index(f[i], "-") != 0) { # range
                m = split(f[i], g, "-")
                if (m != 2 || g[1] >= g[2]) {
                    printf("cut: bad character list: %s\n",
                                   f[i]) > "/dev/stderr"
                    exit 1
                }
                len = g[2] - g[1] + 1
                if (g[1] > 1)  # compute length of filler
                    filler = g[1] - last - 1
                else
                    filler = 0
    

                if (filler)
                    t[field++] = filler
    

                t[field++] = len  # length of field
                last = g[2]
                flist[j++] = field - 1
            } else {
                if (f[i] > 1)
                    filler = f[i] - last - 1
                else
                    filler = 0
                if (filler)
                    t[field++] = filler
                t[field++] = 1
                last = f[i]
                flist[j++] = field - 1
            }
        }
        FIELDWIDTHS = join(t, 1, field - 1)
        nfields = j - 1
    }
    

Next is the rule that processes the data.  If the -s option
is given, then `suppress` is true.  The first `if` statement
makes sure that the input record does have the field separator.  If
`cut` is processing fields, `suppress` is true, and the field
separator character is not in the record, then the record is skipped.

If the record is valid, then `gawk` has split the data
into fields, either using the character in `FS` or using fixed-length
fields and `FIELDWIDTHS`.  The loop goes through the list of fields
that should be printed.  The corresponding field is printed if it contains data.
If the next field also has data, then the separator character is
written out between the fields:

    {
        if (by_fields && suppress && index($0, fs) == 0)
            next
    
        for (i = 1; i <= nfields; i++) {
            if ($flist[i] != "") {
                printf "%s", $flist[i]
                if (i < nfields && $flist[i+1] != "")
                    printf "%s", OFS
            }
        }
        print ""
    }
    

This version of `cut` relies on `gawk`&rsquo;s `FIELDWIDTHS`
variable to do the character-based cutting.  It is possible in
other `awk` implementations to use `substr()`
(see [String Functions](#913-string-manipulation-functions)), but
it is also extremely painful.
The `FIELDWIDTHS` variable supplies an elegant solution to the problem
of picking the input line apart by characters.

---

Next: [Id Program](#1123-printing-out-user-information), Previous: [Cut Program](#1121-cutting-out-fields-and-columns), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.2 Searching for Regular Expressions in Files

The `egrep` utility searches files for patterns.  It uses regular
expressions that are almost identical to those available in `awk`
(see [Regexp](#3-regular-expressions)).
You invoke it as follows:

    egrep [options] 'pattern'files &hellip;
    

The pattern is a regular expression.  In typical usage, the regular
expression is quoted to prevent the shell from expanding any of the
special characters as file name wildcards.  Normally, `egrep`
prints the lines that matched.  If multiple file names are provided on
the command line, each output line is preceded by the name of the file
and a colon.

The options to `egrep` are as follows:

`-c`
Print out a count of the lines that matched the pattern, instead of the
lines themselves.

`-s`
Be silent.  No output is produced and the exit value indicates whether
the pattern was matched.

`-v`
Invert the sense of the test. `egrep` prints the lines that do
*not* match the pattern and exits successfully if the pattern is not
matched.

`-i`
Ignore case distinctions in both the pattern and the input data.

`-l`
Only print (list) the names of the files that matched, not the lines that matched.

`-e pattern`
Use pattern as the regexp to match.  The purpose of the -e
option is to allow patterns that start with a &lsquo;-&rsquo;.

This version uses the `getopt()` library function
(see [Getopt Function](#104-processing-command-line-options))
and the file transition library program
(see [Filetrans Function](#1031-noting-data-file-boundaries)).

The program begins with a descriptive comment and then a `BEGIN` rule
that processes the command-line arguments with `getopt()`.  The -i
(ignore case) option is particularly easy with `gawk`; we just use the
`IGNORECASE` predefined variable
(see [Built-in Variables](#75-predefined-variables)):

    # egrep.awk --- simulate egrep in awk
    #
    # Options:
    #    -c    count of lines
    #    -s    silent - use exit value
    #    -v    invert test, success if no match
    #    -i    ignore case
    #    -l    print filenames only
    #    -e    argument is pattern
    #
    # Requires getopt and file transition library functions
    
    BEGIN {
        while ((c = getopt(ARGC, ARGV, "ce:svil")) != -1) {
            if (c == "c")
                count_only++
            else if (c == "s")
                no_print++
            else if (c == "v")
                invert++
            else if (c == "i")
                IGNORECASE = 1
            else if (c == "l")
                filenames_only++
            else if (c == "e")
                pattern = Optarg
            else
                usage()
        }
    

Next comes the code that handles the `egrep`-specific behavior. If no
pattern is supplied with -e, the first nonoption on the
command line is used.  The `awk` command-line arguments up to `ARGV[Optind]`
are cleared, so that `awk` won&rsquo;t try to process them as files.  If no
files are specified, the standard input is used, and if multiple files are
specified, we make sure to note this so that the file names can precede the
matched lines in the output:

        if (pattern == "")
            pattern = ARGV[Optind++]
    
        for (i = 1; i < Optind; i++)
            ARGV[i] = ""
        if (Optind >= ARGC) {
            ARGV[1] = "-"
            ARGC = 2
        } else if (ARGC - Optind > 1)
            do_filenames++
    
    #    if (IGNORECASE)
    #        pattern = tolower(pattern)
    }
    

The last two lines are commented out, as they are not needed in
`gawk`.  They should be uncommented if you have to use another version
of `awk`.

The next set of lines should be uncommented if you are not using
`gawk`.  This rule translates all the characters in the input line
into lowercase if the -i option is specified.[75](#FOOT75)
The rule is
commented out as it is not necessary with `gawk`:

    #{
    #    if (IGNORECASE)
    #        $0 = tolower($0)
    #}
    

The `beginfile()` function is called by the rule in ftrans.awk
when each new file is processed.  In this case, it is very simple; all it
does is initialize a variable `fcount` to zero. `fcount` tracks
how many lines in the current file matched the pattern.
Naming the parameter `junk` shows we know that `beginfile()`
is called with a parameter, but that we&rsquo;re not interested in its value:

    function beginfile(junk)
    {
        fcount = 0
    }
    

The `endfile()` function is called after each file has been processed.
It affects the output only when the user wants a count of the number of lines that
matched.  `no_print` is true only if the exit status is desired.
`count_only` is true if line counts are desired.  `egrep`
therefore only prints line counts if printing and counting are enabled.
The output format must be adjusted depending upon the number of files to
process.  Finally, `fcount` is added to `total`, so that we
know the total number of lines that matched the pattern:

    function endfile(file)
    {
        if (! no_print && count_only) {
            if (do_filenames)
                print file ":" fcount
            else
                print fcount
        }
    
    

        total += fcount
    }
    

The `BEGINFILE` and `ENDFILE` special patterns
(see [BEGINFILE/ENDFILE](#715-the-beginfile-and-endfile-special-patterns)) could be used, but then the program would be
`gawk`-specific. Additionally, this example was written before
`gawk` acquired `BEGINFILE` and `ENDFILE`.

The following rule does most of the work of matching lines. The variable
`matches` is true if the line matched the pattern. If the user
wants lines that did not match, the sense of `matches` is inverted
using the &lsquo;!&rsquo; operator. `fcount` is incremented with the value of
`matches`, which is either one or zero, depending upon a
successful or unsuccessful match.  If the line does not match, the
`next` statement just moves on to the next record.

A number of additional tests are made, but they are only done if we
are not counting lines.  First, if the user only wants the exit status
(`no_print` is true), then it is enough to know that *one*
line in this file matched, and we can skip on to the next file with
`nextfile`.  Similarly, if we are only printing file names, we can
print the file name, and then skip to the next file with `nextfile`.
Finally, each line is printed, with a leading file name and colon
if necessary:

    {
        matches = ($0 ~ pattern)
        if (invert)
            matches = ! matches
    
        fcount += matches    # 1 or 0
    
        if (! matches)
            next
    
        if (! count_only) {
            if (no_print)
                nextfile
    
            if (filenames_only) {
                print FILENAME
                nextfile
            }
    
            if (do_filenames)
                print FILENAME ":" $0
            else
                print
        }
    }
    

The `END` rule takes care of producing the correct exit status. If
there are no matches, the exit status is one; otherwise, it is zero:

    END {
        exit (total == 0)
    }
    

The `usage()` function prints a usage message in case of invalid options,
and then exits:

    function usage()
    {
        print("Usage: egrep [-csvil] [-e pat] [files ...]") > "/dev/stderr"
        print("\n\tegrep [-csvil] pat [files ...]") > "/dev/stderr"
        exit 1
    }
    

---

Next: [Split Program](#1124-splitting-a-large-file-into-pieces), Previous: [Egrep Program](#1122-searching-for-regular-expressions-in-files), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.3 Printing Out User Information

The `id` utility lists a user&rsquo;s real and effective user ID numbers,
real and effective group ID numbers, and the user&rsquo;s group set, if any.
`id` only prints the effective user ID and group ID if they are
different from the real ones.  If possible, `id` also supplies the
corresponding user and group names.  The output might look like this:

    $ id
    -| uid=1000(arnold) gid=1000(arnold) groups=1000(arnold),4(adm),7(lp),27(sudo)
    

This information is part of what is provided by `gawk`&rsquo;s
`PROCINFO` array (see [Built-in Variables](#75-predefined-variables)).
However, the `id` utility provides a more palatable output than just
individual numbers.

Here is a simple version of `id` written in `awk`.
It uses the user database library functions
(see [Passwd Functions](#105-reading-the-user-database))
and the group database library functions
(see [Group Functions](#106-reading-the-group-database))
from [Library Functions]((#10-a-library-of-awk-functions)).

The program is fairly straightforward.  All the work is done in the
`BEGIN` rule.  The user and group ID numbers are obtained from
`PROCINFO`.
The code is repetitive.  The entry in the user database for the real user ID
number is split into parts at the &lsquo;:&rsquo;. The name is the first field.
Similar code is used for the effective user ID number and the group
numbers:

    # id.awk --- implement id in awk
    #
    # Requires user and group library functions
    # output is:
    # uid=12(foo) euid=34(bar) gid=3(baz) \
    #             egid=5(blat) groups=9(nine),2(two),1(one)
    
    

    BEGIN {
        uid = PROCINFO["uid"]
        euid = PROCINFO["euid"]
        gid = PROCINFO["gid"]
        egid = PROCINFO["egid"]
    

    
    
        printf("uid=%d", uid)
        pw = getpwuid(uid)
        pr_first_field(pw)
    
    

        if (euid != uid) {
            printf(" euid=%d", euid)
            pw = getpwuid(euid)
    

            pr_first_field(pw)
        }
    

    
    
        printf(" gid=%d", gid)
        pw = getgrgid(gid)
        pr_first_field(pw)
    
        if (egid != gid) {
            printf(" egid=%d", egid)
            pw = getgrgid(egid)
            pr_first_field(pw)
        }
    
        for (i = 1; ("group" i) in PROCINFO; i++) {
            if (i == 1)
                printf(" groups=")
            group = PROCINFO["group" i]
            printf("%d", group)
            pw = getgrgid(group)
            pr_first_field(pw)
            if (("group" (i+1)) in PROCINFO)
                printf(",")
        }
    
        print ""
    }
    
    function pr_first_field(str,  a)
    {
        if (str != "") {
            split(str, a, ":")
            printf("(%s)", a[1])
        }
    }
    

The test in the `for` loop is worth noting.
Any supplementary groups in the `PROCINFO` array have the
indices `"group1"` through `"groupN"` for some
N (i.e., the total number of supplementary groups).
However, we don&rsquo;t know in advance how many of these groups
there are.

This loop works by starting at one, concatenating the value with
`"group"`, and then using `in` to see if that value is
in the array (see [Reference to Elements](#812-referring-to-an-array-element)).  Eventually, `i` is incremented past
the last group in the array and the loop exits.

The loop is also correct if there are *no* supplementary
groups; then the condition is false the first time it&rsquo;s
tested, and the loop body never executes.

The `pr_first_field()` function simply isolates out some
code that is used repeatedly, making the whole program
shorter and cleaner. In particular, moving the check for
the empty string into this function saves several lines of code.

---

Next: [Tee Program](#1125-duplicating-output-into-multiple-files), Previous: [Id Program](#1123-printing-out-user-information), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.4 Splitting a Large File into Pieces

The `split` program splits large text files into smaller pieces.
Usage is as follows:[76](#FOOT76)

    split [-count] [file] [prefix]
    

By default,
the output files are named xaa, xab, and so on. Each file has
1,000 lines in it, with the likely exception of the last file. To change the
number of lines in each file, supply a number on the command line
preceded with a minus sign (e.g., &lsquo;-500&rsquo; for files with 500 lines in them
instead of 1,000).  To change the names of the output files to something like
myfileaa, myfileab, and so on, supply an additional
argument that specifies the file name prefix.

Here is a version of `split` in `awk`. It uses the
`ord()` and `chr()` functions presented in
[Ordinal Functions](#1025-translating-between-characters-and-numbers).

The program first sets its defaults, and then tests to make sure there are
not too many arguments.  It then looks at each argument in turn.  The
first argument could be a minus sign followed by a number. If it is, this happens
to look like a negative number, so it is made positive, and that is the
count of lines.  The data file name is skipped over and the final argument
is used as the prefix for the output file names:

    # split.awk --- do split in awk
    #
    # Requires ord() and chr() library functions
    # usage: split [-count] [file] [outname]
    
    BEGIN {
        outfile = "x"    # default
        count = 1000
        if (ARGC > 4)
            usage()
    
        i = 1
        if (i in ARGV && ARGV[i] ~ /^-[[:digit:]]+$/) {
            count = -ARGV[i]
            ARGV[i] = ""
            i++
        }
        # test argv in case reading from stdin instead of file
        if (i in ARGV)
            i++    # skip datafile name
    

        if (i in ARGV) {
            outfile = ARGV[i]
            ARGV[i] = ""
        }
    

        s1 = s2 = "a"
        out = (outfile s1 s2)
    }
    

The next rule does most of the work. `tcount` (temporary count) tracks
how many lines have been printed to the output file so far. If it is greater
than `count`, it is time to close the current file and start a new one.
`s1` and `s2` track the current suffixes for the file name. If
they are both &lsquo;z&rsquo;, the file is just too big.  Otherwise, `s1`
moves to the next letter in the alphabet and `s2` starts over again at
&lsquo;a&rsquo;:

    {
        if (++tcount > count) {
            close(out)
            if (s2 == "z") {
                if (s1 == "z") {
                    printf("split: %s is too large to split\n",
                           FILENAME) > "/dev/stderr"
                    exit 1
                }
                s1 = chr(ord(s1) + 1)
                s2 = "a"
            }
    

            else
                s2 = chr(ord(s2) + 1)
    

            out = (outfile s1 s2)
            tcount = 1
        }
        print > out
    }
    

The `usage()` function simply prints an error message and exits:

    function usage()
    {
        print("usage: split [-num] [file] [outname]") > "/dev/stderr"
        exit 1
    }
    

This program is a bit sloppy; it relies on `awk` to automatically close the last file
instead of doing it in an `END` rule.
It also assumes that letters are contiguous in the character set,
which isn&rsquo;t true for EBCDIC systems.

---

Next: [Uniq Program](#1126-printing-nonduplicated-lines-of-text), Previous: [Split Program](#1124-splitting-a-large-file-into-pieces), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.5 Duplicating Output into Multiple Files

The `tee` program is known as a &ldquo;pipe fitting.&rdquo;  `tee` copies
its standard input to its standard output and also duplicates it to the
files named on the command line.  Its usage is as follows:

    tee [-a] file &hellip;
    

The -a option tells `tee` to append to the named files, instead of
truncating them and starting over.

The `BEGIN` rule first makes a copy of all the command-line arguments
into an array named `copy`.
`ARGV[0]` is not needed, so it is not copied.
`tee` cannot use `ARGV` directly, because `awk` attempts to
process each file name in `ARGV` as input data.

If the first argument is -a, then the flag variable
`append` is set to true, and both `ARGV[1]` and
`copy[1]` are deleted. If `ARGC` is less than two, then no
file names were supplied and `tee` prints a usage message and exits.
Finally, `awk` is forced to read the standard input by setting
`ARGV[1]` to `"-"` and `ARGC` to two:

    # tee.awk --- tee in awk
    #
    # Copy standard input to all named output files.
    # Append content if -a option is supplied.
    #
    BEGIN {
        for (i = 1; i < ARGC; i++)
            copy[i] = ARGV[i]
    
        if (ARGV[1] == "-a") {
            append = 1
            delete ARGV[1]
            delete copy[1]
            ARGC--
        }
        if (ARGC < 2) {
            print "usage: tee [-a] file ..." > "/dev/stderr"
            exit 1
        }
        ARGV[1] = "-"
        ARGC = 2
    }
    

The following single rule does all the work.  Because there is no pattern, it is
executed for each line of input.  The body of the rule simply prints the
line into each file on the command line, and then to the standard output:

    {
        # moving the if outside the loop makes it run faster
        if (append)
            for (i in copy)
                print >> copy[i]
        else
            for (i in copy)
                print > copy[i]
        print
    }
    

It is also possible to write the loop this way:

    for (i in copy)
        if (append)
            print >> copy[i]
    

        else
            print > copy[i]
    

This is more concise, but it is also less efficient.  The &lsquo;if&rsquo; is
tested for each record and for each output file.  By duplicating the loop
body, the &lsquo;if&rsquo; is only tested once for each input record.  If there are
N input records and M output files, the first method only
executes N &lsquo;if&rsquo; statements, while the second executes
N`*`M &lsquo;if&rsquo; statements.

Finally, the `END` rule cleans up by closing all the output files:

    END {
        for (i in copy)
            close(copy[i])
    }
    

---

Next: [Wc Program](#1127-counting-things), Previous: [Tee Program](#1125-duplicating-output-into-multiple-files), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.6 Printing Nonduplicated Lines of Text

The `uniq` utility reads sorted lines of data on its standard
input, and by default removes duplicate lines.  In other words, it only
prints unique lines&mdash;hence the name.  `uniq` has a number of
options. The usage is as follows:

    uniq [-udc [-n]] [+n] [inputfile [outputfile]]
    

The options for `uniq` are:

`-d`
Print only repeated (duplicated) lines.

`-u`
Print only nonrepeated (unique) lines.

`-c`
Count lines. This option overrides -d and -u.  Both repeated
and nonrepeated lines are counted.

`-n`
Skip n fields before comparing lines.  The definition of fields
is similar to `awk`&rsquo;s default: nonwhitespace characters separated
by runs of spaces and/or TABs.

`+n`
Skip n characters before comparing lines.  Any fields specified with
&lsquo;-n&rsquo; are skipped first.

`inputfile`
Data is read from the input file named on the command line, instead of from
the standard input.

`outputfile`
The generated output is sent to the named output file, instead of to the
standard output.

Normally `uniq` behaves as if both the -d and
-u options are provided.

`uniq` uses the
`getopt()` library function
(see [Getopt Function](#104-processing-command-line-options))
and the `join()` library function
(see [Join Function](#1026-merging-an-array-into-a-string)).

The program begins with a `usage()` function and then a brief outline of
the options and their meanings in comments.
The `BEGIN` rule deals with the command-line arguments and options. It
uses a trick to get `getopt()` to handle options of the form &lsquo;-25&rsquo;,
treating such an option as the option letter &lsquo;2&rsquo; with an argument of
&lsquo;5&rsquo;. If indeed two or more digits are supplied (`Optarg` looks
like a number), `Optarg` is
concatenated with the option digit and then the result is added to zero to make
it into a number.  If there is only one digit in the option, then
`Optarg` is not needed. In this case, `Optind` must be decremented so that
`getopt()` processes it next time.  This code is admittedly a bit
tricky.

If no options are supplied, then the default is taken, to print both
repeated and nonrepeated lines.  The output file, if provided, is assigned
to `outputfile`.  Early on, `outputfile` is initialized to the
standard output, /dev/stdout:

    # uniq.awk --- do uniq in awk
    #
    # Requires getopt() and join() library functions
    

    
    
    function usage()
    {
        print("Usage: uniq [-udc [-n]] [+n] [ in [ out ]]") > "/dev/stderr"
        exit 1
    }
    
    # -c    count lines. overrides -d and -u
    # -d    only repeated lines
    # -u    only nonrepeated lines
    # -n    skip n fields
    # +n    skip n characters, skip fields first
    
    BEGIN {
        count = 1
        outputfile = "/dev/stdout"
        opts = "udc0:1:2:3:4:5:6:7:8:9:"
        while ((c = getopt(ARGC, ARGV, opts)) != -1) {
            if (c == "u")
                non_repeated_only++
            else if (c == "d")
                repeated_only++
            else if (c == "c")
                do_count++
            else if (index("0123456789", c) != 0) {
                # getopt() requires args to options
                # this messes us up for things like -5
                if (Optarg ~ /^[[:digit:]]+$/)
                    fcount = (c Optarg) + 0
                else {
                    fcount = c + 0
                    Optind--
                }
            } else
                usage()
        }
    
    

        if (ARGV[Optind] ~ /^\+[[:digit:]]+$/) {
            charcount = substr(ARGV[Optind], 2) + 0
            Optind++
        }
    

    
    
        for (i = 1; i < Optind; i++)
            ARGV[i] = ""
    
        if (repeated_only == 0 && non_repeated_only == 0)
            repeated_only = non_repeated_only = 1
    
        if (ARGC - Optind == 2) {
            outputfile = ARGV[ARGC - 1]
            ARGV[ARGC - 1] = ""
        }
    }
    

The following function, `are_equal()`, compares the current line,
`$0`, to the previous line, `last`.  It handles skipping fields
and characters.  If no field count and no character count are specified,
`are_equal()` returns one or zero depending upon the result of a
simple string comparison of `last` and `$0`.

Otherwise, things get more complicated.  If fields have to be skipped,
each line is broken into an array using `split()` (see [String Functions](#913-string-manipulation-functions)); the desired fields are then joined back into a line
using `join()`.  The joined lines are stored in `clast` and
`cline`.  If no fields are skipped, `clast` and `cline`
are set to `last` and `$0`, respectively.  Finally, if
characters are skipped, `substr()` is used to strip off the leading
`charcount` characters in `clast` and `cline`.  The two
strings are then compared and `are_equal()` returns the result:

    function are_equal(    n, m, clast, cline, alast, aline)
    {
        if (fcount == 0 && charcount == 0)
            return (last == $0)
    

    
    
        if (fcount > 0) {
            n = split(last, alast)
            m = split($0, aline)
            clast = join(alast, fcount+1, n)
            cline = join(aline, fcount+1, m)
        } else {
            clast = last
            cline = $0
        }
        if (charcount) {
            clast = substr(clast, charcount + 1)
            cline = substr(cline, charcount + 1)
        }
    

    
    
        return (clast == cline)
    }
    

The following two rules are the body of the program.  The first one is
executed only for the very first line of data.  It sets `last` equal to
`$0`, so that subsequent lines of text have something to be compared to.

The second rule does the work. The variable `equal` is one or zero,
depending upon the results of `are_equal()`&rsquo;s comparison. If `uniq`
is counting repeated lines, and the lines are equal, then it increments the `count` variable.
Otherwise, it prints the line and resets `count`,
because the two lines are not equal.

If `uniq` is not counting, and if the lines are equal, `count` is incremented.
Nothing is printed, as the point is to remove duplicates.
Otherwise, if `uniq` is counting repeated lines and more than
one line is seen, or if `uniq` is counting nonrepeated lines
and only one line is seen, then the line is printed, and `count`
is reset.

Finally, similar logic is used in the `END` rule to print the final
line of input data:

    NR == 1 {
        last = $0
        next
    }
    
    {
        equal = are_equal()
    
        if (do_count) {    # overrides -d and -u
            if (equal)
                count++
            else {
                printf("%4d %s\n", count, last) > outputfile
                last = $0
                count = 1    # reset
            }
            next
        }
    
        if (equal)
            count++
        else {
            if ((repeated_only && count > 1) ||
                (non_repeated_only && count == 1))
                    print last > outputfile
            last = $0
            count = 1
        }
    }
    
    END {
        if (do_count)
            printf("%4d %s\n", count, last) > outputfile
    

        else if ((repeated_only && count > 1) ||
                (non_repeated_only && count == 1))
            print last > outputfile
        close(outputfile)
    }
    

---

Previous: [Uniq Program](#1126-printing-nonduplicated-lines-of-text), Up: [Clones](#112-running-awk-without-input-files)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.2.7 Counting Things

The `wc` (word count) utility counts lines, words, and characters in
one or more input files. Its usage is as follows:

    wc [-lwc] [files &hellip;]
    

If no files are specified on the command line, `wc` reads its standard
input. If there are multiple files, it also prints total counts for all
the files.  The options and their meanings are as follows:

`-l`
Count only lines.

`-w`
Count only words.
A &ldquo;word&rdquo; is a contiguous sequence of nonwhitespace characters, separated
by spaces and/or TABs.  Luckily, this is the normal way `awk` separates
fields in its input data.

`-c`
Count only characters.

Implementing `wc` in `awk` is particularly elegant,
because `awk` does a lot of the work for us; it splits lines into
words (i.e., fields) and counts them, it counts lines (i.e., records),
and it can easily tell us how long a line is.

This program uses the `getopt()` library function
(see [Getopt Function](#104-processing-command-line-options))
and the file-transition functions
(see [Filetrans Function](#1031-noting-data-file-boundaries)).

This version has one notable difference from traditional versions of
`wc`: it always prints the counts in the order lines, words,
and characters.  Traditional versions note the order of the -l,
-w, and -c options on the command line, and print the
counts in that order.

The `BEGIN` rule does the argument processing.  The variable
`print_total` is true if more than one file is named on the
command line:

    # wc.awk --- count lines, words, characters
    
    # Options:
    #    -l    only count lines
    #    -w    only count words
    #    -c    only count characters
    #
    # Default is to count lines, words, characters
    #
    # Requires getopt() and file transition library functions
    
    BEGIN {
        # let getopt() print a message about
        # invalid options. we ignore them
        while ((c = getopt(ARGC, ARGV, "lwc")) != -1) {
            if (c == "l")
                do_lines = 1
            else if (c == "w")
                do_words = 1
            else if (c == "c")
                do_chars = 1
        }
        for (i = 1; i < Optind; i++)
            ARGV[i] = ""
    
        # if no options, do all
        if (! do_lines && ! do_words && ! do_chars)
            do_lines = do_words = do_chars = 1
    
        print_total = (ARGC - i > 1)
    }
    

The `beginfile()` function is simple; it just resets the counts of lines,
words, and characters to zero, and saves the current file name in
`fname`:

    function beginfile(file)
    {
        lines = words = chars = 0
        fname = FILENAME
    }
    

The `endfile()` function adds the current file&rsquo;s numbers to the
running totals of lines, words, and characters.  It then prints out those
numbers for the file that was just read. It relies on `beginfile()`
to reset the numbers for the following data file:

    function endfile(file)
    {
        tlines += lines
        twords += words
        tchars += chars
        if (do_lines)
            printf "\t%d", lines
    

        if (do_words)
            printf "\t%d", words
    

        if (do_chars)
            printf "\t%d", chars
        printf "\t%s\n", fname
    }
    

There is one rule that is executed for each line. It adds the length of
the record, plus one, to `chars`.[77](#FOOT77)
Adding one plus the record length
is needed because the newline character separating records (the value
of `RS`) is not part of the record itself, and thus not included
in its length.  Next, `lines` is incremented for each line read,
and `words` is incremented by the value of `NF`, which is the
number of &ldquo;words&rdquo; on this line:

    # do per line
    {
        chars += length($0) + 1    # get newline
        lines++
        words += NF
    }
    

Finally, the `END` rule simply prints the totals for all the files:

    END {
        if (print_total) {
            if (do_lines)
                printf "\t%d", tlines
            if (do_words)
                printf "\t%d", twords
            if (do_chars)
                printf "\t%d", tchars
            print "\ttotal"
        }
    }
    

---

Next: [Programs Summary](#114-summary), Previous: [Clones](#112-running-awk-without-input-files), Up: [Sample Programs](#11-practical-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

### 11.3 A Grab Bag of `awk` Programs

This section is a large &ldquo;grab bag&rdquo; of miscellaneous programs.
We hope you find them both interesting and enjoyable.

&bull; [Dupword Program](#1131-finding-duplicated-words-in-a-document):  Finding duplicated words in a document.
&bull; [Alarm Program](#1132-an-alarm-clock-program):  An alarm clock.
&bull; [Translate Program](#1133-transliterating-characters):  A program similar to the `tr` utility.
&bull; [Labels Program](#1134-printing-mailing-labels):  Printing mailing labels.
&bull; [Word Sorting](#1135-generating-word-usage-counts):  A program to produce a word usage count.
&bull; [History Sorting](#1136-removing-duplicates-from-unsorted-text):  Eliminating duplicate entries from a history
                                file.
&bull; [Extract Program](#1137-extracting-programs-from-texinfo-source-files):  Pulling out programs from Texinfo source
                                files.
&bull; [Simple Sed](#1138-a-simple-stream-editor):  A Simple Stream Editor.
&bull; [Igawk Program](#1139-an-easy-way-to-use-library-functions):  A wrapper for `awk` that includes
                                files.
&bull; [Anagram Program](#11310-finding-anagrams-from-a-dictionary):  Finding anagrams from a dictionary.
&bull; [Signature Program](#11311-and-now-for-something-completely-different):  People do amazing things with too much time on
                                their hands.

---

Next: [Alarm Program](#1132-an-alarm-clock-program), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.1 Finding Duplicated Words in a Document

A common error when writing large amounts of prose is to accidentally
duplicate words.  Typically you will see this in text as something like &ldquo;the
the program does the following&hellip;&rdquo;  When the text is online, often
the duplicated words occur at the end of one line and the
beginning of
another, making them very difficult to spot.

This program, dupword.awk, scans through a file one line at a time
and looks for adjacent occurrences of the same word.  It also saves the last
word on a line (in the variable `prev`) for comparison with the first
word on the next line.

The first two statements make sure that the line is all lowercase,
so that, for example, &ldquo;The&rdquo; and &ldquo;the&rdquo; compare equal to each other.
The next statement replaces nonalphanumeric and nonwhitespace characters
with spaces, so that punctuation does not affect the comparison either.
The characters are replaced with spaces so that formatting controls
don&rsquo;t create nonsense words (e.g., the Texinfo &lsquo;@code{NF}&rsquo;
becomes &lsquo;codeNF&rsquo; if punctuation is simply deleted).  The record is
then resplit into fields, yielding just the actual words on the line,
and ensuring that there are no empty fields.

If there are no fields left after removing all the punctuation, the
current record is skipped.  Otherwise, the program loops through each
word, comparing it to the previous one:

    # dupword.awk --- find duplicate words in text
    {
        $0 = tolower($0)
        gsub(/[^[:alnum:][:blank:]]/, " ");
        $0 = $0         # re-split
        if (NF == 0)
            next
        if ($1 == prev)
            printf("%s:%d: duplicate %s\n",
                FILENAME, FNR, $1)
        for (i = 2; i <= NF; i++)
            if ($i == $(i-1))
                printf("%s:%d: duplicate %s\n",
                    FILENAME, FNR, $i)
        prev = $NF
    }
    

---

Next: [Translate Program](#1133-transliterating-characters), Previous: [Dupword Program](#1131-finding-duplicated-words-in-a-document), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.2 An Alarm Clock Program

> Nothing cures insomnia like a ringing alarm clock.

&mdash; *Arnold Robbins*

> Sleep is for web developers.

&mdash; *Erik Quanstrom*

The following program is a simple &ldquo;alarm clock&rdquo; program.
You give it a time of day and an optional message.  At the specified time,
it prints the message on the standard output. In addition, you can give it
the number of times to repeat the message as well as a delay between
repetitions.

This program uses the `getlocaltime()` function from
[Getlocaltime Function](#1027-managing-the-time-of-day).

All the work is done in the `BEGIN` rule.  The first part is argument
checking and setting of defaults: the delay, the count, and the message to
print.  If the user supplied a message without the ASCII BEL
character (known as the &ldquo;alert&rdquo; character, `"\a"`), then it is added to
the message.  (On many systems, printing the ASCII BEL generates an
audible alert. Thus, when the alarm goes off, the system calls attention
to itself in case the user is not looking at the computer.)
Just for a change, this program uses a `switch` statement
(see [Switch Statement](#745-the-switch-statement)), but the processing could be done with a series of
`if`-`else` statements instead.
Here is the program:

    # alarm.awk --- set an alarm
    #
    # Requires getlocaltime() library function
    # usage: alarm time [ "message" [ count [ delay ] ] ]
    
    BEGIN {
        # Initial argument sanity checking
        usage1 = "usage: alarm time ['message' [count [delay]]]"
        usage2 = sprintf("\t(%s) time ::= hh:mm", ARGV[1])
    
        if (ARGC < 2) {
            print usage1 > "/dev/stderr"
            print usage2 > "/dev/stderr"
            exit 1
        }
        switch (ARGC) {
        case 5:
            delay = ARGV[4] + 0
            # fall through
        case 4:
            count = ARGV[3] + 0
            # fall through
        case 3:
            message = ARGV[2]
            break
        default:
            if (ARGV[1] !~ /[[:digit:]]?[[:digit:]]:[[:digit:]]{2}/) {
                print usage1 > "/dev/stderr"
                print usage2 > "/dev/stderr"
                exit 1
            }
            break
        }
    
        # set defaults for once we reach the desired time
        if (delay == 0)
            delay = 180    # 3 minutes
    

        if (count == 0)
            count = 5
    

        if (message == "")
            message = sprintf("\aIt is now %s!\a", ARGV[1])
        else if (index(message, "\a") == 0)
            message = "\a" message "\a"
    

The next section of code turns the alarm time into hours and minutes,
converts it (if necessary) to a 24-hour clock, and then turns that
time into a count of the seconds since midnight.  Next it turns the current
time into a count of seconds since midnight.  The difference between the two
is how long to wait before setting off the alarm:

        # split up alarm time
        split(ARGV[1], atime, ":")
        hour = atime[1] + 0    # force numeric
        minute = atime[2] + 0  # force numeric
    
        # get current broken down time
        getlocaltime(now)
    
        # if time given is 12-hour hours and it's after that
        # hour, e.g., `alarm 5:30' at 9 a.m. means 5:30 p.m.,
        # then add 12 to real hour
        if (hour < 12 && now["hour"] > hour)
            hour += 12
    
        # set target time in seconds since midnight
        target = (hour * 60 * 60) + (minute * 60)
    
        # get current time in seconds since midnight
        current = (now["hour"] * 60 * 60) + \
                   (now["minute"] * 60) + now["second"]
    
        # how long to sleep for
        naptime = target - current
        if (naptime <= 0) {
            print "alarm: time is in the past!" > "/dev/stderr"
            exit 1
        }
    

Finally, the program uses the `system()` function
(see [I/O Functions](#914-inputoutput-functions))
to call the `sleep` utility.  The `sleep` utility simply pauses
for the given number of seconds.  If the exit status is not zero,
the program assumes that `sleep` was interrupted and exits. If
`sleep` exited with an OK status (zero), then the program prints the
message in a loop, again using `sleep` to delay for however many
seconds are necessary:

        # zzzzzz..... go away if interrupted
        if (system(sprintf("sleep %d", naptime)) != 0)
            exit 1
    
        # time to notify!
        command = sprintf("sleep %d", delay)
        for (i = 1; i <= count; i++) {
            print message
            # if sleep command interrupted, go away
            if (system(command) != 0)
                break
        }
    
        exit 0
    }
    

---

Next: [Labels Program](#1134-printing-mailing-labels), Previous: [Alarm Program](#1132-an-alarm-clock-program), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.3 Transliterating Characters

The system `tr` utility transliterates characters.  For example, it is
often used to map uppercase letters into lowercase for further processing:

    generate data | tr 'A-Z' 'a-z' | process data &hellip;
    

`tr` requires two lists of characters.[78](#FOOT78)  When processing the input, the
first character in the first list is replaced with the first character
in the second list, the second character in the first list is replaced
with the second character in the second list, and so on.  If there are
more characters in the &ldquo;from&rdquo; list than in the &ldquo;to&rdquo; list, the last
character of the &ldquo;to&rdquo; list is used for the remaining characters in the
&ldquo;from&rdquo; list.

Once upon a time,
a user proposed adding a transliteration function
to `gawk`.
The following program was written to
prove that character transliteration could be done with a user-level
function.  This program is not as complete as the system `tr` utility,
but it does most of the job.

The `translate` program was written long before `gawk`
acquired the ability to split each character in a string into separate
array elements.  Thus, it makes repeated use of the `substr()`,
`index()`, and `gsub()` built-in functions (see [String Functions](#913-string-manipulation-functions)).  There are two functions.  The first, `stranslate()`,
takes three arguments:

`from`
A list of characters from which to translate

`to`
A list of characters to which to translate

`target`
The string on which to do the translation

Associative arrays make the translation part fairly easy. `t_ar` holds
the &ldquo;to&rdquo; characters, indexed by the &ldquo;from&rdquo; characters.  Then a simple
loop goes through `from`, one character at a time.  For each character
in `from`, if the character appears in `target`,
it is replaced with the corresponding `to` character.

The `translate()` function calls `stranslate()`, using `$0`
as the target.  The main program sets two global variables, `FROM` and
`TO`, from the command line, and then changes `ARGV` so that
`awk` reads from the standard input.

Finally, the processing rule simply calls `translate()` for each record:

    # translate.awk --- do tr-like stuff
    # Bugs: does not handle things like tr A-Z a-z; it has
    # to be spelled out. However, if `to' is shorter than `from',
    # the last character in `to' is used for the rest of `from'.
    
    function stranslate(from, to, target,     lf, lt, ltarget, t_ar, i, c,
                                                                   result)
    {
        lf = length(from)
        lt = length(to)
        ltarget = length(target)
        for (i = 1; i <= lt; i++)
            t_ar[substr(from, i, 1)] = substr(to, i, 1)
        if (lt < lf)
            for (; i <= lf; i++)
                t_ar[substr(from, i, 1)] = substr(to, lt, 1)
        for (i = 1; i <= ltarget; i++) {
            c = substr(target, i, 1)
            if (c in t_ar)
                c = t_ar[c]
            result = result c
        }
        return result
    }
    
    function translate(from, to)
    {
        return $0 = stranslate(from, to, $0)
    }
    
    # main program
    BEGIN {
    

        if (ARGC < 3) {
            print "usage: translate from to" > "/dev/stderr"
            exit
        }
    

        FROM = ARGV[1]
        TO = ARGV[2]
        ARGC = 2
        ARGV[1] = "-"
    }
    
    {
        translate(FROM, TO)
        print
    }
    

It is possible to do character transliteration in a user-level
function, but it is not necessarily efficient, and we (the `gawk`
developers) started to consider adding a built-in function.  However,
shortly after writing this program, we learned that Brian Kernighan
had added the `toupper()` and `tolower()` functions to his
`awk` (see [String Functions](#913-string-manipulation-functions)).  These functions handle the
vast majority of the cases where character transliteration is necessary,
and so we chose to simply add those functions to `gawk` as well
and then leave well enough alone.

An obvious improvement to this program would be to set up the
`t_ar` array only once, in a `BEGIN` rule. However, this
assumes that the &ldquo;from&rdquo; and &ldquo;to&rdquo; lists
will never change throughout the lifetime of the program.

Another obvious improvement is to enable the use of ranges,
such as &lsquo;a-z&rsquo;, as allowed by the `tr` utility.
Look at the code for cut.awk (see [Cut Program](#1121-cutting-out-fields-and-columns))
for inspiration.

---

Next: [Word Sorting](#1135-generating-word-usage-counts), Previous: [Translate Program](#1133-transliterating-characters), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.4 Printing Mailing Labels

Here is a &ldquo;real-world&rdquo;[79](#FOOT79)
program.  This
script reads lists of names and
addresses and generates mailing labels.  Each page of labels has 20 labels
on it, two across and 10 down.  The addresses are guaranteed to be no more
than five lines of data.  Each address is separated from the next by a blank
line.

The basic idea is to read 20 labels&rsquo; worth of data.  Each line of each label
is stored in the `line` array.  The single rule takes care of filling
the `line` array and printing the page when 20 labels have been read.

The `BEGIN` rule simply sets `RS` to the empty string, so that
`awk` splits records at blank lines
(see [Records](#41-how-input-is-split-into-records)).
It sets `MAXLINES` to 100, because 100 is the maximum number
of lines on the page
(20 * 5 = 100).

Most of the work is done in the `printpage()` function.
The label lines are stored sequentially in the `line` array.  But they
have to print horizontally: `line[1]` next to `line[6]`,
`line[2]` next to `line[7]`, and so on.  Two loops
accomplish this.  The outer loop, controlled by `i`, steps through
every 10 lines of data; this is each row of labels.  The inner loop,
controlled by `j`, goes through the lines within the row.
As `j` goes from 0 to 4, &lsquo;i+j&rsquo; is the `j`th line in
the row, and &lsquo;i+j+5&rsquo; is the entry next to it.  The output ends up
looking something like this:

    line 1          line 6
    line 2          line 7
    line 3          line 8
    line 4          line 9
    line 5          line 10
    &hellip;
    

The `printf` format string &lsquo;%-41s&rsquo; left-aligns
the data and prints it within a fixed-width field.

As a final note, an extra blank line is printed at lines 21 and 61, to keep
the output lined up on the labels.  This is dependent on the particular
brand of labels in use when the program was written.  You will also note
that there are two blank lines at the top and two blank lines at the bottom.

The `END` rule arranges to flush the final page of labels; there may
not have been an even multiple of 20 labels in the data:

    # labels.awk --- print mailing labels
    
    # Each label is 5 lines of data that may have blank lines.
    # The label sheets have 2 blank lines at the top and 2 at
    # the bottom.
    
    BEGIN    { RS = "" ; MAXLINES = 100 }
    
    function printpage(    i, j)
    {
        if (Nlines <= 0)
            return
    
        printf "\n\n"        # header
    
        for (i = 1; i <= Nlines; i += 10) {
            if (i == 21 || i == 61)
                print ""
            for (j = 0; j < 5; j++) {
                if (i + j > MAXLINES)
                    break
                printf "   %-41s %s\n", line[i+j], line[i+j+5]
            }
            print ""
        }
    
        printf "\n\n"        # footer
    
        delete line
    }
    
    # main rule
    {
        if (Count >= 20) {
            printpage()
            Count = 0
            Nlines = 0
        }
        n = split($0, a, "\n")
        for (i = 1; i <= n; i++)
            line[++Nlines] = a[i]
        for (; i <= 5; i++)
            line[++Nlines] = ""
        Count++
    }
    
    END {
        printpage()
    }
    

---

Next: [History Sorting](#1136-removing-duplicates-from-unsorted-text), Previous: [Labels Program](#1134-printing-mailing-labels), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.5 Generating Word-Usage Counts

When working with large amounts of text, it can be interesting to know
how often different words appear.  For example, an author may overuse
certain words, in which case he or she might wish to find synonyms to substitute
for words that appear too often. This subsection develops a
program for counting words and presenting the frequency information
in a useful format.

At first glance, a program like this would seem to do the job:

    # wordfreq-first-try.awk --- print list of word frequencies
    
    {
        for (i = 1; i <= NF; i++)
            freq[$i]++
    }
    
    

    END {
        for (word in freq)
            printf "%s\t%d\n", word, freq[word]
    }
    

The program relies on `awk`&rsquo;s default field-splitting
mechanism to break each line up into &ldquo;words&rdquo; and uses an
associative array named `freq`, indexed by each word, to count
the number of times the word occurs. In the `END` rule,
it prints the counts.

This program has several problems that prevent it from being
useful on real text files:

-  The `awk` language considers upper- and lowercase characters to be
distinct.  Therefore, &ldquo;bartender&rdquo; and &ldquo;Bartender&rdquo; are not treated
as the same word.  This is undesirable, because words are capitalized
if they begin sentences in normal text, and a frequency analyzer should
not be sensitive to capitalization.

-  Words are detected using the `awk` convention that fields are
separated just by whitespace.  Other characters in the input (except
newlines) don&rsquo;t have any special meaning to `awk`.  This means that
punctuation characters count as part of words.

-  The output does not come out in any useful order.  You&rsquo;re more likely to be
interested in which words occur most frequently or in having an alphabetized
table of how frequently each word occurs.

The first problem can be solved by using `tolower()` to remove case
distinctions.  The second problem can be solved by using `gsub()`
to remove punctuation characters.  Finally, we solve the third problem
by using the system `sort` utility to process the output of the
`awk` script.  Here is the new version of the program:

    # wordfreq.awk --- print list of word frequencies
    
    {
        $0 = tolower($0)    # remove case distinctions
        # remove punctuation
        gsub(/[^[:alnum:]_[:blank:]]/, "", $0)
        for (i = 1; i <= NF; i++)
            freq[$i]++
    }
    
    END {
        for (word in freq)
            printf "%s\t%d\n", word, freq[word]
    }
    

The regexp `/[^[:alnum:]_[:blank:]]/` might have been written
`/[[:punct:]]/`, but then underscores would also be removed,
and we want to keep them.

Assuming we have saved this program in a file named wordfreq.awk,
and that the data is in file1, the following pipeline:

    awk -f wordfreq.awk file1 | sort -k 2nr
    

produces a table of the words appearing in file1 in order of
decreasing frequency.

The `awk` program suitably massages the
data and produces a word frequency table, which is not ordered.
The `awk` script&rsquo;s output is then sorted by the `sort`
utility and printed on the screen.

The options given to `sort`
specify a sort that uses the second field of each input line (skipping
one field), that the sort keys should be treated as numeric quantities
(otherwise &lsquo;15&rsquo; would come before &lsquo;5&rsquo;), and that the sorting
should be done in descending (reverse) order.

The `sort` could even be done from within the program, by changing
the `END` action to:

    END {
        sort = "sort -k 2nr"
        for (word in freq)
            printf "%s\t%d\n", word, freq[word] | sort
        close(sort)
    }
    

This way of sorting must be used on systems that do not
have true pipes at the command-line (or batch-file) level.
See the general operating system documentation for more information on how
to use the `sort` program.

---

Next: [Extract Program](#1137-extracting-programs-from-texinfo-source-files), Previous: [Word Sorting](#1135-generating-word-usage-counts), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.6 Removing Duplicates from Unsorted Text

The `uniq` program
(see [Uniq Program](#1126-printing-nonduplicated-lines-of-text))
removes duplicate lines from *sorted* data.

Suppose, however, you need to remove duplicate lines from a data file but
that you want to preserve the order the lines are in.  A good example of
this might be a shell history file.  The history file keeps a copy of all
the commands you have entered, and it is not unusual to repeat a command
several times in a row.  Occasionally you might want to compact the history
by removing duplicate entries.  Yet it is desirable to maintain the order
of the original commands.

This simple program does the job.  It uses two arrays.  The `data`
array is indexed by the text of each line.
For each line, `data[$0]` is incremented.
If a particular line has not
been seen before, then `data[$0]` is zero.
In this case, the text of the line is stored in `lines[count]`.
Each element of `lines` is a unique command, and the indices of
`lines` indicate the order in which those lines are encountered.
The `END` rule simply prints out the lines, in order:

    # histsort.awk --- compact a shell history file
    # Thanks to Byron Rakitzis for the general idea
    
    

    {
        if (data[$0]++ == 0)
            lines[++count] = $0
    }
    

    END {
        for (i = 1; i <= count; i++)
            print lines[i]
    }
    

This program also provides a foundation for generating other useful
information.  For example, using the following `print` statement in the
`END` rule indicates how often a particular command is used:

    print data[lines[i]], lines[i]
    

This works because `data[$0]` is incremented each time a line is
seen.

---

Next: [Simple Sed](#1138-a-simple-stream-editor), Previous: [History Sorting](#1136-removing-duplicates-from-unsorted-text), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.7 Extracting Programs from Texinfo Source Files

Both this chapter and the previous chapter
([Library Functions]((#10-a-library-of-awk-functions)))
present a large number of `awk` programs.
If you want to experiment with these programs, it is tedious to type
them in by hand.  Here we present a program that can extract parts of a
Texinfo input file into separate files.

This Web page is written in [Texinfo](https://www.gnu.org/software/texinfo/),
the GNU Project&rsquo;s document formatting language.
A single Texinfo source file can be used to produce both
printed documentation, with TeX, and online documentation.
(Texinfo is fully documented in the book
Texinfo&mdash;The GNU Documentation Format,
available from the Free Software Foundation,
and also available [online](https://www.gnu.org/software/texinfo/manual/texinfo/).)

For our purposes, it is enough to know three things about Texinfo input
files:

-  The &ldquo;at&rdquo; symbol (&lsquo;@&rsquo;) is special in Texinfo, much as
the backslash (&lsquo;\&rsquo;) is in C
or `awk`.  Literal &lsquo;@&rsquo; symbols are represented in Texinfo source
files as &lsquo;@@&rsquo;.

-  Comments start with either &lsquo;@c&rsquo; or &lsquo;@comment&rsquo;.
The file-extraction program works by using special comments that start
at the beginning of a line.

-  Lines containing &lsquo;@group&rsquo; and &lsquo;@end group&rsquo; commands bracket
example text that should not be split across a page boundary.
(Unfortunately, TeX isn&rsquo;t always smart enough to do things exactly right,
so we have to give it some help.)

The following program, extract.awk, reads through a Texinfo source
file and does two things, based on the special comments.
Upon seeing &lsquo;@c system &hellip;&rsquo;,
it runs a command, by extracting the command text from the
control line and passing it on to the `system()` function
(see [I/O Functions](#914-inputoutput-functions)).
Upon seeing &lsquo;@c file filename&rsquo;, each subsequent line is sent to
the file filename, until &lsquo;@c endfile&rsquo; is encountered.
The rules in extract.awk match either &lsquo;@c&rsquo; or
&lsquo;@comment&rsquo; by letting the &lsquo;omment&rsquo; part be optional.
Lines containing &lsquo;@group&rsquo; and &lsquo;@end group&rsquo; are simply removed.
extract.awk uses the `join()` library function
(see [Join Function](#1026-merging-an-array-into-a-string)).

The example programs in the online Texinfo source for GAWK: Effective AWK Programming
(gawktexi.in) have all been bracketed inside &lsquo;file&rsquo; and
&lsquo;endfile&rsquo; lines.  The `gawk` distribution uses a copy of
extract.awk to extract the sample programs and install many
of them in a standard directory where `gawk` can find them.
The Texinfo file looks something like this:

    &hellip;
    This program has a @code{BEGIN} rule
    that prints a nice message:
    
    @example
    @c file examples/messages.awk
    BEGIN @{ print "Don't panic!" @}
    @c endfile
    @end example
    
    It also prints some final advice:
    
    @example
    @c file examples/messages.awk
    END @{ print "Always avoid bored archaeologists!" @}
    @c endfile
    @end example
    &hellip;
    

extract.awk begins by setting `IGNORECASE` to one, so that
mixed upper- and lowercase letters in the directives won&rsquo;t matter.

The first rule handles calling `system()`, checking that a command is
given (`NF` is at least three) and also checking that the command
exits with a zero exit status, signifying OK:

    # extract.awk --- extract files and run programs from Texinfo files
    
    BEGIN    { IGNORECASE = 1 }
    
    /^@c(omment)?[ \t]+system/ {
        if (NF < 3) {
            e = ("extract: " FILENAME ":" FNR)
            e = (e  ": badly formed `system' line")
            print e > "/dev/stderr"
            next
        }
        $1 = ""
        $2 = ""
        stat = system($0)
        if (stat != 0) {
            e = ("extract: " FILENAME ":" FNR)
            e = (e ": warning: system returned " stat)
            print e > "/dev/stderr"
        }
    }
    

The variable `e` is used so that the rule
fits nicely on the screen.

The second rule handles moving data into files.  It verifies that a
file name is given in the directive.  If the file named is not the
current file, then the current file is closed.  Keeping the current file
open until a new file is encountered allows the use of the &lsquo;>&rsquo;
redirection for printing the contents, keeping open-file management
simple.

The `for` loop does the work.  It reads lines using `getline`
(see [Getline](#410-explicit-input-with-getline)).
For an unexpected end-of-file, it calls the `unexpected_eof()`
function.  If the line is an &ldquo;endfile&rdquo; line, then it breaks out of
the loop.
If the line is an &lsquo;@group&rsquo; or &lsquo;@end group&rsquo; line, then it
ignores it and goes on to the next line.
Similarly, comments within examples are also ignored.

Most of the work is in the following few lines.  If the line has no &lsquo;@&rsquo;
symbols, the program can print it directly.
Otherwise, each leading &lsquo;@&rsquo; must be stripped off.
To remove the &lsquo;@&rsquo; symbols, the line is split into separate elements of
the array `a`, using the `split()` function
(see [String Functions](#913-string-manipulation-functions)).
The &lsquo;@&rsquo; symbol is used as the separator character.
Each element of `a` that is empty indicates two successive &lsquo;@&rsquo;
symbols in the original line.  For each two empty elements (&lsquo;@@&rsquo; in
the original file), we have to add a single &lsquo;@&rsquo; symbol back in.

When the processing of the array is finished, `join()` is called with the
value of `SUBSEP` (see [Multidimensional](#85-multidimensional-arrays)),
to rejoin the pieces back into a single
line.  That line is then printed to the output file:

    /^@c(omment)?[ \t]+file/ {
        if (NF != 3) {
            e = ("extract: " FILENAME ":" FNR ": badly formed `file' line")
            print e > "/dev/stderr"
            next
        }
        if ($3 != curfile) {
            if (curfile != "")
                close(curfile)
            curfile = $3
        }
    
        for (;;) {
            if ((getline line) <= 0)
                unexpected_eof()
            if (line ~ /^@c(omment)?[ \t]+endfile/)
                break
            else if (line ~ /^@(end[ \t]+)?group/)
                continue
            else if (line ~ /^@c(omment+)?[ \t]+/)
                continue
            if (index(line, "@") == 0) {
                print line > curfile
                continue
            }
            n = split(line, a, "@")
            # if a[1] == "", means leading @,
            # don't add one back in.
            for (i = 2; i <= n; i++) {
                if (a[i] == "") { # was an @@
                    a[i] = "@"
                    if (a[i+1] == "")
                        i++
                }
            }
    

            print join(a, 1, n, SUBSEP) > curfile
        }
    }
    

An important thing to note is the use of the &lsquo;>&rsquo; redirection.
Output done with &lsquo;>&rsquo; only opens the file once; it stays open and
subsequent output is appended to the file
(see [Redirection](#56-redirecting-output-of-print-and-printf)).
This makes it easy to mix program text and explanatory prose for the same
sample source file (as has been done here!) without any hassle.  The file is
only closed when a new data file name is encountered or at the end of the
input file.

Finally, the function `unexpected_eof()` prints an appropriate
error message and then exits.
The `END` rule handles the final cleanup, closing the open file:

    function unexpected_eof()
    {
        printf("extract: %s:%d: unexpected EOF or error\n",
                         FILENAME, FNR) > "/dev/stderr"
        exit 1
    }
    

    
    
    END {
        if (curfile)
            close(curfile)
    }
    

---

Next: [Igawk Program](#1139-an-easy-way-to-use-library-functions), Previous: [Extract Program](#1137-extracting-programs-from-texinfo-source-files), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.8 A Simple Stream Editor

The `sed` utility is a *stream editor*, a program that reads a
stream of data, makes changes to it, and passes it on.
It is often used to make global changes to a large file or to a stream
of data generated by a pipeline of commands.
Although `sed` is a complicated program in its own right, its most common
use is to perform global substitutions in the middle of a pipeline:

    command1 < orig.data | sed 's/old/new/g' | command2 > result
    

Here, &lsquo;s/old/new/g&rsquo; tells `sed` to look for the regexp
&lsquo;old&rsquo; on each input line and globally replace it with the text
&lsquo;new&rsquo; (i.e., all the occurrences on a line).  This is similar to
`awk`&rsquo;s `gsub()` function
(see [String Functions](#913-string-manipulation-functions)).

The following program, awksed.awk, accepts at least two command-line
arguments: the pattern to look for and the text to replace it with. Any
additional arguments are treated as data file names to process. If none
are provided, the standard input is used:

    # awksed.awk --- do s/foo/bar/g using just print
    #    Thanks to Michael Brennan for the idea
    
    function usage()
    {
        print "usage: awksed pat repl [files...]" > "/dev/stderr"
        exit 1
    }
    
    

    BEGIN {
        # validate arguments
        if (ARGC < 3)
            usage()
    

    
    
        RS = ARGV[1]
        ORS = ARGV[2]
    
        # don't use arguments as files
        ARGV[1] = ARGV[2] = ""
    }
    
    

    # look ma, no hands!
    {
        if (RT == "")
            printf "%s", $0
        else
            print
    }
    

The program relies on `gawk`&rsquo;s ability to have `RS` be a regexp,
as well as on the setting of `RT` to the actual text that terminates the
record (see [Records](#41-how-input-is-split-into-records)).

The idea is to have `RS` be the pattern to look for. `gawk`
automatically sets `$0` to the text between matches of the pattern.
This is text that we want to keep, unmodified.  Then, by setting `ORS`
to the replacement text, a simple `print` statement outputs the
text we want to keep, followed by the replacement text.

There is one wrinkle to this scheme, which is what to do if the last record
doesn&rsquo;t end with text that matches `RS`.  Using a `print`
statement unconditionally prints the replacement text, which is not correct.
However, if the file did not end in text that matches `RS`, `RT`
is set to the null string.  In this case, we can print `$0` using
`printf`
(see [Printf](#55-using-printf-statements-for-fancier-printing)).

The `BEGIN` rule handles the setup, checking for the right number
of arguments and calling `usage()` if there is a problem. Then it sets
`RS` and `ORS` from the command-line arguments and sets
`ARGV[1]` and `ARGV[2]` to the null string, so that they are
not treated as file names
(see [ARGC and ARGV](#753-using-argc-and-argv)).

The `usage()` function prints an error message and exits.
Finally, the single rule handles the printing scheme outlined earlier,
using `print` or `printf` as appropriate, depending upon the
value of `RT`.

---

Next: [Anagram Program](#11310-finding-anagrams-from-a-dictionary), Previous: [Simple Sed](#1138-a-simple-stream-editor), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.9 An Easy Way to Use Library Functions

In [Include Files](#27-including-other-files-into-your-program), we saw how `gawk` provides a built-in
file-inclusion capability.  However, this is a `gawk` extension.
This section provides the motivation for making file inclusion
available for standard `awk`, and shows how to do it using a
combination of shell and `awk` programming.

Using library functions in `awk` can be very beneficial. It
encourages code reuse and the writing of general functions. Programs are
smaller and therefore clearer.
However, using library functions is only easy when writing `awk`
programs; it is painful when running them, requiring multiple -f
options.  If `gawk` is unavailable, then so too is the `AWKPATH`
environment variable and the ability to put `awk` functions into a
library directory (see [Options](#22-command-line-options)).
It would be nice to be able to write programs in the following manner:

    # library functions
    @include getopt.awk
    @include join.awk
    &hellip;
    
    # main program
    BEGIN {
        while ((c = getopt(ARGC, ARGV, "a:b:cde")) != -1)
            &hellip;
        &hellip;
    }
    

The following program, igawk.sh, provides this service.
It simulates `gawk`&rsquo;s searching of the `AWKPATH` variable
and also allows *nested* includes (i.e., a file that is included
with `@include` can contain further `@include` statements).
`igawk` makes an effort to only include files once, so that nested
includes don&rsquo;t accidentally include a library function twice.

`igawk` should behave just like `gawk` externally.  This
means it should accept all of `gawk`&rsquo;s command-line arguments,
including the ability to have multiple source files specified via
-f and the ability to mix command-line and library source files.

The program is written using the POSIX Shell (`sh`) command
language.[80](#FOOT80) It works as follows:

1.  Loop through the arguments, saving anything that doesn&rsquo;t represent
`awk` source code for later, when the expanded program is run.

2.  For any arguments that do represent `awk` text, put the arguments into
a shell variable that will be expanded.  There are two cases:

1.  Literal text, provided with -e or --source.  This
text is just appended directly.

2.  Source file names, provided with -f.  We use a neat trick and
append &lsquo;@include filename&rsquo; to the shell variable&rsquo;s contents.
Because the file-inclusion program works the way `gawk` does, this
gets the text of the file included in the program at the correct point.

3.  Run an `awk` program (naturally) over the shell variable&rsquo;s contents to expand
`@include` statements.  The expanded program is placed in a second
shell variable.

4.  Run the expanded program with `gawk` and any other original command-line
arguments that the user supplied (such as the data file names).

This program uses shell variables extensively: for storing command-line arguments and
the text of the `awk` program that will expand the user&rsquo;s program, for the
user&rsquo;s original program, and for the expanded program.  Doing so removes some
potential problems that might arise were we to use temporary files instead,
at the cost of making the script somewhat more complicated.

The initial part of the program turns on shell tracing if the first
argument is &lsquo;debug&rsquo;.

The next part loops through all the command-line arguments.
There are several cases of interest:

--
This ends the arguments to `igawk`.  Anything else should be passed on
to the user&rsquo;s `awk` program without being evaluated.

-W
This indicates that the next option is specific to `gawk`.  To make
argument processing easier, the -W is appended to the front of the
remaining arguments and the loop continues.  (This is an `sh`
programming trick.  Don&rsquo;t worry about it if you are not familiar with
`sh`.)

-v, -F
These are saved and passed on to `gawk`.

-f, --file, --file=, -Wfile=
The file name is appended to the shell variable `program` with an
`@include` statement.
The `expr` utility is used to remove the leading option part of the
argument (e.g., &lsquo;--file=&rsquo;).
(Typical `sh` usage would be to use the `echo` and `sed`
utilities to do this work.  Unfortunately, some versions of `echo` evaluate
escape sequences in their arguments, possibly mangling the program text.
Using `expr` avoids this problem.)

--source, --source=, -Wsource=
The source text is appended to `program`.

--version, -Wversion
`igawk` prints its version number, runs &lsquo;gawk --version&rsquo;
to get the `gawk` version information, and then exits.

If none of the -f, --file, -Wfile, --source,
or -Wsource arguments are supplied, then the first nonoption argument
should be the `awk` program.  If there are no command-line
arguments left, `igawk` prints an error message and exits.
Otherwise, the first argument is appended to `program`.
In any case, after the arguments have been processed,
the shell variable
`program` contains the complete text of the original `awk`
program.

The program is as follows:

    #! /bin/sh
    # igawk --- like gawk but do @include processing
    
    if [ "$1" = debug ]
    then
        set -x
        shift
    fi
    
    # A literal newline, so that program text is formatted correctly
    n='
    '
    
    # Initialize variables to empty
    program=
    opts=
    
    while [ $# -ne 0 ] # loop over arguments
    do
        case $1 in
        --)     shift
                break ;;
    
        -W)     shift
                # The ${x?'message here'} construct prints a
                # diagnostic if $x is the null string
                set -- -W"${@?'missing operand'}"
                continue ;;
    
        -[vF])  opts="$opts $1 '${2?'missing operand'}'"
                shift ;;
    
        -[vF]*) opts="$opts '$1'" ;;
    
        -f)     program="$program$n@include ${2?'missing operand'}"
                shift ;;
    
        -f*)    f=$(expr "$1" : '-f\(.*\)')
                program="$program$n@include $f" ;;
    
        -[W-]file=*)
                f=$(expr "$1" : '-.file=\(.*\)')
                program="$program$n@include $f" ;;
    
        -[W-]file)
                program="$program$n@include ${2?'missing operand'}"
                shift ;;
    
        -[W-]source=*)
                t=$(expr "$1" : '-.source=\(.*\)')
                program="$program$n$t" ;;
    
        -[W-]source)
                program="$program$n${2?'missing operand'}"
                shift ;;
    
        -[W-]version)
                echo igawk: version 3.0 1>&2
                gawk --version
                exit 0 ;;
    
        -[W-]*) opts="$opts '$1'" ;;
    
        *)      break ;;
        esac
        shift
    done
    
    if [ -z "$program" ]
    then
         program=${1?'missing program'}
         shift
    fi
    
    # At this point, `program' has the program.
    

The `awk` program to process `@include` directives
is stored in the shell variable `expand_prog`.  Doing this keeps
the shell script readable.  The `awk` program
reads through the user&rsquo;s program, one line at a time, using `getline`
(see [Getline](#410-explicit-input-with-getline)).  The input
file names and `@include` statements are managed using a stack.
As each `@include` is encountered, the current file name is
&ldquo;pushed&rdquo; onto the stack and the file named in the `@include`
directive becomes the current file name.  As each file is finished,
the stack is &ldquo;popped,&rdquo; and the previous input file becomes the current
input file again.  The process is started by making the original file
the first one on the stack.

The `pathto()` function does the work of finding the full path to
a file.  It simulates `gawk`&rsquo;s behavior when searching the
`AWKPATH` environment variable
(see [AWKPATH Variable](#251-the-awkpath-environment-variable)).
If a file name has a &lsquo;/&rsquo; in it, no path search is done.
Similarly, if the file name is `"-"`, then that string is
used as-is.  Otherwise,
the file name is concatenated with the name of each directory in
the path, and an attempt is made to open the generated file name.
The only way to test if a file can be read in `awk` is to go
ahead and try to read it with `getline`; this is what `pathto()`
does.[81](#FOOT81)
If the file can be read, it is closed and the file name
is returned:

    expand_prog='
    
    function pathto(file,    i, t, junk)
    {
        if (index(file, "/") != 0)
            return file
    
        if (file == "-")
            return file
    
        for (i = 1; i <= ndirs; i++) {
            t = (pathlist[i] "/" file)
    

            if ((getline junk < t) > 0) {
                # found it
                close(t)
                return t
            }
    

        }
        return ""
    }
    

The main program is contained inside one `BEGIN` rule.  The first thing it
does is set up the `pathlist` array that `pathto()` uses.  After
splitting the path on &lsquo;:&rsquo;, null elements are replaced with `"."`,
which represents the current directory:

    BEGIN {
        path = ENVIRON["AWKPATH"]
        ndirs = split(path, pathlist, ":")
        for (i = 1; i <= ndirs; i++) {
            if (pathlist[i] == "")
                pathlist[i] = "."
        }
    

The stack is initialized with `ARGV[1]`, which will be `"/dev/stdin"`.
The main loop comes next.  Input lines are read in succession. Lines that
do not start with `@include` are printed verbatim.
If the line does start with `@include`, the file name is in `$2`.
`pathto()` is called to generate the full path.  If it cannot, then the program
prints an error message and continues.

The next thing to check is if the file is included already.  The
`processed` array is indexed by the full file name of each included
file and it tracks this information for us.  If the file is
seen again, a warning message is printed. Otherwise, the new file name is
pushed onto the stack and processing continues.

Finally, when `getline` encounters the end of the input file, the file
is closed and the stack is popped.  When `stackptr` is less than zero,
the program is done:

        stackptr = 0
        input[stackptr] = ARGV[1] # ARGV[1] is first file
    
        for (; stackptr >= 0; stackptr--) {
            while ((getline < input[stackptr]) > 0) {
                if (tolower($1) != "@include") {
                    print
                    continue
                }
                fpath = pathto($2)
                if (fpath == "") {
                    printf("igawk: %s:%d: cannot find %s\n",
                        input[stackptr], FNR, $2) > "/dev/stderr"
                    continue
                }
                if (! (fpath in processed)) {
                    processed[fpath] = input[stackptr]
                    input[++stackptr] = fpath  # push onto stack
                } else
                    print $2, "included in", input[stackptr],
                        "already included in",
                        processed[fpath] > "/dev/stderr"
            }
            close(input[stackptr])
        }
    }'  # close quote ends `expand_prog' variable
    
    processed_program=$(gawk -- "$expand_prog" /dev/stdin << EOF
    $program
    EOF
    )
    

The shell construct &lsquo;command << marker&rsquo; is called
a *here document*.  Everything in the shell script up to the
marker is fed to command as input.  The shell processes
the contents of the here document for variable and command substitution
(and possibly other things as well, depending upon the shell).

The shell construct &lsquo;$(&hellip;)&rsquo; is called *command substitution*.
The output of the command inside the parentheses is substituted
into the command line.
Because the result is used in a variable assignment,
it is saved as a single string, even if the results contain whitespace.

The expanded program is saved in the variable `processed_program`.
It&rsquo;s done in these steps:

1.  Run `gawk` with the `@include`-processing program (the
value of the `expand_prog` shell variable) reading standard input.

2.  Standard input is the contents of the user&rsquo;s program,
from the shell variable `program`.
Feed its contents to `gawk` via a here document.

3.  Save the results of this processing in the shell variable
`processed_program` by using command substitution.

The last step is to call `gawk` with the expanded program,
along with the original
options and command-line arguments that the user supplied:

    eval gawk $opts -- '"$processed_program"' '"$@"'
    

The `eval` command is a shell construct that reruns the shell&rsquo;s parsing
process.  This keeps things properly quoted.

This version of `igawk` represents the fifth version of this program.
There are four key simplifications that make the program work better:

-  Using `@include` even for the files named with -f makes building
the initial collected `awk` program much simpler; all the
`@include` processing can be done once.

-  Not trying to save the line read with `getline`
in the `pathto()` function when testing for the
file&rsquo;s accessibility for use with the main program simplifies things
considerably.

-  Using a `getline` loop in the `BEGIN` rule does it all in one
place.  It is not necessary to call out to a separate loop for processing
nested `@include` statements.

-  Instead of saving the expanded program in a temporary file, putting it in a shell variable
avoids some potential security problems.
This has the disadvantage that the script relies upon more features
of the `sh` language, making it harder to follow for those who
aren&rsquo;t familiar with `sh`.

Also, this program illustrates that it is often worthwhile to combine
`sh` and `awk` programming together.  You can usually
accomplish quite a lot, without having to resort to low-level programming
in C or C++, and it is frequently easier to do certain kinds of string
and argument manipulation using the shell than it is in `awk`.

Finally, `igawk` shows that it is not always necessary to add new
features to a program; they can often be layered on top.[82](#FOOT82)

---

Next: [Signature Program](#11311-and-now-for-something-completely-different), Previous: [Igawk Program](#1139-an-easy-way-to-use-library-functions), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.10 Finding Anagrams from a Dictionary

An interesting programming challenge is to
search for *anagrams* in a
word list (such as
/usr/share/dict/words on many GNU/Linux systems).
One word is an anagram of another if both words contain
the same letters
(e.g., &ldquo;babbling&rdquo; and &ldquo;blabbing&rdquo;).

Column 2, Problem C, of Jon Bentley&rsquo;s Programming Pearls, Second
Edition, presents an elegant algorithm.  The idea is to give words that
are anagrams a common signature, sort all the words together by their
signatures, and then print them.  Dr. Bentley observes that taking the
letters in each word and sorting them produces those common signatures.

The following program uses arrays of arrays to bring together
words with the same signature and array sorting to print the words
in sorted order:

    # anagram.awk --- An implementation of the anagram-finding algorithm
    #                 from Jon Bentley's "Programming Pearls," 2nd edition.
    #                 Addison Wesley, 2000, ISBN 0-201-65788-0.
    #                 Column 2, Problem C, section 2.8, pp 18-20.
    
    /'s$/   { next }        # Skip possessives
    

The program starts with a header, and then a rule to skip
possessives in the dictionary file. The next rule builds
up the data structure. The first dimension of the array
is indexed by the signature; the second dimension is the word
itself:

    {
        key = word2key($1)  # Build signature
        data[key][$1] = $1  # Store word with signature
    }
    

The `word2key()` function creates the signature.
It splits the word apart into individual letters,
sorts the letters, and then joins them back together:

    # word2key --- split word apart into letters, sort, and join back together
    
    function word2key(word,     a, i, n, result)
    {
        n = split(word, a, "")
        asort(a)
    
        for (i = 1; i <= n; i++)
            result = result a[i]
    
        return result
    }
    

Finally, the `END` rule traverses the array
and prints out the anagram lists.  It sends the output
to the system `sort` command because otherwise
the anagrams would appear in arbitrary order:

    END {
        sort = "sort"
        for (key in data) {
            # Sort words with same key
            nwords = asorti(data[key], words)
            if (nwords == 1)
                continue
    
            # And print. Minor glitch: trailing space at end of each line
            for (j = 1; j <= nwords; j++)
                printf("%s ", words[j]) | sort
            print "" | sort
        }
        close(sort)
    }
    

Here is some partial output when the program is run:

    $ gawk -f anagram.awk /usr/share/dict/words | grep '^b'
    &hellip;
    babbled blabbed
    babbler blabber brabble
    babblers blabbers brabbles
    babbling blabbing
    babbly blabby
    babel bable
    babels beslab
    babery yabber
    &hellip;
    

---

Previous: [Anagram Program](#11310-finding-anagrams-from-a-dictionary), Up: [Miscellaneous Programs](#113-running-long-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

#### 11.3.11 And Now for Something Completely Different

The following program was written by Davide Brini
and is published on [his website](http://backreference.org/2011/02/03/obfuscated-awk/).
It serves as his signature in the Usenet group `comp.lang.awk`.
He supplies the following copyright terms:

> Copyright &copy; 2008 Davide Brini
> 
> 
> Copying and distribution of the code published in this page, with or without
> modification, are permitted in any medium without royalty provided the copyright
> notice and this notice are preserved.

Here is the program:

    awk 'BEGIN{O="~"~"~";o="=="=="==";o+=+o;x=O""O;while(X++<=x+o+o)c=c"%c";
    printf c,(x-O)*(x-O),x*(x-o)-o,x*(x-O)+x-O-o,+x*(x-O)-x+o,X*(o*o+O)+x-O,
    X*(X-x)-o*o,(x+X)*o*o+o,x*(X-x)-O-O,x-O+(O+o+X+x)*(o+O),X*X-X*(x-O)-x+O,
    O+X*(o*(o+O)+O),+x+O+X*o,x*(x-o),(o+X+x)*o*o-(x-O-O),O+(X-x)*(X+O),x-O}'
    

We leave it to you to determine what the program does.  (If you are
truly desperate to understand it, see Chris Johansen&rsquo;s explanation,
which is embedded in the Texinfo source file for this Web page.)

---

Next: [Programs Exercises](#115-exercises), Previous: [Miscellaneous Programs](#113-running-long-programs), Up: [Sample Programs](#11-practical-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

### 11.4 Summary

-  The programs provided in this chapter
continue on the theme that reading programs is an excellent way to learn
Good Programming.

-  Using &lsquo;#!&rsquo; to make `awk` programs directly runnable makes
them easier to use.  Otherwise, invoke the program using &lsquo;awk
-f &hellip;&rsquo;.

-  Reimplementing standard POSIX programs in `awk` is a pleasant
exercise; `awk`&rsquo;s expressive power lets you write such programs
in relatively few lines of code, yet they are functionally complete
and usable.

-  One of standard `awk`&rsquo;s weaknesses is working with individual
characters.  The ability to use `split()` with the empty string as
the separator can considerably simplify such tasks.

-  The examples here demonstrate the usefulness of the library
functions from [Library Functions]((#10-a-library-of-awk-functions))
for a number of real (if small) programs.

-  Besides reinventing POSIX wheels, other programs solved a selection of
interesting problems, such as finding duplicate words in text, printing
mailing labels, and finding anagrams.

---

Previous: [Programs Summary](#114-summary), Up: [Sample Programs](#11-practical-awk-programs)   [[Contents](#table-of-contents)][[Index](#Index)]

### 11.5 Exercises

1.  Rewrite cut.awk (see [Cut Program](#1121-cutting-out-fields-and-columns))
using `split()` with `""` as the separator.

2.  In [Egrep Program](#1122-searching-for-regular-expressions-in-files), we mentioned that &lsquo;egrep -i&rsquo; could be
simulated in versions of `awk` without `IGNORECASE` by
using `tolower()` on the line and the pattern. In a footnote there,
we also mentioned that this solution has a bug: the translated line is
output, and not the original one.  Fix this problem.

3.  The POSIX version of `id` takes options that control which
information is printed.  Modify the `awk` version
(see [Id Program](#1123-printing-out-user-information)) to accept the same arguments and perform in the
same way.

4.  The `split.awk` program (see [Split Program](#1124-splitting-a-large-file-into-pieces)) assumes
that letters are contiguous in the character set,
which isn&rsquo;t true for EBCDIC systems.
Fix this problem.
(Hint: Consider a different way to work through the alphabet,
without relying on `ord()` and `chr()`.)

5.  In uniq.awk (see [Uniq Program](#1126-printing-nonduplicated-lines-of-text), the
logic for choosing which lines to print represents a *state
machine*, which is &ldquo;a device that can be in one of a set number of stable
conditions depending on its previous condition and on the present values
of its inputs.&rdquo;[83](#FOOT83)
Brian Kernighan suggests that
&ldquo;an alternative approach to state machines is to just read
the input into an array, then use indexing.  It&rsquo;s almost always
easier code, and for most inputs where you would use this, just
as fast.&rdquo;  Rewrite the logic to follow this
suggestion.

6.  Why can&rsquo;t the wc.awk program (see [Wc Program](#1127-counting-things)) just
use the value of `FNR` in `endfile()`?
Hint: Examine the code in [Filetrans Function](#1031-noting-data-file-boundaries).

7.  Manipulation of individual characters in the `translate` program
(see [Translate Program](#1133-transliterating-characters)) is painful using standard `awk`
functions.  Given that `gawk` can split strings into individual
characters using `""` as the separator, how might you use this
feature to simplify the program?

8.  The extract.awk program (see [Extract Program](#1137-extracting-programs-from-texinfo-source-files)) was written
before `gawk` had the `gensub()` function.  Use it
to simplify the code.

9.  Compare the performance of the awksed.awk program
(see [Simple Sed](#1138-a-simple-stream-editor)) with the more straightforward:

    BEGIN {
        pat = ARGV[1]
        repl = ARGV[2]
        ARGV[1] = ARGV[2] = ""
    }
    
    { gsub(pat, repl); print }
    

10.  What are the advantages and disadvantages of awksed.awk versus
the real `sed` utility?

11.  In [Igawk Program](#1139-an-easy-way-to-use-library-functions), we mentioned that not trying to save the line
read with `getline` in the `pathto()` function when testing
for the file&rsquo;s accessibility for use with the main program simplifies
things considerably.  What problem does this engender though?

12. 
As an additional example of the idea that it is not always necessary to
add new features to a program, consider the idea of having two files in
a directory in the search path:

default.awk
This file contains a set of default library functions, such
as `getopt()` and `assert()`.

site.awk
This file contains library functions that are specific to a site or
installation; i.e., locally developed functions.
Having a separate file allows default.awk to change with
new `gawk` releases, without requiring the system administrator to
update it each time by adding the local functions.

One user
suggested that `gawk` be modified to automatically read these files
upon startup.  Instead, it would be very simple to modify `igawk`
to do this. Since `igawk` can process nested `@include`
directives, default.awk could simply contain `@include`
statements for the desired library functions.
Make this change.

13.  Modify anagram.awk (see [Anagram Program](#11310-finding-anagrams-from-a-dictionary)), to avoid
the use of the external `sort` utility.

---

Next: [Internationalization](#Internationalization), Previous: [Sample Programs](#11-practical-awk-programs), Up: [Top](#Top)   [[Contents](#table-of-contents)][[Index](#Index)]
