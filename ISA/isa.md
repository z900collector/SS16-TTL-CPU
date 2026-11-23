# Instructions

Draft As of 2026-11-23


## Stack Instructions

### POPF

The POPF instruction is used to POP the value off the stack into the Flags Register. The data travels via the [RBUS](/Registers/RBUS.md)

The format of the instruction is:

*POPF*

The instruction takes no arguments.

### POP

The POP instruction is used to return the top value off the stack into the specified Register. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction takes a destination register referred to as "Rd"

The format of the instruction is:

*POP Rd*

"Rd" can be R0 to R7.

### PUSHF

The PUSHF instruction is used to save the contents of the "Flags" Register onto the top of the stack. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction does not take any additional parameters

The format of the instruction is:

*PUSHF*


### PUSH

The PUSH instruction is used to save a value from the specified Register onto the top of the stack. The data travels via the [RBUS](/Registers/RBUS.md)

The instruction takes a "source" register referred to as "Rs"

The format of the instruction is:

*PUSH Rs*

"Rs" can be R0 to R7.

### RSP

The RSP instruction is used to reset the stack pointer back to 0x000. The contents of the RAM are unchanged.

The instruction has no arguments.

The format of the instruction is:

*RSP*

### CALL

The CALL instruction pushes the PC+2 value onto the stack and transfers control to a new location specified in the PC+1 location.
The CALL is paired with a RET instruction which POP's the return value off the stack into the PC and begins execution from that location.

The instruction takes a 16 bit absolute memory address as an argument. This value is either specified directly or it resides in one of the registers.

The format of the instruction is:

*CALL <imm>*

*CALL (Rs)*

### RET

The RET (return) instruction causes the top value on the stack to be retrieved and written to the PC latch, execution the continues from that location.

The instruction has no arguments.

The format of the instruction is:

*RET*

### RETI

The RETI instruction performs the same task as the RET instruction however it also clears the Interupt latch.

The instruction has no arguments.

The format of the instruction is:

*RETI*
NOP
INT x
EI
DI

IN <Port>
OUT <Port>

PUSH
POP
PUSHF
POPF

LDSP <Rd>
STSP <Rs>
RSP

ST
XFER
MOV
TRAN

LD
LDM
LDI
LDFL

CCF
CZF
SCF
SZF
COF
SOF
CSF
SSF



ADD
SUB
DIV
MUL
CMP

NOT
XOR
OR
AND

SHR
SHL
ROR
ROL
SHRC
SHLC
RORC
ROLC

DEC
INC
RRC
RLC

BSET
BCLR
BTST


STPC
LDPC

## Jumps and Branching 

Jump operation involve a 16 bit value while branches occur within 8 bit forward or back.

### JZ

*JZ imm*


### JNZ

*JNZ imm*


### JC

*JC imm*



### JNC

*JNC imm*


### JNE

*JNE imm*


### JMP

*JMP imm*

### BRZ <disp>

*BRZ <disp>*

### BNZ

*BNZ <disp>*


### BRC

*BRC <disp>*

### BNC

*BNC <disp>*

### BR

*BR <disp>*

