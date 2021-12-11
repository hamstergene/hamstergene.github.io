---
title: "Do not use unsigned for non-negativity"
---

It is common to find C/C++ code using unsigned types for no better reason than to annotate non-negativity of an integer. [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Res-nonnegative) and [Google Style Guide](https://google.github.io/styleguide/cppguide.html#Integer_Types) advise against that, but do not elaborate enough why.

Clearly, the name "unsigned integers" makes many developers feel like unsigned types and non-negative values are meant for each other. They are not, and that feeling is false: unsigned integers are not quite the same thing as integers, and using them to represent integer values will likely produce worse, more error-prone code.

### Reason 1. Unsigned is more than different range, it's also different arithmetic

When working with arithmetical numbers like quantities, counts and measurements, we almost always expect them to follow regular arithmetic in which the invariant *x - 1 < x < x + 1* is always true. Signed types behave like that. The main reason signed overflows and underflows are undefined behavior is to guarantee that invariant is either true or the entire program is ill-formed.

Unsigned types however use modulo arithmetic, which does not have such invariant. Documenting an array index `i` as non-negative might be a good intent, but do you also want `i - 1` to be sometimes greater than `i`? For example, the array may never be accessed at negative index, but the index has to become -1 in order to stop the loop. Even experienced coders keep getting caught by this.

*Unsigned integers are a bad idea, because 99.99% of the time developers expect integers to follow regular, not modular, arithmetic*.

### Reason 2. False sense of security

Unsigned types do not really prevent negative values; instead of receiving -1, the code will simply receive e.g. 4294967295 or 18446744073709551615. Both -1 and 18446744073709551615 are equally likely because they will be produced by the same bug.

If anything, unsigned values are harder to validate: a signed index can be checked for non-negativity before use, but every unsigned index is technically valid. Seeing unsigned type misleads code reviewers to ignore negative cases as impossible, while in reality they should be paying *more* attention to negativity due to missing range checks, not less.

*Unsigned integers are a dangerous illusion of stronger typing*.

### Reason 3. Poor tool for the job

What if the value also can never be 0? What if it must never be greater than 63? By repurposing unsigned for range annotations, one is essentially making a poorly planned attempt at statically typing the range. In the end of the day, one still needs range checks, asserts, and tests, regardless if signed or unsigned type was used.

Ordinary documentation comments do a better job than unsigned types, with less downsides. Both enforce nothing anyway.

### Reason 4. Hinders static analysis and optimization

Because signed overflow is undefined, it gives compilers freedom to issue warning or abort the program e.g. `-fwrapv` in Clang/GCC. Signed allows static analyzers to easily point finger at every place where overflow may happen. Optimizers are allowed to assume, for example, that an incremented value will only go one direction.

In unsigned world, overflow is well-defined, so the compiler is not allowed to do anything about it. With unsigned types it is pretty hard for a static analyzer to judge whether a possible overflow should be reported or not, and optimizers can not make any easy predictions.

### Reason 5. Frivolous use of unsigned increases the chance of accidentally mixing signed and unsigned

The semantics of mixing signed and unsigned is a minefield:

0. For example, `(int)12 - (unsigned short)25 < (int)32` is true, as expected.
0. However, `(int)12 - (unsigned int)25 < (int)32` is *false*, only because *25* became a slightly larger unsigned type.
0. `(int)12 - (int)25 < (unsigned int)32` looks like a no-brainer to our arithmetical intuition, but its result is *false*.
    * This typically bites when the unsigned is returned from a function, whose author naively reasoned that "the returned number of objects can never be negative", and you forget to double-check all the types.
0. But wait, `(long long int)12 - (int)25 < (unsigned int)32` is true again! All it took is for *12* to became a larger type.
0. If that's not convincing, remember:
    * Type names are often typedefs that can be changed.
    * Integer sizes are platform and compiler-dependent.

Now, knowing all the above, imagine code-reviewing the expression `if (source_index - findTargetIndex() < getSize())`? Doesn't look like a simple piece of code anymore, does it.

This is problem specific to C/C++ though, other languages may be more reasonable.

### Unsigned `size_t` was a design mistake

Unsignedness of container sizes and subscripts is believed by many C++ committee members to be a design mistake:

* "[Subscripts and sizes should be signed](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1428r0.pdf)" by Bjarne Stroustrup
* [2013 Interactive Panel: Ask Us Anything](https://channel9.msdn.com/Events/GoingNative/2013/Interactive-Panel-Ask-Us-Anything) by Herb Sutter, Bjarne Stroustrup, Andrei Alexandrescu, Stephan T. Lavavej, Chandler Carruth, Sean Parent, Scott Meyers, Michael Wong (long video; scroll to 12:10, 42:40, and 1:02:50)
* "[Signed and Unsigned Types in Interfaces](https://www.aristeia.com/Papers/C++ReportColumns/sep95.pdf)" by Scott Meyers

There was a period when computers used 16/32-bit addressing with RAM sizes well over the maximum integer. Unsigned sizes were often a necessary choice that won 50% more address space. But 64-bit code does not need those tradeoffs. C++20 already has `ssize()` that returns a signed type.

One should not uncritically throw `size_t`/`size_type` all over the code just because the standard library chose unsigned.

### * * *

In one of the interviews, the author of Java programming language has recalled [why Java does not have unsigned types](https://stackoverflow.com/questions/430346/why-doesnt-java-support-unsigned-ints): because it would make core language more complicated: most programmers are not aware there can be more than one arithmetic, and when opting for unsigned rarely expect their numbers to start behaving differently. I.e. same reasons. (Though banning unsigned types entirely from JVM does seem unnecessarily harsh.)

Another good insight that the defining quality of unsigned types is modulo arithmetic, not range, may be to ask [why floating point types do not have unsigned equivalents](https://stackoverflow.com/questions/512022/why-doesnt-c-have-unsigned-floats). Though unsigned floats are possible, without useful math defined on unsigned floats there is no point to invent them.

### How to choose between signed and unsigned

If you think of the value as of a common integer, if arithmetical operations make usual sense (e.g. decrement produces a lesser value), you're already thinking in terms of a signed type.

It does not matter if the value can never be negative; the intermediate results of arithmetical operations may still be. One can't know which operations other programmers will do with such non-negative value:

* Negative array length does not make sense, but subtracting two lengths from each other does.
* Array indexes can not be negative during element access, but can be during computations.

Use unsigned for values that are not integers and/or not subject to arithmetic:

* Bit flags and bit masks
* Opaque memory (`uint8_t*`)
* Constant 1-8 byte sequences e.g. magic numbers in file format headers, components of GUID
* If one legitimately needs 2^N modulo arithmetic
* For extra range (carefully)

Unsigned types are so undesirable for manipulating integers, it would be fair to call them a misnomer. There would be a lot less confusion if they were called something like "machine words" or "byte packs" or whatever, anything but not integers.
