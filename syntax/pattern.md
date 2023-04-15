====== Patterns and pattern matching ======

{{keywords>bash shell scripting glob globbing wildcards filename pattern matching}}

A pattern is a **string description**. Bash uses them in various ways:
  * [[syntax:expansion:globs | Pathname expansion]] (Globbing - matching filenames)
  * Pattern matching in [[syntax:ccmd:conditional_expression | conditional expressions]]
  * [[syntax:pe#substring_removal | Substring removal]] and [[syntax:pe#search_and_replace | search and replace]] in [[syntax:pe | Parameter Expansion]]
  * Pattern-based branching using the [[syntax:ccmd:case | case command]]

The pattern description language is relatively easy. Any character that's not mentioned below matches itself.
The ''NUL'' character may not occur in a pattern. If special characters are quoted, they're matched literally, i.e., without their special meaning.

Do **not** confuse patterns with **//regular expressions//**, because they share some symbols and do similar matching work.

===== Normal pattern language =====

^Sequence^Description^
|''*''|Matches **any string**, including the null string (empty string)|
|''?''|Matches any **single character**|
|''X''|Matches the character ''X'' which can be any character that has no special meaning|
|''\X''|Matches the character ''X'', where the character's special meaning is stripped by the backslash|
|''\\''|Matches a backslash|
|''[...]''|Defines a pattern **bracket expression** (see below). Matches any of the enclosed characters at this position.|


==== Bracket expressions ====

The bracket expression ''[...]'' mentioned above has some useful applications:
^Bracket expression^Description^
|''[XYZ]''|The &quot;normal&quot; bracket expression, matching either ''X'', ''Y'' or ''Z''|
|''[X-Z]''|A range expression: Matching all the characters from ''X'' to ''Y'' (your current **locale**, defines how the characters are **sorted**!)|
|''<nowiki>[[:class:]]</nowiki>''|Matches all the characters defined by a [[https://pubs.opengroup.org/onlinepubs/009696899/basedefs/xbd_chap07.html#tag_07_03_01|POSIX(r) character class]]: ''alnum'', ''alpha'', ''ascii'', ''blank'', ''cntrl'', ''digit'', ''graph'', ''lower'', ''print'', ''punct'', ''space'', ''upper'', ''word'' and ''xdigit''|
|''[^...]''|A negating expression: It matches all the characters that are **not** in the bracket expression|
|''[!...]''|Equivalent to ''[^...]''|
|''<nowiki>[]...]</nowiki>'' or ''[-...]''|Used to include the characters '']'' and ''-'' into the set, they need to be the first characters after the opening bracket|
|''[=C=]''|Matches any character that is eqivalent to the collation weight of ''C'' (current locale!)|
|''<nowiki>[[.SYMBOL.]]</nowiki>''|Matches the collating symbol ''SYMBOL''|




==== Examples ====

Some simple examples using normal pattern matching:

  * Pattern ''&quot;Hello world&quot;'' matches
    * ''Hello world''
  * Pattern ''[Hh]&quot;ello world&quot;'' matches
    * => ''Hello world''
    * => ''hello world''
  * Pattern ''Hello*'' matches (for example)
    * => ''Hello world''
    * => ''Helloworld''
    * => ''HelloWoRlD''
    * => ''Hello''
  * Pattern ''Hello world<nowiki>[[:punct:]]</nowiki>'' matches (for example)
    * => ''Hello world!''
    * => ''Hello world.''
    * => ''Hello world+''
    * => ''Hello world?''
  * Pattern ''<nowiki>[[.backslash.]]Hello[[.vertical-line.]]world[[.exclamation-mark.]]</nowiki>'' matches (using [[https://pubs.opengroup.org/onlinepubs/009696899/basedefs/xbd_chap07.html#tag_07_03_02_04|collation symbols]])
    * => ''\Hello|world!''

===== Extended pattern language =====

If you set the [[internals:shell_options | shell option]] ''extglob'', Bash understands some powerful patterns. A ''<PATTERN-LIST>'' is one or more patterns, separated by the pipe-symbol (''PATTERN|PATTERN'').

|''?(<PATTERN-LIST>)''|Matches **zero or one** occurrence of the given patterns|
|''*(<PATTERN-LIST>)''|Matches **zero or more** occurrences of the given patterns|
|''+(<PATTERN-LIST>)''|Matches **one or more** occurrences of the given patterns|
|''@(<PATTERN-LIST>)''|Matches **one** of the given patterns|
|''!(<PATTERN-LIST>)''|Matches anything **except** one of the given patterns|

==== Examples ====

**__Delete all but one specific file__**
<code>
rm -f !(survivior.txt)
</code>

===== Pattern matching configuration =====

==== Related shell options ====
^option ^classification ^description ^
|''dotglob'' | [[syntax:expansion:globs|globbing]] | see [[syntax:expansion:globs#Customization|Pathname expansion customization]] |
|''extglob'' | global | enable/disable extended pattern matching language, as described above |
|''failglob'' | [[syntax:expansion:globs|globbing]] | see [[syntax:expansion:globs#Customization|Pathname expansion customization]] |
|''nocaseglob'' | [[syntax:expansion:globs|globbing]] | see [[syntax:expansion:globs#Customization|Pathname expansion customization]] |
|''nocasematch'' | pattern/string matching | perform pattern matching without regarding the case of individual letters |
|''nullglob'' | [[syntax:expansion:globs|globbing]] | see [[syntax:expansion:globs#Customization|Pathname expansion customization]] |
|''globasciiranges'' | [[syntax:expansion:globs|globbing]] | see [[syntax:expansion:globs#Customization|Pathname expansion customization]] |


===== Bugs and Portability considerations =====

 * Counter-intuitively, only the ''[!chars]'' syntax for negating a character class is specified by POSIX for shell pattern matching. ''[^chars]'' is merely a commonly-supported extension. Even dash supports ''[^chars]'', but not posh.

 * All of the extglob quantifiers supported by bash were supported by ksh88. The set of extglob quantifiers supported by ksh88 are identical to those supported by Bash, mksh, ksh93, and zsh.

 * mksh does not support POSIX character classes. Therefore, character ranges like ''[0-9]'' are somewhat more portable than an equivalent POSIX class like ''[:digit:]''.

 * Bash uses a custom runtime interpreter for pattern matching. (at least) ksh93 and zsh translate patterns into regexes and then use a regex compiler to emit and cache optimized pattern matching code. This means Bash may be an order of magnitude or more slower in cases that involve complex back-tracking (usually that means extglob quantifier nesting). You may wish to use Bash's regex support (the ''=~'' operator) if performance is a problem, because Bash will use your C library regex implementation rather than its own pattern matcher.

TODO: describe the pattern escape bug https://gist.github.com/ormaaj/6195070

==== ksh93 extras ====

ksh93 supports some very powerful pattern matching features in addition to those described above.

 * ksh93 supports arbitrary quantifiers just like ERE using the ''{from,to}(pattern-list)'' syntax. ''{2,4}(foo)bar'' matches between 2-4 &quot;foo&quot;'s followed by &quot;bar&quot;. ''{2,}(foo)bar'' matches 2 or more &quot;foo&quot;'s followed by &quot;bar&quot;. You can probably figure out the rest. So far, none of the other shells support this syntax.

 * In ksh93, a ''pattern-list'' may be delimited by either ''&'' or ''|''. ''&'' means &quot;all patterns must be matched&quot; instead of &quot;any pattern&quot;. For example, <code>[[ fo0bar == @(fo[0-9]&+([[:alnum:]]))bar ]]</code> would be true while <code>[[ f00bar == @(fo[0-9]&+([[:alnum:]]))bar ]]</code> is false, because all members of the and-list must be satisfied. No other shell supports this so far, but you can simulate some cases in other shells using double extglob negation. The aforementioned ksh93 pattern is equivalent in Bash to: <code>[[ fo0bar == !(!(fo[0-9])|!(+([[:alnum:]])))bar ]]</code>, which is technically more portable, but ugly.

 * ksh93's [[commands/builtin/printf|printf]] builtin can translate from shell patterns to ERE and back again using the ''%R'' and ''%P'' format specifiers respectively.

