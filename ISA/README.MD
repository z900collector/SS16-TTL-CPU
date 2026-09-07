# SS-16 Instruction Set Architecture #

# Overview

Currently I am building a 16 bit version of the TTL based CPU, so the instruction word is fixed at 16 bits. This has been influenced by various RISC CPU designs. Eventually I will move to a 32-Bit design once I have actually built a working system.

The design of the ISA has undergone many evolutions as I process through the final details and create Logisim Circuits testing the design.

## Instruction Format

The 16-Bit ISA uses the top 2 bits (15,14) to define the instruction "type" for the four parallel instruction registers. This leaves 6 bits for the Instruction followed by the lower 8 bits being used for Source and Destination register definitions (3 bits each for 8 Registers) or in the case of I/O operations using IN/OUT, the 8 bits identifies the port.

For Instructions that do not use registers, the S & D bits are basically 0 unless they are used for an 8-bit immediate value.

In a 16 bit design, the bit assignements are currently defined as:

```
FEDCBA98 76543210
TTiiiiii -SSS-DDD
```

Where:

 "TT" is a Type bit.
 "iiiiii" is an Instruction.
 "SSS" is a Source Register
 "DDD" is a Destination Register.
 "-" Not defined (zero)

Note: If there are 16 registers then the format for Rs & Rd is SSSS DDDD

[Current ISA spreadsheet](/ISA/ISA-2026-04-14.xls) 


### Type Bits 

The "T" bits are designated as follows:
```
00------ Misc Instructions and Primary ALU Operations.
01------ Register Moves involving memory/Immediate values.
10------ Transfer Register and Register specific ALU Operations.
11------ Jump/Call Instructions + (HALT -> all F's).
```
STATUS: Very Incomplete! As I build each functional unit I am adding and updating this page.

Refer to InsetSet.h for a full list of currently defined instructions (but not necessarily completed instruction classes).

## Registers

The SS-16's current design has 8 registers called R0 to R7. This allow for a 3-bit addressing scheme within an instruction.
Source and Destination registers that are 3 bits each (giving 8 registers) R0-R7 but can be expanded to 16 registers very easily.

- R0-R3 are specialized registers with extended ALU capability (INC/DEC/SHL/ROL etc).
- R7 is slated as the results register for ALU maths operations.
- R6 can be loaded with the Program Counter (PC) value using the LDPC instruction.
- R5 can be loaded with the Stack Point (SP) value using the LDSPinstruction.

## Instruction mnemonic bit encoding

Bits 13-8 are allocated for instructions.

Below is the 8 bit value representing the upper 8 bits of an instruction:

```
FEDC BA98
00-- ---- Type 0 Instruction - Miscellaneous/ALU 00-4f
01-- ---- Type 1 Instruction - Moves to/from memory 50-7f
10-- ---- Type 2 Instruction - Register operations 80-bf
11-- ---- Type 3 Instruction - Jumps/Calls (+halt) c0-ff
--II IIII Instruction
```

The "Type" bits also form the instruction but are used to break the ISA into four groups that is directly tied to decoding logic.

Each instruction group share similar CPU functionality, this is done to make the decoding easier and faster as well as provide additonal information to a common group of instructions. 

For example, Type 0 instructions do not reference the PC value, while Type 3 instructions do, so the decoding logic for IN,OUT,DI,EI is much simpler that a JZN instruction.

Using four separate Instuction decoding queues should allow different methods of instruction decoding, such as decoding on a functional unit rather than centralising the control signal generation and using lots of decoding EPROMs



**This is still in design phase so this can/will change over time.**


### Type 0 Instructions

The type 0 instructions have a range of 0x00-0x4F
```
	NOP                ; No Operation Instruction
	INT I              ; Jump to Interupt Routine I (0x00 to 0xFF)
	IN <p>,Rd          ; Input from Port <p> where p=0x00 to 0xFF (Lower 8 bits of Instruction).
	OUT <p>,Rs         ; Output to Port <p> from Rs
	SCF                ; Set Carry Flag
	CCF                ; Clear Carry Flag
	EI                 ; Enable Interupts - Making Instruction available but no interupts in design yet
	DI                 ; Disable Interupts

	ADD Rs1,Rs2        ; ADD value in Rs1 and Rs2 and write back to results register with C/Z flags set.
	SUB Rs1,Rs2        ; Subtract Rs1 from Rs2
	DIV Rs1,Rs2        ; Divide Rs1 with Rs2
	MUL Rs1,Rs2        ; Multiple Rs1 with Rs2
```

### Type 1 Instructions

Type 1 Instructions have a range of 0x50-0x7F

```
	LD  Rd,Rs          ; Load Rd with the value from Rs
	LD  Rd,0x52        ; Load Rd with the Immediate (8 bits)value stored in the instruction (single fetch)
	LDI Rd,#1234       ; Load Rd with the 16bit word 0x1234 stored in PC+1
	LDI Rd,0x52        ; Load Rd with the Immediate (8 bits)value stored in the instruction (single fetch)
	LDM Rd,(M)         ; Load Rd with the value stored in imemeory location M
	LDFL Rd            ; Load Flag register into Rd
	ST  RD,#1234       ; store Register into memory location 0x1234 (16 bits)
	XFER Rd, Rs        ; Load the Value from Rs into the "B" latch of Rd (Rd is R0-R3 only)

	MOV (M), Rd        ; Load Rd with value from Code Space
	PUSH Rn            ; Dec SP and then PUSH the Register Rn onto the stack
	POP Rn             ; POP stack into Register Rn and Inc SP.
	LDSP Rd            ; Load SP into Rd
	STSP Rs            ; Load SP from Rs
	RSP                ; Reset Stack Pointer
```

### Type 2 Instructions ###

Type 2 Instructions are for Register operations and have a range of 0x80-0xBF

Notes: SP always points to the last value written. I.e PUSH or return address.
```
	BSET Rn, B         ; Set bit "B" in Register "Rn"
	BCLR Rn, B         ; Clear (set to zero) bit "B" in Register "Rn"
	BTST Rn, B         ; Test bit "B" in Register "Rn" and set/clear "Z" Flag
	DEC Rn             ; Decrement Register "Rn" set/clear "C/Z" Flags
	INC Rn             ; Increment Register "Rn" set/clear "C/Z" Flags
	AND Rn             ; bitwise AND Register Latch A with Latch B set/clear "Z" Flag
	OR  Rn             ; bitwise OR Register Latch A with Latch B set/clear "Z" Flag
	XOR Rn             ; bitwise XOR Register Latch A with Latch B set/clear "Z" Flag 
   SL Rn              ; Shift Left Register "Rn" bit 15 dropped, bit 0 set to 0
	SR Rn              ; As per SL but right shift
	SLC Rn             ; Shift Left through Bit 15 into carry, bit 0 set to 0
	SRC Rn             ; Shift Right, Carry pushed into Bit 15, bit 0 dropped
	XFER Rs, Rd        ; Trasnfer Rs to Rd but writes to both Latch A and B.
```

### Type 3 Instruction ###

Type 3 Instructions are primarily for Jumps/Calls/Returns (+ HALT) 0xC0-0xFF

```
	JMP M             ; Jump to location M in ROM
	JEQ Rs,Rd,M       ; jump if Rs = Rd to M
	JC  label         ; Jump if Carry is set (C=1)
	JNC label         ; Jump if Carry is NOT set (C=0)
	JZ  label         ; Jump if Z Flag is set (Z=1)
	JNZ label         ; Jump if Z Flag is clear (Z=0)
	CALL label        ; Call code at location defined by "label", SP=SP+1, return address pushed to SP
	RET               ; Return to location on SP and SP=SP-1
	HALT              ; Turn off system clock (Basically stop with PC set to location of HALT
	LDPC              ; Load Stack Pointer to Rd
	STPC              ; Store Rs to the Stack Pointer
	RETI              ; Return from Interrupt
```

**Updated 2026-09-08**

