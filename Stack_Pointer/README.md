# Overview #

Place holder for overview of the Stack Pointer logic.

## Theory of operation ##

Unlike a lot of TTL CPU's the SP is a separate "functional Unit" where the memory used for the stack is separate to all other RAM.

The transfer of register contents to and from the SP takes place over the RBUS. 

When a RESET occurs, the SP initializes back to 0xFFFF. The RSP instruction can also be used to reset the SP back to 0xFFFF.

< todo >


## Relevant Instructions ##

* PUSH <Rs>
* POP <Rd>
* RSP
* PUSHF
* POPF
* LDSP <Rd>
* STSP <Rs>

Within the ISA, these instructions are grouped together and they operate on a source Register. If an Immediate value needs to be pushed then it must be moved into a Register and then PUSHed to the stack.

The PUSHF and POPF instructions work on the flags register only.

The RSP instruction is used to reset the SP back to 0xFFFF.

The LDSP and STSP will load the actual SP counter value (RAM address) can be manipulated.


## Interrupts ##

If an interrupt is generated and executes, the current PC value is automatically pushed onto the stack first.

## CALL / RET Login ##

< todo >


## Instruction Decoding ##

< todo >
