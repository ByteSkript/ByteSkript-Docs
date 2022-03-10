---
description: A compilable trigger-oriented script file.
---

# Script (bsk)

ByteSkript files ending with the `.bsk` (**b**yte**sk**ript) extension are regular script files, which can contain members.

When running a `.bsk` file,  no code is directly run but the loading process may trigger a load event (e.g. the trigger from `on script load`.)

These files are designed to be part of a larger, long-running program.

ByteSkript files typically go in the `skript/` directory, but may be triggered individually from any system path.

Scripts may contain single-line `// comments` or multi-line `/* comments */`.
