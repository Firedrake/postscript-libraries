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

    [ 1 2 [ 3 4 ] ] [ 1 2 [ 3 4 ] ] deepeq → true

## cartesianproduct

* cartesianproduct

Returns an array of arrays, the n-ary cartesian product of the array
of input arrays. So for example an input of

    [ [ 1 2 ] [ 3 4 ] ]

would produce

    [ [ 1 3 ] [ 1 4 ] [ 2 3 ] [ 2 4 ] ]

## date

Various date-related calculations. A "ymd" is an array of [ year month
day ] values; a "jd" is the integer value of the Julian date at noon
GMT of that day.

* ymd2jd

Converts a year-month-day tuple to a julian date (integer value at
noon GMT on that day).

    [ 2013 1 1 ] ymd2jd → 2456294

* jd2ymd

Converts a julian date to year-month-day.

    2456294 jd2ymd → [ 2013 1 1 ]

* jd2dow

Extracts the day of the week from a Julian date (0 = Sunday).

    [ 2022 6 13 ] ymd2jd jd2dow → 1

* lastdayofyear

Calculates the last day of the year (0 = Sunday)

    2009 lastdayofyear → 4

* islongyear

Returns `true` for an ISO8601 long year.

    2009 islongyear → true

* yearweeks

Returns the number of ISO8601 weeks in the year. (52, 53 if
`islongyear`.)

    2009 yearweeks → 53

* ymd2isoywd

Converts a year-month-day tuple to an ISO8601 year-week-day tuple.

     [ 1978 1 1 ] ymd2isoywd → [ 1977 52 7 ]

* y2easter

Given a year, returns the year-month-day tuple for Gregorian Easter.

    2022 y2easter → [ 2022 4 17 ]

## iterables

This is basically for treating arrays as lists, and mangling arrays
into dicts and back.

* keys

Given a dict, return an array of the keys.

    << 1 (a) 2 (b) 3 (c) 4 (d) >> keys → [ 1 2 3 4 ]

* values

Given a dict, return an array of the values. (This will probably be in
the same order as `keys`.)

    << 1 (a) 2 (b) 3 (c) 4 (d) >> values → [ (a) (b) (c) (d) ]

* toset

Given an array, deduplicate it and return a dict of (array-value →
true) pairs; the opposite of `keys`.

    [ 1 2 3 4 ] toset → << 1 true 2 true 3 true 4 true >>

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
value, and that value separately.

    [ 1 2 3 ] apop.left → [ 2 3 ] 1

* apop.right

As above but for the final value. `apop` is an alias.

    [ 1 2 3 ] apop.right → [ 1 2 ] 3

* all

Given an array and a function, return `true` if all of the values of
the function applied to a list member are true, otherwise `false`.

    [ 1 2 3 4 ] { 0 gt } all → true
    [ 1 2 3 4 ] { 1 eq } all → false

* any

Given an array and a function, return `true` if any of the values of
the function applied to a list member are true, otherwise `false`.

    [ 1 2 3 4 ] { 0 eq } any → false
    [ 1 2 3 4 ] { 1 eq } any → true

* listmax, listmin

Given an array, return the maximum or minimum value in it. (This uses
the non-standard two-argument `max` and `min` extensions in
GhostScript.)

    [ 6 4 4 5 2 4 ] listmax → 6
    [ 4 3 3 2 3 6 ] listmin → 2

* unique

Given an array which may have duplicate elements, return an array with
no duplicates. (In arbitrary order.)

    [ 1 1 2 3 4 4 4 ] unique quicksort → [ 1 2 3 4 ]

* reverse

Given an array, returns that array reversed.

    [ 1 2 3 4 ] reverse → [ 4 3 2 1 ]

## maths

* genprimes

Generate an (unsorted) list of prime numbers less than or equal to the
parameter. (Often you'll then want to quicksort them.)

    20 genprimes quicksort → [ 2 3 5 7 11 13 17 19 ]

* isprime

Return a boolean value indicating whether the integer argument is
prime.

    67 isprime → true

* primefactor

Generate a dict of prime factors and their powers.

    75 primefactor → << 3 1 5 2 >>

* nthprimelimit

Calculate a number which is guaranteed to be no lower than the nth
prime. This uses the algorithm of Barkley Rosser as given in Explicit
Bounds for Some Functions of Prime Numbers, _American Journal of
Mathematics_, Vol. 63, No. 1 (Jan., 1941), pp. 211-232; the output
will typically be fed to `genprimes`.

    25 nthprimelimit → 110

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

* safestring

Replaces the string on top of the stack with an unlinked copy, so that
you can change the copy without changing the original.

    (abcdef) safestring → (abcdef)

## file

* readlines

Given a text filename, returns an array of strings, one per line in
the file.

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
