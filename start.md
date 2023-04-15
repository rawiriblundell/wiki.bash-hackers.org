[[bash4|]]~~NOTOC~~
~~DISCUSSION:off~~

====== The Bash Hackers Wiki ======

{{keywords&gt;bash shell linux scripting}}

&lt;WRAP center round box 90%&gt;
This wiki is intended to hold documentation of any kind about GNU Bash. The main motivation was to provide //human-readable documentation// and information so users aren't forced to read every bit of the Bash manpage - which can be difficult to understand. However, the docs here are **not** meant as a newbie tutorial.

This wiki and any programs found in this wiki are free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This wiki and its programs are distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.  If not, see &lt;http://www.gnu.org/licenses/&gt;.

[[wishes | What would YOU like to see here?]] (outdated and locked, please use the discussions)

**Stranger!** [[http://wiki.bash-hackers.org/start&amp;do=register | Feel free to register]] and comment or edit the contents. There is a [[meta:need_love|Bash Hackers Wiki needs love page]] that lists some things to do. The registration is only there to prevent SPAM.
&lt;/WRAP&gt;

===== Scripting and general information =====
  * [[bash4 | Bash v4 - a rough overview]] (unmaintained, since Bash 4 is more or less standard)
  * [[scripting:style]] -- an assorted collection of style and optic hints
  * [[scripting:basics]]
  * [[scripting:newbie_traps]]
  * [[scripting:bashbehaviour]]
  * [[scripting:posparams]]
  * [[scripting:processtree]]
  * [[scripting:obsolete]]
  * [[scripting:nonportable]]
  * [[scripting:debuggingtips]]
  * [[scripting:terminalcodes]]
  * [[scripting:tutoriallist]]

===== Code snippets ====

There is a [[snipplets:start|section that holds small code snippets]].

See also [[misc:readthesourceluke|some Bash source code excerpts]].



===== How to.... =====
[[howto:start | Doing specific tasks: concepts, methods, ideas]]:

  * [[howto:mutex | Simple locking (against parallel run)]]
  * [[howto:conffile | Rudimentary config files for your scripts]]
  * [[howto:edit-ed | Editing files with ed(1)]]
  * [[howto:collapsing_functions | Collapsing Functions]]
  * [[howto:redirection_tutorial | Illustrated Redirection Tutorial]] 
  * [[howto:calculate-dc | Calculate with dc(1)]]
  * [[howto:pax | Introduction to pax - the POSIX archiver]]
  * [[howto:getopts_tutorial]] (//under construction!//)
  * [[howto:dissectabadoneliner]] An example of a bad oneliner, breakdown and fix (by ''kojoro'')
  * [[howto:testing-your-scripts | Write tests for ./your-script.sh]] by using bashtest util 


===== Bash syntax and operations =====

  * [[scripting:bashchanges | Bash features overview by version]]
  * [[syntax:basicgrammar | Basic grammar rules ]]
  * [[syntax:quoting | Quoting and character escaping]]
  * [[syntax:grammar:parser_exec | Parsing and execution]]
  * [[syntax:words | Some words about words...]]
  * [[syntax:pattern | Patterns and pattern matching]]
  * [[syntax:arith_expr | Arithmetic expressions]]
  * [[internals:shell_options | List of shell options]]
  * [[syntax:redirection | Redirection]]
  * [[syntax:shellvars | Special parameters and shell variables]]
  * [[syntax:arrays | Arrays]]

&lt;WRAP column 40%&gt;
//**__Compound commands__**//

^**[[syntax:ccmd:intro | Compound commands overview]]**  ^^
^Grouping  ^^
|''{ ...; }''|[[syntax:ccmd:grouping_plain | command grouping]]|
|''( ... )''|[[syntax:ccmd:grouping_subshell | command grouping in a subshell]]|
^Conditionals  ^^
|''&lt;nowiki&gt;[[ ... ]]&lt;/nowiki&gt;''|[[syntax:ccmd:conditional_expression | conditional expression]]| 
|''if ...; then ...; fi''|[[syntax:ccmd:if_clause | conditional branching]]|
|''case ... esac''|[[syntax:ccmd:case | pattern-based branching]]|
^Loops  ^^
|''for word in ...; do ...; done''|[[syntax:ccmd:classic_for | classic for-loop]]|
|''&lt;nowiki&gt;for ((x=1; x&lt;=10; x++)); do ...; done&lt;/nowiki&gt;''|[[syntax:ccmd:c_for | C-style for-loop]]|
|''while ...; do ...; done''|[[syntax:ccmd:while_loop | while loop]]|
|''until ...; do ...; done''|[[syntax:ccmd:until_loop | until loop]]|
^Misc  ^^
|''&lt;nowiki&gt;(( ... ))&lt;/nowiki&gt;''|[[syntax:ccmd:arithmetic_eval | arithmetic evaluation]]|
|''select word in ...; do ...; done''|[[syntax:ccmd:user_select | user selections]]|
&lt;/WRAP&gt;

&lt;WRAP column 40%&gt;
//**__Expansions and substitutions__**//

^**[[syntax:expansion:intro | Introduction to expansions and substitutions]]**  ^^
|''{A,B,C}    {A..C}''|[[syntax:expansion:brace | Brace expansion]]|
|''~/    ~root/''|[[syntax:expansion:tilde | Tilde expansion]]|
|''$FOO    ${BAR%.mp3}''|[[syntax:pe | Parameter expansion]]|
|''`command`    $(command)''|[[syntax:expansion:cmdsubst | Command substitution]]|
|''&lt;(command)    &gt;(command)''|[[syntax:expansion:proc_subst | Process substitution]]|
|''&lt;nowiki&gt;$((1 + 2 + 3))    $[4 + 5 + 6]&lt;/nowiki&gt;''|[[syntax:expansion:arith | Arithmetic expansion]]|
|''Hello    &lt;nowiki&gt;&lt;---&gt;&lt;/nowiki&gt;    Word!''|[[syntax:expansion:wordsplit | Word splitting]]|
|''/data/*-av/*.mp?''|[[syntax:expansion:globs | Pathname expansion]]|
&lt;/WRAP&gt;
&lt;WRAP clear&gt;&lt;/WRAP&gt;

===== Builtin Commands =====

This is a selection of builtin commands and command-like keywords, loosely arranged by their common uses. These are provided directly by the shell, rather than invoked as standalone external commands.

&lt;WRAP column 46%&gt;
^  Declaration commands\\ &lt;wrap center round lo todo box 80%&gt;Commands that set and query attributes/types, and manipulate simple datastructures.&lt;/wrap&gt;  ^^  Alt  ^  Type  ^
|[[commands:builtin:declare | declare]]|Display or set shell variables or functions along with attributes.|  ''typeset''  |  builtin  |
|[[commands:builtin:export | export]]|Display or set shell variables, also giving them the export attribute.|  ''typeset -x''  |  special builtin  |
|[[commands:builtin:eval | eval]]|Evaluate arguments as shell code.|  -  |  special builtin  |
|[[commands:builtin:local | local]]|Declare variables as having function local scope.|  -  |  builtin  |
|[[commands:builtin:readonly | readonly]]|Mark variables or functions as read-only.|  ''typeset -r''  |  special builtin  |
|[[commands:builtin:unset | unset]]|Unset variables and functions.|  -  |  special builtin  |
|[[commands:builtin:shift | shift]]|Shift positional parameters|  -  |  special builtin  |
^  I/O\\ &lt;wrap center round lo todo box 80%&gt;Commands for reading/parsing input, or producing/formatting output of standard streams.&lt;/wrap&gt;  ^^  Alt  ^  Type  ^
|[[syntax:keywords:coproc | coproc]]|Co-processes: Run a command in the background with pipes for reading / writing its standard streams.|  -  |  keyword  |
|[[commands:builtin:echo | echo]]|Create output from arguments.|  -  |  builtin  |
|[[commands:builtin:mapfile | mapfile]]|Read lines of input into an array.|  ''readarray''  |  builtin  |
|[[commands:builtin:printf | printf]]|&quot;advanced ''echo''.&quot;|  -  |  builtin  |
|[[commands:builtin:read | read]]|Read input into variables or arrays, or split strings into fields using delimiters.|  -  |  builtin  |
^  Configuration and Debugging\\ &lt;wrap center round lo todo box 80%&gt;Commands that modify shell behavior, change special options, assist in debugging.&lt;/wrap&gt;  ^^  Alt  ^  Type  ^
|[[commands:builtin:caller | caller]]|Identify/print execution frames.|  -  |  builtin  |
|[[commands:builtin:set | set]]|Set the positional parameters and/or set options that affect shell behaviour.|  -  |  special builtin  |
|[[commands:builtin:shopt | shopt]]|set/get some bash-specific shell options.|  -  |  builtin  |
&lt;/WRAP&gt;

&lt;WRAP column 46%&gt;
^  Control flow and data processing\\ &lt;wrap center round lo todo box 80%&gt;Commands that operate on data and/or affect control flow.&lt;/wrap&gt;                                                           |^  Alt     ^  Type             ^
| [[commands:builtin:true| colon]]                                                                                                         | &quot;true&quot; null command.                                    |  true    |  special builtin  |
| [[commands:builtin:source| dot]]                                                                                                         | Source external files.                                  |  source  |  special builtin  |
| [[commands:builtin:false| false]]                                                                                                        | Fail at doing nothing.                                  |  -       |  builtin          |
| [[commands:builtin:continueBreak| continue / break]]                                                                                     | continue with or break out of loops.                    |  -       |  special builtin  |
| [[commands:builtin:let| let]]                                                                                                            | Arithmetic evaluation simple command.                   |  -       |  builtin          |
| [[commands:builtin:return| return]]                                                                                                      | Return from a function with a specified exit status.    |  -       |  special builtin  |
| [[commands:classictest| []]                                                                                                              | The classic ''test'' simple command.                    |  test    |  builtin          |
^  Process and Job control\\ &lt;wrap center round lo todo box 80%&gt;Commands related to jobs, signals, process groups, subshells.&lt;/wrap&gt;                                                                |^  Alt     ^  Type             ^
| [[commands:builtin:exec| exec]]                                                                                                          | Replace the current shell process or set redirections.  |  -       |  special builtin  |
| [[commands:builtin:exit| exit]]                                                                                                          | Exit the shell.                                         |  -       |  special builtin  |
| [[commands:builtin:kill| kill]]                                                                                                          | Send a signal to specified process(es)                  |  -       | builtin           |
| [[commands:builtin:trap| trap]]                                                                                                          | Set signal handlers or output the current handlers.     |  -       |  special builtin  |
| [[commands:builtin:times| times]]                                                                                                        | Display process times.                                  |  -       |  special builtin  |
| [[commands:builtin:wait| wait]]                                                                                                          | Wait for background jobs and asynchronous lists.        |  -       |  builtin          |
^                                                                                                                                                                                                   |^  Alt     ^  Type             ^
&lt;/WRAP&gt;
&lt;WRAP clear&gt;&lt;/WRAP&gt;
&lt;WRAP hide&gt; previous alphabetical version
&lt;WRAP column 40%&gt;
^A-G^^
|[[commands:builtin:caller | caller]]|identify/print execution frames (Bash builtin)  |
|[[syntax:keywords:coproc | coproc]]|Co-processes (Bash keyword) |
|[[commands:builtin:declare | declare]]|display or set shell variables or functions along with attributes (Bash builtin, synonym: ''typeset'')  |
|[[commands:builtin:exec | exec]]  | replace the shell, set redirections (Bash builtin) |
^I-N^^
|[[commands:builtin:let | let]]|arithmetic evaluation - an old fashioned way (Bash builtin)  |
|[[commands:builtin:mapfile | mapfile]]|Mapping lines of input to an array, also named ''readarray'' (Bash builtin)  |
&lt;/WRAP&gt;

&lt;WRAP column 40%&gt;
^O-T^^
|[[commands:builtin:printf | printf]]|&quot;advanced ''echo''&quot; (Bash builtin)  |
|[[commands:builtin:read | read]]|getting input from ''stdin'' (Bash builtin)  |
|[[commands:builtin:readonly | readonly]]|mark variables or functions as read-only (Bash builtin)  |
|[[commands:builtin:set | set]]|control positional parameters and shell behaviour (Bash builtin)  |
|[[commands:builtin:shopt | shopt]]|set/get shell options (Bash builtin)  |
|[[commands:classictest | test]]|the classic ''test'' command (Bash builtin)  |
^U-Z^^
|[[commands:builtin:unset | unset]]|unset variables and functions (Bash builtin)  |
&lt;/WRAP&gt;
&lt;WRAP clear&gt;&lt;/WRAP&gt;
&lt;/WRAP&gt;
===== Dictionary ======

&lt;note tip&gt;A list of expressions, words, and their meanings is [[dict:index | here]].&lt;/note&gt;

===== Links =====

==== Official Bash links ====

  * [[http://tiswww.case.edu/php/chet/bash/bashtop.html|Chet Ramey's Bash page]] and its [[http://tiswww.case.edu/php/chet/bash/FAQ|FAQ]].
  * [[http://www.gnu.org/software/bash/ | GNU Bash software page]]
  * Official Bash mailing lists:
    * **Bug reports**: &lt;bug-bash@gnu.org&gt; ([[http://mail.gnu.org/pipermail/bug-bash|archives]])
    * **General questions**: &lt;help-bash@gnu.org&gt; ([[http://mail.gnu.org/pipermail/help-bash|archives]])
  * Official Bash git repository:
    * **Browse**: [[http://git.savannah.gnu.org/cgit/bash.git | cgit]]
    * **Clone**: //git:// &lt;nowiki&gt;ssh://git.sv.gnu.org/srv/git/bash.git&lt;/nowiki&gt; • //ssh:// &lt;nowiki&gt;ssh://git.sv.gnu.org/srv/git/bash.git&lt;/nowiki&gt; • //http:// &lt;nowiki&gt;http://git.savannah.gnu.org/r/bash.git&lt;/nowiki&gt;

==== Recommended Shell resources ====

  * [[http://mywiki.wooledge.org/ | Greg's wiki]] - Greg Wooledge's (aka &quot;greycat&quot;) wiki -- with **MASSIVE** information about Bash and UNIX(r) in general.
    * [[http://mywiki.wooledge.org/BashFAQ | BashFAQ]] • [[http://mywiki.wooledge.org/BashGuide | BashGuide]] • [[http://mywiki.wooledge.org/BashPitfalls | BashPitfalls]] • [[http://mywiki.wooledge.org/BashSheet | BashSheet]]
  * [[http://www.in-ulm.de/~mascheck/ | Sven Mascheck's pages]] - A goldmine of information. A must-read.
  * [[https://www.mirbsd.org/ksh-chan.htm | #ksh channel page]] - #ksh Freenode channel page maintains a solid collection of recommended links.
  * [[http://www.grymoire.com/Unix/ | The Grymoire Unix pages]] - Good scripting information, especially read the [[http://www.grymoire.com/Unix/Quote.html | quoting]] guide.
  * [[http://www.shelldorado.com | Heiner's &quot;Shell Dorado&quot;]] - Tips, tricks, links - for every situation.
  * [[http://pubs.opengroup.org/onlinepubs/9699919799/ | The Single Unix Specification (version 4, aka POSIX-2008)]]
  * [[http://www.opengroup.org/austin/ | The Austin Group]] - [[ http://dir.gmane.org/gmane.comp.standards.posix.austin.general | List archives ]], [[ http://austingroupbugs.net/main_page.php | Bug tracker ]]
  * [[http://cfajohnson.com/shell/cus-faq.html | comp.unix.shell FAQ]]
  * [[https://tldp.org/LDP/abs/html/index.html | Advanced Bash-Scripting Guide]] - last update: 10 Mar 2014, but still very useful guide.


=== Documentation / Reference ===

  * **Bash**: [[http://tiswww.case.edu/php/chet/bash/bash.html | man page]] [[http://tiswww.case.edu/php/chet/bash/bashref.html | info page]]
  * **AT&amp;T ksh**: [[http://www2.research.att.com/sw/download/man/man1/ksh88.html | ksh88]] [[http://www2.research.att.com/sw/download/man/man1/ksh.html | ksh93]]
  * [[https://www.mirbsd.org/htman/i386/man1/mksh.htm | mksh]] (pdksh successor) 
  * [[http://zsh.sourceforge.net/Doc/ | zsh]]
  * [[http://man7.org/linux/man-pages/man1/dash.1.html | dash]]
  * [[http://heirloom.sourceforge.net/man/sh.1.html | Heirloom Bourne shell]]
  * [[http://v6shell.org/man/osh.1.html | Thompson shell]]

==== Assorted interesting links ====

  * [[http://www.in-ulm.de/~mascheck/bourne/ | History and development of the traditional Bourne shell family]] - very interesting and nice to read!
  * [[http://www.computerworld.com.au/article/222764/-z_programming_languages_bash_bourne-again_shell | Interview with Chet Ramey]]
  * [[http://www.computerworld.com.au/article/279011/a-z_programming_languages_sh | Interview with Steve Bourne]] • [[https://www.youtube.com/watch?v=2kEJoWfobpA | Stephen Bourne - BSDCan 2015 keynote]]
  * [[http://news.slashdot.org/story/01/02/06/2030205/david-korn-tells-all | Interview with David Korn]]
  * [[https://www.youtube.com/watch?v=bKzonnwoR2I | Kernighan on the Unix pipeline (computerphile video)]]
  * Linux in general, with some shell related stuff: [[http://www.cyberciti.biz/ | nixCraft: Linux Tips, Hacks, Tutorials and Ideas]]
  * Linux tutorials, guides and how-tos: [[https://www.rosehosting.com/blog/ | RoseHosting Blog]], [[https://www.rosehosting.com/blog/script-install-wordpress-on-a-debianubuntu-vps/ | bash script for installing WordPress]] and some [[https://www.rosehosting.com/blog/basic-shell-commands-after-putty-ssh-logon/ | basic shell commands]]
  * [[misc:bashphorisms | Bashphorism list from the Bash IRC channel on Freenode]]
  * [[misc:shell_humor | Some more or less funny commandline stuff]]
  * [[https://thishosting.rocks/how-to-enable-ssh-on-ubuntu/ | How to Enable SSH on Ubuntu Tutorial]]
  * [[https://linuxstans.com/how-to-make-an-awesome-custom-shell-with-zsh/ | How To Make an Awesome Custom Shell with ZSH]]
==== Bash Libraries (needs review) ====

  * [[http://sourceforge.net/projects/oobash/ | An oo-style bash library for bash 4]] - provides tools for rapid script development and huge libraries.
  * [[https://github.com/hornos/shf3 | General purpose shell framework for bash 4]] - in development.
  * [[https://github.com/chilicuil/learn/blob/master/sh/lib| General purpose bash library for bash 4]] - active development

&lt;div hide&gt;
===== Most wanted =====

^Statistics for Month: **April 2012**^^^
|Rank  |  Views  |  Article  |
^1   |  6833|[[howto:getopts_tutorial]] (March: 11663) |
^2   |  4025|[[commands:builtin:printf]] (March: 7079)  |
^3   |  2861|[[syntax:quoting]] (March: 5730)  |
^4   |  2854|[[commands:classictest]] (March: 6145)  |
^5   |  2222|[[syntax:pe]] (March: 4540)  |
^6   |  1760|[[scripting:posparams]] (March: 3591)  |
^7   |  1249|[[bash4]] (March: --)  |
^8   |  1212|[[howto:edit-ed]] (March: 2727)  |
^9   |  1205|[[commands:builtin:read]] (March: --)  |
^10  |  1182|[[howto:mutex]] (March: --)  |

  * &quot;Total visits&quot;: 35144
  * The overall &quot;start&quot; page is not counted
&lt;/div&gt;
===== Contact =====

&lt;WRAP column 40%&gt;
Visit us in ''irc.freenode.net'', channel ''#bash'' ;-)

If you have critiques or suggestions, please feel free to send a mail using the contact form on the right. Note that there is a simple discussion option below every article.

Please also see the [[user:thebonsai:imprint | imprint]] if you have problems with the site and its contents (legality, ...)!

It also would be nice to drop a line when
  * it helped you
  * it didn't help you (something missing / unclear)
  * you like it
  * you don't like it
  * you found mistakes / bugs
Simply: Reader's feedback
&lt;/WRAP&gt;

&lt;WRAP column 40%&gt;
{{contact&gt;subj=Wiki reader feedback}}
&lt;/WRAP&gt;
