====== Word splitting ======

FIXME to be continued!

Word splitting occurs once any of the following expansions are done (and only then!)
  * [[syntax:pe | Parameter expansion]]
  * [[syntax:expansion:cmdsubst | Command substitution]]
  * [[syntax:expansion:arith | Arithmetic expansion]]
Bash will scan the results of these expansions for special ''IFS'' characters that mark word boundaries. This is only done on results that are **not double-quoted**!

===== Internal Field Separator IFS =====

The ''IFS'' variable holds the characters that Bash sees as word boundaries in this step. The default contains the characters
  * <space>
  * <tab>
  * <newline>
These characters are also assumed when IFS is **unset**. When ''IFS'' is **empty** (nullstring), no word splitting is performed at all.




===== Behaviour =====

The results of the expansions mentioned above are scanned for ''IFS''-characters. If **one or more** (in a sequence) of them is found, the expansion result is split at these positions into multiple words.

This doesn't happen when the expansion results were **double-quoted**.

When a null-string (e.g., something that before expanded to >>nothing<<) is found, it is removed, unless it is quoted (''<nowiki>''</nowiki>'' or ''&quot;&quot;'').

__**Again note:**__ Without any expansion beforehand, Bash won't perform word splitting! In this case, the initial token parsing is solely responsible.


===== See also =====
  * [[syntax:expansion:intro | Introduction to expansion and substitution]]
  * [[syntax:quoting | Quoting and escaping]]
