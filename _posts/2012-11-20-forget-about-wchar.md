---
title: "Why you can just forget about UTF-32"
redirect_from:
    - /blog/2012-11-20-wchars/
    - /post/wchar-is-useless/
---

Very often when a discussion of encoding strings comes up someone claims 
UTF-32 allows for "fast" character access at the cost of wasting some memory. Unfortunately, wasting memory is the only thing it does.

There is no such thing as random access to characters or character-by-character manipulation
in real-life Unicode text.

UTF-32 gives random access to *code points*, which is entirely different thing than characters.
Here are things that you might think are easier in UTF-32 than in UTF-8, but in fact they are not:

* **Manipulating case**. You can not take a `char` at `str[i]`, change it's case and put it back into `str[i]`.
    Some code points capitalize to several ones (the German letter ß is capitalized as "SS"; some ligatures like ﬀ or ﬁ do not have uppercase and become FF anf FI).
    The Greek letter Σ has two different lowercase forms: "ς" and "σ" depending on context.

* **Counting characters**. Several code points may produce only one character (combining emoji, diacritics, devanagari scripts),
    and one code point should be treated as two (ligatures).
    One string can have two times more code points but be shorter than another.
    Unicode is ultimately variable-length thing.

* **Drawing or interaction**. You can not draw code points individually for the same reasons.
    Mouse clicks on a displayed glyph may pick a range of code points, not just one index.
    Advancing text cursor in response to right arrow `→` key is much more complicated than incrementing a position number.

* **Searching**. Searching for even an ASCII character like `'e'` by looking for its code point may falsely succeed when it's preceeded with a diacritic.
    And it will fail to find `'f'` when it's composed into a ligature `'ﬂ'`.

* **Comparison**. The only comparison you can do by looking at individually accessed code points is equivalent to `memcmp`.
    For example, in many languages users want characters with diacritics to match precomposed equivalents.

* **Ordering and sorting**. Even “plain” U.S. English text that you would hope to be restricted to ASCII
    will contain fancy characters (think [Beyoncé](http://en.wikipedia.org/wiki/Beyonce))
    and therefore comparing code points will not give you proper alphabetic ordering.

The random access to characters does not exist, because the word "character" is not what people usually think. Doing anything meaningful with text requires parsing *grapheme clusters*, which have variable lengths regardless of which encoding is used.

Choosing UTF-16 and UTF-32 will only achieve wasted memory and potential endianess conversions. UTF-16 is so popular because many years ago people thought that 2 byte UCS-2 will be enough for every letter of every alphabet, and now many important systems (Windows NT, Java, ICU) are stuck with backward compatibility. And UTF-32/UCS-4 is practically stillborn.

P.S. Relevant link: [UTF-8 Everywhere Manifesto](http://utf8everywhere.org)
