<!-- Title: C standard quibbles -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2014/02/c-standard-quibbles.html -->

This article is a collection of personal quibbles regarding the ISO
C standard.  Expect it to be updated sporadically.

There have been three major editions of the ISO C standard:

- **C89/C90:** The original ISO C standard was published in 1990, and was closely
  based on the 1989 ANSI C standard.  The 1989 ANSI and 1990 ISO
  standards describe exactly the same language; ISO added some
  introductory material and renumbered the sections.  The 1995
  amendment added digraphs and wide character support.
- **C99:** The second version was published in 1999.
  [N1256](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf)
  includes the full 1999 standard with the three Technical Corrigenda
  merged into it.
- **C11**: The third version was published in 2011.  The
  [N1570](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf)
  draft is freely available, and is very nearly identical to the
  released standard.  (There has been one minor Technical Corrigendum.)

- [What is an lvalue?](#what-is-an-lvalue)
- [Is `int main()` necessarily valid? Should it be?](#is-int-main-necessarily-valid-should-it-be)
- [What is an expression?](#what-is-an-expression)
- [`getchar()` when `sizeof (int) == 1`](#getchar-when-sizeof-int--1)
- [Infinite loops](#infinite-loops)
- [More stuff ...](#more-stuff-)

### What is an lvalue?

The definition of the term *lvalue* has changed several times over
the years.  The "L" part of the name was originally an abbreviation
of the word "left"; an *lvalue* can appear on the left hand side of
an expression, and an *rvalue* can appear on the right hand side.

**TODO**: Discuss older definitions of *lvalue* and *rvalue* (the
result of evaluating an expression rather than the expression itself).

- Kernigan & Ritchie, "The C Programming Language", 1st edition, 1978:

  > An *object* is a manipulatable region of storage; an *lvalue*
  > is an expression referring to an object.

  This suffers from the same problem as the later ISO C90 definition;
  see below.

- C90 6.2.2.1:

  > An *lvalue* is an expression (with an object type or an
  > incomplete type other than **`void`**) that designates an object.

  Problem: Though this conveys the intent, it implies that a
  dereferenced null pointer is *not* an lvalue, which makes lvalue-ness
  an execution time property.  This is clearly not the intent.

- C99 6.3.2.1p1:
  > An *lvalue* is an expression with an object type or an incomplete
  > type other than **`void`**; if an lvalue does not designate an
  > object when it is evaluated, the behavior is undefined.

  Problem: This says that any expression of an appropriate type is an
  lvalue, which is certainly not the intent.  For example, it says that
  `42` (which is an expression of an object type) is an value, and that
  since it doesn't designate an object, the behavior of any program
  containing `42` is undefined.  What a mess.  The intent is that an
  the evaluation of an lvalue *that's in a context that requires an
  lvalue* has undefined behavior if it doesn't designate an object.

- C11 6.3.2.1p1:
  > An *lvalue* is an expression (with an object type other than
  > **`void`**) that potentially designates an object; if an lvalue
  > does not designate an object when it is evaluated, the behavior
  > is undefined.

  This goes back to the C90 definition and adds the word "potentially"
  ([which was my idea](https://groups.google.com/forum/message/raw?msg=comp.std.c/KXjLg24jzVU/sAQRa0kjbE4J),
  BTW).  This clarifies that a dereferenced null pointer is an lvalue,
  but if it's evaluated in a context that requires an lvalue it has
  undefined behavior.

  I'm still not entirely happy with this, because it's not clear just
  what "potentially designates" means.

Ultimately, I think that the term *lvalue* can be defined
*syntactically*.  I'll work out the details later, but I think you
can go through section 6.5 of the standard and determine that certain
kinds of expressions are always lvalues, other kinds of expressions
never are, and others are an lvalue or not based on easily describable
criteria.

The following expressions, and only these expressions,
are *lvalues*:

- An identifier, if it designates an object (as opposed to a function).
- A string literal
- A parenthesized expression, if the unparenthesized expression is
  an lvalue.
- An array subscripting expression
- A reference to a struct or union member (`x.y`, `x->y`)
- A compound literal
- An indirection expression `*x`

(I don't guarantee this is 100% correct; I'll have to re-check it.)

The standard's definition of *lvalue* should, IMHO, use a list similar
to the above.  The description of the *intent* can still use the
wording of the current definition, perhaps as a footnote.

### Is `int main()` necessarily valid? Should it be?

5.1.2.2.1 defines two permitted definitions for `main`:

- `int main(void) { /* ... */}`
- `int main(int argc, char *argv[]) { /* ... */ }`

followed by:

> or equivalent; or in some other implementation-defined manner.

Which means that compilers *may* accept `void main(void)`, but are
not required to do so (more on that later and elsewhere).

This is a very commonly used definition:

- `int main() { /* ... */ }`

As a *definition*, it says that `main` has no parameters.  As a
*declaration*, though, it *doesn't* say that `main` takes no arguments;
rather, it says that `main` takes an unspecified but fixed number
and type(s) of parameters -- and if you call it with arguments that
are incompatible with the definition, the behavior is undefined.

I argue that `int main()` is *not* equivalent to `int main(void)`,
and therefore is not a valid definition *unless* it's covered by the
"or in some other implementation-defined manner" clause (i.e., unless
the implementation explicitly documents that it supports it).

`int main() { /* ... */ }` is an old-style non-prototype definition.
Support for such definitions is obsolescent feature (C11 6.11.7).

Furthermore, this program:

    int main(void) {
        if (0) {
            main(42);
        }
    }

violates a constraint, whereas this program:

    int main() {
        if (0) {
            main(42);
        }
    }

does not, which implies that the two forms are *not* equivalent.

I wonder whether those who argue that `int main()` is valid because
it's "equivalent" to `int main(void)` would make the same argument for:

    int main(argc, argv)
    int argc;
    char *argv[];
    {
        /* ... */
    }

On the other hand, as long as non-prototype function declarations
and definitions are part of the standard, `int main() { /* ... */ }`
probably *should* be valid.  The entire point of continuing to support
such definitions and declarations is to avoid breaking pre-ANSI code,
written before prototypes were added to the language (it's not as
if non-prototype declarations are useful other than for backward
compatibility).  If `int main()` is invalid, then *no* pre-ANSI program
is a valid C90, C99, or C11 program, which was surely not the intent.

### What is an expression?

The syntax and semantics of expressions are described in section
6.5 of the ISO C standard (which covers 30 pages).  But the formal
*definition* of the word "expression" is in 6.5p1:

> An *expression* is a sequence of operators and operands that
> specifies computation of a value, or that designates an object
> or a function, or that generates side effects, or that performs
> a combination thereof.

That sounds reasonable -- except that a strict reading of that
definition implies that `42` is not an expression.  Why not?
It contains no operators, and `42` can't be an operand if there is
no operator, so it's not "a sequence of operators and operands".

The real definition of *expression* is syntactic; anything that
satisfies the syntactic definition of *expression* (in 6.5.17, and
referring to definitions in the rest of section 6.5) is an expression.

The definition in 6.5p1 either needs to be re-worded so that it
includes primary expressions, or it needs to refer to the grammar.
A more reader-friendly (but perhaps less precise) English description
of what an expression is should still be included.

### `getchar()` when `sizeof (int) == 1`

The standard makes some implicit assumptions about how character
input works.  If `sizeof (int) == 1` (which requires `CHAR_BIT >= 16`),
`EOF` isn't distinct from any valid `char` value.  I think there are
also some assumptions about how unsigned-to-signed conversion works;
the result is implementation-defined, but some possible implementation
definitions would break stdio character input.  I need to study
this further.

### Infinite loops

This was a change made in ISO C 2011.

6.8.5p6 says:

> An iteration statement whose controlling expression is not a constant
> expression, that performs no input/output operations, does not access
> volatile objects, and performs no synchronization or atomic operations
> in its body, controlling expression, or (in the case of a **`for`**
> statement) its *expression-3*, may be assumed by the implementation
> to terminate.

with a footnote:

> This is intended to allow compiler transformations such as removal of
> empty loops even when termination cannot be proven.

So this clause is all about enabling optimizations, and I'm guessing
that it was influenced by the C compiler implementers on the committee.

I presume that they had good reasons for adding this, and that it
makes a signicant difference in the performance of real-world code.
And if you want to write an infinite loop deliberately, you can still
do so because of the "constant expression" exception.

But it means that I can write code whose behavior is well defined
in terms of pre-2011 C, and that can behave differently in C11.
For example:

    const int keep_going = 1;
    while (keep_going) {
	;
    }
    puts("This should never appear");

In C90 and C99, the message "This should never appear" will never be
printed.  In C11, because `keep_going` is not a constant expression,
the compiler can legally *assume* that the loop terminates, and the
message may or may not be printed.

I'd be interested in seeing cases where this additional permission
is actually helpful.

Furthermore, I find the way this permission is worded to be clumsy.
It's a statement about what the implementation is permitted to
*assume*.  What really matters is what the implementation is permitted
to *do*.  A better and more consistent way of expressing this, I think,
would have been something like:

> If an iteration statement whose controlling expression is not a
> constant expression, that performs no input/output operations,
> does not access volatile objects, and performs no synchronization
> or atomic operations in its body, controlling expression, or
> (in the case of a **`for`** statement) its *expression-3* does
> not explicitly terminate, it is unspecified whether it terminates
> or not.

Or it could say that if such a loop does not terminate, the behavior
is undefined -- but that would give compilers much more latitude than
the current wording.

### More stuff ...

... as I think of it.
