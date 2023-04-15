====== The C-style for-loop ======

===== Synopsis =====
&lt;code&gt;
for (( &lt;EXPR1&gt; ; &lt;EXPR2&gt; ; &lt;EXPR3&gt; )); do
  &lt;LIST&gt;
done
&lt;/code&gt;

&lt;code&gt;
# as a special case: without semicolon after ((...))
for (( &lt;EXPR1&gt; ; &lt;EXPR2&gt; ; &lt;EXPR3&gt; )) do
  &lt;LIST&gt;
done
&lt;/code&gt;

&lt;code&gt;
# alternative, historical and undocumented syntax
for (( &lt;EXPR1&gt; ; &lt;EXPR2&gt; ; &lt;EXPR3&gt; )) {
  &lt;LIST&gt;
}
&lt;/code&gt;

===== Description =====

The C-style for-loop is a [[syntax/basicgrammar#compound_commands | compound command]] derived from the equivalent ksh88 feature, which is in turn derived from the C &quot;for&quot; keyword. Its purpose is to provide a convenient way to evaluate arithmetic expressions in a loop, plus initialize any required arithmetic variables. It is one of the main &quot;loop with a counter&quot; mechanisms available in the language. 

The ''&lt;nowiki&gt;((;;))&lt;/nowiki&gt;'' syntax at the top of the loop is not an ordinary [[syntax/ccmd/arithmetic_eval | arithmetic compound command]], but is part of the C-style for-loop's own syntax. The three sections separated by semicolons are [[syntax:arith_expr | arithmetic expression]] contexts. Each time one of the sections is to be evaluated, the section is first processed for: brace, parameter, command, arithmetic, and process substitution/expansion as usual for arithmetic contexts. When the loop is entered for the first time, ''&lt;EXPR1&gt;'' is evaluated, then ''&lt;EXPR2&gt;'' is evaluated and checked. If ''&lt;EXPR2&gt;'' is true, then the loop body is executed. After the first and all subsequent iterations, ''&lt;EXPR1&gt;'' is skipped, ''&lt;EXPR3&gt;'' is evaluated, then ''&lt;EXPR2&gt;'' is evaluated and checked again. This process continues until ''&lt;EXPR2&gt;'' is false.

  * ''&lt;EXPR1&gt;'' is to **initialize variables** before the first run.
  * ''&lt;EXPR2&gt;'' is to **check** for a termination condition. This is always the last section to evaluate prior to leaving the loop.
  * ''&lt;EXPR3&gt;'' is to **change** conditions after every iteration. For example, incrementing a counter.

:!: If one of these arithmetic expressions in the for-loop is empty, it behaves as if it would be 1 (**TRUE** in arithmetic context).

:!: Like all loops (Both types of ''for''-loop, ''while'' and ''until''), this loop can be:
  * Terminated (broken) by the [[commands/builtin/continuebreak | break]] builtin, optionally as ''break N'' to break out of ''N'' levels of nested loops.
  * Forced immediately to the next iteration using the [[commands/builtin/continuebreak | continue]] builtin, optionally as the ''continue N'' analog to ''break N''.

The equivalent construct using a [[syntax/ccmd/while_loop | while loop]] and the [[syntax:ccmd:arithmetic_eval | arithmetic expression compound command]] would be structured as:
&lt;code&gt;
(( &lt;EXPR1&gt; ))
while (( &lt;EXPR2&gt; )); do
  &lt;LIST&gt;
  (( &lt;EXPR3&gt; ))
done
&lt;/code&gt;
The equivalent ''while'' construct isn't exactly the same, because both, the ''for'' and the ''while'' loop behave differently in case you use the [[commands/builtin/continuebreak | continue]] command.


==== Alternate syntax ====

Bash, Ksh93, Mksh, and Zsh also provide an alternate syntax for the ''for'' loop - enclosing the loop body in ''{&lt;nowiki&gt;...&lt;/nowiki&gt;}'' instead of ''do &lt;nowiki&gt;...&lt;/nowiki&gt; done'':

&lt;code&gt;
for ((x=1; x&lt;=3; x++))
{
  echo $x
}
&lt;/code&gt;

This syntax is **not documented** and shouldn't be used. I found the parser definitions for it in 1.x code, and in modern 4.x code. My guess is that it's there for compatibility reasons. Unlike the other aforementioned shells, Bash does not support the analogous syntax for [[syntax/ccmd/case#portability_considerations | case..esac]].

==== Return status ====

The return status is that of the last command executed from ''&lt;LIST&gt;'', or ''FALSE'' if any of the arithmetic expressions failed.

===== Alternatives and best practice =====
&lt;div center round todo 60%&gt;TODO: Show some alternate usages involving functions and local variables for initialization.&lt;/div&gt;
===== Examples =====

==== Simple counter ====

A simple counter, the loop iterates 101 times (&quot;0&quot; to &quot;100&quot; are 101 numbers -&gt; 101 runs!), and everytime the variable ''x'' is set to the current value.

  * It **initializes** ''x = 0''
  * Before every iteration it **checks** if ''x ≤ 100''
  * After every iteration it **changes** ''x++''

&lt;code&gt;
for ((x = 0 ; x &lt;= 100 ; x++)); do
  echo &quot;Counter: $x&quot;
done
&lt;/code&gt;

==== Stepping counter ====

This is the very same counter (compare it to the simple counter example above), but the **change** that is made is a ''x += 10''. That means, it will count from 0 to 100, but with a **step of 10**.
&lt;code&gt;
for ((x = 0 ; x &lt;= 100 ; x += 10)); do
  echo &quot;Counter: $x&quot;
done
&lt;/code&gt;

==== Bits analyzer ====

This example loops through the bit-values of a Byte, beginning from 128, ending at 1. If that bit is set in the ''testbyte'', it prints &quot;''1''&quot;, else &quot;''0''&quot; =&gt; it prints the binary representation of the ''testbyte'' value (8 bits).
&lt;code&gt;
#!/usr/bin/env bash
# Example written for http://wiki.bash-hackers.org/syntax/ccmd/c_for#bits_analyzer
# Based on TheBonsai's original.

function toBin {
	typeset m=$1 n=2 x='x[(n*=2)&gt;m]'
	for ((x = x; n /= 2;)); do
		printf %d $(( m &amp; n &amp;&amp; 1))
	done
}

function main {
	[[ $1 == +([0-9]) ]] || return
	typeset result
	if (( $(ksh -c 'printf %..2d $1' _ &quot;$1&quot;) == ( result = $(toBin &quot;$1&quot;) ) )); then
		printf '%s is %s in base 2!\n' &quot;$1&quot; &quot;$result&quot;
	else
		echo 'Oops, something went wrong with our calculation.' &gt;&amp;2
		exit 1
	fi
}

main &quot;${1:-123}&quot;

# vim: set fenc=utf-8 ff=unix ft=sh :
&lt;/code&gt;

&lt;div hide&gt; 
&lt;code&gt;
testbyte=123
for (( n = 128 ; n &gt;= 1 ; n /= 2 )); do
  if (( testbyte &amp; n )); then
    printf %d 1
  else
    printf %s 0
  fi
done
echo
&lt;/code&gt;
&lt;/div&gt;

Why that one begins at 128 (highest value, on the left) and not 1 (lowest value, on the right)? It's easier to print from left to right...

We arrive at 128 for ''n'' through the recursive arithmetic expression stored in ''x'', which calculates the next-greatest power of 2 after ''m''. To show that it works, we use ksh93 to double-check the answer, because it has a built-in feature for ''printf'' to print a representation of any number in an arbitrary base (up to 64). Very few languages have that ability built-in, even things like Python.

==== Up, down, up, down... ====

This counts up and down from ''0'' to ''${1:-5}'', ''${2:-4}'' times, demonstrating more complicated arithmetic expressions with multiple variables.

&lt;code&gt;
for (( incr = 1, n=0, times = ${2:-4}, step = ${1:-5}; (n += incr) % step || (incr *= -1, --times);)); do
    printf '%*s\n' &quot;$((n+1))&quot; &quot;$n&quot;
done
&lt;/code&gt;
&lt;code&gt; ~ $ bash &lt;(xclip -o)
 1
  2
   3
    4
     5
    4
   3
  2
 1
0
 1
  2
   3
    4
     5
    4
   3
  2
 1
&lt;/code&gt;

===== Portability considerations =====

  * C-style for loops aren't POSIX. They are available in Bash, ksh93, and zsh. All 3 have essentially the same syntax and behavior.
  * C-style for loops aren't available in mksh.

===== Bugs =====

  * //Fixed in 4.3//. &lt;del&gt;There appears to be a bug as of Bash 4.2p10 in which command lists can't be distinguished from the for loop's arithmetic argument delimiter (both semicolons), so command substitutions within the C-style for loop expression can't contain more than one command.&lt;/del&gt;

===== See also =====

  * Internal: [[syntax:arith_expr | Arithmetic expressions]]
  * Internal: [[syntax:ccmd:classic_for | The classic for-loop]]
  * Internal: [[syntax:ccmd:while_loop | The while-loop]]
