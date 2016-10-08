- Feature name: Language and library choices
- RFC pull request: #0012
- Issue: N/A

# Summary
[summary]: #summary

AndromEDA will use C++11/14 for the primary language.  To minimize library
dependencies, libary components in the STL will be preferred.  If something
can't be satisfied by the STL, well-maintained implementations in Boost will
be used.  Qt will be used for the GUI, but the core components will not depend
on Qt.

# Motivation
[motivation]: #motivation

Qt was chosen because of its broad cross-platform compatibility and good
open-source support.  Since Qt was chosen using C++ is mandatory.  Using C++11
is a good choice due to the major updates that it provided to the language.
C++14 is a minor update, and any features can be used supported in both clang
and GCC.

As far as libraries go, the STL is preferred for library components.  Boost
will be used as a backup to the STL when something either is not in the STL or
the STL implementation is not sufficient for what we need.  The selection of
library components in Boost should be done carefully, many boost components are
poorly maintained.  Finally, if the functionality is not in the STL or Boost
another library may be used with the approval of the reviewers.

# Drawbacks
[drawbacks]: #drawbacks

- Using the latest version of C++ prevents use on older systems that haven't
been updated to C++11/14.
- There may be some shoehorning to satisfy the STL use requirement.

# Alternatives
[alternatives]: #alternatives

- GTK can be used instead of Qt, eliminating the need for C++.
- C++99

# Unresolved questions
[unresolved]: #unresolved-questions
