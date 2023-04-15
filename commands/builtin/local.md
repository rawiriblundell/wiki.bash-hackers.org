====== The local builtin command ======

===== Synopsis =====
<code>
local [option] name[=value] ...
</code>

===== Description =====

''local'' is identical to [[commands:builtin:declare|declare]] in every way, and takes all the same options, with two exceptions:
  * Usage outside of a function is an error. Both ''declare'' and ''local'' within a function have the same effect on variable scope, including the -g option.
  * ''local'' with no options prints variable names and values in the same format as ''declare'' with no options, except the variables are filtered to print only locals that were set in the same scope from which ''local'' was called. Variables in parent scopes are not printed.

===== Portability considerations =====

  * ''local'' is not specified by POSIX. Most bourne-like shells don't have a builtin called ''local'', but some such as ''dash'' and the busybox shell do.

  * The behavior of function scope is not defined by POSIX, however local variables are implemented widely by bourne-like shells, and behavior differs substantially. Even the''dash'' shell has local variables.

  * In ksh93, using POSIX-style function definitions, ''typeset'' doesn't set ''local'' variables, but rather acts upon variables of the next-outermost scope (e.g. setting attributes). Using ''typeset'' within functions defined using ksh ''function name {'' syntax, variables follow roughly [[http://community.schemewiki.org/?lexical-scope|lexical-scoping]], except that functions themselves don't have scope, just like Bash. This means that even functions defined within a &quot;function's scope&quot; don't have access to non-local variables except through ''namerefs''.

===== See also =====

