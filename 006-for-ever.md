<!-- Title: for (EVER) -->
<!-- URL:   TBD -->

When I was first learning the C language (more years ago than I care
to admit), I came up with this macro definition:

    #define EVER ;;

This would let me write infinite loops as:

    for (EVER) {
        /* blah blah blah */
    }

At the time, I thought it was very clever.

I still do think it was very clever.  I just no longer think that's
a good thing.

There's nothing wrong with writing code to show off how clever you
are (see, for example, my totally useless [collection of Fizzbuzz
programs](https://github.com/Keith-S-Thompson/fizzbuzz-c).  But when
you're writing code that's meant to be *useful*, write clearly.
(`for (EVER)` might be clearer than `for (;;)` to someone who doesn't
know C, but that's not your audience).
