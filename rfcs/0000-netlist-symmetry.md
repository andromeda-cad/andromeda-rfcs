- Feature name: netlist-symmetry
- RFC pull request: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

Make the _netlist_ the master document during a session.  Allow any
View (program) in the suite alter the netlist and propagate changes to
the others _including the schematic_.

# Motivation
[motivation]: #motivation

This design would allow very advanced behaviors like pin swap, circuit
changes from the PCB side, scripted changes, back-importing
parasitics, and reverse engineering workflows to effect changes to the
schematic using a unified interface.

# Detailed design
[design]: #detailed-design

Some EDA programs like KiCAD treat schematics as the absolute master
document, that makes it tricky to propagate back changes from the PCB
into the schematic.  This RFC proposes to make the _netlist_ be the
master document during an editing session, being able to switch which
program can edit it or not.

## Netlist

What this RFC calls _netlist_ is a data structure that contains all
the components with their properties and pins and all _nets_. Nets are
lists of pins that are electrically connected together, plus
additional properties like net class, tags and attributes.

## Forward workflow

During normal operation, the schematic capture program will modify the
netlist as the user draws the circuit, adding connections when the
user draws wires, removing them when the user deletes them. The
schematic capture program can then push this netlist state to the PCB
layout program, which will import the necessary footprints and signal
connections using rats nest wires until the user routes all
tracks. The PCB layout program is not allowed to change any of the
connections, and attempting to route tracks that bridge two nets will
result in a DRC violation.

## Implementing Symmetry

Pressing a mode switch in the PCB layout program would allow it to
change the netlist connectivity, just like drawing wires in the
schematic capture program, the user can draw or delete ratsnest lines
and then propagate changes back to the schematic.

The most basic, stupidest algorithm that will work for this is to
implement the concept of rats nest in the schematic side too. That is,
when the schematic is (temporarily) not in control of the netlist,
connections that do not match the updated netlist are disconnected,
and the correct connections are rendered as straight lines between the
pins that need to be connected.

Optionally, programs/views that modify the netlist can _annotate_ the
netlist update to enhance the behavior of the receiving program. For
example: if the PCB layout program does a pin swap, it can add an
annotation to the netlist update saying which component had it's pins
swapped, and which pins. Then the schematic program can try and do the
most useful thing, for example, for a boxy symbol it could swap the
location of the pins in the symbol so that the wires don't get
disconnected, for 2 pin components it could just flip or rotate, or it
could just break connection near to the swapped pins and do the
ratsnest right there, according to use preference.

The receiving program is allowed to make a best effort to guess the
least astonishing way to deal with an update.

## Reverse engineering

An extreme case that shows the flexibility of this approach is reverse
engineering a PCB from photographs. the user could import the
different layers of the board as uncommitted copper areas, then place
footprints on the corresponding places and use a flood fill algorithm
to rebuild the board connectivity, that will generate a netlist out of
the physical connections between the footprint pins. The user can set
the refdes and value of every component on the board, then set which
symbol corresponds to it.

Back importing this netlist to the schematic would produce something
similar to what happens when one imports a schematic on a blank
board. all components would appear spread around with rats nest lines
connecting them. The user can then drag components around and
reconstruct the schematic.

# Drawbacks
[drawbacks]: #drawbacks


# Alternatives
[alternatives]: #alternatives

# Unresolved questions
[unresolved]: #unresolved-questions

* How to deal with multi-page schematics, and rats nests spanning
  multiple pages?
* How should changes be annotated?
* How smart should the schematic netlist import algorithm be?
