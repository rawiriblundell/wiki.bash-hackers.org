====== The trap builtin command ======

===== Synopsis =====
<code>trap [-lp] [[ARGUMENT] SIGNAL]</code>

===== Description =====
The ''trap'' command is used to &quot;trap&quot; signals and other events. In this context, &quot;trapping&quot; means to install handler code.

The shell code ''ARGUMENT'' is to be read and executed whenever the shell receives a signal or another event ''SIGNAL''. The given ''SIGNAL'' specification can be
  * the name of a signal with the SIG prefix, e.g. ''SIGTERM''
  * the name of a signal without the SIG prefix, e.g. ''TERM''
  * the number of a signal (see ''trap -l''), e.g. ''15''
  * the name or number of a special event (see table below), e.g. ''EXIT''

Without any options or operands, ''trap'' prints a list of installed traps in a reusable format (equivalent to the ''-p'' option).

Special ''ARGUMENT''s
  * if ''ARGUMENT'' is absent or ''-'' (dash), the signal/event handler is reset to its original value
  * if ''ARGUMENT'' is the null string, the signal/event is ignored

Special events
^Name       ^Code  ^Description  ^
|''EXIT''    |0     |executed on shell exit  |
|''DEBUG''   |      |executed before every simple command  |
|''RETURN''  |      |executed when a shell function or a sourced code finishes executing  |
|''ERR''     |      |executed each time a command's failure would cause the shell to exit when the [[commands:builtin:set|''-e'' option (''errexit'')]] is enabled  |

==== Options ====

^Option  ^Description  ^
|''-l''  |print a list of signal names and their corresponding numbers  |
|''-p''  |display the trap commands associated with each signal specification in a reusable format  |

==== Return status ====

^Status  ^Reason  ^
|0       |no error/success    |
|!=0     |invalid option  |
|!=0     |invalid signal specification  |

===== Examples =====

==== List installed traps ====

<code>
trap
</code>

==== Ignore terminal interrupt (Ctrl-C, SIGINT) ====

<code>
trap '' INT
</code>

===== Portability considerations =====

  * ''trap'' is specified by POSIX(R) without the ''-l'' and ''-p'' options
  * in POSIX(R), beside signals, only ''EXIT'' (0) is valid as an event

===== See also =====

  * [[commands:builtin:set|the set command]] for the ''-e'' (''errexit'') option
