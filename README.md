# Postscript Libraries

These library functions add some modern functionality to the admirably
lightweight PostScript language.

There are two ways to use them: with the `run` function, or with
direct inclusion. The former is easier with GhostScript, though you'll
need to disable the SAFER option, e.g.:

    gs -q -dNOSAFER -sDEVICE=txtwrite -o - filename.ps

or using the `runps` shell script included. Or take each function (and
its dependencies) and copy it into your single program file; I'm
working on a helper program to do this automatically.

I'm licencing these under GPL v3, but if you do actually get any use
out of them, or want any functionality added, I'd love to hear about
it.

# The Libraries
## deepeq

* deepeq

Returns `true` if the two values on the top of the stack are equal –
recursively checking strings, arrays and dictionaries for matching
values.

## cartesianproduct

* cartesianproduct

Returns an array of arrays, the n-ary cartesian product of the array
of input arrays. So for example an input of

    [ [ 1 2 ] [ 3 4 ] ]

would produce

    [ [ 1 3 ] [ 1 4 ] [ 2 3 ] [ 2 4 ] ]

## iterables

This is basically for treating arrays as lists, and mangling arrays
into dicts and back.

* keys

Given a dict, return an array of the keys.

* values

Given a dict, return an array of the values. (This will probably be in
the same order as `keys`.)

* toset

Given an array, deduplicate it and return a dict of (array-value →
true) pairs; the opposite of `keys`.

* map

Given an array and a procedure, apply the procedure to each array
element in turn, returning an array of the output.

    [ 1 2 3 ] { 1 add } map → [ 2 3 4 ]

* filter

Given an array and a procedure that returns a boolean, output an array
consisting only of the values that cause a true output.

    [ 1 2 3 4 5 ] { 2 mod 0 eq } → [ 2 4 ]

* reduce

Given an array and a procedure that takes two inputs and leaves one on
the stack, apply it repeatedly to reduce the array to a single value.

    [ 1 2 3 4 5 ] { add } reduce → 15

* reduce_init

As above, but with an initial value for the accumulator.

    0 [ 1 2 4 5 ] { 1 exch div add } reduce_init → 1.95

* enumerate

Given an array, produce an array twice the length, consisting of pairs
of index and array value.

    [ (a) (b) (c) ] enumerate → [ 0 (a) 1 (b) 2 (c) ]

* enumerate.array

Given an array, produce an array twice the length, consisting of array pairs
of index and array value.

    [ (a) (b) (c) ] enumerate → [ [ 0 (a) ] [ 1 (b) ] [ 2 (c) ] ]

* apush.right

Create a new array consisting of the input array and a new value.
`apush` is an alias.

    [ 1 2 ] 3 apush.right → [ 1 2 3 ]

* apush.left

As above but inserts a new initial value.

    [ 2 3 ] 1 apush.left → [ 1 2 3 ]

* apop.left

Create a new array consisting of the input array without its initial
value, and that value separately. `apop` is an alias.

    [ 1 2 3 ] apop.left → [ 2 3 ] 1

* apop.right

As above but for the first value.

    [ 1 2 3 ] apop.right → [ 1 2 ] 3

* all

Given a list and a function, return `true` if all of the values of the
function applied to a list member are true, otherwise `false`.

    [ 1 2 3 4 ] { 0 gt } all → true
    [ 1 2 3 4 ] { 1 eq } all → false

* any

Given a list and a function, return `true` if any of the values of the
function applied to a list member are true, otherwise `false`.

    [ 1 2 3 4 ] { 0 eq } any → false
    [ 1 2 3 4 ] { 1 eq } any → true

* listmax, listmin

Given a list, return the maximum or minimum value in it. (This uses
the non-standard two-argument `max` and `min` extensions in
GhostScript.)

## juliandate

* dmy2jd

Converts a year-month-day tuple to a julian date (integer value at
noon GMT on that day).

    2013 1 1 dmy2jd → 2456294

* jd2dmy

Converts a julian date to year-month-day.

    2456294 jd2dmy → 2013 1 1

## maths

* genprimes

Generate an (unsorted) list of prime numbers less than or equal to the
parameter. (Often you'll then want to quicksort them.)

    20 genprimes quicksort → [ 2 3 5 7 11 13 17 19 ]

* primefactor

Generate a dict of prime factors and their powers.

    75 primefactor → << 3 1 5 2 >>

* gcd

Calculate the greatest common divisor (highest common factor) of two
numbers.

    8 12 gcd → 4

* lcm

Calculate the lowest common multiple of two numbers.

    4 6 lcm → 12

## permute

* permute

Permutes an array in place (using Heap's algorithm), applying a
procedure to each permutation. Note that this changes the contents of
the array, rather than building a new one each time.

    [ 1 2 3 ] { [ exch aload pop ] } permute → [1 2 3] [2 1 3] [3 1 2]
                                               [1 3 2] [2 3 1] [3 2 1]

## quicksort

* quicksort

Sorts an array in place.

     [ 4 9 4 7 ] quicksort → [ 4 4 7 9 ]

## strings

* strconcat

Concatenates two strings.

    (abc) (def) strconcat (abcdef)

* strjoin

Joins an array of strings with a joining string.

    [ (abc) (def) ] (, ) strjoin (abc, def)

* strsplit

Breaks down a string into an array of strings (eliding any zero-length
output).

    (this and that and the other) ( and ) strsplit
    → [ (this) (that) (the other) ]

* s2a

Converts a string to an array of character values.

    (abcdef) s2a → [97 98 99 100 101 102]

* a2s

Converts an array of character values to a string.

    [97 98 99 100 101 102] a2s → (abcdef)

## test

Start a series of tests with `(string) test.start`.

Build tests to output booleans, and run `test` on each.

End the series with `test.end`.

Example:

    (Boolean) test.start
    true test
    false not test
    test.end

Output:

Boolean: Passed 2/2 (100%)
