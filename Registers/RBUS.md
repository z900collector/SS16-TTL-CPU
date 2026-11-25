# RBUS

The "RBUS" is a 16-Bit wide data bus that links all the registers together. The purpose of the RBUS is to provide a separate data bus to the [DBUS](DBUS.md) so data can move between registers while other instructions are executing in the Instruction Pipeline.

The RBUS is used for a variety of tasks:

- Idependantly move data from Register to Register. Includes [TRAN](/ISA/isa.md#TRAN) and [XFER](/ISA/isa.md#XFER) instructions.
- Register to SP ([PUSH](/ISA/isa.md#PUSH) Rs instruction).
- SP to Register using [POP](/ISA/isa.md#POP) Rd 
- PC to Register.
- PC to SP via the [CALL](/ISA/isa.md#CALL) instruction.
- SP to PC [RET](/ISA/isa.md#RET) instruction.
- Pushing the flags and poping them back using [PUSHF](/ISA/PUSHF.md) and [POPF](/ISA/isa.md@POPF).
- and to present data to one side of the ALU with the [DBUS](DBUS.md) used to feed the "A" side of the ALU.

