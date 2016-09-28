- Feature name: file-formats
- RFC pull request: #0003
- Issue: Picking a file format is a **BIG DEAL**

# Summary
[summary]: #summary

AndromEDA* will have a non-zero number of file types to read/write/mangle. As far as possible, these file types should follow a consistent encoding format.

This RFC proposes that JSON be selected as the file encoding standard for native AndromEDA* file types.

\* name pending

# Motivation
[motivation]: #motivation

* Consistent file types reduces the amount of code that needs to be written. This cuts down on the amount of code that subsequently needs to be rewritten.
* Use of an extensible supported format means that (hopefully) it will not need to change multiple times (cf KiCad)
* Human-readable format means that there's at least a chance that AndromEDA* files are somewhat useful for SCM tools
* Qt has good JSON builtin tools

# Detailed design
[design]: #detailed-design

The file encoding should meet the following requirements:

* Small file-size footprint
* Human readable (this also helps with SCM compatibility)
* Well-known (meaning that parsers and encoding tools are available)
* Simple yet robust syntax
* Forward-compatible (no hard-coded sequences of data)
* Flexible / extensible - ability to encode complex data
* Consistent - File must remain unchanged through multiple load/save sequences (if no actual changes are made)

Obviously the actual data hierarchy within each file type will differ, but these data should be encoded using a consistent schema.

# Drawbacks
[drawbacks]: #drawbacks

* Potential to bloat file size with extra characters required for data tagging and wrapping
* Perhaps there are data types that are hard to encode with JSON?

# Alternatives
[alternatives]: #alternatives

* [MessagePack](http://msgpack.org/index.html) - Much smaller files than JSON, with wide ranging support. However, not human readable, so SCM integration would be terrible.
* XML - Larger file size, less readable, some people hate it.
* [S-expression](https://en.wikipedia.org/wiki/S-expression) - Fewer encodable types. Usage could lead to world shortage of parentheses 

# Unresolved questions
[unresolved]: #unresolved-questions

* What file types would actually need to be encoded?
* How fast is Qt at parsing large JSON files? (Also, what is the RAM overhead?)

