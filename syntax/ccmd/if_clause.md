====== The if-clause ======

===== Synopsis =====
&lt;code&gt;
if &lt;LIST&gt;; then
  &lt;LIST&gt;
fi
&lt;/code&gt;
&lt;code&gt;
if &lt;LIST&gt;; then
  &lt;LIST&gt;
else
  &lt;LIST&gt;
fi
&lt;/code&gt;
&lt;code&gt;
if &lt;LIST&gt;; then
  &lt;LIST&gt;
elif &lt;LIST&gt;; then
  &lt;LIST&gt;
else
  &lt;LIST&gt;
fi
&lt;/code&gt;

===== Description =====
The ''if''-clause can control the script's flow (what's executed) by looking at the exit codes of other commands.

All commandsets ''&lt;LIST&gt;'' are interpreted as [[syntax:basicgrammar#lists | command lists]], thus they can contain the whole palette from [[syntax:basicgrammar#simple_commands | simple commands]] over [[syntax:basicgrammar#pipelines | pipelines]] to [[syntax:basicgrammar#compound_commands | compound commands]] (and their combination) as condition.

==== Operation ====
The **''if &lt;LIST&gt;''** commands are executed. If the exit code was 0 (TRUE) then the **''then &lt;LIST&gt;''** commands are executed, otherwise the **''elif &lt;LIST&gt;''** commands and their **''then &lt;LIST&gt;''** statements are executed in turn, if all down to the last one fails, the **''else &lt;LIST&gt;''** commands are executed, if one of the ''elif'' succeeds, its ''then'' thread is executed, and the ''if''-clause finishes.

Basically, the ''elif'' clauses are just additional conditions to test (like a chain of conditions) if the very first condition failed. If one of the conditions fails, the ''else'' commands are executed, otherwise the commands of the condition that succeeded.


===== Examples =====
**Check if a specific user exists in /etc/passwd :-)**
&lt;code&gt;
if grep ^myuser: /etc/passwd &gt;/dev/null 2&gt;&amp;1; then
  echo &quot;Yes, it seems I'm real&quot;
else
  echo &quot;Uh - am I a ghost?&quot;
fi
&lt;/code&gt;

**Mount with check**
&lt;code&gt;
if ! mount /mnt/backup &gt;/dev/null 2&gt;&amp;1; then
  echo &quot;FATAL: backup mount failed&quot; &gt;&amp;2
  exit 1
fi
&lt;/code&gt;

**Multiple commands as condition**

It's perfectly valid to do:
&lt;code&gt;
if echo &quot;I'm testing!&quot;; [ -e /some/file ]; then
  ...
fi
&lt;/code&gt;
The exit code that dictates the condition's value is the exit code of the very last command executed in the condition-list (here: The ''[ -e /some/file ]'')

**A complete pipe as condition**

A complete pipe can also be used as condition. It's very similar to the example above (multiple commands):
&lt;code&gt;
if echo &quot;Hello world!&quot; | grep -i hello &gt;/dev/null 2&gt;&amp;1; then
  echo &quot;You just said 'hello', yeah?&quot;
fi
&lt;/code&gt;

===== Portability considerations =====


===== See also =====
  * Internal: [[commands:classictest | the classic test command]]
