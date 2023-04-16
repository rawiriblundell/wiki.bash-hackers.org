====== The if-clause ======

===== Synopsis =====
<code>
if <LIST>; then
  <LIST>
fi
</code>
<code>
if <LIST>; then
  <LIST>
else
  <LIST>
fi
</code>
<code>
if <LIST>; then
  <LIST>
elif <LIST>; then
  <LIST>
else
  <LIST>
fi
</code>

===== Description =====
The ''if''-clause can control the script's flow (what's executed) by looking at the exit codes of other commands.

All commandsets ''<LIST>'' are interpreted as [[syntax:basicgrammar#lists | command lists]], thus they can contain the whole palette from [[syntax:basicgrammar#simple_commands | simple commands]] over [[syntax:basicgrammar#pipelines | pipelines]] to [[syntax:basicgrammar#compound_commands | compound commands]] (and their combination) as condition.

==== Operation ====
The **''if <LIST>''** commands are executed. If the exit code was 0 (TRUE) then the **''then <LIST>''** commands are executed, otherwise the **''elif <LIST>''** commands and their **''then <LIST>''** statements are executed in turn, if all down to the last one fails, the **''else <LIST>''** commands are executed, if one of the ''elif'' succeeds, its ''then'' thread is executed, and the ''if''-clause finishes.

Basically, the ''elif'' clauses are just additional conditions to test (like a chain of conditions) if the very first condition failed. If one of the conditions fails, the ''else'' commands are executed, otherwise the commands of the condition that succeeded.


===== Examples =====
**Check if a specific user exists in /etc/passwd :-)**
<code>
if grep ^myuser: /etc/passwd >/dev/null 2>&1; then
  echo &quot;Yes, it seems I'm real&quot;
else
  echo &quot;Uh - am I a ghost?&quot;
fi
</code>

**Mount with check**
<code>
if ! mount /mnt/backup >/dev/null 2>&1; then
  echo &quot;FATAL: backup mount failed&quot; >&2
  exit 1
fi
</code>

**Multiple commands as condition**

It's perfectly valid to do:
<code>
if echo &quot;I'm testing!&quot;; [ -e /some/file ]; then
  ...
fi
</code>
The exit code that dictates the condition's value is the exit code of the very last command executed in the condition-list (here: The ''[ -e /some/file ]'')

**A complete pipe as condition**

A complete pipe can also be used as condition. It's very similar to the example above (multiple commands):
<code>
if echo &quot;Hello world!&quot; | grep -i hello >/dev/null 2>&1; then
  echo &quot;You just said 'hello', yeah?&quot;
fi
</code>

===== Portability considerations =====


===== See also =====
  * Internal: [[commands:classictest | the classic test command]]
