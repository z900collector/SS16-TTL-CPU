# Overview #

**DRAFT** -  

This project uses a 16-Bit stack but unlike other projects, the stack is not part of normal user RAM, instead it is implemented as a separate "Functional Unit".

One of the advantages of this is a register value can be PUSHed or POPed to/from the SP relatively independant of other operations and in effect parts of the operation can be done in parallel with other tasks.

The transfer of register contents to and from the SP takes place over the RBUS. 

## Theory of operation ##

### Reset ###

When a RESET occurs, the SP initializes back to 0xFFFF. The RSP instruction can also be used to reset the SP back to 0xFFFF.

### PUSH ###

A PUSH operation requires the Register value to be presented on the RBUS, a signal is raised to latch the bus into the SP unit and a push signal asserted.

#### States ####

* The SP will increment the SP counter
* The SP RAM CS will be asserted as will the latch OE pin and the RAM WR pin.
* Then the OE, CS and WR signals are de-asserted.
* The current value of the SP counter is also latched so it can be read back if required.

### POP ###

THe POP signal will cause the current SP RAM data to be made available on the RBUS so it can be latched back into the required register (including the PC register).

#### States ####

* SP RAM CS asserted and SP RBUS Tri-State buffer direction set.
* Buffer OE asserted, value now appears on RBUS.
* The destination Register needs to set the RBUS direction buffer to input and assert the OE pin.
* Latch the RBUS value into the "A" Latch.
* Tri-state the RBUS buffer
* Assert RBUS free signal.
* SP unit on receiving RBUS Free, disabled the Buffer OE, drops CS pin to the RAM and increments SP by 1.
* SP Unit latches counter value into SP latch.

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
