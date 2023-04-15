====== The export builtin command ======

===== Synopsis =====

<code>
export [-fn] [NAME[=VALUE] ...]
export -p
</code>

===== Description =====

The ''export'' builtin command is used to mark variables or functions referenced by ''NAME'' for automatic export to the environment. If ''NAME'' is a shell variable, a value ''VALUE'' can be assigned before exporting it.

==== Options ====

^Option  ^Description  ^
|''-f''  |refer to shell functions  |
|''-n''  |remove the export property from any referenced ''NAME''  |
|''-p''  |print all exported variables, with ''-f'', print all exported functions - all in a format re-usable as input |

An argument of ''%%--%%'' disables further option processing.
==== Return status ====

^Status  ^Reason  ^
|0    |no error  |
|!=0  |invalid option  |
|!=0  |a given ''NAME'' is invalid  |

===== Examples =====

Set the display to use when launching a GUI application (useful during SSH sessions):
<code>
export DISPLAY=&quot;:0&quot;
</code>

Set your default text editor (e.g. SublimeText):
<code>
export EDITOR=subl
</code> 
===== Portability considerations =====

  * in POSIX(r), only the ''-p'' option is specified
  * in POSIX(r), only variables (with value assignment) are to be exported, not shell functions

===== See also =====

  * [[commands:builtin:declare]]
