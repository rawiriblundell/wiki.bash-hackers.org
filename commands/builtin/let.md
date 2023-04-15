====== The let builtin command ======

===== Synopsis =====

<code>
let arg [arg ...]
</code>

===== Description =====
The ''let'' builtin command evaluates each supplied word from left to right as an [[syntax:arith_expr | arithmetic expression]] and returns an exit code according to the truth value of the rightmost expression.
  * 0 (TRUE) when ''arg'' evaluated to not 0 (arithmetic &quot;true&quot;)
  * 1 (FALSE) when ''arg'' evaluated to 0 (arithmetic &quot;false&quot;)
For this return code mapping, please see [[syntax:arith_expr#arithmetic_expressions_and_return_codes | this section]]. They work in the same way as ''<nowiki>((</nowiki>''.

===== Examples =====
''let'' is very similar to [[syntax:ccmd:arithmetic_eval | ((]] - the only difference being ''let'' is a builtin (simple command), and ''<nowiki>((</nowiki>'' is a compound command. The arguments to ''let'' are therefore subject to all the same expansions and substitutions as any other simple command - requiring proper quoting and escaping - whereas the contents of ''<nowiki>((</nowiki>'' aren't subject to [[syntax:expansion:wordsplit | word-splitting]] or [[syntax:expansion:globs | pathname expansion]] (almost never desirable for arithmetic). For this reason, **the [[syntax:ccmd:arithmetic_eval | arithmetic compound command]] should generally be preferred over ''let''**.

<code>
$ let 'b = a' &quot;(a += 3) + $((a = 1)), b++&quot;
$ echo &quot;$a - $b - $?&quot;
4 - 2 - 0
</code>

Is equivalent to the [[syntax:ccmd:arithmetic_eval | arithmetic evaluation compound command]]:

<code>
$ (( b = a, (a += 3) + $((a = 1)), b++ ))
$ echo &quot;$a - $b - $?&quot;
4 - 2 - 0
</code>

<WRAP info>
Remember that inside arithmetic evaluation contexts, all other expansions are processed as usual (from left-to-right), and the resulting text is evaluated as an arithmetic expression. Arithmetic already has a way to control precedence using parentheses, so it's very rare to need to nest arithmetic expansions within one another. It's used above only to illustrate how this precedence works.
</WRAP>

Unlike ''<nowiki>((</nowiki>'', being a simple command ''let'' has its own environment. In Bash, built-ins that can set variables process any arithmetic under their own environment, which makes the variable effectively &quot;local&quot; to the builtin unless the variable is also set or modified by the builtin. This differs in other shells, such as ksh93, where environment assignments to regular builtins are always local even if the variable is modified by the builtin.

<code>
 ~ $ ( y=1+1 let x=y; declare -p x y )
declare -- x=&quot;2&quot;
bash: declare: y: not found

 ~ $ ( y=1+1 let x=y++; declare -p x y )
declare -- x=&quot;2&quot;
declare -- y=&quot;3&quot;
</code>

This can be useful in certain situations where a temporary variable is needed.

===== Portability considerations =====

  * the ''let'' command is not specified by POSIX(r). The portable alternative is: <code>[ &quot;$(( <EXPRESSION> ))&quot; -ne 0 ]</code>. To make portable scripts simpler and cleaner, ''let'' can be defined as: <code>
# POSIX
let() {
    IFS=, command eval test '$(($*))' -ne 0
}
</code> Aside from differences in supported arithmetic features, this should be identical to the Bash/Ksh ''let''.
  * It seems to be a common misunderstanding that ''let'' has some legacy purpose. Both ''let'' and [[syntax/ccmd/arithmetic_eval | ((...))]] were ksh88 features and almost identical in terms of portability as everything that inherited one also tended to get the other. Don't choose ''let'' over ''(('' expecting it to work in more places.
  * [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/expr.html#tag_20_42 | expr(1)]] is a command one is likely to come across sooner or later. While it is more &quot;standard&quot; than ''let'', the above should always be preferred. Both [[syntax:arith_expr | arithmetic expansion]]s and the ''['' test operator are specified by POSIX(r) and satisfy almost all of expr's use-cases. Unlike ''let'', ''expr'' cannot assign directly to bash variables but instead returns a result on stdout. ''expr'' takes each operator it recognizes as a separate word and then concatenates them into a single expression that's evaluated according to it's own rules (which differ from shell arithmetic). ''let'' parses each word it recieves on its own and evaluates it as an expression without generating any output other than a return code.
  * For unknown reasons, ''let'' is one of the Bash commands with special parsing for arguments formatted like compound array assignments. See: [[commands/builtin/eval#portability_considerations | eval]] for details. There are no known practical uses for this. Parentheses are treated as grouping operators and compound assignment is not possible by arithmetic expressions.

===== See also =====

  * Internal: [[syntax:expansion:arith | arithmetic expansion]]
  * Internal: [[syntax:arith_expr | arithmetic expressions]]
