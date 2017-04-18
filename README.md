# SFDC Coding Conventions

The following is a work in progress. Some conventions will need to be modifed per project. More often consistency within the project is more important than the exact convention that is followed. 

> You can drive on the left or right hand side of the road. Just make sure everyone does it consistently.

Based largely on the following locations:

*	[What is a good set of coding conventions for Salesforce development?](https://salesforce.stackexchange.com/q/9605/102)
*	[Apex Style Guide](https://github.com/PolarisProject/salesforceStyleGuide/blob/master/Apex%20style%20guide.md)
*	[Naming Conventions](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_naming_conventions.htm)
* [Salesforce Coding Conventions](https://docs.google.com/a/callawaycloudconsulting.com/document/d/1q8edvOeq-OBUqpeYShNvdrfwj_0nBHWpuIA172KFjKk/edit#)

## Introduction
### Why Have Code Conventions

Code conventions are important to programmers for a number of reasons:
* 80% of the lifetime cost of a piece of software goes to maintenance (or at least [the majority of the cost](https://softwareengineering.stackexchange.com/q/47991/4813))
* Hardly any software is maintained for its whole life by the original author
* Code conventions improve the readability of software, allowing engineers to understand new code more quickly and thoroughly
* [Source code is our product](http://wiki.c2.com/?TheSourceCodeIsTheProduct), and as much effort should be made in packaging it cleanly as one would with a consumer product at your grocery store
* Developers can focus more on the objective and less of the nuances of code formatting.
* Less ongoing debate about often subjective stylics differences.


A good place to start is the [Java Coding Conventions](http://www.oracle.com/technetwork/java/codeconventions-150003.pdf) since the languages are so similar. For Salesforce the conventions also need to cover Salesforce specific uses such as:
* formatting SOQL queries, 
* validation rules
* formula fields.

The goal of this style guide is to provide consistency between the contributions of individual developers. It's important to have a standard so that:

1.	New and old developers, and outside contractors of all sorts can easily read, understand and maintain the code base.
2.	Code merges are easy to handle and are content-ful, not style-ful.

## SFDC custom object conventions

Field naming, object naming, every field must have descriptive help text and description, buttons vs custom links, etc...

### Boolean variables
Aim to name these so they can naturally be read aloud in if statement conditions (or in a Visualforce-referenced boolean getter). Salesforce uses this approach for SObject field names such as ```Opportunity.isWon```

```java
Boolean hasExceededCapacity = false; 
\\...
if (hasExceededCapacity) \\...
```

### List, Sets, and Maps

The three primary (and only) [collection classes](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_collections.htm) are common candidates for variables. While they are all collections their behaviour and methods differ.

Very roughly:

* Lists can be indexed and iterated, used for DML
* Sets are good for uniqueness and SOQL `IN` clauses
* Maps are directly indexable by key and crucial to bulkification

As such, it is useful to be able to distinguish the type from the collection variable name. This can be accomplised with a suffix of either List, Set, or Map.

If a List, the List is named with some prefix indicating what is being collected followed by the purpose of the list:

```java
List<Account> accountsToBeUpdatedList; // List to use in DML statement to update Accounts
```

If a Set, the Set is named with some prefix indicating what is being collected followed the purpose of the set:

```java
Set<ID> opportunityIdsProcessedSet; // Set of OpportunityIds used in a SOQL where clause
```

If a Map, the Map is always named as keyToWhatMap as in:

```java
Map<ID,Account> accountIdToAccountMap; // Map of account Ids to Accounts

Map<ID,List<Account>> accountIdToAccountListMap; // Map of account Ids to List of Accounts
```

The advantage to the above is that when you have a method/class with lists, sets, and maps, it is easy to see what each variable does and what its inherent 'limitations/features' are as you debug or extend.

Avoid:

Collection names that don't make it apparent that the variable is a collection rather than a single record.

```java
 List<Account> acct; // Might be confused with being a single Account without using plural name
 ```

Not implying what the variables type is.

```java
 Map<ID,Contact> contacts; // variable name implies a list but is declared as a map!
```

**this**

Using the `this.` prefix can help disambiguate is a variable is local to the method of a class instance variable.

```java
public class Foo { 
    Integer aUpdCounter;

    public bar() { 
        this.aUpdCounter++; // use of this.
    }
}
```

### Classes

Class names should be nouns, in mixed case with the first letter of each internal word capitalized ([Upper Camel Case](http://wiki.c2.com/?UpperCamelCase)). Try to keep your class names simple and descriptive. Use whole words—avoid acronyms and abbreviations (unless the abbreviation is much more widely used than the long form, such as URL or HTML). Examples:

```java
public class Raster { }

public class ImageSprite { }
```

### Interfaces

Interface names should be capitalized like class names. Example:

```java
public interface PricingCalculatorTarget { }
```

### Methods

Methods should be verbs, in mixed case with the first letter lowercase, with the first letter of each internal word capitalized. AKA: [Lower Camel](http://wiki.c2.com/?LowerCamelCase)

Case

```java
run();

runFast();

getBackground();
```

### Variables

Except for constants, all instance, class, and class variables are in mixed case with a lowercase first letter. Internal words start with capital letters. AKA: [Lower Camel](http://wiki.c2.com/?LowerCamelCase)

Variable names should be short yet meaningful. The choice of a variable name should be mnemonic— that is, designed to indicate to the casual observer the intent of its use. 

One-character variable names should be **avoided** *except* for temporary “throwaway” variables. Common names for temporary variables are i, j, k, m, and n for integers; c, d, and e for characters. These would be minimal in scope, such as for a `for` loop control variable. 

Examples

```java
Integer totalOrderCount;

Double pageWidth;

Account currentAccount;
```

### Constants

The names of constants should be all uppercase with words separated by
under-scores (`_`)

```java
Integer MIN_WIDTH = 4;

Integer MAX_HEIGHT = 999;
```

File Organization
=================

Apex Classes
------------

An apex class consists of sections that should be separated by blank lines and multi-line comment identifying each section. Within a section related declarations should be grouped functionality with a single line comment and then alphabetically.

Apex classes longer than 1000 lines are cumbersome and should be avoided.

Templates should be used as a starting point when available.

Class Layout
------------

```java
/* 
 * Description:  
 *    What is the purpose of this class? 
 */ 
public class YourClass {  
    /* Constants */
    /* Static Variables */
    /* Instance Variables */
    /* Static Initializers */
    /* Factory Methods */
    /* Constructors */
    /* Instance Methods */
    /* Static Methods */
    /* Inner Classes */
}
```

Header Comments
---------------

Header comments are block comments at the start of each apex class and should list a brief one line description of the class purpose. The header comments may optionally be expanded to include info on proper usage or caveats.

Ideally the modifications made to the class will be tracked by source control rather than headers indicating developer names. I.e. No need to modify the header with the name and email address of everyone who has ever modified the file.

Example:

```java
/*
 *   Description:
 *   Core methods for generating agreements from opportunities
 */
```

> **Trigger rules**

* Aim for **one** Trigger per sObject type. This is important to maintain control of the order of execution.
* The trigger should do the absolute minimal amount of work to delagate the work off to an Apex class (or classes). The important point here is the the trigger itself doesn't enforce the business logic.

SOQL
----

For SOQL keep it as readable as possible so reader can see its function
in a quick scan. I might move the query into its own self describing
named function, too, if that helps.

1.  The first line has the assignment to the variable and the end line
    has the closing bracket.
2.  Use Uppercase first letter for keywords (seems to work well with syntax highlighter in IDE, isn't a pain to type, and the
    Force.com IDE's schema browser generates it that way).
3.  Group like fields on the same line (e.g., from the same parent), breaking for new groups of fields or if it gets too long to fit in a reasonable IDE window (100 characters?).
  1.  Primary fields of an object first.
  2.  Parent fields second.
  3.  Related lists last.
4.  Four spaces for indentation.
5.  Indent for readability.

## Indentation

Four spaces should be used as the unit of indentation. Spaces for tabs are preferred over tabs for tabs as tabs are inconsistently expressed as four and eight spaces in various tools. Additionally web based editor tools, i.e. the developer console, frequently don't support tabs and convert them on editing.

**_Note that [indentation contributes to the apex character limit](https://salesforce.stackexchange.com/a/37267/102). Spaces versus tabs can become significant with larger code bases_**

### Line Length

Avoid lines longer than 80 characters. While large monitors have made this limit less important, many tools either hide parts of lines longer than 80 characters, requiring alternate approaches or a lot of horizontal scrolling. For example, bitbucket requires you to scroll horizontally while reviewing code requests for longer lines.

### Wrapping Lines

When an expression will not fit on a single line, break it according to these general principles:

* Break after a comma
* Break before an operator
* Prefer higher-level breaks to lower-level breaks
* Align the new line with the beginning of the expression at the same level on the previous line
* If the above rules lead to confusing code or to code that’s squished up against the right margin, just indent 8 spaces instead.

Examples of breaking method calls 
```
function(longExpression1,
longExpression2, longExpression3, longExpression4, longExpression5);

 var = function1(longExpression1, function2(longExpression2, longExpression3));
 ```

Following are two examples of breaking an arithmetic expression. The first is preferred, since the break occurs outside the parenthesized expression, which is at the higher level.

AVOID:

```java
longName1 = longName2 * (longName3 + longName4
                         - longName5) + 4 * longName6;
```

BETTER:

```java
longName1 = longName2 * (longName3 + longName4 - longName5)
            + 4 * longName6;
```

Following are two examples of indenting method declarations. The first is the conventional case. The second would shift the second and third lines to the far right if it used conventional indentation, so instead it indents only 8 spaces.

CONVENTIONAL:

```java
someMethod(Integer anArg, Account anotherArg, String yetAnotherArg,
            Case andStillAnotherArg) {
    \\...
}
```

ALTERNATE TO AVOID DEEP INDENTS:

```java
private static override absurdlyLongMethodNameFromHell(Integer anArg,
        Account anotherArg, String yetAnotherArg,
        Case andStillAnotherArg) {
    \\ ...
}
```

Line wrapping for if statements should generally use the 8-space rule, since conventional (4 space) indentation makes seeing the body
difficult.

AVOID:

```java
if ((condition1 && condition2)
    || (condition3 && condition4)
    ||!(condition5 && condition6)) { // BAD WRAPS
    doSomethingAboutIt(); // MAKE THIS LINE EASY TO MISS
}
```

BETTER:

```java
if ((condition1 && condition2)
        || (condition3 && condition4) 
        ||!(condition5 && condition6)) {
    doSomethingAboutIt();
}
```

ALSO GOOD:

```java
 if ((condition1 && condition2) || (condition3 && condition4)
         ||!(condition5 && condition6)) {
     doSomethingAboutIt();
 }
```

Here are three acceptable ways to format ternary expressions:
```java
alpha = (aLongBooleanExpression) ? beta : gamma;
alpha = (aLongBooleanExpression) ? beta 
                                 : gamma;
alpha = (aLongBooleanExpression) 
        ? beta 
        : gamma;
```

Comments
========

When to Comment
---------------

The best kind of comments are the ones you don’t need. You should first strive to make your code as simple as possible to understand without relying on comments as a crutch. Only at the point where the code cannot be made easier to understand should you begin to add comments. 
_Adapted from_ [*Coding Horror: Code Tells You How, Comments Tell You
Why*](http://www.codinghorror.com/blog/2006/12/code-tells-you-how-comments-tell-you-why.html)

Block Comments (aka Multi-Line Comments)
----------------------------------------

With the exception of [*header comments*](), block comments should be avoided due to issues with Sublime Text’s native re-indentation feature. Instead use end-of-line comments (//).

Traditional Java comments have an asterisk “\*” at the beginning of each line except the first and last. All lines except the first should be indented one space. Text snippets can help make writing in this style easier.

```java
/*
 * Here is a block comment.
 */
```

~~Optionally, you can skip the asterisks at the beginning of each line as shown below~~

```java
/*
 Description: Another form of block comment
*/
```

Single-Line Comments
--------------------

Short comments can appear on a single line indented to the level of the code that follows. A single-line comment should be preceded by a blank line.

```java
if (condition) {

    // Handle the condition.
    //...
}
```

Trailing Comments
-----------------

Very short comments can appear on the same line as the code they describe, but should be shifted far enough to separate them from the
statements. If more than one short comment appears in a chunk of code, they should all be indented to the same tab setting.

```java
 if (a == 2) { 
     return true; // special case
 } else { 
     return isPrime(a); // works only for odd a
 }
```

Declarations
============

Number Per Line
---------------

One declaration per line is recommended since it encourages commenting.
In other words,

```java
 Integer level; // indentation level
 Integer size; // size of table
```

is preferred over

```java
 Integer var1, var2;
```

In absolutely no case should variables and functions be declared on the
same line. Example:

```
 Long dbaddr, getDbaddr(); // WRONG!
```

Do not put different types on the same line. Example:

```
 Integer foo; String\[\] fooarray; //WRONG!
```

Placement
---------

Place declarations to [minimize variable scope](https://refactoring.com/catalog/reduceScopeOfVariable.html).

~~Put declarations only at the beginning of blocks. (A block is any code surrounded by curly braces `{` and `}`.) Don't wait to declare variables until their first use; it can confuse the unwary programmer and hamper code portability within the scope.~~

Variables should be declared as close to their use as possible.

```java
public void MyMethod() {
    Integer int1; // beginning of method block 
    if (condition) {
        Integer int2; // beginning of "if" block
        \\ ...
    }
}
```

The one exception to the rule is indexes of for loops, which can be declared in the for statement:

```java
 for (Integer i = 0; i &lt; maxLoops; i++) { ...
```

Avoid local declarations that hide declarations at higher levels. For example, do not declare the same variable name in an inner block:

```java
Integer count;
\\...

func() {
    if (condition) {
        Integer count; // AVOID!
        \\...
    }
    \\...
}
```

Initialization
--------------

Try to initialize local variables where they’re declared. The only reason not to initialize a variable where it’s declared is if the
initial value depends on some computation occurring first.

Class and Interface Declarations
--------------------------------

When coding classes and interfaces, the following formatting rules
should be followed:

* No space between a method name and the parenthesis “(“ starting its parameter list
* Open brace “{” appears at the end of the same line as the declaration statement
* Closing brace “}” starts a line by itself indented to match its  corresponding opening statement, except when it is a null statement
 the “}” should appear immediately after the “{“ 
 
 ```
 class Sample extends
 BaseClass {

 Integer ivar1;

 Integer ivar2;

 Sample(Integer i, Integer j) { ivar1 = i;

 ivar2 = j;

 }

 Integer emptyMethod() {}

 ...

 }

```

* Methods are separated by a blank line

Statements
==========

Simple Statements
-----------------

Each line should contain at most one statement. Example:

```
 argv++; argc--; // AVOID!
```

Another example:

```
 if (err) {

 system.debug(‘error’); return; //VERY WRONG!

 }
```

Compound Statements
-------------------

Compound statements are statements that contain lists of statements
enclosed in braces

“{ statements }”. See the following sections for examples.

* The enclosed statements should be indented one more level than the
 compound statement.

* The opening brace should be at the end of the line that begins the
 compound statement; the closing brace should begin a line and be
 indented to the beginning of the compound statement.

* Braces are used around all statements, even singletons, when they are part of a control structure, such as a if-else or for statement. This makes it easier to add statements without accidentally introducing bugs due to forgetting to add braces.

return Statements
-----------------

A return statement with a value should not use parentheses unless they
make the return value more obvious in some way. Example:

```
 return;
 return myDisk.size();
 return (size ? size : defaultSize);
```

if, if-else, if-else-if-else Statements
---------------------------------------

The if-else class of statements should have the following form:

```
 if (condition) { 
     statements;
 }

 if (condition) {
     statements; 
 } else { 
     statements; 
 }

 if (condition) { 
     statements; 
 } else if (condition) {
     statements; 
 } else if (condition) { 
     statements;
 } else { 
     statements; 
 }
```

Note: if statements always use braces {}. Avoid the following error-prone form:

```
if (condition) //AVOID! THIS OMITS THE BRACES {}!
    statement;
```

for Statements
--------------

A for statement should have the following form:

```
 for (initialization; condition; update) { 
    statements;
 }
```

An empty for statement (one in which all the work is done in the
initialization, condition, and update clauses) should have the following
form:

```
 for (initialization; condition; update);
```

while Statements
----------------

A while statement should have the following form:

```
 while (condition) { statements;

 }
```

An empty while statement should have the following form:

```
 while (condition);
```

do-while Statements
-------------------

 A do-while statement should have the following form: 
 
```
 do { 
    statements;
 } while (condition);
```

try-catch Statements
--------------------

A try-catch statement should have the following format:

```
 try {

 statements;

 } catch (ExceptionClass e) { statements;

 }
```

White Space
===========

Blank Lines
-----------

Blank lines improve readability by setting off sections of code that are
logically related.

Two blank lines should always be used in the following circumstances:

* Between sections of a source le
* Between class and interface denitions

One blank line should always be used in the following circumstances:

* Between methods
* Between the local variables in a method and its first statement
* Before a block or single-line comment
* Between logical sections inside a method to improve readability

Blank Spaces
------------

Blank spaces should be used in the following circumstances:

* A keyword followed by a parenthesis should be separated by a space.
 Example:

```java
while (true) {
    \\...
}
```

Note: A blank space should not be used between a method name and its opening parenthesis. This helps to distinguish keywords from method
calls.

* A blank space should appear after commas in argument lists.

* All binary operators except `.` should be separated from their operands by spaces. Blank spaces should never separate unary operators such as unary minus, increment (`++`), and decrement (`--`) from their operands. Example:

```java
 a += c + d;
 a = (a + b) / (c \* d);
 while (d++ = s++) {
     n++;
 }
 system.debug(‘size is ' + foo + '\\n');
```

* The expressions in a for statement should be separated by blank spaces. Example:
```java
 for (Integer i = 0; i < 100; i++) {
 }
```

* Casts should be followed by a blank. Examples: 
```java
myMethod((Integer)aNum, (Account)x);
myFunc((Integer)(cp + 5), ((Integer)(i + 3)));

```

Test Code
---------

* Aim to name Test classes with a `_Test` suffix. This will make finding the corresponding test classes easier.
* Have meaningful assertions in every test case. Code coverage isn't the primary goal of the test cases, it's a by product of good testing.

Batch Jobs
----------

Before calling `Database.executeBatch()`, add a guard condition to check if either of ``System.isFuture() || System.isBatch()`` are true. If they are the batch job can't be started.
