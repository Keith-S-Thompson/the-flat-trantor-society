<!-- Title: C standard quibbles -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2014/02/c-standard-quibbles.html -->

This article is a collection of personal quibbles regarding the ISO
C standard.  Expect it to be updated sporadically.

There have been three major editions of the ISO C standard:

- **C89/C90:** The original ISO C standard was published in 1990, and was closely
  based on the 1989 ANSI C standard.  The 1989 ANSI and 1990 ISO
  standards describe exactly the same language; ISO added some
  introductory material and renumbered the sections.
  [This web page](http://www.bsb.me.uk/ansi-c/ansi-c) appears to be
  a draft of the ANSI version of the standard.  The 1995 amendment
  added digraphs and wide character support.
- **C99:** The second version was published in 1999.
  [N1256](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf)
  includes the full 1999 standard with the three Technical Corrigenda
  merged into it.
- **C11**: The third version was published in 2011.  The
  [N1570](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf)
  draft is freely available, and is very nearly identical to the
  released standard.  (There has been one minor Technical Corrigendum.)

<!-- This does not yet reflect new content -->

Subtopics (these links work on
[my GitHub page](https://github.com/Keith-S-Thompson/the-flat-trantor-society/blob/master/005-c-standard-quibbles.md]
but not on the blog). **TODO:** Figure out how to fix that or give up and delete the links):

- [Is `int main()` necessarily valid? Should it be?](#is-int-main-necessarily-valid-should-it-be)
- [What is an lvalue?](#what-is-an-lvalue)
- [What is an expression?](#what-is-an-expression)
- [Infinite loops](#infinite-loops)
- [`fgetc()` when `sizeof (int) == 1`](#fgetc-when-sizeof-int--1)
- [More stuff ...](#more-stuff-)

<!-- more -->

### Is `int main()` necessarily valid? Should it be?

##### ISO C 5.1.2.2.1 Program startup

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

I've posted a question about this on
[Stack Overflow](https://stackoverflow.com):
["Is int main() { } (without “void”) valid and portable in ISO C?"]
(http://stackoverflow.com/q/29190986/827263).
I've posted [an answer](http://stackoverflow.com/a/29190987/827263), but
the other answers raise some interesting points.

### What is an lvalue?

##### ISO C 6.2.2.1p1 Lvalues, arrays, and function designators

The definition of the term *lvalue* (sometimes written *l-value*)
has changed several times over the years.  The "L" part of the
name was originally an abbreviation of the word "left"; an *lvalue*
can appear on the left hand side of an assignment, and an *rvalue*
can appear on the right hand side.

My (somewhat vague) recollection is that the term "l-value"
originally referred to a kind of *value*, not (as it does now
in C) to a kind of *expression*.  Given that `n` is an integer
variable, the expression `n` could be "evaluated for its l-value"
(which identifies the object that it designates, ignoring any value
stored in that object), or it could be "evaluated for its r-value"
(which retrieves the value stored in the object).  The expression
`n` would be evaluated for its l-value if it appeared on the left
side of an assignment, or for its r-value in most other contexts.
Apparently the terms "l-value" and "r-value" originated in
[CPL](http://en.wikipedia.org/wiki/CPL_(programming_language)),
the ancestor of BCPL, which led to B, which led to C.

Note carefully that, under this definition, an "l-value" is *not* a
pointer value.  An "l-value" was the *identity* of an object, not its
address.  (Evaluating an expression for its l-value might well involve
computing an address internally.)

I've tried <strike>and failed</strike> to find a reference for these
definitions, but a footnote in section 6.3.2.1 of the C standard:

    The name "lvalue" comes originally from the assignment expression
    **`E1 = E2`**, in which the left operand **`E1`** is required
    to be a (modifiable) lvalue.  It is perhaps better considered as
    representing an object "locator value".  What is sometimes called
    "rvalue" is in this International Standard described as the
    "value of an expression".

at least strongly suggests that an *rvalue* is a value, not an
expression that yields a value -- though an lvalue is a kind of
expression.  The term "rvalue" does not appear anywhere else in the
C standard.

**UPDATE :** I've found a reference.
[Wikipedia](http://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue)
says that "The notion of l-values and r-values was introduced
by CPL".  CPL was the ancestor of BCPL, which was the ancestor
of B, which was the ancestor of C.  I haven't found an actual
CPL manual (it's not clear that one exists), but the [BCPL
manual](http://cm.bell-labs.com/cm/cs/who/dmr/bcpl.pdf) says:

> An RVALUE is a binary bit pattern of a fixed length (which is
> implementation dependent), it is usually the size of a computer word.

and

> An LVALUE is a bit pattern representing a storage location containing
> an Rvalue.

This differs from C's description of values in that it refers to bit
patterns, and the definition of "lvalue" seems to match C's idea of
(the representation of) the *address* of an object.  Still, it shows
that lvalue and rvalues were originally kinds of *values*, while in
modern C an lvalue is a kind of *expression*.

But that's all pre-C history.

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
*syntactically*.  I think you can go through section 6.5 of the
standard and determine that certain kinds of expressions are always
lvalues, other kinds of expressions never are, and others are an
lvalue or not based on criteria that are easy to specify.

An expression is an *lvalue* if and only if it one of the following:

- An identifier that is not a function name or enumeration constant;
- A string literal;
- A parenthesized expression, if and only if the unparenthesized
  expression is an lvalue;
- An indirection expression `*x`;
- A subscript expression (`x[y]`) (this follows from the definition of
  the subscript operator and the fact that `*x` is an lvalue)
- A reference to a struct or union member (`x.y`, `x->y`); or
- A compound literal.

(I don't guarantee this is 100% correct.)

The standard's definition of *lvalue* should, IMHO, use a list similar
to the above.  The description of the *intent* can still use the
wording of the current definition, perhaps as a footnote.

### Null pointer constants and parenthesized expressions

##### ISO C 6.3.2.3 Pointers (under 6.3 Conversions)

Paragraph 3:

> An integer constant expression with the value 0, or such an
> expression cast to type **`void *`**, is called a *null pointer
> constant*.

The problem: 6.5.1 (Primary expressions) says that a parenthesized
expression

> is an lvalue, a function designator, or a void expression if
> the unparenthesized expression is, respectively, an lvalue,
> a function designator, or a void expression.

It *doesn't* say that a parenthesized null pointer constant is a null
pointer constant.

Which implies, strictly speaking, that `(void*)0` is a null pointer constant,
but `((void*)0)` is not.

And since 7.1.2 "Standard headers" requires:

> Any definition of an object-like macro described in this clause shall
> expand to code that is fully protected by parentheses where necessary,
> so that it groups in an arbitrary expression as if it were a single
> identifier.

this implies that the `NULL` macro may not be defined as `(void*)0`,
since, for example, that would cause `sizeof NULL` to be a syntax
error.

I'm sure that most C implementations do treat a parenthesized null
pointer constant as a null pointer constant, and define `NULL` either
as `0`, as `((void*)0)`, or in some other manner.

Even if `((void*)0)` were not a null pointer constant, it would
still be an expression of type `void*` whose value is a null pointer,
and it would still be an *address constant*.
So what difference does it make?  [This
question](http://stackoverflow.com/q/26477209/827263) on [Stack
Overflow](http://stackoverflow.com/) asks about this issue (and
confirms that someone actually reads this blog!).


While composing [my
answer](http://stackoverflow.com/a/26477262/827263), I found (with
help from commenters) several cases where it actually matters:

- An expression of pointer-to-function type may be compared (`==` or `!=`)
  to a null pointer constant.  It may not be compared to an arbitrary
  expression of type `void*`.
- Similarly, a null pointer constant may be assigned to an object
  of pointer-to-function type, but an arbitrary expression  of type
  `void*` may not; there is no implicit conversion from `void*`
  to a pointer-to-function type.  The same constraints apply to
  initialization and function argument passing.
- As I mentioned above, the macro `NULL` must expand to a null
  pointer constant.  This is a constraint on implementations, not
  on programmers.

### What is an expression?

##### ISO C 6.5 Expressions

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

### Integer constant expressions

##### ISO C 6.6.6 Constant expressions, paragraph 6

Credit for this goes to [Stack Overflow](http://www.stackoverflow.com) user
[pablo1977](http://stackoverflow.com/users/2698605/pablo1977) who posted
[this question](http://stackoverflow.com/q/21972815/827263).

6.6.6p6 says:

> An *integer constant expression* shall have integer type and
> shall only have operands that are integer constants, enumeration
> constants, character constants, **`sizeof`** expressions whose
> results are integer constants, **`_Alignof`** expressions, and
> floating constants that are the immediate operands of casts. Cast
> operators in an integer constant expression shall only convert
> arithmetic types to integer types, except as part of an operand
> to the **`sizeof`** or **`_Alignof`** operator.

The problem: There's no indication that a parenthesized constant is a
constant.  So `(int)3.14` is a constant expression, but `(int)(3.14)`,
strictly speaking, is not, because `3.14` is a floating constant but
`(3.14)` is not.

It seems obvious that if `(int)3.14` is an integer constant expression,
then there's no reason that `(int)(3.14)` shouldn't be one as well,
and though I haven't checked I suspect that all existing compilers
treat it as one.  If the wording of the standard is to be corrected,
some care will have to be taken so that both `(int)(3.14)` and
`(int)((3.14))` are integer constant expressions

### Infinite loops

##### ISO C 6.8.5 Iteration statements, paragraph 6

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

### `printf` formats for short and char types

##### ISO C 7.21.6.1 The `fprintf` function

Arguments to variadic functions (after the initial fixed arguments)
undergo argument promotions, so that an argument of type `float` is
promoted to `double`, and an argument of any integer type narrower
than `int` is promoted to either `int` or `unsigned int`.  This is
described in 6.5.2.2, "Function calls".

So it's not actually possible, for example, to pass a `short` argument
to `printf` or `fprintf`; it will always be passed as a converted
`int` value.

And yet the `printf` functions support `"hh""` and `"h"` length
modifies that specify `signed char`, `unsigned char`, `signed short`,
or `unsigned short` arguments.

The semantics are well defined.  The standard says that the argument
will have been promoted according to the integer promotions, but its
value is *converted* to the specified type before printing.

For `"%hd"`, the argument will normally be of type `short`, which is
promoted to `int` -- but there's no prohibition on passing an argument
that's of type `int`.  If the argument value is in the range `SHRT_MIN`
to `SHRT_MAX`, then the behavior of `"%hd"` is identical to the
behavior of `"%d"`.  If it's outside that range, then the conversion
yields an implementation-defined result.  For `"%hu"` vs. `"%u"`, or
`"%hx"` vs. `"%x"`, the behavior is still identical if the value is
in range, but the result of an unsigned conversion is well defined.

The `"hh"` and `"h"` length modifiers may be combined with any of the
**`d`**, **`i`**, **`o`**, **`u`**, **`x`**, or **`X`** conversion
specifiers.

They can be meaningfully used with the **`n`** conversion
specifier. `"%n"` stores the number of characters written so far
via a corresponding `int*` argument, and can be used with any of the
`"hh"`, `"h"`, `"l"`, `"ll"`, `"j"`, `"z"`, or `"t"` length modifiers.
But how useful is it to store a count in a `short` or `signed char`
object rather than an `int` object?

So the question is, why were the `"h"` and `"hh"` length modifiers
added to the language?  I don't have an answer to that.

### `fgetc()` when `sizeof (int) == 1`

##### ISO C 7.21.7.1 The `fgetc` function

The standard makes some implicit assumptions about how character
input works.  If `sizeof (int) == 1` (which requires `CHAR_BIT >= 16`),
`EOF` isn't distinct from any valid `char` value.  I think there are
also some assumptions about how unsigned-to-signed conversion works;
the result is implementation-defined, but some possible implementation
definitions would break stdio character input.  I need to study
this further.

### More stuff ...

... as I think of it.
