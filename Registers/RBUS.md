# RBUS #

The "RBUS" is a 16-Bit wide data bus that links all the registers together and is also connected to:

* The Stack Pointer (SP) Module.
* One side of the Arithmetic and Logic Unit (ALU).
* The Program Counter (PC).

The purpose RBUSs is to provide a separate data bus to the [DBUS](DBUS.md) so data can move between registers while other instructions are executing in the Instruction Pipeline.

The RBUS can idependantly move data from Register to Register, Register to SP (PUSH Rs instruction), SP to Register (POP Rd instruction) present data to one side of the ALU ( the other side is via the [DBUS](DBUS.md) ) and for pushing the flags and poping them back (PUSHF and POPF operations).

