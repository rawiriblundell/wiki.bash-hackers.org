====== The shopt builtin command ======

The ''shopt'' builtin manages [[internals:shell_options | shell options]], a set of boolean (''on''/''off'') configuration variables that control the behaviour of the shell.

===== Synopsis =====

<code>
shopt [-pqsu] [-o] <OPTNAME...>
</code>

===== Description =====

Note: Some of these options and other shell options can also be set with [[commands:builtin:set | the set builtin]].

==== Options ====

^Option^Description^
|''-o''|Restrict the values of ''<OPTNAME...>'' to only those also known by [[commands:builtin:set | the set builtin]]|
|''-p''|Print all shell options and their current value. **Default**.|
|''-q''|Quiet mode. Set exit code if named option is set. For multiple options: ''TRUE'' if all options are set, ''FALSE'' otherwise|
|''-s''|Enable (__s__et) the shell options named by ''<OPTNAME...>'' or list all //enabled// options if no names are given|
|''-u''|Disabe (__u__nset) the shell options named by ''<OPTNAME...>'' or list all //disabled// options if no names are given|

As noted above, if only ''-s'' or ''-u'' are given without any option names, only the currently enabled (''-s'') or disabled (''-u'') options are printed.

==== Exit code ====

When listing options, the exit code is ''TRUE'' (0), if all options are enabled, ''FALSE'' otherwise.

When setting/unsetting an option, the exit code is ''TRUE'' unless the named option doesn't exitst.

===== Examples ======

Enable the ''nullglob'' option:
<code>
shopt -s nullglob
</code>

===== Portability considerations ======

The ''shopt'' command is not portable accross different shells.

===== See also ======

  * Internal: [[commands:builtin:set | The set builtin command]]
  * Internal: [[internals:shell_options | List of shell options]]
