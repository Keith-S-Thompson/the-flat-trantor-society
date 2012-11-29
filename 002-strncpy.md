<!-- Title: No, strncpy() is not a "safer" strcpy() -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2012/03/no-strncpy-is-not-safer-strcpy.html -->

The C standard library declares a number of string functions in the
standard header `<string.h>`.

By the standards of some other languages, C's string handling is
fairly primitive.  Strings are simply arrays of characters terminated
by a null character `'\0'`, and are manipulated via `char*` pointers.
C has no string type.  Instead, a "string" is a data *layout*, not a data *type*.
Quoting the ISO C standard:

> A *string* is a contiguous sequence of characters terminated by
> and including the first null character.

So what happens if you call a C string function with a pointer into
a char array that isn't properly terminated by a null character?
Such an array does not contain a "string" in the sense that C
defines the term, and the behavior of most of C's string functions
on such arrays is *undefined*. That doesn't mean the function will
fail cleanly, or even that your program will crash; it means that
as far as the standard is concerned, literally anything can happen.
In practice, what typically happens is that the function will keep
looking for that terminating null character either until it finds it
in some chunk of memory it really shouldn't be looking at, or until
it crashes because it looked in some chunk of memory that it really
shouldn't be looking at.

To *partially* address this, C provides "safer" versions of some
string functions, versions that let you specify the maximum size of
an array.  For example, the strcmp() function compares two strings,
but can fail badly if either of the arguments points to something that
isn't a string.  The strncmp() function is a bit safer; it requires
a third argument that specifies the maximum number of characters to
examine in each array:

- `int strcmp (const char *s1, const char *s2);`
- `int strncmp(const char *s1, const char *s2, size_t n);`

Which brings us (finally!) to the topic of this article: the
`strncpy()` function.

`strcpy()` is a fairly straightforward string function.  Given two
pointers, it copies the string pointed to by the second pointer into
the array pointed to by first.  (The order of the arguments mimics
the order of the operands in an assignment statement.)  It's up to
the caller to ensure that there's enough room in the target array to
hold the copied contents.

So you'd *think* that `strncpy()` would be a "safer" version of
`strcpy()`.  And given their respective declarations, that's exactly
what it looks like:

- `char *strcpy (char *dest, const char *src);`
- `char *strncpy(char *dest, const char *src, size_t n);`

But no, that's not what the `strncpy()` function does at all.

Here's the description of `strcpy()` from the [latest draft of the
C standard](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf):

> The **strcpy** function copies the string pointed to by **s2** (including the
> terminating null character) into the array pointed to by **s1**. If copying
> takes place between objects that overlap, the behavior is undefined.

And here's the corresponding description of `strncpy()`:

> The **strncpy** function copies not more than **n** characters
> (characters that follow a null character are not copied) from the array
> pointed to by **s2** to the array pointed to by **s1**.  If copying
> takes place between objects that overlap, the behavior is undefined.

So far, so good, right?  Almost -- but there's more:

> If the array pointed to by **s2** is a string that is shorter than
> **n** characters, null characters are appended to the copy in the array
> pointed to by **s1**, until **n** characters in all have been written.

That second paragraph means that if the string pointed to by `s2` is
shorter than `n` characters, it doesn't just copy `n` characters and add a
terminating null character, which is what you'd expect. It adds null
characters until it's copied a total of `n` characters.  If the source
string is 5 characters long, and the target is a 1024-byte buffer,
and you set n to the size of the target, `strncpy` will copy those
5 characters and then fill all 1019 remaining bytes in the target
with null characters.  Since all it takes to terminate a string is
a single null character, this is almost always a waste of time.

Ok, so that's not so bad.  CPUs are fast these days, and filling a
buffer with zeros is not an expensive operation, right?  Unless you're
doing it a few billion times, but let's not worry about premature
optimization.

The trap is in that first paragraph.  If the target buffer is 5
characters long, you'd quite reasonably set `n` to 5.  But if the
source string is longer than 5 characters, then you'll end up without
a terminating null character in the target array.  In other words,
the target array *won't contain a string*. Try to treat it as if it
does (say, by calling `strlen()` on it or passing it to `printf())`,
and Bad Things Can Happen.

The description of the `strcpy()` and `strncpy()` functions is
identical in the 1990, 1999, and 2011 versions of the ISO C standard --
except that C99 and C11 add a footnote to the `strncpy()` description:

> Thus, if there is no null character in the first **n** characters
> of the array pointed to by **s2**, the result will not be
> null-terminated.

The bottom line is this: in spite of its frankly misleading name,
`strncpy()` isn't really a string function.

Now having a function like this in the standard library isn't such
a bad thing in itself.  It's designed to deal with a specialized
data structure, a fixed-size character array of **N** characters
that can contain up to **N** characters of actual data, with the
rest of the array (if any) padded with 0 or more null characters.
Early Unix systems used such a structure to hold file names in
directories, for example (though it's not clear that `strncpy()`
was invented for that specific purpose).

The problem is that the name `strncpy()` strongly implies that it's a
"safer" version of `strcpy()`.  It isn't.

Most of the other `strn*()` functions are safer versions of their
unbounded counterparts; see, for example, `strcat()` vs. `strncat()`,
and `strlen()` vs `strnlen()`.

It's because `strncpy()`'s name implies something that it isn't that
it's such a trap for the unwary. It's not a useless function, but I
see far more incorrect uses of it than correct uses.  This article
is my modest attempt to spread the word that `strncpy()` isn't what
you probably think it is.

I've put together a
[small demo](https://github.com/Keith-S-Thompson/strncpy_demo)
as a GitHub project.
