====== Grouping commands in a subshell ======

===== Synopsis =====
&lt;code&gt;
( &lt;LIST&gt; )
&lt;/code&gt;


===== Description =====
The [[syntax:basicgrammar#lists|list]] ''&lt;LIST&gt;'' is executed in a separate shell - a subprocess. No changes to the environment (variables etc...) are reflected in the &quot;main shell&quot;.

===== Examples =====

Execute a command in a different directory.
&lt;code bash&gt;
echo &quot;$PWD&quot;
( cd /usr; echo &quot;$PWD&quot; )
echo &quot;$PWD&quot; # Still in the original directory.
&lt;/code&gt;

===== Portability considerations =====

  * The subshell compound command is specified by POSIX.
  * Avoid ambiguous syntax.
&lt;code bash&gt;
(((1+1))) # Equivalent to: (( (1+1) ))
&lt;/code&gt;

===== See also =====
  * [[syntax:ccmd:grouping_plain | grouping commands]]
  * [[http://mywiki.wooledge.org/SubShell | Subshells on Greycat's wiki]]
