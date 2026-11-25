# DBUS

The "DBUS" is a 16-Bit wide data bus that links the register file, the user and system memory and the instruction pipelines.

It is the primary path where instructions are fetched from Code RAM and passed into the Instuction Latches ready for decoding and execution.

User data primary travels along the [RBUS](RBUS.md), this allows Instruction fetching to occur in parallel with data traversing the RBUS.
