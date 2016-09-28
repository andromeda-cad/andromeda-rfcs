- Feature name: build-system
- RFC pull request: #0001
- Issue: N/A

# Summary
[summary]: #summary

CMake is to be used as the build system for AndromEDA.

# Motivation
[motivation]: #motivation

CMake is cross-platform and supports Qt. Unlike qmake and
[qbs](http://blog.qt.io/blog/2012/02/15/introducing-qbs/), it is well known by
non-Qt developers and not overly specific.

# Detailed design
[design]: #detailed-design

Implementation is as usual for CMake projects. The root of the code repository
shall contain a master `CMakeLists.txt` file controlling the build, and each
component subdirectory contains its own. A `CMakeModules` directory can contain
any CMake modules required that are not shipped with CMake. Build options should
be documented in the code repository's readme.

# Drawbacks
[drawbacks]: #drawbacks

Using CMake adds a build dependency, and it is fairly complicated and unfriendly
at times.

# Alternatives
[alternatives]: #alternatives

- [Bazel](https://bazel.io/) - no Windows support
- [qmake](http://doc.qt.io/qt-5/qmake-manual.html) - tied to Qt
- [qbs](http://blog.qt.io/blog/2012/02/15/introducing-qbs/) - tied to Qt

# Unresolved questions
[unresolved]: #unresolved-questions
