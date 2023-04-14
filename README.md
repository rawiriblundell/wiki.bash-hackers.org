# wiki.bash-hackers.org
Extraction of wiki.bash-hackers.org from the Wayback Machine

This is targeting pages that have been captured by the Wayback Machine that specifically have '?do=edit' on the end of their URL.  This gives us the markdown source.

See the incomplete script "archive_crawler" to see my working.

- TODO: Second crawl
- TODO: Filter out all the non-markdown garbage.  It looks like everything up to `<div class="editBox" role="application">`, and everything after `</div><!-- /content --></div>` is a good first cull.

# LICENSE

As per the original wiki.bash-hackers.org:

> Except where otherwise noted, content on this wiki is licensed under the following license:
> [GNU Free Documentation License 1.3](https://web.archive.org/web/20220930131429/http://www.gnu.org/licenses/fdl-1.3.html)

# COPYRIGHT

The original copyright belongs to Jan Schampera (TheBonsai) and subsequent contributors, 2007 - 2023.

It's extremely important to me that copyright and attribution are given where required - the original contributors are worth their dues, and IIRC I'm one of them.

If you're one of the original contributors and you believe I've violated your copyright in anyway, please let me know in the first instance.
