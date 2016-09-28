- Feature name: library-architecture
- RFC pull request: #0002
- Issue: N/A

# Summary
[summary]: #summary

Component library architecture proposal

# Motivation
[motivation]: #motivation

KiCad has some good library features when compared to other EDA tools, and also some drawbacks. This RFC proposes a general layout for organization of component (symbol) libraries.

The defined library architecture should be:

* Extensible / forwards compatible (as far as possible)
* SCM friendly
* Lean in terms of file size (as far as is practicable)
* Simple to parse
* Self descriptive

# Detailed design
[design]: #detailed-design

## Symbol Libraries

KiCad symbol librares are each split into two files:

* .lib (description of graphical representation of symbol)
* .dcm (documentation)

This is messy.

On a positive note, the disconnect between Symbols and Footprints should be maintained - symbols can be linked to one-or-more footprints via association filters.

**Hierarchy Proposal:**

Similar symbols should be grouped together with their descriptions in a single library file (proposed file format: *.star* - because whimsy). The *ALIAS* concept can be extended to provide greater flexibility.

A library file contains the following object types (in addition to library metadata):

* `COMPONENT` - A top level component 
* `ALIAS` - Each component has one or more aliases. Aliases are functionaly equivalent in that they can share the same graphical representation
* `SYMBOL` - Each component has one or more symbols. Symbols are graphical representations of the components and while visually different, must be functionally equivalent
* `PART` - Each symbol may be split into one or more parts, which allow separation of the symbol in the schematic diagram
* `PIN` - Self explanatory
* `TEXT` - Graphical text
* `SHAPE` - Drawing objects (multiple e.g. arc, polyline, etc)

```
Library.star
{
    Metadata    // Library metadata
    {
        Description
        ...
    }
    Components []   // Array of top-level component
        component_1
        {
        
            <top_level_cmp_data>    // Other data TBD
        
            Aliases []  // Array of ALIASES that share the same pinout
                alias_1
                {
                    name: string
                    description: string
                    keywords: string, csv
                    datasheet: link
                    spice_model: link       // Link to a SPICE model? (or should it be spAce model?)
                    footprint_filters []    // Array of footprint filters for this ALIAS
                    {
                        filter_1
                        ...
                        filter_n
                    }
                    custom_parameters       // Any custom parameters specific to this ALIAS
                    {
                        param_1 : value_1
                        ...
                        param_n : value_n
                    }
                }
                alias_2 {}
                ...
                alias_n {}
        Symbols []  // Array of SYMBOL objects
            symbol_1
            {
                parts: int          // Number of PARTS in this symbol
                <metadata>          // Other SYMBOL metadata (as required)
                Pins []             // Array of pin objects
                    pin_1
                    {
                        name: string
                        pads: []    // Multiple pads can be associated with a single pin
                        part: int   // Which PART is this PIN associated with?
                        style: string
                        hidden: bool    // (Support hidden pins?)
                        inverted: bool
                        position: point
                        length: float
                        <other data>    // TBD
                    }
                    ...
                    pin_n {}
                Text []             // Array of TEXT objects
                    text_1
                    {
                        text: string
                        pos: point
                        part: int
                        <other data>
                    }
                    ...
                    text_n {}
                Shapes []           // Array of SHAPE objects
                    shape_1
                    {
                        type: string
                        part: int
                        <shape-specific-data>
                    }
                    ...
                    shape_n {}
            }
            ...
            symbol_n {}
        }
        ...
        component_n {}
}
```            
        
*Note: This proposed library format is agnostic to the actual file encoding*

# Improvements
[improvements]: #improvements

The proposed hierarchy makes some key improvements over the KiCad model:

* Library metadata - The .star file has its own metadata (e.g. description of library contents)
* Increased ALIAS flexibility - Each ALIAS gets its own footprint associations
* Custom parameters - Library can now house arbitrary key:value pairs for each ALIAS
* Multiple graphical representations - Multiple graphical representations of the same component, shared between all aliases
* Multiple pads per pin - For components with (for e.g.) many GND pads that must be connected together, this can be achieved with a single graphical PIN object

# Drawbacks
[drawbacks]: #drawbacks

* Library size may become unwieldy - care will need to be taken to ensure libraries are quick to parse and write.

# Alternatives
[alternatives]: #alternatives

Single component to single footprint mapping - reduces flexibility and drastically increases size of library

# Unresolved questions
[unresolved]: #unresolved-questions

Actual file format TBD.
