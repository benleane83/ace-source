---
description: ESQL best practices and guidelines for Copilot
applyTo: "**/*.esql"
---

# ESQL Overview

Extended Structured Query Language (ESQL) is a programming language defined by WebSphere® Message Broker to define and manipulate data within a message flow.

This section contains introductory information about ESQL:
- For descriptions of ESQL user tasks, see "Writing ESQL" on page 25
- For reference information about ESQL, see "ESQL reference" on page 159

Read the following information before you proceed:
- An overview of message flows in Message flows overview
- An overview of message trees in The message tree, and the topics within this container, paying special attention to Logical tree structure
ESQL is based on Structured Query Language (SQL) which is in common usage with relational databases such as DB2®. ESQL extends the constructs of the SQL language to provide support for you to work with message and database content to define the behavior of nodes in a message flow.

The ESQL code that you create to customize nodes within a message flow is defined in an ESQL file, typically named `<message_flow_name>.esql`, which is associated with the message flow project. 

## ESQL Usage in Built-in Nodes

You can use ESQL in the following built-in nodes:
- Compute node
- Database node
- Filter node

You can also use ESQL to create functions and procedures that you can use in the following built-in nodes:
- DataDelete node
- DataInsert node
- DataUpdate node
- Extract node
- Mapping node
- Warehouse node

## Core Concepts

To use ESQL correctly and efficiently in your message flows, you must also understand the following concepts:
- Data types
- Variables
- Field references
- Operators
- Statements
- Functions
- Procedures
- Modules

## Debugging

Use the ESQL debugger, which is part of the flow debugger, to debug the code that you write. The debugger steps through ESQL code statement by statement, so that you can view and check the results of every line of code that is executed.
## ESQL Data Types

A data type defines the characteristics of an item of data, and determines how that data is processed. ESQL supports six data types, listed later in this section. Data that is retrieved from databases, received in a self-defining message, or defined in a message model (using MRM data types), is mapped to one of these basic ESQL types when it is processed in ESQL expressions.

Within a broker, the fields of a message contain data that has a definite data type. It is also possible to use intermediate variables to help process a message. You must `DECLARE` all such variables with a data type before use. A variable's data type is fixed; If you try to assign values of a different type you get either an implicit cast or an exception. Message fields do not have a fixed data type, and you can assign values of a different type. The field adopts the new value and type.

It is not always possible to predict the data type that results from evaluating an expression. This is because expressions are compiled without reference to any kind of message schema, and so some type errors are not caught until run time.

### Data Type Categories

ESQL defines the following categories of data. Each category contains one or more data types:
- Boolean
- Datetime
- Null
- Numeric
- Reference
- String
## ESQL Variables

An ESQL variable is a data field that is used to help process a message.

You must `DECLARE` a variable and state its type before you can use it. A variable's data type is fixed; if you code ESQL that assigns a value of a different type, either an implicit cast to the data type of the target is implemented or an exception is raised (if the implicit cast is not supported).

To define a variable and give it a name, use the `DECLARE` statement.

**Important:** The names of ESQL variables are case sensitive; therefore, make sure that you use the correct case in all places. The simplest way to guarantee that you are using the correct case is always to define variables using uppercase names.

The workbench marks variables that have not been defined. Remove all these warnings before deploying a message flow.

You can assign an initial value to the variable on the `DECLARE` statement. If an initial value is not specified, scalar variables are initialized with the special value NULL, and `ROW` variables are initialized to an empty state. Subsequently, you can change the variable's value using the `SET` statement.

Three types of built-in node can contain ESQL code and therefore support the use of ESQL variables:
- Compute node
- Database node
- Filter node
### Variable Scope, Lifetime, and Sharing
How widespread and for how long a particular ESQL variable is available, is
described by its scope, lifetime, and sharing:
**A variable's scope**
is a measure of the range over which it is visible. In the broker
environment, the scope of variables is typically limited to the individual
node.
**A variable's lifetime**
is a measure of the time for which it retains its value. In the broker
environment, the lifetime of a variable varies but is typically restricted to
the life of a thread within a node.
**A variable's sharing characteristics**
indicate whether each thread has its own copy of the variable or one
variable is `SHARED` between many threads. In the broker environment,
variables are typically not shared.
### Types of Variables
External
External variables (defined with the `EXTERNAL` keyword) are also known
as user-defined properties, see “### User-Defined Properties in ESQL” on page 6.
They exist for the entire lifetime of a message flow and are visible to all
messages passing through the flow. You can define `EXTERNAL` variables only at
the module and schema level. You can modify their initial values
(optionally `SET` by the `DECLARE` statement) at design time, using the
Message Flow editor, or at deployment time, using the Broker Archive
editor. You can query and `SET` the values of user-defined properties at run
time by using the Configuration Manager Proxy (CMP). For more
information, see Setting user-defined properties at run time in a CMP
application.
Normal
Normal variables variables have a lifetime of just one message passing
through a node. They are visible to that message only. To define a normal
variables, omit both the `EXTERNAL` and `SHARED` keywords.
Shared
Shared variables can be used to implement an in-memory cache in the
message flow, see Optimizing message flow response times. `SHARED` variables
have a long lifetime and are visible to multiple messages passing through a
flow, see “### Long-Lived Variables” on page 7. They exist for the lifetime of
the execution group process, the lifetime of the flow or node, or the
lifetime of the node's SQL that declares the variable (whichever is the
shortest). They are initialized when the first message passes through the
flow or node after each broker startup.
See also the ATOMIC option of the “BEGIN ... END statement” on page
191. The `BEGIN ATOMIC` construct is useful when a number of changes
need to be made to a `SHARED` variable and it is important to prevent other
instances seeing the intermediate states of the data.
For information about specific variable types, see:
- “### User-Defined Properties in ESQL” (external variables)
- “### Long-Lived Variables” on page 7 (shared variables)
### User-Defined Properties in ESQL
You can access user-defined properties (UDPs) as variables in your ESQL program
by specifying the `EXTERNAL` keyword on a `DECLARE` statement. For example,
the ESQL statement `DECLARE` today `EXTERNAL` CHARACTER 'monday' defines a
user-defined property called today with an initial value monday.
Before you can use a user-defined property, you must define the property in the
Message Flow editor when you construct a message flow that uses it. When you
define a UDP in the Message Flow editor, you must define a value and the
property type. The value can be a default value, which varies according to the type
of the UDP. The value that is assigned to the UDP in the Message Flow editor
takes precedence over a value that you have assigned to the UDP in your ESQL
program.
|
|
|
you can also define a UDP for a subflow. A UDP has global scope and is not
specific to a particular subflow. If you reuse a subflow in a message flow, and
those subflows have identical UDPs, you cannot `SET` the UDPs to different values.
Before you deploy the message flow that uses the UDP, you can change the value
of the UDP in the Broker Archive editor. If you try to deploy a message flow that
contains a UDP that has had no value assigned to it, a deployment failure occurs.
For more information, see “Configuring a message flow at deployment time with
user-defined properties” on page 154.
You can use UDPs to `SET` configuration data, and use them like typical properties.
No `EXTERNAL` calls to user-written plug-ins or parsing of environment trees are
involved, and parsing costs of reading data out of trees are removed. The value of
the UDP is finalized in the variable at deployment time.
You can `DECLARE` UDPs only in modules or schemas. You can query, discover, and
set UDPs at run time, to dynamically change the behavior of a message flow. For
more information, see User-defined properties.
You can access UDPs from the following built-in nodes that use ESQL:
- Compute
6 ESQL
- Database
- Filter
For a description of how to access a UDP from a JavaCompute node, see Accessing
message flow user-defined properties from a JavaCompute node.
### Long-Lived Variables
You can use appropriate long-lived ESQL data types to cache data in memory.
Sometimes data has to be stored beyond the lifetime of a single message passing
through a flow. One way to store this data is to store the data in a database. Using
a database is good for long-term persistence and transactionality, but access
(particularly write access) is slow.
Alternatively, you can use appropriate long-lived ESQL data types to provide an
in-memory cache of the data for a certain period of time. Using long-lived ESQL
data types makes access faster than from a database, although this speed is at the
expense of shorter persistence and no transactionality.
You create long-lifetime variables by using the `SHARED` keyword on the
DECLARE statement. For further information, see “DECLARE statement” on page
235.
The following sample demonstrates how to define `SHARED` variables using the
DECLARE statement. The sample demonstrates how to store routing information
in a database table and use `SHARED` variables to store the database table in memory
in the message flow to improve performance.
- Message Routing
You can view samples information only when you use the information center that
is integrated with the Message Broker Toolkit or the online information center.
Long-lived data types have an extended lifetime beyond that of a single message
passing through a node. Long-lived data types are `SHARED` between threads and
exist for the life of a message flow (strictly speaking the time between
configuration changes to a message flow), as described in the following table.
Scope
Short lifetime variables
Schema & Module
Routine Local
Node
Life
Shared
Thread within
node
Node
Block Local
Long lifetime variables
Node Shared
Node
Node
Thread within
routine
Thread within
block
Not at all
Not at all
Not at all
Life of node
Flow Shared
Flow
Features of long-lived ESQL data types include:
Life of flow
- The ability to handle large amounts of long-lifetime data.
- The joining of data to messages fast.
All threads of flow
All threads of flow
- On multiple processor machines, multiple threads can access the same data
simultaneously.
- Subsequent messages can access the data left by a previous message.
- Long lifetime read-write data can be `SHARED` between threads, because there is no
long-term association between threads and messages.
- In contrast to data stored in database tables in the environment, this type of data
is stored privately; that is, within the broker.
- ROWvariables can be used to create a modifiable copy of the input message;
see “ESQL ROW data type” on page 169.
- It is possible to create `SHARED` constants.
Atypical use of these data types might be in a flow in which data tables are
'read-only' as far as the flow is concerned. Although the table data is not actually
static, the flow does not change it, and thousands of messages pass through the
flow before there is any change to the table data.
Examples include:
- Atable which contains a day's credit card transactions. The table is created each
day and that day's messages are run against it. Then the flow is stopped, the
table updated and the next day's messages run. These flows might perform
better if they cache the table data rather than read it from a database for each
message.
- The accumulation and integration of data from multiple messages.
### Broker Properties
For each broker, WebSphere Message Broker maintains a `SET` of properties. You can
access some of these properties from your ESQL programs. A subset of the
properties is also accessible from Java™ programs. It can be useful, at run time, to
have real-time access to details of a specific node, flow, or broker.
### Broker Properties are divided into four categories:
- Properties that relate to a specific node
- Properties that relate to nodes in general
- Properties that relate to a message flow
- Properties that relate to the execution group
For a description of the broker, flow, and node properties that are accessible from
ESQL and Java, see “### Broker Properties that are accessible from ESQL and Java” on
page 377.
### Broker Properties have the following characteristics.
- They are grouped by broker, execution group, flow, and node.
- They are case sensitive. Their names always start with an uppercase letter.
- They return NULL if they do not contain a value.
All nodes that allow user programs to edit ESQL support access to broker
properties. These nodes are:
- Compute nodes
- Database nodes
- Filter nodes
8 ESQL
- All derivatives of these nodes
User-defined properties can be queried, discovered, and `SET` at run time to
dynamically change the behavior of a message flow. You can use the Configuration
Manager Proxy (CMP) API to manipulate these properties, which can be used by a
systems monitoring tool to perform automated actions in response to situations
that it detects in the monitored systems. For more information, see User-defined
properties.
Acomplex property is a property to which you can assign multiple values. Complex
properties are displayed in a table in the Properties view, where you can add, edit,
and delete values, and change the order of the values in the table. You cannot
promote complex properties; therefore, they do not appear in the Promote
properties dialog box. Nor can you configure complex properties; therefore, they
are not supported in the Broker Archive editor. For an example of a complex
property, see the Query elements property of the DatabaseRoute node.
For more information about editing a node's properties, see Configuring a message
flow node.
## ESQL Field References
An ESQL field reference is a sequence of period-separated values that identify a
specific field (which might be a structure) within a message tree or a database
table.
The path from the root of the information to the specific field is traced using the
parent-child relationships.
Afield reference is used in an ESQL statement to identify the field that is to be
referenced, updated, or created within the message or database table. For example,
you might use the following identifier as a message field reference:
Body.Invoice.Payment
You can use an ESQL variable of type REFERENCE to `SET` up a dynamic pointer to
contain a field reference. This might be useful in creating a fixed reference to a
commonly-referenced point within a message; for example the start of a particular
structure that contains repeating fields.
Afield reference can also specify element types, XML namespace identifications,
indexes and a type constraint; see “ESQL field reference overview” on page 174 for
further details.
The first name in a field reference is sometimes known as a Correlation name.
## ESQL Operators
An ESQL operator is a character or symbol that you can use in expressions to
specify relationships between fields or values.
ESQL supports the following groups of operators:
|
|
|
|
- Comparison operators, to compare one value to another value (for example, less
than). Refer to “ESQL simple comparison operators” on page 181 and “ESQL
complex comparison operators” on page 182 for details of the supported
operators and their use.
- Logical operators, to perform logical operations on one or two terms (for
example, AND). Refer to “ESQL logical operators” on page 185 for details of the
supported operators and their use.
- Numeric operators, to indicate operations on numeric data (for example, +).
Refer to “ESQL numeric operators” on page 186 for details of the supported
operators and their use.
There are some restrictions on the application of some operators to data types; not
all lead to a meaningful operation. These are documented where they apply to
each operator.
Operators that return a Boolean value (TRUE or FALSE), for example the greater
than operator, are also known as predicates.
## ESQL Statements
An ESQL statement is an instruction that represents a step in a sequence of actions
or a `SET` of declarations.
ESQL provides a large number of different statements that perform different types
of operation. All ## ESQL Statements start with a keyword that identifies the type of
statement and end with a semicolon. An ESQL program consists of a number of
statements that are processed in the order they are written.
As an example, consider the following ESQL program:
DECLARE x INTEGER;
SET x = 42;
This program consists of two statements. The first starts with the keyword
DECLARE and ends at the first semicolon. The second statement starts with the
keyword `SET` and ends at the second semicolon. These two statements are written
on separate lines and it is conventional (but not required) that they be so. You will
notice that the language keywords are written in capital letters. This is also the
convention but is not required; mixed case and lowercase are acceptable.
The first statement declares a variable called x of type INTEGER, that is, it reserves
a space in the computer's memory large enough to hold an integer value and
allows this space to be subsequently referred to in the program by the name x. The
second statement sets the value of the variable x to 42. A number appearing in an
ESQL program without decimal point and not within quotation marks is known as
an integer literal.
ESQL has a number of data types and each has its own way of writing literal
values. These are described in “ESQL data types” on page 4.
For a full description of all the ## ESQL Statements, see “## ESQL Statements” on page
188.
### ESQL Nested Statements
An ESQL nested statement is a statement that is contained within another
statement.
Consider the following ESQL program fragment:
10 ESQL
IF Size> 100.00 THEN
SET X = 0;
SET Y = 0;
SET REVERSE = FALSE;
ELSE
SET X = 639;
SET Y = 479;
SET REVERSE = TRUE;
END IF;
In this example, you can see a single IF statement containing the optional ELSE
clause. Both the IF and ELSE portions contain three nested statements. Those
within the IF clause are processed if the operator > (greater than) returns the value
TRUE (that is, if Size has a value greater than 100.00); otherwise, those within the
ELSE clause are processed.
Many statements can have expressions nested within them, but only a few can
have statements nested within them. The key difference between an expression and
a statement is that an expression calculates a value to be used, whereas a statement
performs an action (usually changing the state of the program) but does not
produce a value.
## ESQL Functions
Afunction is an ESQL construct that calculates a value from a number of given
input values.
Afunction usually has input parameters and can, but does not usually have,
output parameters. It returns a value calculated by the algorithm described by its
statement. This statement is usually a compound statement, such as BEGIN... END,
because this allows an unlimited number of nested statements to be used to
implement the algorithm.
ESQL provides a number of predefined, or “built-in”, functions which you can use
freely within expressions. You can also use the CREATE FUNCTION statement to
define your own functions.
When you define a function, you must give it a unique name. The name is
handled in a case insensitive way (that is, use of the name with any combination
of uppercase and lowercase letters matches the declaration). This is in contrast to
the names that you `DECLARE` for schemas, constants, variables, and labels, which are
handled in a case sensitive way, and which you must specify exactly as you
declared them.
Consider the following ESQL program fragment:
SET Diameter = SQRT(Area / 3.142) * 2;
In this example, the function SQRT (square root) is given the value inside the
brackets (itself the result of an expression, a divide operation) and its result is used
in a further expression, a multiply operation. Its return value is assigned to the
variable Diameter. See “Calling ## ESQL Functions” on page 278 for information about
all the built-in ## ESQL Functions.
In addition, an ESQL expression can refer to a function in another broker schema
(that is, a function defined by a CREATE FUNCTION statement in an ESQL file in
the same or in a different dependent project). To resolve the name of the called
function, you must do one of the following:
- Specify the fully-qualified name (<SchemaName>.<FunctionName>) of the called
function.
- Include a PATH statement to make all functions from the named schema visible.
Note that this technique only works if the schemas do not contain
identically-named functions. The PATH statement must be coded in the same
ESQL file, but not within any MODULE.
Note that you cannot define a function within an EVAL statement or an EVAL
function.
## ESQL Procedures
An ESQL procedure is a subroutine that has no return value. It can accept input
parameters from, and return output parameters to, the caller.
Procedures are very similar to functions. The main difference between them is that,
unlike functions, procedures have no return value. Thus they cannot form part of
an expression and are invoked by using the CALL statement. Procedures
commonly have output parameters
You can implement a procedure in ESQL (an internal procedure) or as a database
stored procedure (an `EXTERNAL` procedure). The ESQL procedure must be a single
ESQL statement, although that statement can be a compound statement such as
BEGIN END. You cannot define a procedure within an EVAL statement or an
EVAL function.
When you define a procedure, give it a name. The name is handled in a case
insensitive way (that is, use of the name with any combination of uppercase and
lowercase letters matches the declaration). That is in contrast to the names that you
declare for schemas, constants, variables, and labels, which are handled in a case
sensitive way, and which you must specify exactly as you declared them.
An ESQL expression can include a reference to a procedure in another broker
schema (defined in an ESQL file in the same or a different dependent project). If
you want to use this technique, either fully qualify the procedure, or include a
PATH statement that sets the qualifier. The PATH statement must be coded in the
same ESQL file, but not within a MODULE.
An `EXTERNAL` database procedure is indicated by the keyword `EXTERNAL` and the
external procedure name. This procedure must be defined in the database and in
the broker, and the name specified with the `EXTERNAL` keyword and the name of
the stored database procedure must be the same, although parameter names do not
have to match. The ESQL procedure name can be different from the `EXTERNAL` name
it defines.
Overloaded procedures are not supported to any database. (An overloaded
procedure is one that has the same name as another procedure in the same
database schema which has a different number of parameters, or parameters with
different types.) If the broker detects that a procedure has been overloaded, it
raises an exception.
Dynamic schema name resolution for stored procedures is supported; when you
define the procedure you must specify a wildcard for the schema that is resolved
before invocation of the procedure by ESQL. This is explained further in “Invoking
stored procedures” on page 77.
12 ESQL
## ESQL Modules
Amodule is a sequence of declarations that define variables and their initialization,
and a sequence of subroutine (function and procedure) declarations that define a
specific behavior for a message flow node.
Amodule must begin with the CREATE node_type MODULE statement and end with
an END MODULE statement. The node_type must be one of COMPUTE, DATABASE,
or FILTER. The entry point of the ESQL code is the function named MAIN, which
has MODULE scope.
Each module is identified by a name which follows CREATE node_type MODULE. The
name might be created for you with a default value, which you can modify, or you
can create it yourself. The name is handled in a case insensitive way (that is, use of
the name with any combination of uppercase and lowercase letters matches the
declaration). That is in contrast to the names that you `DECLARE` for schemas,
constants, variables, and labels, which are handled in a case sensitive way, and
which you must specify exactly as you declared them.
You must create the code for a module in an ESQL file which has a suffix of .esql.
You must create this file in the same broker schema as the node that references it.
There must be one module of the correct type for each corresponding node, and it
is specific to that node and cannot be used by any other node.
When you create an ESQL file (or complete a task that creates one), you indicate
the message flow project and broker schema with which the file is associated as
well as specifying the name for the file.
Within the ESQL file, the name of each module is determined by the value of the
corresponding property of the message flow node. For example, the property ESQL
Module for the Compute node specifies the name of the node's module in the ESQL
file. The default value for this property is the name of the node. You can specify a
different name, but you must ensure that the value of the property and the name
of the module that provides the required function are the same.
The module must contain the function MAIN, which is the entry point for the
module. This is included automatically if the module is created for you. Within
MAIN, you can code ESQL to configure the behavior of the node. If you include
ESQL within the module that declares variables, constants, functions, and
procedures, these are of local scope only and can be used within this single
module.
If you want to reuse ESQL constants, functions, or procedures, you must declare
them at broker schema level. You can then refer to these from any resource within
that broker schema, in the same or another project. If you want to use this
technique, either fully qualify the procedure, or include a PATH statement that sets
the qualifier. The PATH statement must be coded in the same ESQL file, but not
within any MODULE.