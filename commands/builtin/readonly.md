====== The readonly builtin command ======

===== Synopsis =====

&lt;code&gt;
readonly [-p] [-a] [-A] [-f] [NAME[=VALUE] ...]
&lt;/code&gt;

===== Description =====

The ''readonly'' builtin command is used to mark variables or functions as read-only, which means unchangeable. This implies that it can't be unset anymore.  A ''readonly'' variable may not be redefined in child scopes.  A readonly global may not be redefined as a function local variable.  Simple command environment assignments may not reference readonly variables. 

==== Options ====

^Option  ^Description  ^
|''-a''  |refer to normal arrays  |
|''-A''  |refer to associative arrays  |
|''-f''  |refer to functions  |
|''-p''  |print all read-only variables or functions, ''-a'', ''-A'' and ''-f'' can be used to filter. The output is reusable as input |

An argument of ''%%--%%'' disables further option processing.
==== Return status ====

^Status  ^Reason  ^
|0    |no error  |
|!=0  |invalid option  |
|!=0  |invalid combination of options  |
|!=0  |a given ''NAME'' is invalid  |

===== Examples =====

===== Portability considerations =====

  * in POSIX(r), only the ''-p'' option is specified

===== See also =====

  * [[commands:builtin:declare]]
