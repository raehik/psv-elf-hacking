Vita hardware details
=====================

The Vita uses an ARM Cortex-A9 CPU, which implements the ARMv7-A ISA, and
includes the VFPv3 FPU coprocessor.

Note that Ghidra 10.1's ARMv7 doesn't handle the optional VFP. (I think it's
specifically ARMv7-M.) Because of this, you'll probably want to import Vita ELFs
as ARMv8. Ghidra 10.1 preselects the correct configuration for you.
