---
title: "Do not use unsigned for non-negativity"
---

It is common to find C/C++ code using unsigned types for no better reason than to annotate non-negativity of an integer. [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Res-nonnegative) and [Google Style Guide](https://google.github.io/styleguide/cppguide.html#Integer_Types) advise against that, but do not elaborate enough why.

"Unsigned integers" is a misnomer that clearly makes developers feel some sort of obligation to substitute an unsigned type whenever the value can never be negative. In reality, there is no such obligation, and on the contrary, doing so produces more error-prone code.

Representing integers is not even the main purpose of unsigned types; their purpose is to represent machine words. There would be a lot less confusion if they would be named something like `byte`, `word`, `dword` etc.

### Reason 1. Unsigned is more than different range, it's also different arithmetic

When working with arithmetical numbers like quantities, counts and measurements, we almost always expect them to follow regular arithmetic in which the invariant *x - 1 < x < x + 1* is always true. Signed types behave like that. The main reason signed overflows and underflows are undefined behavior is to guarantee that invariant is either true or the entire program is ill-formed.

Unsigned types however use modulo arithmetic, which does not have such invariant. Documenting an array index `i` as non-negative might be a good intent, but do you also want `i - 1` to be sometimes greater than `i`? Even experienced coders get caught by this.

Pros of using unsigned for non-negativity are negligible, cons are runtime errors easily missable during a code review. It is a bad trade.

### Reason 2. False sense of security

Unsigned types do not really prevent negative values; instead of receiving -1, the code will simply receive e.g. 4294967295 or 18446744073709551615. Both -1 and 18446744073709551615 are equally likely because they will be produced by the same bug.

If anything, unsigned values are harder to validate: a signed array index can be checked with `assert(x >= 0)`, but for an unsigned index every value is technically valid. Unsigned types also trick the reviewers to ignore negative cases as impossible, while in reality they should be paying *more* attention to negativity (due to missing range asserts), not less.

There already is a non-enforcing way to annotate value range: documentation comments. They hold the same enforcing power as unsigned types: none, but they do not subtly change arithmetic and can document more conditions than mere non-negativity.

### Reason 3. Unsigned is poor tool for the job

What if the value also can never be 0? What if it must never be greater than 63? By repurposing unsigned for range annotations, one is essentially making a poorly planned attempt at statically typing the range. In the end of the day, one still needs range checks, asserts, and tests, regardless if signed or unsigned type was used.

If you really want to go down the rabbit hole of stronger typing, get a library with an integer subrange type. It will be self-documenting, it will avoid unexpected modulo arithmetic, and it will help to catch bugs not introduce them.

### Reason 4. Potentially cripples static analysis and optimization

Because signed overflow is undefined, it gives compilers freedom to issue warning or abort the program e.g. `-fwrapv` in Clang/GCC. Signed allows static analyzers to easily point finger at every place where overflow may happen. Optimizers are allowed to assume, for example, that an incremented value will only go one direction.

In unsigned world, overflow is well-defined, so the compiler is not allowed to do anything about it. With unsigned types it is pretty hard for a static analyzer to judge whether a possible overflow should be reported or not, and optimizers can not make any easy predictions.

### Unsigned `size_t` was a design mistake

Unsignedness of container sizes and subscripts is believed by many C++ committee members to be a design mistake:

* "[Subscripts and sizes should be signed](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1428r0.pdf)" by Bjarne Stroustrup
* [2013 Interactive Panel: Ask Us Anything](https://channel9.msdn.com/Events/GoingNative/2013/Interactive-Panel-Ask-Us-Anything) by Herb Sutter, Bjarne Stroustrup, Andrei Alexandrescu, Stephan T. Lavavej, Chandler Carruth, Sean Parent, Scott Meyers, Michael Wong (long video; scroll to 12:10, 42:40, and 1:02:50)
* "[Signed and Unsigned Types in Interfaces](https://www.aristeia.com/Papers/C++ReportColumns/sep95.pdf)" by Scott Meyers

There was a period when computers used 16/32-bit addressing with RAM sizes well over the maximum integer. Unsigned sizes were often a necessary choice that won 50% more address space. But 64-bit code does not need those tradeoffs. C++20 already has `ssize()` that returns a signed type.

One should not uncritically throw `size_t`/`size_type` all over the code just because the standard library chose unsigned.

### * * *

In one of the interviews, the author of Java programming language has recalled [why Java does not have unsigned types](https://stackoverflow.com/questions/430346/why-doesnt-java-support-unsigned-ints): because it would make core language more complicated: most programmers are not aware there can be more than one arithmetic, and when opting for unsigned rarely expect their numbers to start behaving differently. I.e. same reasons. (Though banning unsigned types entirely from JVM does seem unnecessarily harsh.)

Another good insight that the defining quality of unsigned types is modulo arithmetic, not range, is the question [why floating point types do not have unsigned equivalents](https://stackoverflow.com/questions/512022/why-doesnt-c-have-unsigned-floats). Though unsigned floats are possible, without useful math defined on unsigned floats there is no point to invent them.

### How to choose between signed and unsigned

If you think of the value as of a common integer, if arithmetical operations make usual sense (e.g. decrement produces a lesser value), you're already thinking in terms of signed type.

It does not matter if the value can never be negative; the intermediate results of arithmetical operations may still be. One can't know which operations other programmers will do with such non-negative value:

* Negative array length does not make sense, but subtracting one length from another will produce negatives.
* Negative array index will never be used for subscript, but `for(;i>=0;--i)` does use it in a comparison.

Use unsigned for values that are not integers and/or not subject to arithmetic:

* Bit flags and bit masks
* Opaque memory (`uint8_t*`)
* Constant 1-8 byte sequences e.g. magic numbers in file format headers, components of GUID
* If one legitimately needs 2^N modulo arithmetic
* For extra range (carefully)
