<!-- Title: Markdown -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2012/11/markdown.html -->

I've decided to start composing and maintaining this blog using
[Markdown](http://daringfireball.net/projects/markdown/).

If you're not familiar with it (or even if you are), Markdown is a
text-to-HTML conversion tool for web writers.  Raw Markdown is much
more readable and easier to work with than raw HTML.  It doesn't
*directly* provide the full power of HTML, though you can include raw
HTML in a Markdown document -- and you can do *italics*, **bold**,
and ***bold italics*** directly in Markdown.

It's used (in slightly different flavors) on [GitHub](https://github.com/)
and on the [StackExchange](http://stackexchange.com/) network of sites,
among other places.

All posts on this blog are maintained as [a GitHub
project](https://github.com/Keith-S-Thompson/the-flat-trantor-society).
If you're sufficiently curious, you can see the Markdown form of all
the articles, and how I've revised them over time.

One thing I've noticed with the composition software used by
blogspot.com is that switching between the "HTML" and "Compose"
views *changes the HTML*; in particular, it removes `<p>` paragraph
markup, replacing it by `<br />` line breaks.  Because of this I need
to copy the Markdown-generated HTML into the HTML window and click
the **Update** button *without* looking at the preview.  Annoying,
but not fatal.

Markdown is converted to HTML by the
[`markdown`](http://daringfireball.net/projects/downloads/Markdown_1.0.1.zip)
command, which is also available as a .deb package on Debian and
Debian-derived systems such as Ubuntu and Linux Mint:

```
sudo apt-get install markdown
```

It should be available for other systems as well.  I run a simple
`gen-html` script (included in the GitHub project for this blog), and
then manually copy-and-paste the generated HTML into blogspot.com's
web interface.  The manual step is annoying, but overall it should
make it easier to write and maintain this blog.

[Pandoc](http://johnmacfarlane.net/pandoc/) is another good conversion
tool, handles numerous other formats as well. It should be available
for most systems.

Who knows, I might even get around to posting more articles!
