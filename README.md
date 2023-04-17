# wiki.bash-hackers.org

Extraction of wiki.bash-hackers.org from the Wayback Machine

This is targeting pages that have been captured by the Wayback Machine that specifically have `?do=edit` on the end of their URL.  These pages give us the Dokuwiki Markup source, relatively unmolested.

See the incomplete script "archive_crawler" to see my working.  I would not recommend blindly running it - it's beta quality at best.  Just read it and this page to follow the logic... or just fork this repo... or whatever, I'm not your Dad.

- TODO: Markdown linting and transformations
- TODO: Perhaps add a "This was downloaded from [wayback url here] on [date]" to each page...
- TODO: Import page-edit history as git log entries?  `?do=revisions` is the secret sauce there...

## Getting the latest capture URL from archive.org

archive.org does not appear to have an exhaustive API, but it does have at least one API call that is relevant to our needs.  It's called `available`.

How it works is you run a `curl -X GET` against `https://archive.org/wayback/available?url=[YOUR URL HERE]` and it returns to you with a bit of JSON.  For example:

```bash
curl -s -X GET "https://archive.org/wayback/available?url=https://wiki.bash-hackers.org/howto/mutex?do=edit" | jq -r '.'
{
  "url": "https://wiki.bash-hackers.org/howto/mutex?do=edit",
  "archived_snapshots": {
    "closest": {
      "status": "200",
      "available": true,
      "url": "http://web.archive.org/web/20220615023742/https://wiki.bash-hackers.org/howto/mutex?do=edit",
      "timestamp": "20220615023742"
    }
  }
}
```

The path `'.archived_snapshots.closest.url'` will, therefore, either have a URL of a latest capture, or it will return `null`.

Because different pages have different timestamps in their capture, you can't just do something like:

```bash
curl -s -X GET "http://web.archive.org/web/20220615023742/https://wiki.bash-hackers.org/some/other/page?do=edit"

```

Because the timestamp in the URL may or may not match.  So we use this API call to locate genuine resources.

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

[ TARGET DOKUWIKI MARKUP CODE EXISTS HERE]

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
>  
> [GNU Free Documentation License 1.3](https://web.archive.org/web/20220930131429/http://www.gnu.org/licenses/fdl-1.3.html)

## COPYRIGHT

The original copyright belongs to Jan Schampera (TheBonsai) and subsequent contributors, 2007 - 2023.

It's extremely important to me that copyright and attribution are given where required - the original contributors are worth their dues, and IIRC I'm one of them.

If you're one of the original contributors and you believe I've violated your copyright in anyway, please let me know in the first instance.
