---
title: "Some libraries should ship with mocks"
---

Most complex or volatile libraries should ship with two extra things: an abstract interface or protocol (whatever the language has), and a mock that implements it. Standard libraries of programming languages should lead by example, as engineers often look up to them when designing libraries.

This should become normal practice.

Today, every serious codebase ends up mocking the same dependencies: HTTP clients, filesystems, clocks. Each team rewrites roughly the same test doubles, with slightly different assumptions and slightly different bugs. This is wasted effort.

A library-provided mock removes that duplication. It stays in sync with the real API, minimizing effort needed to do a major version upgrade, encouraging upgrading more often. It encodes the behaviors that actually matter. The people who designed the library already know how it should be faked, and usually they know best.

The abstract interface also helps the library author define and document the testable surface area: it clearly separates what’s public API from what’s an implementation detail.

And the mock is a chance to inform users about lesser known scenarios and possibilities, which they may otherwise forget. How many bugs slip into production not because the project had no tests, but because the first implementation didn’t realize a test case was needed?

The lack of this practice quietly raises the cost of testing. Before writing a test, one must first decide how to mock a complicated API, which dependency injection practice or framework is needed, whether all of that is even worth the effort. Not only are people lazy, but many projects evolve from an untested MVP/prototype, especially when built by early enthusiasts who just want something that works and don’t think about (or don’t yet know) good testing practices. Many tests already die at that step. Dependency injection and inversion of control still aren’t ubiquitous—not because people doubt them, but because they’re expensive to adopt.

Consider an HTTP client. I think most software nowadays need one, so everyone ends up mocking it. Timeouts, retries, partial failures, slow responses. Pretty sure everyone misses a few HTTP corner cases while reinventing their own mock. Entire libraries exist just to fill this gap. The HTTP client library authors could have done all that much better than most of their users, because they’re more familiar with the domain’s intricacies.

Or the filesystem. Even “simple” code wraps it to avoid touching the real disk. In-memory filesystems are reinvented endlessly, usually incompletely. Does your filesystem mock know that the filename may not end up identical to what was given when creating it (e.g. due to Unicode normalization), or did you not know it was possible? The filesystem library authors would know: they worked with many filesystems for years. Just seeing that functionality in the mock would be enough of a hint to consider having a case for that.

Or time. Ask a junior engineer--or a coding agent--to produce code that depends on time, and you will likely see tests trying to `sleep()`. Because the tightly coupled default makes this a more straightforward solution to come by.

The costs are real. Maybe not much for each one of us independently, but if a dependency has a million other users, we can fairly multiply that by a million. Why keep reinventing the same wheels?

Of course maintaining a mock is extra work for library authors, many of whom make open source for free. That’s a valid objection, but I think it misses something. If a larger product will need your library, then it will also want comprehensive testing only possible with a mock for that library, but if you only provide one of those two, isn't that kinda like publishing unfinished work? A season without a finale. You are a software user too and it's in your interest to help software come out better tested.

Unfortunately, it’s not as simple as saying, please accept a contribution that adds an interface and a mock. The API has to be designed that way from v1. If v1 exposes only implementation details, there will be thousands or millions of users depending on it, and then radically changing the design will only make them complain and refuse to upgrade.

If libraries made testability a first-class concern, testing would stop feeling like a separate discipline. It would become the default way code is written.

Naturally, all of this only applies to libraries that access volatile information, external state, or are so complicated that it takes 500 LOC just to construct a value even without using the library to compute it. I don't remember wanting a mock for Zlib, for example.
