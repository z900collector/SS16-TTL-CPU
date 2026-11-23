# Instructions #

Draft As of 2026-11-23


## Stack Instructions ##

### POPF ###

The POPF instruction is used to POP the value off the stack into the Flags Register. The data travels via the [RBUS](/Registers/RBUS.md)

The format of the instruction is:

*POPF*

The instruction takes no arguments.

### POP ###

The POP instruction is used to return the top value off the stack into the specified Register. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction takes a destination register referred to as "Rd"

The format of the instruction is:

*POP Rd*

"Rd" can be R0 to R7.

### PUSHF ###

The PUSHF instruction is used to save the contents of the "Flags" Register onto the top of the stack. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction does not take any additional parameters

The format of the instruction is:

*PUSHF*


### PUSH.md ###

The PUSH instruction is used to save a value from the specified Register onto the top of the stack. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction takes a "source" register referred to as "Rs"

The format of the instruction is:

*PUSH Rs*

"Rs" can be R0 to R7.

### RSP ###

The RSP instruction is used to reset the stack pointer back to 0x000. The contents of the RAM are unchanged.

The instruction has no arguments.

The format of the instruction is:

*RSP*


