

<!-- ============================================================ -->
<!-- 01.Introduction.md -->
<!-- ============================================================ -->

# 1. Introduction

## 1.1 Overview

LLMK (LLM Kaizen Language) is a programming language designed specifically for AI-assisted software development.

The primary goal of LLMK is to maximize readability and predictability for both Large Language Models (LLMs) and human developers.

Unlike traditional programming languages, LLMK prioritizes explicit structure, deterministic behavior, and minimal syntax over expressive complexity.

---

## 1.2 Design Goals

LLMK is designed with the following goals.

- Maximize readability for LLMs.
- Minimize syntax ambiguity.
- Reduce unnecessary language features.
- Encourage explicit program structure.
- Make data flow easy to understand.
- Make side effects explicit.
- Improve maintainability of large codebases.
- Keep the language simple and consistent.

---

## 1.3 Language Philosophy

LLMK follows several core design principles.

### Simplicity

The language should contain only features that provide clear value.

Unnecessary syntax and multiple ways to express the same concept should be avoided.

### Explicitness

Program behavior should be explicit.

Hidden behavior and implicit rules should be minimized.

### Predictability

The same source code should always be interpreted in the same way.

Language rules should be simple enough that both humans and LLMs can understand them consistently.

### AI-First Design

Language features are designed to improve AI understanding rather than maximize programmer convenience.

Readability and deterministic structure are prioritized over compact syntax.

---

## 1.4 Scope

This specification defines:

- Lexical structure
- Program structure
- Type system
- Variables
- Functions
- Control flow
- Error handling
- Module system
- Memory model
- Concurrency model

This specification does not define:

- Compiler implementation
- Runtime implementation
- Virtual machine
- Package manager
- Standard library implementation

---

## 1.5 Conformance

A compiler or interpreter conforms to this specification if it implements all required language rules described in this document.

Requirements use the following terminology.

- **shall** indicates a mandatory requirement.
- **should** indicates a recommendation.
- **may** indicates an optional feature or implementation choice.

---

## 1.6 Version

This document defines the LLMK Language Specification Version 1.1.



<!-- ============================================================ -->
<!-- 02.Lexical_Structure.md -->
<!-- ============================================================ -->

# 2. Lexical Structure

## 2.1 Source Files

LLMK source files shall use the `.llmk` file extension.

Each source file defines exactly one module.

---

## 2.2 Character Encoding

Source files shall be encoded using UTF-8.

Line endings may use either LF or CRLF.

Implementations shall treat both formats equivalently.

---

## 2.3 Whitespace

Whitespace separates language tokens.

The following characters are considered whitespace.

- Space
- Horizontal tab
- Line break

Multiple consecutive whitespace characters are treated as a single separator, except inside string literals.

---

## 2.4 Statements

Every executable statement shall occupy exactly one line.

The following are executable statements.

- Variable declaration
- Assignment
- Function call
- IF
- MATCH
- FOR
- PARFOR

This rule minimizes structural ambiguity and improves readability for both humans and LLMs.

---

## 2.5 Multi-line Constructs

Executable statements shall not span multiple lines.

The only exceptions are:

- Function parameter lists
- TYPE instance creation
- MATCH case blocks

Example:

```
FUNCTION Create(

    STRING name

    STRING email

)
```

Example:

```
User(

    name,

    email
)
```

Example:

```
MATCH result

SUCCESS HandleSuccess()

ERROR HandleError()
```

---

## 2.6 Parentheses

Parentheses shall only be used for:

- Function parameter lists
- Function arguments
- TYPE instance creation

Parentheses shall not be used to control expression evaluation order.

Valid:

```
CreateUser(name)
```

```
User(name, email)
```

Invalid:

```
(a + b)

(A == B)
```

---

## 2.7 Comments

Comments begin with `#`.

A comment occupies the entire line.

Inline comments are not supported.

Example:

```
# Validate user input

LET BOOL is_valid = Validate(user)
```

Invalid:

```
LET BOOL is_valid = Validate(user) # Validate user
```

---

## 2.8 Semicolons

Semicolons are not supported.

Statements are terminated by line breaks.

---

## 2.9 Identifiers

Identifiers shall begin with a letter or underscore.

Subsequent characters may contain:

- Letters
- Digits
- Underscores

Identifiers are case-sensitive.

---

## 2.10 Reserved Keywords

The following keywords are reserved.

```
TYPE
FUNCTION

LET
VAR
REF

IF
MATCH
FOR
PARFOR

TRUE
FALSE
NONE

OPTIONAL
RESULT
ARRAY

ERROR
```

Reserved keywords shall not be used as identifiers.

---

## 2.11 Language Philosophy

The lexical structure follows these principles.

- One executable statement per line.
- Explicit structure over compact syntax.
- Minimal punctuation.
- Deterministic parsing.
- Readability for both humans and LLMs.


<!-- ============================================================ -->
<!-- 03.Program_Structure.md -->
<!-- ============================================================ -->

# 3. Program Structure

## 3.1 Overview

An LLMK program consists of one or more modules.

Each module is defined by a single source file.

Modules organize both data and behavior.

---

## 3.2 One Module per File

Each source file shall define exactly one module.

The filename, module name, and TYPE name shall be identical.

Example:

```
User.llmk
```

```
TYPE User
```

This rule establishes a one-to-one relationship between files, modules, and TYPEs.

---

## 3.3 One TYPE per Module

Each module shall define exactly one TYPE.

Multiple TYPE definitions in a single module are not supported.

Example:

```
TYPE User

    STRING name

    STRING email
```

Invalid:

```
TYPE User

...

TYPE Address

...
```

---

## 3.4 Module Members

A module consists of:

- One TYPE definition.
- Zero or more FUNCTION definitions.

Example:

```
TYPE User

    STRING name

    STRING email


FUNCTION Create(

    STRING name

)

FUNCTION Validate(

    User user

)

FUNCTION Save(

    User user

)
```

Functions belong to the module in which they are defined.

---

## 3.5 Program Entry Point

LLMK does not define a reserved entry function.

Names such as `Main` or `Start` have no special meaning.

The execution environment specifies the module and function to execute.

Example:

```
llmk run Application Start
```

This executes the `Start` function defined in `Application.llmk`.

---

## 3.6 Module Independence

Modules should represent a single concept or responsibility.

Related functionality should be grouped into the same module.

Unrelated functionality should be separated into different modules.

---

## 3.7 Module Access

Modules interact through public TYPEs and FUNCTIONs.

All TYPE fields are public.

All FUNCTIONs are public.

LLMK does not support visibility modifiers such as:

- public
- private
- protected

---

## 3.8 Circular Dependencies

Modules should avoid circular dependencies.

Implementations may reject circular module references.

---

## 3.9 Design Principles

The module system follows these principles.

- One file represents one concept.
- One module defines one TYPE.
- One TYPE corresponds to one source file.
- Behavior belongs to the module.
- No hidden entry point exists.
- Simple program organization improves maintainability and LLM understanding.


<!-- ============================================================ -->
<!-- 04.Built-in_Type_System.md -->
<!-- ============================================================ -->

# 4. Built-in Type System

## 4.1 Overview

LLMK provides a small set of built-in types.

These types are sufficient to construct all user-defined TYPEs and programs.

The language intentionally minimizes the number of primitive types to improve simplicity and readability.

---

## 4.2 Primitive Types

LLMK defines the following primitive types.

| Type | Description |
|------|-------------|
| BOOL | Boolean value |
| INT | Signed integer |
| DOUBLE | Double-precision floating-point number |
| STRING | UTF-8 character sequence |

---

## 4.3 Type Modifiers

LLMK applies type modifiers as prefixes.

A type modifier shall precede the underlying type.

The built-in type modifiers are:

- ARRAY
- OPTIONAL
- RESULT

Examples:

```
ARRAY STRING

OPTIONAL User

RESULT User

ARRAY RESULT User
```

Type modifiers may be combined.

When multiple type modifiers are present, the rightmost type is the underlying type.

Each modifier successively wraps the type to its right.

For example:

```
ARRAY RESULT User
```

is interpreted as:

```
User
→ RESULT User
→ ARRAY RESULT User
```

This rule provides a simple and deterministic type composition model for both humans and LLMs.

---

## 4.4 Boolean

BOOL represents a logical value.

Possible values are:

```
TRUE
FALSE
```

Boolean values are commonly used by IF statements and logical expressions.

Type modifiers shall always be written from the outermost type to the innermost type.

---

## 4.5 Integer

INT represents a signed integer.

The implementation defines the integer size.

Programs should not assume a specific bit width.

---

## 4.6 Double

DOUBLE represents a double-precision floating-point number.

Implementations shall follow IEEE 754 where practical.

---

## 4.7 String

STRING represents immutable UTF-8 text.

String literals are enclosed by double quotation marks.

Example:

```
LET STRING name = "Alice"
```

---

## 4.8 ARRAY

ARRAY represents an ordered collection of values.

All elements shall have the same type.

Example:

```
ARRAY INT

ARRAY STRING

ARRAY User
```

The order of elements shall be preserved.

---

## 4.9 OPTIONAL

OPTIONAL represents either:

- A value
- NONE

Example:

```
OPTIONAL STRING

OPTIONAL User
```

OPTIONAL explicitly represents the absence of a value.

---

## 4.10 RESULT

RESULT represents either:

- SUCCESS with a value
- ERROR with an error value

Example:

```
RESULT User

RESULT STRING

RESULT NONE
```

RESULT explicitly represents operation success or failure.

---

## 4.11 NONE

NONE represents the absence of a meaningful value.

NONE is used when:

- No value exists.
- A function has no meaningful return value.
- A RESULT succeeds without returning data.

Examples:

```
OPTIONAL NONE

RESULT NONE
```

LLMK represents "no meaningful value" using the single concept of NONE.

---

## 4.12 Type Composition

Built-in types may be combined.

Examples:

```
ARRAY STRING

OPTIONAL User

RESULT User

ARRAY RESULT User
```

Nested combinations are permitted.

---

## 4.13 Design Principles

The built-in type system follows these principles.

- Small and consistent.
- Explicit representation of absence.
- Explicit representation of errors.
- Deterministic behavior.
- Suitable for both humans and LLMs.


<!-- ============================================================ -->
<!-- 05.Variables.md -->
<!-- ============================================================ -->

# 5. Variables

## 5.1 Overview

LLMK provides three variable declarations.

- LET
- VAR
- REF

Each declaration has a distinct purpose.

---

## 5.2 LET

LET declares an immutable variable.

A LET variable shall be initialized when declared.

A LET variable shall not be reassigned.

Example:

```
LET STRING name = "Alice"

LET INT count = 10
```

---

## 5.3 VAR

VAR declares a mutable variable.

A VAR variable shall be initialized when declared.

A VAR variable may be reassigned.

Example:

```
VAR INT count = 0

count = count + 1
```

---

## 5.4 REF

REF declares a reference parameter.

REF shall only be used in function parameter lists.

REF shall not be used for local variable declarations.

A REF parameter refers to the caller's variable.

Example:

```
FUNCTION Increment(

    REF INT value

)

value = value + 1

NONE
```

---

## 5.5 Initialization

Every variable shall be initialized before use.

Uninitialized variables are not supported.

---

## 5.6 Assignment

Assignment replaces the current value of a mutable variable.

Only VAR variables and REF parameters may be assigned.

LET variables shall not be assigned after initialization.

Example:

```
VAR INT count = 0

count = count + 1
```

Invalid:

```
LET INT count = 0

count = 1
```

---

## 5.7 TYPE Variables

TYPE instances are always mutable.

LET and VAR apply only to the variable itself.

They do not change the mutability of the TYPE instance.

A LET variable shall not be reassigned.

However, the fields of the referenced TYPE instance may be modified.

Example:

```
LET User user = User.Create(
    "Alice",
    20
)

user.name = "Bob"      # Valid
user.age = 21          # Valid
```

The following is invalid because the variable itself is reassigned.

```
LET User user = User.Create(
    "Alice",
    20
)

user = User.Create(
    "Bob",
    21
)
```

A VAR variable permits both field modification and reassignment.

Example:

```
VAR User user = User.Create(
    "Alice",
    20
)

user.name = "Bob"      # Valid

user = User.Create(
    "Charlie",
    30
)                       # Valid
```

This design treats TYPE as a mutable data container.

Variable mutability and TYPE mutability are independent concepts.

---

## 5.8 Default Values

Variables do not have implicit default values.

Every variable shall be explicitly initialized.

TYPE fields are automatically initialized according to their declared types.

The default values are:

| Type | Default Value |
|------|---------------|
| BOOL | FALSE |
| INT | 0 |
| DOUBLE | 0.0 |
| STRING | "" |
| ARRAY | Empty array |
| OPTIONAL | NONE |
| RESULT | ERROR |
| TYPE | Field defaults |

---

## 5.9 Design Principles

The variable system follows these principles.

- Explicit initialization.
- Immutable variables by default.
- Mutable state is explicit.
- References exist only as function parameters.
- TYPE represents mutable data.
- Variable behavior is deterministic and easy to understand.




<!-- ============================================================ -->
<!-- 06.Functions.md -->
<!-- ============================================================ -->

# 6. Functions

## 6.1 Overview

Functions define executable behavior.

All behavior in LLMK is implemented as functions.

Functions belong to modules and shall always be invoked using a qualified module name.

Functions do not belong to TYPEs.

---

## 6.2 Function Definition

Functions are declared using the `FUNCTION` keyword.

Example:

```
FUNCTION User.Create(

    STRING name

    INT age

)

LET User user = User(
    name,
    age
)

user
```

---

## 6.3 Parameters

Function parameters are declared in order.

Each parameter consists of:

- Type
- Parameter name

Example:

```
FUNCTION User.Update(

    REF User user

    STRING name

)
```

Parameter names shall be unique within a function.

---

## 6.4 REF Parameters

REF indicates pass-by-reference.

REF may only be applied to function parameters.

REF shall precede the parameter type.

Example:

```
FUNCTION User.Update(

    REF User user

)
```

Local REF variables are not supported.

---

## 6.5 Function Invocation

Functions shall always be invoked using the module name.

Example:

```
User.Create()

User.Validate()

File.Read()

Json.Serialize()
```

Unqualified function calls are not permitted.

---

## 6.6 Return Values

Functions do not declare a return type.

The return type is determined by the type of the final returned value.

Every function shall terminate with exactly one value.

The final statement determines the returned value.

Example:

```
FUNCTION User.Create(

    STRING name

)

User(
    name
)
```

The return type of this function is `User`.

---

## 6.7 NONE

Functions that do not produce meaningful data shall return `NONE`.

Example:

```
FUNCTION Logger.Log(

    STRING message

)

Console.Write(message)

NONE
```

---

## 6.8 RETURN Statement

The `RETURN` statement is not supported.

Functions always execute to the final statement.

Early return is not permitted.

Programs should decompose complex logic into multiple functions instead of relying on early returns.

---

## 6.9 Named Arguments

Named arguments are not supported.

Function arguments are positional.

Example:

```
User.Create(
    "Alice",
    20
)
```

---

## 6.10 Design Principles

The function system follows these principles.

- Functions define behavior.
- TYPEs define data.
- Functions always terminate with a value.
- Return types are inferred from the final value.
- RETURN is not supported.
- Parameter passing is explicit.
- Simplicity is preferred over language complexity.

A function always ends with a value.



<!-- ============================================================ -->
<!-- 07.Control_Flow.md -->
<!-- ============================================================ -->

# 7. Control Flow

## 7.1 Overview

LLMK provides four control flow constructs.

- IF
- MATCH
- FOR
- PARFOR

Control flow is expressed by function invocation rather than executable blocks.

---

## 7.2 IF

IF conditionally invokes one function.

The condition shall evaluate to BOOL.

The invoked statement shall be exactly one function call.

Example:

```
IF is_valid User.Save(user)
```

ELSE is not supported.

Nested executable blocks are not supported.

---

## 7.3 MATCH

MATCH selects one function invocation based on the value of an expression.

MATCH may be used with any comparable value.

When the matched value is of type RESULT, the reserved labels `SUCCESS` and `ERROR` shall be used.

Each branch consists of exactly one function invocation.

```
MATCH status
READY Process.Start()
STOPPED Process.Stop()
```

```
MATCH result
SUCCESS User.Save()
ERROR Logger.LogError()
```

---

## 7.4 FOR

FOR sequentially processes every element of an ARRAY.

Example:

```
FOR users User.Validate()
```

The current element is automatically passed as the first function parameter.

The current element shall not be specified explicitly in the FOR or PARFOR statement.

The invoked function may optionally accept one additional `REF TYPE` parameter.

Example:

```
VAR Statistics statistics = Statistics()

FOR users User.Validate(statistics)
```

Conceptually, each iteration performs:

```
User.Validate(user, statistics)
```

FOR processes elements in input order.

Every iteration shall execute.

---

## 7.5 PARFOR

PARFOR executes every element of an ARRAY independently.

Example:

```
LET ARRAY RESULT User results =
    PARFOR users User.Validate()
```

The current element is automatically passed as the first function parameter.

The current element shall not be specified explicitly in the FOR or PARFOR statement.

The invoked function shall accept exactly one parameter.

REF parameters are not permitted.

The execution order is implementation-defined.

PARFOR waits for all iterations to complete.

Returned values are collected into an ARRAY preserving the input order.

A failure in one iteration shall not prevent the execution of the remaining iterations.

---

## 7.6 Design Principles

The control flow system follows these principles.

- Function invocation is the fundamental execution model.
- Executable blocks are minimized.
- Nested control flow is avoided.
- Sequential and parallel execution are explicit.
- Shared mutable state is prohibited in PARFOR.
- Every control flow construct is deterministic.

Control flow is expressed by function invocation rather than executable blocks.


<!-- ============================================================ -->
<!-- 08.Error_Handling.md -->
<!-- ============================================================ -->

# 8. Error Handling

## 8.1 Overview

LLMK represents recoverable operations explicitly.

The language provides two built-in types for this purpose:

- OPTIONAL
- RESULT

OPTIONAL represents the absence of a value.

RESULT represents the success or failure of an operation.

Error handling is explicit and deterministic.

---

## 8.2 OPTIONAL

OPTIONAL represents either:

- A value
- NONE

Example:

```
OPTIONAL User
```

Functions returning OPTIONAL shall explicitly return either a value or NONE.

---

## 8.3 RESULT

RESULT represents either:

- SUCCESS with a value
- ERROR with an Error

Example:

```
RESULT User
```

Functions returning RESULT shall explicitly return either:

- SUCCESS
- ERROR

The error value shall be of the built-in `Error` TYPE.

---

## 8.4 Error TYPE

LLMK defines a standard Error TYPE.

```
TYPE Error

    STRING code

    STRING message

    OPTIONAL STRING detail
```

Implementations may extend this TYPE with additional fields.

Programs should use `code` for machine-readable error identification and `message` for human-readable descriptions.

---

## 8.5 MATCH

MATCH evaluates a RESULT value.

Each branch consists of exactly one function invocation.

Example:

```
MATCH result

SUCCESS User.Save()

ERROR Logger.LogError()
```

MATCH does not support executable blocks.

MATCH does not support nested statements.

---

## 8.6 Error Propagation

LLMK does not provide automatic error propagation.

Functions shall explicitly process every RESULT value.

Errors shall be handled through MATCH.

Example:

```
LET RESULT User result = User.Load(id)

MATCH result

SUCCESS User.Save()

ERROR Logger.LogError()
```

---

## 8.7 Design Principles

The error handling system follows these principles.

- Success and failure are explicit.
- Error handling is deterministic.
- Errors are represented by a single standard Error TYPE.
- Automatic exception propagation is not supported.
- MATCH provides explicit branching for RESULT values.
- Error handling is easy to understand for both humans and LLMs.

LLMK does not support exceptions. Recoverable failures are represented explicitly by RESULT.



<!-- ============================================================ -->
<!-- 09.Standard_Library.md -->
<!-- ============================================================ -->

# 9. Standard Library

## 9.1 Overview

LLMK provides a standard library.

The standard library defines common TYPEs and FUNCTIONs that are available to all programs.

The standard library is specified separately from this language specification.

---

## 9.2 Scope

The standard library includes functionality such as:

- String processing
- Array operations
- File system access
- JSON processing
- Date and time
- Math
- Networking
- Error utilities

The exact APIs are defined in the Standard Library Specification.

---

## 9.3 Language Independence

The LLMK language specification is independent of the standard library.

A compiler shall implement the language regardless of the availability of standard library modules.

Programs may provide alternative implementations of standard library modules where permitted by the implementation.

---

## 9.4 Design Principles

The standard library follows these principles.

- Small and consistent.
- Explicit behavior.
- Minimal API surface.
- AI-friendly naming.
- Stable compatibility.


<!-- ============================================================ -->
<!-- 10.Formal_Grammar.md -->
<!-- ============================================================ -->

# 10. Formal Grammar

## 10.1 Overview

This chapter defines the formal grammar of LLMK.

The grammar specifies the syntactic structure of valid programs.

---

## 10.2 Program

```ebnf
program =
    { import_statement }
    { type_definition }
    { function_definition } ;
```

---

## 10.3 Import

```ebnf
import_statement =
    "IMPORT"
    identifier ;
```

---

## 10.4 TYPE

```ebnf
type_definition =
    "TYPE"
    identifier
    { field_definition } ;

field_definition =
    type
    identifier ;
```

A source file shall contain at most one TYPE definition.

The TYPE name shall be identical to the source file name.

The module name shall be identical to the TYPE name.

---

## 10.5 Function

```ebnf
function_definition =
    "FUNCTION"
    qualified_identifier
    "("
        { parameter }
    ")"
    { statement }
    return_value ;

parameter =
    [ "REF" ]
    type
    identifier ;
```

Functions do not declare a return type.

The return type is determined by the final returned value.

---

## 10.6 Variable Declaration

```ebnf
variable_declaration =
      let_declaration
    | var_declaration ;

let_declaration =
    "LET"
    type
    identifier
    "="
    expression ;

var_declaration =
    "VAR"
    type
    identifier
    "="
    expression ;
```

---

## 10.7 Statements

```ebnf
statement =
      variable_declaration
    | assignment
    | function_call
    | if_statement
    | match_statement
    | for_statement
    | parfor_statement ;
```

---

## 10.8 IF

```ebnf
if_statement =
    "IF"
    expression
    function_call ;
```

---

## 10.9 MATCH

```ebnf
match_statement =
    "MATCH"
    expression
    { match_case } ;

match_case =
      "SUCCESS" function_call
    | "ERROR" function_call
    | literal function_call ;
```

---

## 10.10 FOR

```ebnf
for_statement =
    "FOR"
    expression
    function_call ;
```

The current element is automatically passed as the first parameter.

The current element shall not be specified explicitly in the FOR statement.

The invoked function shall accept:

- the collection element as its first parameter
- optionally one REF TYPE parameter as its second parameter

---

## 10.11 PARFOR

```ebnf
parfor_statement =
    "PARFOR"
    expression
    function_call ;
```

The current element is automatically passed as the first parameter.

The current element shall not be specified explicitly in the FOR statement.

The invoked function shall accept exactly one parameter.

REF parameters are not permitted.

---

## 10.12 Return Value

```ebnf
return_value =
    expression ;
```

Every function shall terminate with exactly one return value.

The RETURN statement is not supported.

---

## 10.13 Expressions

```ebnf
expression =
      literal
    | identifier
    | member_access
    | function_call
    | binary_expression ;
```

---

## 10.14 Type

```ebnf
type =
      primitive_type
    | type_identifier
    | modified_type ;

modified_type =
      "ARRAY" type
    | "OPTIONAL" type
    | "RESULT" type ;
```

Type modifiers are always written before the underlying type.

---

## 10.15 Design Principles

The formal grammar follows these principles.

- Small and deterministic.
- No nested executable blocks.
- Functions are the primary execution unit.
- Every function ends with a value.
- Type modifiers are written as prefixes.


<!-- ============================================================ -->
<!-- 11.Expressions_and_Operators.md -->
<!-- ============================================================ -->

# 11. Expressions and Operators

## 11.1 Overview

Expressions compute values.

Expressions shall be simple, deterministic, and easy to understand.

Complex expressions should be decomposed into multiple LET variables.

---

## 11.2 Literals

LLMK supports the following literal types.

- BOOL
- INT
- DOUBLE
- STRING
- NONE

Examples:

```
TRUE

FALSE

123

3.14

"Hello"

NONE
```

---

## 11.3 Arithmetic Operators

The following arithmetic operators are supported.

| Operator | Description |
|----------|-------------|
| + | Addition |
| - | Subtraction |
| * | Multiplication |
| / | Division |
| % | Remainder |

Example:

```
LET INT total = count + 10
```

---

## 11.4 Comparison Operators

The following comparison operators are supported.

| Operator | Description |
|----------|-------------|
| == | Equal |
| != | Not equal |
| < | Less than |
| <= | Less than or equal |
| > | Greater than |
| >= | Greater than or equal |

Comparison expressions evaluate to BOOL.

Example:

```
LET BOOL is_equal = value_a == value_b
```

---

## 11.5 Logical Operators

The following logical operators are supported.

| Operator | Description |
|----------|-------------|
| && | Logical AND |
| \|\| | Logical OR |
| ! | Logical NOT |

Logical expressions evaluate to BOOL.

Example:

```
LET BOOL is_valid = has_name && has_email
```

---

## 11.6 Evaluation Order

Operands shall be evaluated from left to right.

Logical operators shall use short-circuit evaluation.

Evaluation order is deterministic.

---

## 11.7 Parentheses

Parentheses shall not be used to change operator precedence.

If an expression becomes difficult to read, it should be decomposed into multiple LET variables.

Recommended:

```
LET BOOL is_equal = value_a == value_b

LET BOOL is_valid = has_permission && is_equal
```

Not recommended:

```
LET BOOL is_valid =
    has_permission &&
    (value_a == value_b)
```

---

## 11.8 Expression Simplicity

Expressions should remain simple.

Programs should prefer explicit intermediate variables over deeply nested expressions.

Recommended:

```
LET BOOL same_user = user_a == user_b

LET BOOL active = user.is_active

LET BOOL result = same_user && active
```

---

## 11.9 Function Calls

A function call is an expression.

Its value is the value returned by the invoked function.

Example:

```
LET User user = User.Load(id)
```

---

## 11.10 Member Access

TYPE fields are accessed using the dot operator.

Example:

```
user.name

user.age
```

Member access may be used wherever expressions are permitted.

---

## 11.11 Design Principles

The expression system follows these principles.

- Expressions are deterministic.
- Operator precedence is fixed.
- Parentheses are not used to alter evaluation order.
- Complex expressions should be decomposed into LET variables.
- Expression evaluation is easy to understand for both humans and LLMs.



<!-- ============================================================ -->
<!-- 12.Module_System.md -->
<!-- ============================================================ -->

# 12. Module System

## 12.1 Overview

Every source file defines exactly one module.

A module groups one TYPE definition and its related functions.

Modules are the primary unit of code organization.

---

## 12.2 Module Identity

A module name shall be identical to:

- the source file name
- the TYPE name

Example:

```
User.llmk

TYPE User
```

This correspondence shall always be one-to-one.

---

## 12.3 One TYPE per Module

A source file shall contain exactly one TYPE definition.

Multiple TYPE definitions within a single source file are not permitted.

Example:

```
User.llmk

TYPE User
```

Invalid:

```
TYPE User

TYPE Address
```

---

## 12.4 Import

Modules shall be imported explicitly.

Example:

```
IMPORT User

IMPORT File

IMPORT Json
```

Only imported modules may be referenced.

---

## 12.5 Qualified Function Calls

Every function belongs to a module.

Functions shall always be invoked using the module name.

Example:

```
User.Create()

User.Validate()

Json.Serialize()

File.Read()
```

Unqualified function calls are not supported.

Invalid:

```
Create()

Validate()
```

---

## 12.6 TYPE Construction

A TYPE instance is constructed using the TYPE name.

Constructor arguments shall follow the field declaration order.

Example:

```
LET User user = User(
    "Alice",
    20
)
```

Constructors perform field initialization only.

Constructor implementations are not supported.

---

## 12.7 Behavior and Data

A TYPE represents data only.

A TYPE does not contain methods.

Behavior belongs to the module.

Example:

```
TYPE User

    STRING name

    INT age
```

```
FUNCTION User.Create(...)

FUNCTION User.Validate(...)

FUNCTION User.Save(...)
```

---

## 12.8 Visibility

All TYPE fields are public.

Functions are public within their module.

LLMK does not provide public/private/protected access modifiers.

---

## 12.9 Module Independence

Modules communicate only through:

- TYPEs
- Function calls

Global variables are not supported.

Modules shall not implicitly depend on one another.

---

## 12.10 Design Principles

The module system follows these principles.

- One file defines one module.
- One module defines one TYPE.
- One TYPE corresponds to one source file.
- Behavior belongs to modules.
- Data belongs to TYPEs.
- All function calls are explicitly qualified.
- Modules are independent and easy to understand.

TYPE definitions shall form an acyclic dependency graph. Circular TYPE dependencies are not permitted.


<!-- ============================================================ -->
<!-- 13.TYPE_System.md -->
<!-- ============================================================ -->

# 13. TYPE System

## 13.1 Overview

TYPE is the fundamental data abstraction in LLMK.

A TYPE represents a mutable data container.

TYPEs are used to group related data.

A TYPE is not an object.

---

## 13.2 TYPE Definition

A TYPE consists of an ordered list of fields.

Each field has a type and a name.

Example:

```
TYPE User

    STRING name

    INT age

    OPTIONAL STRING email
```

---

## 13.3 TYPE Identity

Each TYPE shall have a unique name.

The TYPE name shall be identical to:

- the source file name
- the module name

A TYPE shall not be defined more than once.

---

## 13.4 Fields

Every field shall have:

- a type
- a name

Fields are declared in order.

The declaration order defines the constructor parameter order.

---

## 13.5 Default Initialization

Every field is automatically initialized.

Default values are determined by the field type.

| Type | Default Value |
|------|---------------|
| BOOL | FALSE |
| INT | 0 |
| DOUBLE | 0.0 |
| STRING | "" |
| ARRAY | Empty array |
| OPTIONAL | NONE |
| RESULT | ERROR |
| TYPE | Field defaults |

---

## 13.6 Constructor

A TYPE may be constructed using its TYPE name.

Example:

```
LET User user = User(
    "Alice",
    20,
    NONE
)
```

Constructor arguments shall be positional.

Named arguments are not supported.

Constructors perform field initialization only.

Constructor implementations are not supported.

---

## 13.7 Mutability

TYPE instances are always mutable.

TYPE mutability is independent of variable mutability.

Fields may be modified after construction.

Example:

```
user.age = 21
```

Variable mutability and TYPE mutability are independent concepts.

---

## 13.8 Methods

TYPEs do not define methods.

All behavior shall be implemented as module functions.

Example:

```
User.Validate(user)

User.Save(user)
```

---

## 13.9 Visibility

All TYPE fields are public.

LLMK does not provide access modifiers.

---

## 13.10 TYPE Dependencies

TYPE definitions shall form an acyclic dependency graph.

Circular TYPE dependencies are not permitted.

Modules shall communicate through explicit TYPE definitions and function calls.

---

## 13.11 Design Principles

The TYPE system follows these principles.

- TYPE represents data only.
- Behavior belongs to modules.
- TYPEs are mutable.
- Constructors perform initialization only.
- TYPE definitions are acyclic.
- One TYPE corresponds to one module.
- One module corresponds to one source file.


<!-- ============================================================ -->
<!-- 14.Memory_and_Reference_Model.md -->
<!-- ============================================================ -->

# 14. Memory and Reference Model

## 14.1 Overview

LLMK uses a simple and deterministic memory model.

Values, variables, and references are distinct concepts.

The language minimizes implicit sharing to improve readability, safety, and AI-assisted code generation.

---

## 14.2 Value Semantics

Values are passed by value by default.

The observable behavior shall be equivalent to pass-by-value.

Example:

```
FUNCTION User.Process(

    User user

)

user.age = 30

NONE
```

Modifying `user` inside the function does not modify the caller's variable.

---

## 14.3 Reference Semantics

References are explicit.

A reference shall be declared using the `REF` keyword.

REF may only appear in a function parameter list.

Example:

```
FUNCTION User.UpdateAge(

    REF User user

    INT age

)

user.age = age

NONE
```

The function modifies the caller's variable through the reference.

---

## 14.4 Local Variables

Local references are not supported.

The following is invalid:

```
REF User user
```

Only function parameters may use `REF`.

---

## 14.5 Assignment

Assignment changes the value of a mutable variable.

LET variables cannot be reassigned.

VAR variables may be reassigned.

TYPE instances remain mutable regardless of whether the variable is declared using LET or VAR.

---

## 14.6 Shared Mutable State

Shared mutable state shall be explicit.

LLMK does not provide implicit shared references.

Sharing mutable data requires a REF parameter.

---

## 14.7 FOR

FOR may invoke a function with:

- the current element as the first parameter
- optionally one `REF TYPE` parameter as the second parameter

Example:

```
VAR Statistics statistics = Statistics()

FOR users User.Validate(statistics)
```

Conceptually, each iteration executes:

```
User.Validate(user, statistics)
```

The iterations execute sequentially.

---

## 14.8 PARFOR

PARFOR executes each iteration independently.

The invoked function shall accept exactly one parameter.

REF parameters are not permitted.

Example:

```
LET ARRAY RESULT User results =
    PARFOR users User.Validate()
```

The execution order is unspecified.

PARFOR waits for all iterations to complete.

Returned values are collected into an array preserving the input order.

Failures in one iteration shall not prevent the execution of the remaining iterations.

---

## 14.9 Deterministic Behavior

The memory model is deterministic.

The language avoids implicit aliasing and hidden shared state.

This improves readability, compiler implementation, and AI-assisted program generation.

---

## 14.10 Design Principles

The memory model follows these principles.

- Value semantics by default.
- References are explicit.
- REF is limited to function parameters.
- Shared mutable state is explicit.
- PARFOR does not permit shared mutable state.
- Deterministic behavior is preferred over implicit optimization.


<!-- ============================================================ -->
<!-- 15.Concurrency_Model.md -->
<!-- ============================================================ -->

# 15. Concurrency Model

## 15.1 Overview

LLMK provides a simple concurrency model.

Parallel execution is supported only through `PARFOR`.

The language does not expose threads, locks, mutexes, or atomic operations.

---

## 15.2 PARFOR

PARFOR executes every element of an ARRAY independently.

The execution order is unspecified.

Every iteration shall execute.

PARFOR waits until all iterations have completed.

Returned values are collected into an ARRAY preserving the input order.

---

## 15.3 Function Requirements

The invoked function shall accept exactly one parameter.

REF parameters are not permitted.

Functions executed by PARFOR should avoid observable side effects.

---

## 15.4 Failure Handling

A failure in one iteration shall not terminate the remaining iterations.

Every iteration shall complete independently.

Failures are represented by the returned RESULT values.

---

## 15.5 Determinism

The scheduling order of PARFOR is implementation-defined.

However, the observable behavior shall remain deterministic.

The returned ARRAY shall preserve the order of the input ARRAY.

---

## 15.6 Implementation Freedom

Implementations may execute PARFOR using any suitable mechanism.

Examples include:

- Native threads
- Thread pools
- Coroutines
- Task schedulers
- Distributed execution

The implementation strategy shall not affect the observable behavior of programs.

---

## 15.7 Design Principles

The concurrency model follows these principles.

- Explicit parallelism.
- No shared mutable state.
- No explicit synchronization primitives.
- Deterministic observable behavior.
- Implementation-independent execution model.


<!-- ============================================================ -->
<!-- 16.Versioning_and_Compatibility.md -->
<!-- ============================================================ -->

# 16. Versioning and Compatibility

## 16.1 Overview

The LLMK language specification follows semantic versioning.

Language evolution shall prioritize stability, readability, and compatibility.

---

## 16.2 Version Numbering

The language specification uses the following format.

```
MAJOR.MINOR
```

Examples:

```
1.0

1.1

2.0
```

---

## 16.3 Major Version

A major version introduces incompatible language changes.

Examples include:

- Removing language features
- Changing language syntax
- Changing language semantics

Major versions may require source code updates.

---

## 16.4 Minor Version

A minor version introduces backward-compatible improvements.

Examples include:

- New standard library modules
- Additional built-in functions
- Clarifications to the specification
- New optional language features

Existing programs should continue to compile without modification.

---

## 16.5 Compatibility Principles

Language evolution shall follow these principles.

- Preserve readability.
- Preserve determinism.
- Preserve simplicity.
- Preserve AI friendliness.
- Minimize breaking changes.

---

## 16.6 Implementation Conformance

An implementation shall declare the supported language version.

Programs may specify the required language version.

Implementations should reject programs requiring unsupported language versions.

---

## 16.7 Design Principles

Language evolution should favor long-term stability over rapid feature growth.

Features should only be added when they clearly improve the language while preserving the core design philosophy.


<!-- ============================================================ -->
<!-- 17.Future_Extensions.md -->
<!-- ============================================================ -->

# 17. Future Extensions

## 17.1 Overview

This chapter describes the principles for future language evolution.

Future language features shall remain consistent with the design philosophy of LLMK.

---

## 17.2 Core Principles

New language features should satisfy the following requirements.

- Improve readability.
- Improve determinism.
- Improve AI-assisted programming.
- Reduce ambiguity.
- Minimize language complexity.

---

## 17.3 Compatibility

Future language features should preserve backward compatibility whenever practical.

Breaking changes should be reserved for major language versions.

---

## 17.4 Language Growth

The core language should remain small.

Whenever practical, new functionality should be provided through the standard library instead of introducing new language syntax.

---

## 17.5 Design Philosophy

LLMK is designed as an AI-first programming language.

The language favors explicitness over implicit behavior.

The language favors simplicity over feature richness.

The language favors deterministic behavior over implementation-specific behavior.

The language favors maintainability over syntactic convenience.

---

## 17.6 Closing Statement

The purpose of LLMK is to provide a programming language that is equally understandable by humans and large language models.

Every language feature should contribute to that goal.


<!-- ============================================================ -->
<!-- 18.Outline_of_the_Paper.md -->
<!-- ============================================================ -->

# 18. Outline of the Paper

LLMK is not designed to be the smallest language.
It is designed to be the clearest language for both humans and large language models.

