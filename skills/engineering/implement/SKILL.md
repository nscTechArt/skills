---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Leave the changes in the working tree.

Once done, call the Skill tool with "code-review" to review the work. Give it the fixed point `HEAD` (it reviews the uncommitted working tree) and the spec or tickets you implemented as the spec, so it asks the user for neither.
