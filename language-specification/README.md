# LLM K Language Specification

LLM K (LLM Kaizen Language) is an AI-first programming language designed to maximize clarity, determinism, and maintainability for both humans and large language models.

The **LLM K (LLM Kaizen Language)** language specification is organized into multiple chapters.

Each chapter defines one aspect of the language. The chapters are intended to be read in numerical order.

This specification defines the **LLM K language** only. The standard library, compiler implementation, examples, and other supporting documents are maintained separately.

---

# Language Philosophy

LLM K is designed as an **AI-first programming language** while remaining simple, deterministic, and easy for humans to understand.

The language follows these core principles:

* One TYPE = One Module = One Source File
* Functions define behavior
* TYPEs define data
* One executable statement per line
* Every function ends with exactly one value
* Explicit behavior over implicit behavior
* Deterministic execution
* Minimal language complexity
* AI-friendly syntax and semantics

---

# Language Specification

|                                                                Chapter | Description                                                          |
| ---------------------------------------------------------------------: | -------------------------------------------------------------------- |
|                                 [01. Introduction](01.Introduction.md) | Purpose, goals, terminology and overall language philosophy.         |
|                       [02. Lexical Structure](02.Lexical_Structure.md) | Source format, identifiers, keywords, literals and comments.         |
|                       [03. Program Structure](03.Program_Structure.md) | Overall source file organization and program structure.              |
|                 [04. Built-in Type System](04.Built-in_Type_System.md) | Primitive types, built-in generic types and default values.          |
|                                       [05. Variables](05.Variables.md) | LET, VAR, mutability and variable declaration rules.                 |
|                                       [06. Functions](06.Functions.md) | Function definitions, parameters, REF, return values and invocation. |
|                                 [07. Control Flow](07.Control_Flow.md) | IF, MATCH, FOR and PARFOR.                                           |
|                             [08. Error Handling](08.Error_Handling.md) | OPTIONAL, RESULT and explicit error handling.                        |
|                         [09. Standard Library](09.Standard_Library.md) | Scope and architecture of the standard library.                      |
|                             [10. Formal Grammar](10.Formal_Grammar.md) | Formal EBNF grammar of the language.                                 |
|       [11. Expressions and Operators](11.Expressions_and_Operators.md) | Expressions, operators and evaluation rules.                         |
|                               [12. Module System](12.Module_System.md) | Modules, IMPORT, TYPEs and source files.                             |
|                                   [13. TYPE System](13.TYPE_System.md) | TYPE definitions, constructors and the data model.                   |
|     [14. Memory and Reference Model](14.Memory_and_Reference_Model.md) | Value semantics, REF semantics and memory model.                     |
|                       [15. Concurrency Model](15.Concurrency_Model.md) | PARFOR execution model and concurrency rules.                        |
| [16. Versioning and Compatibility](16.Versioning_and_Compatibility.md) | Language versioning and compatibility policy.                        |
|                       [17. Future Extensions](17.Future_Extensions.md) | Principles for future language evolution.                            |

---

# Related Documents

The following documents provide additional background information but are **not part of the language specification**.

| Document                                               | Description                                                                                                                                             |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [18. Outline of the Paper](18.Outline_of_the_Paper.md) | Draft structure of the accompanying research paper describing the motivation, design philosophy, specification, implementation and evaluation of LLM K. |

---

# Planned Documents

The following documents are planned as separate specifications.

* Standard Library Specification
* Compiler Specification
* Style Guide
* Programming Examples
* Language Tutorial

---

# Design Goals

LLM K aims to provide a programming language that is equally understandable by humans and large language models.

The language prioritizes:

* Simplicity over feature richness
* Explicit behavior over implicit behavior
* Readability over syntactic convenience
* Deterministic execution over implementation-specific behavior
* Long-term maintainability over short-term optimization

---

# Repository Structure

```text
language-specification/
├── README.md
├── 01.Introduction.md
├── 02.Lexical_Structure.md
├── 03.Program_Structure.md
├── 04.Built-in_Type_System.md
├── 05.Variables.md
├── 06.Functions.md
├── 07.Control_Flow.md
├── 08.Error_Handling.md
├── 09.Standard_Library.md
├── 10.Formal_Grammar.md
├── 11.Expressions_and_Operators.md
├── 12.Module_System.md
├── 13.TYPE_System.md
├── 14.Memory_and_Reference_Model.md
├── 15.Concurrency_Model.md
├── 16.Versioning_and_Compatibility.md
├── 17.Future_Extensions.md
└── 18.Outline_of_the_Paper.md
```

---

# Contributing

When proposing language changes:

* Preserve the core design philosophy.
* Minimize language complexity.
* Prefer explicit and deterministic behavior.
* Maintain backward compatibility whenever practical.
* Consider both human readability and AI readability.

Language changes should improve the language without compromising its simplicity or consistency.
