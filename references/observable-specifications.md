# Observable specifications

Read this when writing or evaluating a behavior specification, especially when
choosing an assertion or responding to a mutation survivor.

Specify the response or state a caller can observe. Do not specify private
implementation details or which collaborator methods were called. This keeps a
specification tied to its contract, so a behavior-preserving refactor does not
break it.

Use controllable infrastructure to set up inputs, but assert the resulting
domain outcome. When a collaborator owns a representation, use its own
formatting or comparison operation rather than duplicating its representation
in an assertion. Add a specification for a mutation survivor only when it
captures intended behavior, not merely to kill the mutant.
