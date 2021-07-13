Tools and further documentation
===============================

Decompiling, repacking eboots
-----------------------------

eboots are thinly-veiled ELFs in disguise. Many ELF tools will work just fine,
though some might be confused by the custom field values, and the ELFs are fully
stripped of any debug symbols.

Tools
^^^^^

* TODO: vitasdk toolchain
* TODO: hex editor recommendations (Bless for Linux -- could be better tho...)
* `Ghidra <https://ghidra-sre.org/>`_
* TODO: the VitaHeaders thing, for resolving many NIDs

Docs
^^^^

* `HENkaku wiki <https://wiki.henkaku.xyz/vita/Main_Page>`_: exhaustive
  high-quality low-level Vita documentation, managed by the HENkaku team.
  Hardware & kernel focused. If this site manages to say something contradictory
  to HENkaku's work, we're definitely in the wrong.

* `taiHEN docs <https://tai.henkaku.xyz/docs/>`_: docs for the taiHEN plugin
  framework, for dynamic patching. This site will likely focus on the static
  side -- mostly because I can't find any example usages of taiHEN for doing
  what I want, and static patching is a bit simpler when it works.
