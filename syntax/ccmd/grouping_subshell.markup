====== Grouping commands in a subshell ======

===== Synopsis =====
<code>
( <LIST> )
</code>


===== Description =====
The [[syntax:basicgrammar#lists|list]] ''<LIST>'' is executed in a separate shell - a subprocess. No changes to the environment (variables etc...) are reflected in the &quot;main shell&quot;.

===== Examples =====

Execute a command in a different directory.
<code bash>
echo &quot;$PWD&quot;
( cd /usr; echo &quot;$PWD&quot; )
echo &quot;$PWD&quot; # Still in the original directory.
</code>

===== Portability considerations =====

  * The subshell compound command is specified by POSIX.
  * Avoid ambiguous syntax.
<code bash>
(((1+1))) # Equivalent to: (( (1+1) ))
</code>

===== See also =====
  * [[syntax:ccmd:grouping_plain | grouping commands]]
  * [[http://mywiki.wooledge.org/SubShell | Subshells on Greycat's wiki]]
