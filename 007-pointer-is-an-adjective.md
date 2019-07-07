<!-- Title: "pointer" is an adjective, not a noun -->
<!-- URL:   TBD -->

One of the biggest sources of confusion in C (and C++) is the
odd relationship between arrays and pointers.  There's a common
misconception that arrays are really just pointers (they're not),
or that strings are pointers of type `char*` (they aren't).

This has been covered elsewhere, particularly in section 6 of the
[comp.lang.c FAQ](http://www.c-faq.com/).  (Note: I generally share
a link to the entire FAQ, not to an individual section or question,
in the hope that the reader will see what else is there.)

I think I've come up with a way of talking about arrays and pointers
that makes them easier to understand, namely:

- "**Pointer**" is an adjective, not a noun.
- "**Array**" is an adjective, not a noun.

<!-- more -->

We can refer to "a pointer" (and the C standard sometimes does so),
but I've found that it's clearer to refer to "a pointer type",
"a pointer object", "a pointer expression", "a pointer value", etc.
(In general, a "pointer **FOO**" is "a **FOO** of pointer type".)  And the
same for "array".

Array types are in a very real sense second-class types in C.  There
*are* array values (the value of an array object consists of the values
of its elements), but there are no operations that operate on array
values.  Defining an array object defines an object of array type; it
does not create a pointer object.  But most operations on array values
or array objects do so indirectly, via pointers to their elements.

There are two rules that cause confusion:

First, an array expression is, in most but not all contexts, implicitly
converted to a pointer expression that yields the address of the
initial element of the corresponding array object.  The exceptions are
when the array expression is the operand of unary `&`, the operand
of unary `sizeof`, or when it's a string literal used to initialize
an array object.  (This is not a type conversion in the usual sense.
It's a compile-time adjustment that changes an expression of array
type to an expression of pointer type.)

Second, a function parameter defined with an array type is adjusted
to become a parameter of pointer type.

You'll see people claiming that "arrays are really just pointers".
They aren't, but given something like this:

    int obj[10] = { 0 };
    printf("*obj = %d\n", *obj);

*seems* to support the claim.  But in fact the *object* `obj`, defined
on the first line is an array object (there is no pointer object
defined anywhere in this code), but the *expression* `obj`, after
conversion, is a pointer expression.  If I say "`obj` is an array",
am I talking about the object whose name is `obj`, the expression
`obj` before the compile-time conversion, or the expression `obj`
after the compile-time conversion?
