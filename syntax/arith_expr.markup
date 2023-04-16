====== Arithmetic expressions ======

{{keywords>bash shell scripting math arithmetic C calculation integer}}

Arithmetic expressions are used in several situations:
  * [[syntax:ccmd:arithmetic_eval | arithmetic evaluation command]]
  * [[syntax:expansion:arith | arithmetic expansion]]
  * [[syntax:pe#substring_expansion | substring parameter expansion]]
  * [[commands:builtin:let | the ''let'' builtin command]]
  * [[syntax:ccmd:c_for | C-style for loop]]
  * [[syntax:arrays | array indexing]]
  * [[syntax:ccmd:conditional_expression | conditional expressions]]
  * Assignment statements, and arguments to declaration commands of variables with the integer attribute.

These expressions are evaluated following some rules described below. The operators and rules of arithmetic expressions are mainly derived from the C programming language.

This article describes the theory of the used syntax and the behaviour. To get practical examples without big explanations, see [[http://mywiki.wooledge.org/BashGuide/CompoundCommands#Arithmetic_Evaluation | this page on Greg's wiki]].

===== Constants =====

Mathematical constants are simply fixed values you write: ''1'', ''3567'', or ''4326''. Bash interprets some notations specially:
  * ''0...'' (leading zero) is interpreted as an **octal** value
  * ''0x...'' is interpreted as a **hex** value
  * ''0X...'' also interpreted as a **hex**
  * ''<BASE>#...'' is interpreted as a number according to the **specified base** ''<BASE>'', e.g., ''2#00111011'' (see below)
If you have a constant set in a variable, like,
<code>
x=03254
</code>
this is interpreted as an octal value. If you want it to be interpreted as a decimal value, you need to expand the parameter and specify base 10:
<code>
# this is interpreted as a decimal:
echo $(( 10#$x ))

# this is interpreted as an octal:
echo $(( x ))

# this is an invalid digit for base 10 (the &quot;x&quot;)...:
echo $(( 10#x ))
</code>

===== Different bases =====

For a constant, the base can be specified using the form
<code>
<BASE>#<DIGITS...>
</code>

Regardless of the specified base, the arithmetic expressions will, if ever displayed, be **displayed in decimal**!

When no base is specified, the base 10 (decimal) is assumed, except when the prefixes as mentioned above (octals, hexadecimals) are present. The specified base can range from 2 to 64. To represent digits in a specified base greater than 10, characters other than 0 to 9 are needed (in this order, low => high):
  * ''0 ... 9''
  * ''a ... z''
  * ''A ... Z''
  * ''@''
  * ''_''

Let's quickly invent a new number system with base 43 to show what I mean:
<code>
$ echo $((43#1))
1

$ echo $((43#a))
10

$echo $((43#A))
36

$ echo $((43#G))
42

$ echo $((43#H))
bash: 43#H: value too great for base (error token is &quot;43#H&quot;)
</code>

If you have no clue what a base is and why there might be other bases, and what numbers are and how they are built, then you don't need different bases.

If you want to convert between the usual bases (octal, decimal, hex), use [[commands:builtin:printf | the printf command]] and its format strings.

===== Shell variables =====

Shell variables can of course be used as operands, even when the integer attribute is not turned on (by ''declare -i <NAME>''). If the variable is empty (null) or unset, its reference evaluates to 0. If the variable doesn't hold a value that looks like a valid expression (numbers or operations), the expression is re-used to reference, for example, the named parameters, e.g.:
<code>
test=string
string=3

echo $((test))
# will output &quot;3&quot;!
</code>
Of course, in the end, when it finally evaluates to something that is **not** a valid arithmetic expression (newlines, ordinary text, ...) then you'll get an error.

When variables are referenced, the notation ''1 + $X'' is equivalent to the notation ''1 + X'', both are allowed.

When variables are referenced like ''$X'', the rules of [[syntax:pe | parameter expansion]] apply and are performed **before** the expression is evaluated. Thus, a construct like ''${MYSTRING:4:3}'' is valid inside an arithmetic expression.

===== Truth =====

Unlike command exit and return codes, arithmetic expressions evaluate to logical &quot;true&quot; when they are not 0. When they are 0, they evaluate to &quot;false&quot;. The [[syntax:ccmd:arithmetic_eval | arithmetic evaluation compound command]] reverses the &quot;truth&quot; of an arithmetic expression to match the &quot;truth&quot; of command exit codes:
  * if the arithmetic expression brings up a value not 0 (arithmetic true), it returns 0 (shell true)
  * if the arithmetic expression evaluates to 0 (arithmetic false), it returns 1 (shell false)
That means, the following ''if''-clause will execute the ''else''-thread:
<code>
if ((0)); then
  echo &quot;true&quot;
else
  echo &quot;false&quot;
fi
</code>

===== Operators =====

==== Assignment ====

^Operator^Description^
|''<ID> = <EXPR>''|normal assignment|
|''<ID> *= <EXPR>''|equivalent to ''<ID> = <ID> * <EXPR>'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''<ID> /= <EXPR>''|equivalent to ''<ID> = <ID> / <EXPR>'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''<ID> %= <EXPR>''|equivalent to ''<ID> = <ID> % <EXPR>'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''<ID> += <EXPR>''|equivalent to ''<ID> = <ID> + <EXPR>'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''<ID> -= <EXPR>''|equivalent to ''<ID> = <ID> - <EXPR>'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''<ID> <nowiki><<=</nowiki> <NUMBER>''|equivalent to ''<ID> = <ID> <nowiki><<</nowiki> <NUMBER>'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''<ID> <nowiki>>>=</nowiki> <NUMBER>''|equivalent to ''<ID> = <ID> <nowiki>>></nowiki> <NUMBER>'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''<ID> &= <EXPR>''|equivalent to ''<ID> = <ID> & <EXPR>'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''<ID> ^= <EXPR>''|equivalent to ''<ID> = <ID> ^ <EXPR>'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''<ID> <nowiki>|=</nowiki> <EXPR>''|equivalent to ''<ID> = <ID> <nowiki>|</nowiki> <EXPR>'', see [[syntax:arith_expr#bit_operations | bit operations]]|

==== Calculations ====

^Operator^Description^
|''*''|multiplication|
|''/''|division|
|''%''|remainder (modulo)|
|''+''|addition|
|''-''|subtraction|
|''<nowiki>**</nowiki>''|exponentiation|

==== Comparisons ====

^Operator^Description^
|''<''|comparison: less than|
|''>''|comparison: greater than|
|''<nowiki><=</nowiki>''|comparison: less than or equal|
|''<nowiki>>=</nowiki>''|comparison: greater than or equal|
|''<nowiki>==</nowiki>''|equality|
|''!=''|inequality|

==== Bit operations ====

^Operator^Description^
|''~''|bitwise negation|
|''<nowiki><<</nowiki>''|bitwise shifting (left)|
|''<nowiki>>></nowiki>''|bitwise shifting (right)|
|''&''|bitwise AND|
|''^''|bitwise exclusive OR (XOR)|
|''|''|bitwise OR|

==== Logical ====

^Operator^Description^
|''!''|logical negation|
|''<nowiki>&&</nowiki>''|logical AND|
|''<nowiki>||</nowiki>''|logical OR|

==== Misc ====

^ Operator                      ^ Description                                                                           ^
| ''id++''                      | **post-increment** of the variable ''id'' (not required by POSIX(r))                  |
| ''id%%--%%''                  | **post-decrement** of the variable ''id'' (not required by POSIX(r))                  |
| ''++id''                      | **pre-increment** of the variable ''id'' (not required by POSIX(r))                   |
| ''%%--%%id''                  | **pre-decrement** of the variable ''id'' (not required by POSIX(r))                   |
| ''+''                         | unary plus                                                                            |
| ''-''                         | unary minus                                                                           |
| ''<EXPR> ? <EXPR> : <EXPR>''  | conditional (ternary) operator \\ <condition> ? <result-if-true> : <result-if-false>  |
| ''<EXPR> , <EXPR>''           | expression list                                                                       |
| ''( <EXPR> )''                | subexpression (to force precedence)                                                   |


===== Precedence =====

The operator precedence is as follows (highest -> lowest):

  * Postfix (''id++'', ''<nowiki>id--</nowiki>'')
  * Prefix (''++id'', ''<nowiki>--id</nowiki>'')
  * Unary minus and plus (''-'', ''+'')
  * Logical and bitwise negation (''!'', ''~'')
  * Exponentiation (''<nowiki>**</nowiki>'')
  * Multiplication, division, remainder (''*'', ''/'', ''%'')
  * Addition, subtraction (''+'', ''-'')
  * Bitwise shifts (''<nowiki><<</nowiki>'', ''<nowiki>>></nowiki>'')
  * Comparison (''<'', ''>'', ''<nowiki><=</nowiki>'', ''<nowiki>>=</nowiki>'')
  * (In-)equality (''<nowiki>==</nowiki>'', ''!='')
  * Bitwise AND (''&'')
  * Bitwise XOR (''^'')
  * Bitwise OR (''|'')
  * Logical AND (''<nowiki>&&</nowiki>'')
  * Logical OR (''<nowiki>||</nowiki>'')
  * Ternary operator (''<EXPR> ? <EXPR> : <EXPR>'')
  * Assignments (''='', ''*='', ''/='', ''%='', ''+='', ''-='', ''<nowiki><<=</nowiki>'', ''<nowiki>>>=</nowiki>'', ''&='', ''^='', ''|='')
  * Expression list operator (''<EXPR> , <EXPR>'')

The precedence can be adjusted using subexpressions of the form ''( <EXPR> )'' at any time. These subexpressions are always evaluated first.

===== Arithmetic expressions and return codes =====

Bash's overall language construct is based on exit codes or return codes of commands or functions to be executed. ''if'' statements, ''while'' loops, etc., they all take the return codes of commands as conditions.

Now the problem is: The return codes (0 means &quot;TRUE&quot; or &quot;SUCCESS&quot;, not 0 means &quot;FALSE&quot; or &quot;FAILURE&quot;) don't correspond to the meaning of the result of an arithmetic expression (0 means &quot;FALSE&quot;, not 0 means &quot;TRUE&quot;).

That's why all commands and keywords that do arithmetic operations attempt to **translate** the arithmetical meaning into an equivalent return code. This simply means:
  * if the arithmetic operation evaluates to 0 (&quot;FALSE&quot;), the return code is not 0 (&quot;FAILURE&quot;)
  * if the arithmetic operation evaluates to 1 (&quot;TRUE&quot;), the return code is 0 (&quot;SUCCESS&quot;)

This way, you can easily use arithmetic expressions (along with the commands or keywords that operate them) as conditions for ''if'', ''while'' and all the others, including ''set -e'' for autoexit on error:
<code bash>
MY_TEST_FLAG=0

if ((MY_TEST_FLAG)); then
  echo &quot;MY_TEST_FLAG is ON&quot;
else
  echo &quot;MY_TEST_FLAG is OFF&quot;
fi
</code>
<WRAP center round important>
Beware that ''set -e'' can change the runtime behavior of scripts.  For example,

This non-equivalence of code behavior deserves some attention.
Consider what happens if v happens to be zero in the expression below:

<code bash>((v += 0))
echo $?</code>
1

(&quot;FAILURE&quot;)

<code bash>v=$((v + 0))
echo $?</code>
0

(&quot;SUCCESS&quot;)

The return code behavior is not equivalent to the arithmetic behavior, as has been noted.

A workaround is to use a list operation that returns True, or use the second assignment style.
<code bash>((v += 0)) || :
echo $?</code>
0

(&quot;SUCCESS&quot;)

This change in code behavior was discovered once the script was run under set -e.
</WRAP>
===== Arithmetic expressions in Bash =====

  * [[syntax:ccmd:c_for | The C-style for-loop]]
  * [[syntax:expansion:arith | Arithmetic expansion]]
  * [[syntax:ccmd:arithmetic_eval | Arithmetic evaluation compound command]]
  * [[commands:builtin:let | The &quot;let&quot; builtin command]]
