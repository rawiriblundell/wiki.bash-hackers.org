# wiki.bash-hackers.org
Extraction of wiki.bash-hackers.org from the Wayback Machine

This is targeting pages that have been captured by the Wayback Machine that specifically have `'?do=edit'` on the end of their URL.  This gives us the Dokuwiki Markup source.

See the incomplete script "archive_crawler" to see my working.

- TODO: Parse the already downloaded files for any missing links
- TODO: Markdown conversion from Dokuwiki Markup to GitHub Markdown using pandoc
- TODO: Markdown linting
- TODO: Rinse and repeat

## Extracting the Dokuwiki Markup
So the pages that have `'?do-edit'` on the end of their URL appear to have a reliable and predictable structure:

```bash
[ LINES ABOVE REMOVED FOR BREVITY ]
    <div class="toolbar group">
        <div id="draft__status" class="draft__status"></div>
        <div id="tool__bar" class="tool__bar"></div>
    </div>
    <form id="dw__editform" method="post" action="" accept-charset="utf-8" class=" form-inline"><div class="no">
<input type="hidden" name="sectok" value=""/><input type="hidden" name="id" value="wishes"/>[REST OF LINE REMOVED FOR BREVITY]

[ TARGET MARKDOWN CODE EXISTS HERE]

</textarea>
<div id="wiki__editbar" class="editBar">
<div id="size__ctl">
</div>
[ LINES BELOW REMOVED FOR BREVITY ]
```

So basically, we remove everything from the first line to the line that contains `name="sectok"`, and then we remove everything after `</textarea>`, and what's left should be the Dokuwiki Markup that we want.

## LICENSE

As per the original wiki.bash-hackers.org:

> Except where otherwise noted, content on this wiki is licensed under the following license:
> [GNU Free Documentation License 1.3](https://web.archive.org/web/20220930131429/http://www.gnu.org/licenses/fdl-1.3.html)

## COPYRIGHT

The original copyright belongs to Jan Schampera (TheBonsai) and subsequent contributors, 2007 - 2023.

It's extremely important to me that copyright and attribution are given where required - the original contributors are worth their dues, and IIRC I'm one of them.

If you're one of the original contributors and you believe I've violated your copyright in anyway, please let me know in the first instance.
