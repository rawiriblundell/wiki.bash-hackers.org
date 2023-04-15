====== Dissect a bad oneliner ======


&lt;code bash&gt;$ ls *.zip | while read i; do j=`echo $i | sed 's/.zip//g'`; mkdir $j; cd $j; unzip ../$i; cd ..; done&lt;/code&gt;

This is an actual one-liner someone asked about in ''#bash''. **There are several things wrong with it. Let's break it down!**

&lt;code bash&gt;$ ls *.zip | while read i; do ...; done&lt;/code&gt;

(Please read [[http://mywiki.wooledge.org/ParsingLs]].) This command executes ''ls'' on the expansion of ''*.zip''. Assuming there are filenames in the current directory that end in '.zip', ls will give a human-readable list of those names. The output of ls is not for parsing. But in sh and bash alike, we can loop safely over the glob itself:

&lt;code bash&gt;$ for i in *.zip; do j=`echo $i | sed 's/.zip//g'`; mkdir $j; cd $j; unzip ../$i; cd ..; done&lt;/code&gt;

Let's break it down some more!

&lt;code bash&gt;j=`echo $i | sed 's/.zip//g'` # where $i is some name ending in '.zip'&lt;/code&gt;

The goal here seems to be get the filename without its ''.zip'' extension. In fact, there is a POSIX(r)-compliant command to do this: ''basename'' The implementation here is suboptimal in several ways, but the only thing that's genuinely error-prone with this is &quot;''echo $i''&quot;. Echoing an //unquoted// variable means [[syntax:expansion:wordsplit | wordsplitting]] will take place, so any whitespace in ''$i'' will essentially be normalized. In ''sh'' it is necessary to use an external command and a subshell to achieve the goal, but we can eliminate the pipe (subshells, external commands, and pipes carry extra overhead when they launch, so they can really hurt performance in a loop). Just for good measure, let's use the more readable, [[syntax:expansion:cmdsubst | modern]] ''$()'' construct instead of the old style backticks:

&lt;code bash&gt;sh $ for i in *.zip; do j=$(basename &quot;$i&quot; &quot;.zip&quot;); mkdir $j; cd $j; unzip ../$i; cd ..; done&lt;/code&gt;

In Bash we don't need the subshell or the external basename command. See [[syntax:pe#substring_removal | Substring removal with parameter expansion]]:

&lt;code bash&gt;bash $ for i in *.zip; do j=&quot;${i%.zip}&quot;; mkdir $j; cd $j; unzip ../$i; cd ..; done&lt;/code&gt;

Let's keep going:

&lt;code bash&gt;$ mkdir $j; cd $j; ...; cd ..&lt;/code&gt;

As a programmer, you **never** know the situation under which your program will run. Even if you do, the following best practice will never hurt: When a following command depends on the success of a previous command(s), check for success! You can do this with the &quot;''&amp;&amp;''&quot; conjunction, that way, if the previous command fails, bash will not try to execute the following command(s). It's fully POSIX(r). Oh, and remember what I said about [[syntax:expansion:wordsplit | wordsplitting]] in the previous step? Well, if you don't quote ''$j'', wordsplitting can happen again.

&lt;code bash&gt;$ mkdir &quot;$j&quot; &amp;&amp; cd &quot;$j&quot; &amp;&amp; ... &amp;&amp; cd ..&lt;/code&gt;

That's almost right, but there's one problem -- what happens if ''$j'' contains a slash? Then ''cd ..'' will not return to the original directory. That's wrong! ''cd -'' causes cd to return to the previous working directory, so it's a much better choice:

&lt;code bash&gt;$ mkdir &quot;$j&quot; &amp;&amp; cd &quot;$j&quot; &amp;&amp; ... &amp;&amp; cd -&lt;/code&gt;

(If it occurred to you that I forgot to check for success after cd -, good job! You could do this with ''{ cd - || break; }'', but I'm going to leave that out because it's verbose and I think it's likely that we will be able to get back to our original working directory without a problem.)

So now we have:

&lt;code bash&gt;sh $ for i in *.zip; do j=$(basename &quot;$i&quot; &quot;.zip&quot;); mkdir &quot;$j&quot; &amp;&amp; cd &quot;$j&quot; &amp;&amp; unzip ../$i &amp;&amp; cd -; done&lt;/code&gt;

&lt;code bash&gt;bash $ for i in *.zip; do j=&quot;${i%.zip}&quot;; mkdir &quot;$j&quot; &amp;&amp; cd &quot;$j&quot; &amp;&amp; unzip ../$i &amp;&amp; cd -; done&lt;/code&gt;

Let's throw the ''unzip'' command back in the mix:

&lt;code bash&gt;mkdir &quot;$j&quot; &amp;&amp; cd &quot;$j&quot; &amp;&amp; unzip ../$i &amp;&amp; cd -&lt;/code&gt;

Well, besides word splitting, there's nothing terribly wrong with this. Still, did it occur to you that unzip might already be able to target a directory? There isn't a standard for the ''unzip'' command, but all the implementations I've seen can do it with the -d flag. So we can drop the cd commands entirely:

&lt;code bash&gt;$ mkdir &quot;$j&quot; &amp;&amp; unzip -d &quot;$j&quot; &quot;$i&quot;&lt;/code&gt;

&lt;code bash&gt;sh $ for i in *.zip; do j=$(basename &quot;$i&quot; &quot;.zip&quot;); mkdir &quot;$j&quot; &amp;&amp; unzip -d &quot;$j&quot; &quot;$i&quot;; done&lt;/code&gt;

&lt;code bash&gt;bash $ for i in *.zip; do j=&quot;${i%.zip}&quot;; mkdir &quot;$j&quot; &amp;&amp; unzip -d &quot;$j&quot; &quot;$i&quot;; done&lt;/code&gt;

There! That's as good as it gets.
