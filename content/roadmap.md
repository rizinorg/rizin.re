 Concrete high-level feature areas and changes.

# 0.10

* Rewrite RzNum to support proper formulas, bitvectors, floats, and so on
* Support STABS (pre-DWARF) debug information loading
* Refactor native debugger
* Add support for proper preprocessor in the type parser
- Add support for Texas Instruments TMS320 family beyond C6x: C2x, C5x (both legacy and new), C28x, improve C6x support
- Add RzIL uplifting for Texas Instruments TMS320 family
- Add RzIL uplifting for RISC-V, Infineon C166, Motorola M68k
- Improve use by the AI agents and add an MCP server

Full milestone is at https://github.com/rizinorg/rizin/milestone/29

# 1.0

- Complete migration to RzArch - merge disassembly, assembly, analysis, parsing, uplifting, etc into one plugin for each architecture
* Complete migration from ESIL to RzIL for all supported architectures and features
* Add KB (Knowledge Base) support for storing metainformation in logic fact-based form
* Refactor and merge various visual modes
* Remove concept of the "block" in favor of direct transparent IO access
* Refactor types to introduce type scope
* Big files loading support
* Remove GPL-only code in favor of LGPL
* Stable and documented API
* Create documentation for the framework structure and all modules
* Create RzIL specification

Full milestone is at https://github.com/rizinorg/rizin/milestone/5
