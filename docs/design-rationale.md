# Design Rationale

## 1. Background

Large Language Models (LLMs) are increasingly used for software development.

LLMs can generate new code, modify existing code, explain programs, and perform software maintenance. As these capabilities improve, programming languages themselves become part of the interface between humans and LLMs.

Most existing programming languages were primarily designed around human programmers and the needs of conventional software development.

LLMs can work with these languages, but their characteristics also create an opportunity to reconsider how programming languages should be designed.

LLM K was created to explore this opportunity.

The goal is not to create a language that is merely smaller than existing languages. The goal is to design a language whose syntax and semantics make programs easier to generate, understand, modify, and maintain for both humans and LLMs.

---

## 2. Design Goals

LLM K is designed around the following goals:

* Clarity
* Explicitness
* Determinism
* Simplicity
* Maintainability
* AI-assisted programmability

These goals are closely related.

A language that reduces implicit behavior is easier to reason about.

A language that reduces ambiguity is easier to parse and analyze.

A language that uses explicit and predictable structures is easier to modify safely.

Therefore, LLM K prioritizes explicit and deterministic program structures over syntactic convenience and feature richness.

---

## 3. AI-First Does Not Mean AI-Only

LLM K is an AI-first programming language, but it is not intended to be an AI-only language.

Programs should remain understandable and maintainable by humans.

The design therefore considers two audiences simultaneously:

* Human programmers
* Large Language Models

A language feature is preferred when it improves clarity for both.

A feature that is convenient for humans but introduces unnecessary ambiguity for LLMs is treated cautiously.

Likewise, syntax designed only for machine processing but difficult for humans to read is not considered a desirable solution.

The objective is a common representation that both humans and LLMs can reason about effectively.

---

## 4. Explicitness Over Implicit Behavior

LLM K favors explicit behavior over implicit behavior.

Implicit behavior increases the amount of context that must be remembered when reading or modifying code.

For example, LLM K requires variables to be explicitly declared with a type and initialized with a value.

This avoids situations where the meaning of an uninitialized variable depends on an implicit default value.

The same principle is applied throughout the language.

Examples include:

* Explicit variable declarations
* Explicit initialization
* Explicit module-qualified function calls
* Explicit reference parameters
* Explicit parallel execution
* Explicit error handling

The objective is to make important program behavior visible in the source code.

---

## 5. One TYPE = One Module = One Source File

LLM K defines a strict relationship between TYPEs, modules, and source files.

One TYPE corresponds to one module, and one module corresponds to one source file.

This provides a clear mapping between:

```text
TYPE
  =
Module
  =
Source File
```

This design makes the structural boundary of a program immediately visible.

For humans, the mapping simplifies navigation and maintenance.

For LLMs, it provides a predictable unit for code retrieval, generation, modification, and analysis.

A model does not need to infer which TYPE belongs to which module or determine how multiple TYPE definitions within a file are related.

The source tree itself becomes part of the program structure.

---

## 6. Functions Define Behavior, TYPEs Define Data

LLM K separates behavior from data.

TYPEs represent data.

Functions implement behavior.

TYPEs do not contain methods or executable behavior.

This separation reduces the number of relationships that must be considered when analyzing a program.

A TYPE can be understood primarily as a data structure, while a FUNCTION can be understood primarily as an operation.

This also supports the one TYPE = one module = one file model.

---

## 7. Simple Functions

LLM K intentionally simplifies function behavior.

Functions do not declare a return type.

Instead, every function ends with exactly one value, and that value determines the result of the function.

The `RETURN` statement is not provided.

This design gives every function a clearly defined endpoint.

A reader does not need to search through a function to determine whether execution can terminate at multiple return statements.

For an LLM, this also reduces the number of possible control-flow paths that must be considered when analyzing or modifying a function.

The intent is not to remove useful functionality, but to make the structure of a function more predictable.

---

## 8. Simple Control Flow

LLM K provides a small number of control-flow constructs:

* IF
* MATCH
* FOR
* PARFOR

The language deliberately avoids unnecessary variations of control flow.

For example, `ELSE` is not provided.

Alternative branches can be represented using `MATCH`, negated conditions, or separate functions.

Control flow is centered around function invocation rather than large nested executable blocks.

This keeps the structure of a program shallow and makes individual operations explicit.

The objective is to reduce deeply nested structures that are difficult for both humans and LLMs to analyze.

---

## 9. Simple Expressions

LLM K deliberately restricts expression complexity.

An expression contains at most one operator.

For example:

```text
LET INT x = b * c
LET INT z = a + x
```

is preferred over:

```text
LET INT z = a + b * c
```

The purpose is not to make arithmetic less expressive.

The same computation can always be decomposed into intermediate variables.

The benefit is that LLM K does not require programmers or LLMs to reason about complex operator precedence within a single expression.

Parentheses are therefore not needed to control operator precedence.

This creates a simpler and more explicit evaluation model.

---

## 10. Explicit Error Handling

Error handling is designed around explicit result values.

LLM K provides `OPTIONAL` and `RESULT` as language-level concepts.

A function that may fail can explicitly represent that possibility in its result.

The language does not rely on an implicit default error state for `RESULT`.

A RESULT value must be explicitly produced.

This avoids a situation in which an automatically initialized variable represents a failure that never actually occurred.

The design follows the broader principle that important program states should be represented explicitly.

The `Error` TYPE itself belongs to the standard library rather than being part of the core language.

This keeps the core language small while allowing the standard library to define the concrete error representation.

---

## 11. Restricted Reference Semantics

LLM K uses value semantics by default.

Reference semantics are available through `REF`, but `REF` is restricted to function parameters.

Local reference variables are not provided.

This restriction reduces aliasing and makes data flow easier to reason about.

When a function accepts a `REF` parameter, the source code explicitly communicates that the function may operate on shared mutable data.

The language therefore distinguishes ordinary value passing from mutation through references without requiring a complex general-purpose reference system.

---

## 12. Explicit Parallelism

LLM K provides explicit parallel execution through `PARFOR`.

The language does not provide `async/await` as a core language feature.

It also does not expose general-purpose threads, locks, mutexes, or other low-level synchronization primitives as part of the language model.

The reason is not that asynchronous or concurrent computation is unimportant.

Rather, LLM K attempts to provide a small and predictable abstraction for the common case of independent parallel operations.

`PARFOR` expresses the intent directly:

> Execute all elements of this collection in parallel.

The implementation may use threads, tasks, a thread pool, distributed execution, or another mechanism.

Only the observable behavior defined by the language specification matters.

This separates the programming model from the implementation mechanism.

---

## 13. Deterministic Observable Behavior

LLM K distinguishes between implementation freedom and observable language behavior.

An implementation may internally use different strategies as long as the externally observable behavior conforms to the language specification.

For example, an implementation may choose different mechanisms for value handling, memory management, or parallel execution.

The language does not prescribe unnecessary implementation details.

This allows implementations to evolve without changing the meaning of programs.

At the same time, behavior that is visible to the programmer must remain predictable.

The goal is therefore:

> Implementation freedom without semantic ambiguity.

---

## 14. Explicit Initialization

LLM K requires variables to be initialized when they are declared.

This design avoids implicit uninitialized states and eliminates the need for programmers to remember language-specific default values for ordinary variables.

It also prevents a distinction between:

```text
declared but not meaningfully initialized
```

and

```text
explicitly initialized
```

from becoming part of ordinary program reasoning.

For `RESULT` in particular, this means there is no implicit default `ERROR` value.

A result representing success or failure must be produced explicitly.

This is consistent with the overall philosophy of explicit state representation.

---

## 15. Minimal Core Language

LLM K intentionally keeps the core language small.

Features that can be expressed effectively through functions or the standard library should not automatically become language syntax.

This principle is particularly important for an AI-first language.

Every additional language feature introduces:

* More syntax
* More semantic rules
* More interactions between features
* More cases that an LLM must understand
* More opportunities for ambiguous code generation

Therefore, language features should be added only when they provide a clear benefit that cannot be achieved appropriately outside the core language.

The standard library provides an important extension mechanism without increasing the complexity of the core language.

---

## 16. Syntax and Semantics Over Convenience

LLM K does not attempt to minimize the number of characters required to write a program.

Instead, it attempts to minimize unnecessary ambiguity and structural complexity.

A slightly longer program can be preferable when its structure is easier to understand.

For example:

```text
LET INT x = b * c
LET INT z = a + x
```

is longer than:

```text
LET INT z = a + b * c
```

but the evaluation structure is immediately visible.

LLM K therefore favors explicit intermediate steps when they improve clarity.

The goal is not minimum source length.

The goal is minimum unnecessary reasoning complexity.

---

## 17. Trade-offs

The design choices in LLM K intentionally sacrifice some forms of syntactic convenience.

Examples include:

* No `RETURN`
* No `ELSE`
* No complex multi-operator expressions
* No local `REF`
* No `async/await`
* Restricted control flow
* Explicit initialization
* Explicit module-qualified function calls

These restrictions may make some programs more verbose than their equivalents in conventional programming languages.

This is intentional.

LLM K prioritizes predictable structure and maintainability over maximum syntactic flexibility.

The language should be evaluated based on whether these trade-offs improve software development involving both humans and LLMs.

---

## 18. Design Principle

The design decisions throughout LLM K can be summarized as follows:

> **Make important behavior explicit, reduce unnecessary choices, and keep program structure predictable.**

LLM K is not intended to be the language with the fewest features.

It is intended to explore whether a programming language can be designed so that its programs are easier for both humans and large language models to understand, generate, modify, and maintain.

The language specification defines the resulting rules.

This document explains the reasoning behind those rules.
