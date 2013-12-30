This repository contains the content for my technical blog, [The Flat
Trantor Society](http://the-flat-trantor-society.blogspot.com/).

I initially composed the articles using blogspot's own
tools, which don't (as far as I can tell) allow for using
[Markdown](http://daringfireball.net/projects/markdown/), and make
code formatting particularly difficult.

I've now copied the contents of the first two posts and manually
converted them to Markdown format.  For any future posts, I'll first
compose them as Markdown, then copy-and-paste the generated HTML
into blogspot.

Future posts (once I acquire the proverbial Round Tuit):

- [[DONE]](http://the-flat-trantor-society.blogspot.com/2013/12/where-should-control-key-be.html)
  Where should the Control key be located?  Almost all modern computer
  keyboards put the Caps Lock key directly to the left of the 'A'
  key, with the Shift key below it and the Control key below Shift.
  I strongly prefer to have the Control key directly to the left of
  'A', and I rarely use Caps Lock.  I'll explain why, and how you can 
  reconfigure your own keyboard in software.
- What is an "lvalue", and how has the meaning evolved over time?
  The three editions of the ISO C standard have three different
  definitions of the term "lvalue".  The definition has improved
  over time, though all of them differ from the original meaning.
  (The wording in the 2011 standard is based on a suggestion I made
  in comp.std.c.)
- Shell commands: `echo` vs. `printf`.
- `#!/usr/bin/foo` vs. `#!/usr/bin/env foo`
- `crontab -e` considered harmful; more generally, how to manage your
  crontab. Don't be tempted to edit `/etc/crontab`; per-user crontabs
  exist for a reason.
- Google Groups considered harmful
- `void main()` is useful for detecting bad C textbooks (should I
  resist the temptation to mention "shibboleth"?
- Multidimensional arrays in C. Array of array vs. pointer to array
  vs. array of pointers vs. pointer to pointer -- and more for higher
  dimensions. Added flexibility (not having to specify sizes at compile
  time, or definition time for VLAs) comes with added responsibility
  (managing allocation and deallocation). Pictures with boxes and arrows.
- C is more strongly typed than you think it is.
- This is why people use Windows (a few things Linux should do better).
- Floating-point equality: using a simple epsilon is not good enough.
- Bash documentation. Debian doesn't include the bash-doc package for
  "free software" reasons; explain how to install it yourself (see
  `$INFOPATH`). And the documentation for the special parameters `$*
  $@ $# $? $- $$ $! $0 $_` is hard to find; provide my patch that
  updates the document.<br>
  References:
  - http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=713923
  - http://stackoverflow.com/q/20272085/827263
