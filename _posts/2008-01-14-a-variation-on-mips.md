---
layout: post
title: A Variation on MIPS
---

Parallel ISA (PISA) Overview
----------------------------

Uses PC-indirect addressing to specify 'registers'. Meant to easily
enable out-of-order execution and multiple-issue logic in
hardware. Otherwise exactly like MIPS. Pronounced as "pizza."

The modifications are to the source and destination registers. Source
registers are not addressed directly, but rather by providing the PC
offset to the instruction whose result will be used. The destination
register field is removed.

Example:

    addiu   $0, 5   ; x = 5
    addiu   $0, 4   ; y = 4
    add     -1, -2  ; adds x + y

Hardware can now easily determine that the first two loads can happen
in parallel --- or out of order --- but that the addition depends on
the results of the loads. The add cannot be issued until both loads
complete. Essentially, the ISA makes dependencies between instructions
very clear.

Register File
-------------

Essentially a "cache" of registers. Because each entry needs to keep
track of the PC of the instruction that wrote it, the register file
needs to keep a tag record for each entry. This will incur a much
higher hardware overhead in the register file. A direct-mapped
approach is used to reduce this penalty.

To support parallel operation two additional bits are needed for each
entry:

- Valid bit: because the register file is now essentially a cache, a
  valid bit is needed for each entry to ensure that it is valid data.

- Pending bit: when an instruction is issued the destination register
  entry will have the pending bit set high. Any instruction down the
  line that depends on this register entry will be stalled until the
  pending bit is lowered again.

Non-sequential code
-------------------

This approach works well for sequential code, but begins to break down
when loops and branches are used. Take this example:

    addiu   $0, 1
    addiu   $0, 5       ; x = 5
    
    loop:
    blez    -1, done    ; if( x==0 ) goto done
    subi    -2, -3      ; x = x-1
    j       loop
    
    done:

The `blez` branch, because it only checks the result of the `ldi 0x05`
instruction, will never be taken. The `subi` instruction stores its
result at a +1 offset to the `blez`, which the branch does not check.

This issue gives us the following addition to the instruction set. The
`rd` field of the MIPS ISA, previously unused in this implementation,
will now be used to store an optional destination entry in the
register file. The above example can be rewritten as:

    addiu   $0, 1
    addiu   $0, 5, +2   ; x = 5, store into PC+2
    
    loop:
    blez    +1, done    ; if( x==0 ) goto done
    subi    0, -3       ; x = x-1
    j       loop
    
    done:

The compiler should assign the branch condition to inspect the result
of the last instruction within the loop to assign to the register in
question. The last instruction <em>before</em> the branch evaluation
should be compiled to be written to the same PC as the former
instruction.

How does this affect the parallel operation of the processor? It
should have no effect on the operation and should require minimal
additional hardware.  Previously, without the destination register
field, instructions were essentially writing to offset `0`. All that
has changed is that instructions can now write to other offsets. The
valid and pending bits will still ensure correct parallel operation of
superscalar implementations.

This approach has the disadvantage of being taxing on
compilers. Whether this proves to be a major issue or not will need to
be seen.

Disadvantages
-------------

This approach suffers from another disadvantage: the inability to
reference the result of an instruction that is at a greater offset
than `N`, where `N` is the register file size. Because the register
file uses the PC of an instruction to store entries, an instruction
more than an offset of `N` away from another cannot use the result of
the latter. This is an inherent limitation of the instruction set.

A workaround is to write a value that must be accessed later to
memory. This might lead to an increased number of memory accesses
throughout the program, which will lead to decreased performance.

Another simple solution is to simply increase the size of the register
file. Though this would lead to increased hardware costs and might
lead to longer delays in register file reads, this would solve the
problem somewhat.

Interestingly, the problem could also be alleviated by adding a second
layer register file cache, much like adding a second layer data
cache. This would offer the benefits of making register entries
available for longer periods, but has the disadvantage of making
program flow harder to predict. A compiler would need to keep track of
the simulated cache state to determine if a register entry will still
be available at a later point in the program.
