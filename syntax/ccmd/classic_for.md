====== The classic for-loop ======

===== Synopsis =====
&lt;code&gt;
for &lt;NAME&gt;; do
  &lt;LIST&gt;
done
&lt;/code&gt;

&lt;code&gt;
for &lt;NAME&gt; in &lt;WORDS&gt;; do
  &lt;LIST&gt;
done
&lt;/code&gt;
alternative, historical and undocumented syntax ((http://pubs.opengroup.org/onlinepubs/9699919799/xrat/V4_xcu_chap02.html#tag_23_02_09_12))
&lt;code&gt;
for &lt;NAME&gt;; {
  &lt;LIST&gt;
}

for &lt;NAME&gt; in &lt;WORDS&gt;; {
  &lt;LIST&gt;
}

&lt;/code&gt;

===== Description =====

For every word in ''&lt;WORDS&gt;'', one iteration of the loop is performed and the variable ''&lt;NAME&gt;'' is set to the current word. If no &quot;''in &lt;WORDS&gt;''&quot; is present to give an own word-list, then the positional parameters (''&quot;$@&quot;'') are used (the arguments to the script or function). In this case (and only in this case), the semicolon between the variable name and the ''do'' is optional.


If you use the loop-variable inside the for-loop and it can contain spaces, you need to quote it, since normal word-splitting procedures apply.


:!: Like all loops (both ''for''-loops, ''while'' and ''until''), this loop can be
  * terminated (broken) by the ''break'' command, optionally as ''break N'' to break ''N'' levels of nested loops
  * forced to immediately do the next iteration using the ''continue'' command, optionally as ''continue N'' analog to ''break N''

Bash knows an alternative syntax for the ''for'' loop, enclosing the loop body in ''{&lt;nowiki&gt;...&lt;/nowiki&gt;}'' instead of ''do &lt;nowiki&gt;...&lt;/nowiki&gt; done'':
&lt;code bash&gt;
for x in 1 2 3
{
  echo $x
}
&lt;/code&gt;
This syntax is **not documented** and should not be used. I found the parser definitions for it in 1.x code, and in modern 4.x code. My guess is that it's there for compatiblity reasons. This syntax is not specified by POSIX(r).


==== Return status ====

The return status is the one of the last command executed in ''&lt;LIST&gt;'' or ''0'' (''TRUE''), if the item list ''&lt;WORDS&gt;'' evaluates to nothing (i.e.: &quot;is empty&quot;!).


===== Examples =====
==== Iterate over array elements ====

With some array syntax (see [[syntax:arrays]]) you can easily &quot;feed&quot; the for-loop to iterate over all elements in an array (by mass-expanding all elements):
&lt;code bash&gt;
for element in &quot;${myarray[@]}&quot;; do
  echo &quot;Element: $element&quot;
done
&lt;/code&gt;

Another way is to mass-expand all used indexes and access the array by index:
&lt;code bash&gt;
for index in &quot;${!myarray[@]}&quot;; do
  echo &quot;Element[$index]: ${myarray[$index]}&quot;
done
&lt;/code&gt;

==== List positional parameters ====
You can use this [[syntax:basicgrammar#shell_function_definitions | function]] to test how arguments to a command will be interpreted and parsed, and finally used:
&lt;code bash&gt;
argtest() {
  n=1
  for arg; do
    echo &quot;Argument $((n++)): \&quot;$arg\&quot;&quot;
  done
}
&lt;/code&gt;


==== Loop through a directory ====
Since pathname expansion will expand all filenames to separate words, regardless of spaces, you can use the for-loop to iterate through filenames in a directory:
&lt;code bash&gt;
for fn in *; do
  if [ -h &quot;$fn&quot; ]; then
    echo -n &quot;Symlink: &quot;
  elif [ -d &quot;$fn&quot; ]; then
    echo -n &quot;Dir: &quot;
  elif [ -f &quot;$fn&quot; ]; then
    echo -n &quot;File: &quot;
  else
    echo -n &quot;Unknown: &quot;
  fi
  echo &quot;$fn&quot;
done
&lt;/code&gt;
Stupid example, I know ;-)

==== Loop over lines of output ====

To be complete: You can change the internal field separator (IFS) to a newline and thus make a for-loop iterating over lines instead of words:

&lt;code bash&gt;
IFS=$'\n'
for f in $(ls); do
  echo $f
done
&lt;/code&gt;

This is just an example. In //general//
  * it's not a good idea to parse ''ls(1)'' output
  * the [[syntax:ccmd:while_loop|while loop]] (using the ''read'' command) is a better joice to iterate over lines

==== Nested for-loops ====

It's of course possible to use another for-loop as ''&lt;LIST&gt;''. Here, counting from 0 to 99 in a weird way:

&lt;code bash&gt;
for x in 0 1 2 3 4 5 6 7 8 9; do
  for y in 0 1 2 3 4 5 6 7 8 9; do
    echo $x$y
  done
done
&lt;/code&gt;

==== Loop over a number range ====

Beginning in Bash 4, you can also use &quot;sequence expression&quot; form of [[syntax:expansion:brace|brace expansion]] syntax when looping over numbers, and this form does not create leading zeroes unless you ask for them:

&lt;code bash&gt;
# 100 numbers, no leading zeroes
for x in {0..99}; do
  echo $x
done
&lt;/code&gt;

&lt;code bash&gt;
# Every other number, width 3
for x in {000..99..2}; do
  echo $x
done
&lt;/code&gt;

WARNING: the entire list is created before looping starts.  If your list is huge this may be an issue, but no more so than for a glob that expands to a huge list.

===== Portability considerations =====

===== See also =====

