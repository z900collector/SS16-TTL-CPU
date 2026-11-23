# Instructions

Draft As of 2026-11-23

## Miscelaneous Instructions

### NOP

The NOP instruction is a no operation instruction that advances the program counter by 1 and nothing else. It is typically used to delay or fill in time by using clock cycles without altering any state within the CPU.
 
The NOP instruction takes no arguments.

The format of the instruction is:

*NOP*

### INT

The INT instruction is used to jump into the interupt table at location 0x0038 which is identical to the Z80 Mode 1 interrupt.

Provision is being made to specify the address offset similar to the 8080/8085 processor.

The INT instruction takes 1 argument (but it is ignored at this stage).

*INT x*


### EI

The EI instruction is used to Enable all maskable Interrupts. 

The EI instruction takes no arguments.

The format of the instruction is:

*EI*


### DI

The DI instruction is used to Disable all maskable Interrupts. 

The DI instruction takes no arguments.

The format of the instruction is:

*DI*


### IN

The IN instruction is used to input an 8-bit value from an I/O sub-system. It requires an 8-bit port to read from and a destination Register to write the value to.  The upper 8 bits are zero'd on write.

The iregister is specified in the upper 8 bits of the instruction and the port is specified in the lower 8-bits of the instruction.

The IN instruction takes two arguments.

*IN <Port>,<Rd>*


### OUT

The OUT instruction is used to output an 8-bit value to the I/O sub-system. It requires an 8-bit port to write to and a value to write. 
The 8-bit value is obtained from the low byte of the specified source register. The port is specified in the lower 8-bits of the instruction.

The OUT instruction takes two arguments.

The format of the instruction is:

*OUT <Rs>, <Port>*

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

LDSP <Rd>
STSP <Rs>

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

