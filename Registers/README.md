# Registers

##Overview

Influenced by a range of CPU designs, I have settled on the following specifications for the Registers.

The design will have eight (8) registers, identified as R0 to R7, all are 16 bits wide.

Registers R0-R3 will include the ability to perform some simple ALU functions separate to the main ALU:

* INC / DEC
* BSET / BCLR / BTST
* Shifts / Rotates
* Zero / Invert / Bitwise OR / AND / XOR operations

Registers R4-R7 are basically latches that are connected to both the [DBUS](DBUS.md) and [RBUS](RBUS.md). These registers can also hold the current value/result of the following:

- Primary ALU output result.
- Program Counter Latches.
- Stack Counter Latches.
- Future expansion (maybe I/O instructions).

Having eight registers means having 3 bits for a source and 3 bits for a destination register in the ISA format. Provision has been made to go to 4 bits allowing 16 registers in a more advanced design.

 All registers are connected to both the [DBUS](DBUS.md) and [RBUS](RBUS.md).

##Register Usage

###Registers R0-R3 

Registers R0 to R3 are known as "Advanced Registers" as they contain two registers in one and an ALU for bitwise operations. The two registers are known as Latch "A" and Latch "B". The "A" Latch is written to using standard [MOVE](/ISA/isa.md#MOVE) / [LD](/ISA/isa.md#LD) / [ST](/ISA/isa.md#ST) instructions. The "B" Latch is set using the [XFER](/ISA/isa.nd#XFER) and [TRAN](/ISA/isa.md#TRAN) instructions.

See the ISA for more details, but basically the format for "XFER" and "TRAN" is:

 **XFER Rs,Rd** - where "Rs" is Latch "A" in the "Source" register and "Rd" is the "B" Latch in the "Destination" register.

Note: You cannot read back the value of the "B" Latch.

 **TRAN R** - The TRAN instruction tansfers the value in the "A" Latch into the "B" Latch within the same register. I used the simulation tool "Logisim Evolution" to verify this capability and the circuit fully supports this so I just need to add it into the ISA and work up the control signals.

**Note: If "Rd" is specified as R4 to R7 then no operation is performed as these Registers do not have a "B" Latch.**

Circuit Simulation of R0 register using Logisim (Additional components required still).

![S16-TTL-CPU](Register_R0.png?raw=true)

###Registers R4-R7

These registers are basically a scalled down Advanced Register but contain additional logic to latch the value from an alternate Bus into the Register.

####Register R4

No special function at this stage.

####Register R5

No special function at this stage.

####Register R6

The instruction [LDPC](/ISA/isa.md#LDPC) reads th Program Counter value and latches it into register R6. This does not involve data fllowing over the DBUS or RBUS buses).

####Register R7

The result of operations using the primary ALU are stored in the R7 register. These operations are ADD, SUB, DIV and MUL, I can dedicate a register as the ALU results register (at this stage it's R0) and use the D-Bus and R-Bus as inputs to the Arithmetic ALU like other designs.

The ALU design and operation is documented elsewhere and is still in design stage but it should support the ADD, SUB, DIV and MUL instructions and a range of Bitwise logic.


##Register Control Logic - Draft Ideas

Each Register has a collection of signals to control the onboard logic, these signals are generated from the Register Controller/Sequencer (RCS) using hard logic rather than using an EPROM design (as used in many TTL CPU projects).
The Instruction Decoder generates the actual Instruction Signal such as "LD", "LDI", "XFER", "LDM" etc and presents the Source and Destination CPU Values to the register controller. The RCS then generates ENABLE signals for the source/destination registers and the Latch/Output enable signals combined with the T0-T6 signals. When the operation completes, another register based instruction can be loaded and executed if fetched next.

This simplifies the IR decode, execute logic and allows the Instruction fetch cycle to re-occur directly after the RCS takes over (it would do it's task(s) during the fetch, decode cycle), then be ready at the Execute cycle for the next register operation if there was one directly after the current one. Theoretically, a non-register instruction could be fetched and processed a few cycles after this sequence has started.

The first four Registers (R0-R3) will also include an Increment/Decrement capability to increase the speed of loop counting and Rotate/Shift Operations. Since logic gates are relatively cheap, adding bitwise logical operations such as AND, OR, XOR etc will also enhance each register.


###Possible Issues

There maybe timing issues to still debug but once I build the register modules, the design is 95% complete and is ready to prototype.
Using Logsim has shown the timing is working as expected.


####ALU Flags

The flags from local ALU operations need to be propergated to a global flags register which is where the Instruction Registers would look if needed. Flags are latched at the ID stage.


####Secondary Register Latch

The first four registers have a secondary register latch ("B" Latch) to enable the basic ALU operations to be performed locally, the 2nd latch is loaded by an XFER instruction (XFER Rs, Rd) This would be different to a "LD" instruction which moves data into the "A" Latch of a Register. The XFER would load "A" and "B" at the same time. A logic operation would then be an XFER, followed by an LD, followed by the ALU operation. Results get written back to the "A" latch or if coded, transferred to another register using the R-BUS. The key points are the XFER is in progress as the LD is being fetched and decoded, then at the end of the LD, the ALU operation could execute. As it is occurring, another Instruction fetch is already in progress.


## Reference Drawing

The current design from September 2025 is still current with some changes from Buffers to Latches. While the initial circuit simulation looks good, I still need to drop this onto a bread board and complete the register control logic.

The diagram below will be updated to reflect the changes from Buffers to Latches.

![S16-TTL-CPU](/REG-Signals-2025-09-09.jpg?raw=true)

## Terminology

* Rs - Source Register
* Rd - Destination Register
* DBUS - Primary Data bus, connected to register file and RAM/ROM
* RBUS - Unique Register only Bus
* Primary ALU - Mathematical Operations
* RCS - Register Control/Sequencer
* T0 - Time State 0 (there are six "T" state -> T0-T6)
* R[0..7] - Register 0 to 7
* IR - Instruction Register - A latch to capture the Instruction as read from ROM before any decoding.
* IDR - Second IR latch in decode stage, 6 bits in size.

## Supporting Information

The ISA documentation is here: [ISA](ISA/README.md)

**Updated 2026-11-25**
