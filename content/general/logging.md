---
title: "Logging"
date: 2020-05-09T08:12:10+01:00
draft: False
weight: 40
pre: "<b>4. </b>"
tags: ["general", "logging"]
---

Logs are designed to be **read**.  The same rules regarding readable coding style and comments applies to logs.  Remember, the logs are the first things checked when you have that 2am incident where the customer is screaming at you - comprehensive, useful logging is your friend.

### Simple Logging Rules

#### 1. Log meaningful messages.

* Aim to provide useful information about what the program is doing.
* Avoid ambiguous or repetitive messages devoid of context, for example 'processing request'.

{{% notice warning %}}
Always assume logs are public information.  Cutesy messages look unprofessional.  **NEVER** log passwords, password hashes, salts, crypto keys or other security material.
{{% /notice %}}

#### 2. Use appropriate log levels.

These are generally recognised log levels between different logging systems.  Its generally a good idea to allow the level to be configured externally.

| Level          | Usage |
| -------------- | ----- |
| TRACE or DEBUG | Use for debug output, or tracing through a program.  Guard against large, expensive output (ie big objects tree traversals, or large arrays) by detecting if the log level is enabled. |
| INFO           | General behaviour messages, for example what function the program is performing. |
| WARN           | Potential precursor events, but are not themselves errors. |
| ERROR          | Error conditions that may allow the program to continue running. |
| FATAL          | The program will abort, message should contain diagnostic information why. |

#### 3. Provide _systematic context_ to the message.

Always provide enough information to provide context to the message.  This should include:

* Correlation or Trace Identifier for a request
* Source or a request (at least once)
* Specific content in a request relevant to message.

#### 4. Structure the messages

* Each line on STDOUT may be parsed as a single JSON snippet
* Context can be standardised
