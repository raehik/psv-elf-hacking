Stack reallocation
==================

Say you're patching an executable to fix some problem. You eventually come to
realize that an array was poorly sized, and if you could increase its length,
you could resolve your problem. The kicker: the array is allocated on the stack.

This is challenging from a machine code patching standpoint. You're unable to
add instructions, because it would throw off all the absolute offsets in the
file. You can't just change some static length, because the problem is the stack
layout itself.

Here I demonstrate a method for adding unused stack memory in some function via
in-place instruction patching. In short, we enlarge the stack frame, then
rearrange it as we please, relocating local variables to elsewhere in the stack
frame, in order to give us a block of unused memory anywhere we want -- for
example, at the end of an array that is too small.

It's aimed at the Vita, meaning ARMv8 Thumb and GCC compilation conventions, but
the idea should work with any machine code that has matching stack usage
concepts: a stack frame managed at the start and end of functions, and local
variables based off a stack pointer.


Overview
--------

1. enlarge stack frame
2. relocate any stack arguments
3. relocate any stack locals in the way of your wanted memory
4. use the newly-prepared memory (e.g. patch some length args)


Enlarge stack frame
^^^^^^^^^^^^^^^^^^^

Functions allocate stack space by subtracting a set value from the stack pointer
``sp`` at the start. Disregarding some call convention details, this gives them
that amount of stack-allocated memory. This memory is then accessed via positive
relative offsets from ``sp``. For example, say we want 8 bytes of stack memory.
At the start of our function, we write::

    sub sp, #0x8

We could perhaps place 2 4-byte integers at ``sp+0x0`` and ``sp+0x4``
respectively. We would read them into ``r0`` like so::

    ldr r0, [sp,#0x0]
    ldr r0, [sp,#0x4]

The stack pointer remains unaltered throughout the function, so you always know
where a given stack local is. Disassemblers can usually figure it out a rough
stack frame layout from machine code, with some thinking.

At the end of the function, the stack pointer is reset to its original
position::

    add sp, #0x8

Vita executables seem to align stack frames to set sizes. One reason is probably
so that ``add sp``, ``sub sp`` instructions are always 16-bit instructions, a
cool ARM Thumb feature that is intent on ruining reverse engineers' fun. These
usually heavily limit the permitted immediates.

For an example, let's say your function allocates ``0x10c`` bytes of stack
space. We would enlarge the stack frame to the maximum available ``0x1fc`` like
so::

    sub sp, #0x10c -> sub sp, #0x1fc
    add sp, #0x10c -> add sp, #0x1fc

I say "maximum available" because that's the largest immediate we can use in
this case. Note that you can use expressions in assembly, so you can
conveniently write it like this instead::

    sub sp, #0x10c + 0xf0
    add sp, #0x10c + 0xf0


Relocate stack arguments
^^^^^^^^^^^^^^^^^^^^^^^^

By resizing the stack frame, you've added a bunch of unused memory to the end of
the frame. This is fine for locals which, relatively, don't move, but the Vita's
ARM calling convention places function arguments in the stack when they don't
fit into registers. Such arguments are placed at the end of the frame - which
you've just moved. (Actually, I guess they're placed in some frame header, but
I don't know the details.)

This is resolved by relocated stack arguments to their correct position in the
enlarged stack frame. You need to patch all references to stack arguments.

In any case, once I saw a stack argument at ``0x128`` in a function that had
requested ``0x10c`` of stack. I don't know the specifics, but luckily, there's
not much thinking to do::

       ldr r2, [sp, #0x128]
    -> ldr r2, [sp, #0x128 + 0xf0]

Easy enough.


Relocate stack locals
^^^^^^^^^^^^^^^^^^^^^

This was the part that surprised me. Turns out you can just... relocate locals
on the stack. Just patch every reference to their position relative to ``sp``
and you're good.

Say you're trying to enlarge an array allocated in the middle of your stack
frame. You can relocate locals to your newly-freed memory gradually, starting
with the ones closest to the end of the array. Open up your disassembler, check
your stack frame and shift those locals.

I wouldn't be surprised if I just got somewhat lucky with the functions I had to
patch, and having them "just work". You might have complications here. If you
need to do any manual disassembly, I recommend analysing instructions involving
``sp``. I have little guidance for this part, because the first, dumbest thing I
tried worked.

To relocate a stack local quickly using Ghidra, find the next one you want to
relocate in the stack frame (right click memory listing at function definition,
``Function -> Edit Stack Frame...``), right click any instance of it in the
memory listing, ``References -> Show references to [var]``, and shift every
instance as you need.


Use the newly-prepared memory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You might have to patch some arguments sent to functions to increase lengths,
perhaps in the function or in the function's callers. Depends entirely on the
program.


TODO
----

* Might have a ton of unused stack space already due to aligning? Up to 0x50.
  Should see what happens if I move stuff into there lol.
