# SS16-TTL-CPU
An attempt at building a superscalar TTL CPU using 74HC/HCT Logic - 16 Bit version


# Introduction

I have been inspired to design this TTL based CPU after decades of building and writting code for embedded systems. I started in 1976 when the z80 came out and worked on a range of CPU's up till the around the 2000's when I focused on my Systems Engineering career and left smaller processors behind. In 2024 I saw a TTL CPU page and was wrapped, prior to starting this design I researched all the available designs on the Internet to get a clear view of what has been built before, an understanding of the history of TTL Home Brew Computers and the philosophy that motivates people to spend a huge amount of hours and brain power on designing and building these projects.

# The current state of most TTL CPU Projects

Having studied a huge range of 8/16 and the odd 32-bit TTL projects on offer, I decided to do something more advanced than the current designs. 

Most simple TTL CPU designs have the following:

* A single data bus
* A single control unit
* EPROM/EEPROM based microcode design
* Single Instruction Register (IR)
* Basic latched registers
* Stack Pointer RAM shared with general RAM.
* Limited memory range
* Fixed Machine Cycle times (i.e. T0 - T1 - T2 -T3 etc) for ALL instructions.
* Slightly more advanced designs have a modified Harvard Architecture. With separate RAM and EPROM address spaces.
* Low clock frequency typical < 4MHz

However, there are huge bottlenecks in these designs, **they do work and work well** for what they are, but I wanted something:

* Faster >10Mhz <50Mhz,
* More sophisticated design (take as many features from RISC based designs) and
* More modular in it's design, think "Functional Units".
* Pipelined and parallel operations where possible.
* Easy to increase the ISA capabilities without having to burn EEPROMS.
* Pure 74xx series chips, no PLA, FPGA etc

## The Issues with current designs

In basic point form, the issues that are common to most TTL CPU designs are:

- EPROMs have delay times of upwards of 100-150ns or worse thus limiting clock timing.
- Single Bus designs cause a bottleneck, particularly with ALU operations.
- ALU operations often rely on [74181](https://www.righto.com/2017/03/inside-vintage-74181-alu-chip-how-it.html) which is very much a legacy chip.
- Most have a single Instruction fetch/decode and execute phase with no overlap.
- Single control unit with complex control line distribution. In most cases the need for more control signals means adding more EPROMS to breakout the individual signals.
- Registers as basic latches without any additional logic capability.
- RAM, Code and Stack in one address space.

Some concessions:

* The [74283](https://www.ti.com/lit/gpn/SN54S283) 4-bit binary adder is still manufactured and easy to obtain, so this is acceptable to me.

There are some slightly more advanced designs, that have one or more of these features:

- An ALU bus directly connected to some of the system registers.
- Registers implemented using counter chips allowing Increment / Decrement operations.
- ALU implemented as logic gates with mux chips removing the reliance on vintage End of life (EOL) chips like the 74LS181
- Dual stage Pipeline with simple logic decoding.
- Dual bus support - limited but still usable.
- MMU like features to address memory beyond 16-bit Address Bus range.

## Philosophical Goals

- Use 74 series TTL chips where possible in the core CPU design and Implementation (avoid 74LSxxx). 
- Aim for a clean and elegant circuit design where possible.
- As RISC like as possible.
- Clean/Uniform instruction set design.
- Modular approach rather than central control.
- Modified Harvard Architecture - separate code, Stack and User RAM.
- Well defined register usage similar to RISC-V and MIPs but not a direct implementation of them.
- Implement Interrupts.

Most designs have similar goals to this so it fits within the Home Brew TTL Computer design ideals.

# Block Diagram (Still in design stage)

![SS16-TTL-CPU](Block-Diagram-2025-10-14.png?raw=true)

# CPU Features

In order to make the CPU faster, the design will need to implement a degree of parallelism and eliminate the slowest components, particulary EPROMS. 

To avoid the path of CISC designs (think instruction bloat), the CPU needs to implement as many features of a RISC platform. To help achieve this, I have a fixed format Instruction Set Architecture (ISA) with 8 bits for instructions and 8 bits shared between Register based instructions, I/O instructions and special purpose instructions. See [SS-16 ISA](/ISA).

To perform data writes simultaneously to code fetches, the CPU needs to implement a modified [Harvard Architecture](https://en.wikipedia.org/wiki/Harvard_architecture) design with a separate code and RAM space. Moving code to Register can be achieved using the MR instruction where as LD and ST instructions Load and Store to RAM

For something different, the Stack space will also have it's own RAM space separate to code and User space.

The following topics are where performance can be improved allowing multiple tasks to be performed within the same clock cycles and reducing bottlenecks in the overall design:

## RBUS
Rather than a single bus for register access, I am aiming to provide two data buses, the conventional Data Bus (referenced as DBUS in the design) and a separate register bus called "RBUS" for register-to-register moves. This also includes moving data to and from the Stack Pointer Register and Program Counter Register.


For arithmetic ALU operations like ADD, SUB, DIV and MUL, I can dedicate a register as the ALU results register (at this stage it's R0) and use the DBUS and RBUS as inputs to the Arithmetic ALU like other designs.

Current (draft) design as of September 2025. I still need to drop this onto a bread board and complete the register control logic.
![SS16-TTL-CPU](REG-Signals-2025-09-09.jpg?raw=true)

## Instruction Groups

Using an idea from the MIPS CPU, I have aimed for a fixed size instruction set that uses 2 bits as a "I-Type" field and have four separate Instruction registers (from a hardware perspective, think [74HC139](https://www.ti.com/lit/gpn/SN74HCT139) and 74HC574 Latches). This gives each of the four groups of instructions 6 bits or 64 different instructions per group.

By grouping related Instructions together, the control logic can then be implemented in logic gates for many instructions without needing EPROM style decoding. The decoding can also be passed onto the functional unit that the instructions apply to (where possible).
This allows both a parallel pipeline design where instruction fetching continues on each 2nd cycle (unless paused) and segregates the control logic needed to handle just the signals for the instructions to be handled by that pipeline.

The ISA details are still in draft stage, once the Assembler project is completed. But basically 2 bits for the I-Type and the remaining 6 bits for the range of instructions in each group gives 8 bits for Instructions and add in 6 bits for the register selection leaves 2 bits in the first 16-bit word. Immediate values could be an additional fetch if the instruction requires an immediate value.

When the design is expanded to 32 bits, the Immediate value can be the lower 16 bits.

The ISA documentation is [here](/ISA/README.MD)

So far the Identified Groups are:

* Miscellaneous Instructions (like NOP)
* Register Operations
* ALU Operations
* Call, Jump operations.

## Advanced Registers

Influenced by the MIPS CPU again, my initial design has registers R0 to R7. Having eight (8) registers means having 3 bits for a source and 3 bits for a destination register format. Rather than have the Main Controller/Sequencer (MCS) control the registers, the MCS would signal the registers of an operation via a Register Control Bus and the Register Controller/Sequencer (RCS) does the task. This allows the Instruction fetch cycle to re-occur directly after the RCS takes over (it would do it's task(s) during the fetch, decode cycle), then be ready at the Execute cycle for the next register operation if there was one directly after the current one. 

The first four Registers (R0-R3) will also include an Increment/Decrement capability to increase the speed of loop counting and Rotate/Shift Operations. Since logic gates are relatively cheap, adding bitwise logical operations such as AND, OR, XOR etc will also enhance each register.

The remaining four (4) registers will not have the ALU functionalit built in but will have the ability to fetch and hold the SP and PC values via a single instruction (LDSP and LDPC).

### Possible Issues ###

There maybe timing issues to still debug but once I build the register modules, the design is 95% complete and is ready to prototype.

### Localised ALU functions

Registers R0-R3 will include the ability to perform the following instructions that would typically be handled by an ALU:

* INC / DEC
* BSET / BCLR / BTST
* Shifts / Rotates
* Zero / Invert / Bitwise OR / AND / XOR operations

The flags from these operations would be pushed to a global flags register which is where the Instruction Registers would look if needed. Flags are latched at the ID stage.

### Secondary Register Latch ##

The first four registers have a secondary register latch ("B" Latch) to enable the basic ALU operations to be performed locally, the 2nd latch is loaded by an XFER instruction (XFER Rs, Rd) This would be different to a "LD" instruction which moves data into the "A" Latch of a Register.

The XFER would load from the "A" of Rs into the "B" of Rd at the same time. This would then be followed by the ALU operation. Results get written to an internal latch and then back into the "A" latch.

The XFER instruction, combined with the "B" Latch is designed to speed up bitwise operations significantly as they can be done while the D-Bus is freed up to get the next instruction from Code RAM.

## Separate Stack RAM ##

Why does stack RAM need to be part of main memory?

I asked myself this many times, logically a Stack is built from the top of RAM and works down as the stack grows and RAM usage grows upwards. If they meet, you are in trouble, but generally they are separate (conceptually) but share the same chips. 

If you split the stack RAM into a separate exclusive address space and have the SP register output to a "local" SP bus via some counter chips (74HC161/74HC191/74HC193), then include the control logic to manage PUSH, POP, CALL, RET and RSP (Reset SP) then you can do stack operations from registers while still having the Instruction Data Bus fetch and process more instructions, this should increase performance on stack operations. 

You can also execute stack operations independant of User RAM access as the stack uses the R-Bus while RAM is access using the D-Bus.

### Theory ###

On reset (or via the RSP Instruction) the stack is initialized to 0xFFFF using pull up resistors on the "D" inputs to the counters, the MR and /LOAD signals then set that address, the output of the counter chips drive the address bus for the SRAM. Only a CALL or RETurn requires the SRAM to present data back to the Main Data Bus. The PUSH and POP instructions would be "register" operations using the R-Bus.

The SP would be a separate "functional Unit" and can incorporate the control logic needed to manage moving data around.

The design of the SP is 80% there, I need to commit my sketches to KiCad and do some block diagrams. I have also started breadboarding the SP Module.

## Advanced ALU

With a number of functions integrated into the registers, the ALU can focus on Addition/Subtraction/Division and Multiplication. The details of this are still in sketch stage but there are several ways to implement this using TTL logic only. 

## Program Counter

The inital design is for a 16-bit PC giving 64K Code memory and 64K Data memory. I have been reviewing bank switching designs and even a home built MMU design and will decided on this later. The initial block diagram above does not include the PC Add/sub logic for relative addressing, that part is still in sketch stage. I'm not happy with designs that use the 74181 chips to add to the PC, there must be a better way!
Where designs have used the ALU to implement ADD/SUB operations for the program counter, that functionality would be built into the PC module. The PC Module is basically dedicated to Instruction fetching. 

I am still working on the best way of moving data between code space and data space, for the time being the LD and MV instructions can do this as the PC and MAR are separate. I could use a move coplex instruction to move blocks of data for things like tables and strings/character data that might be needed in a program.

## Implement Microcode in SRAM - (Wish List Item)

Due to the latency of EEPROMs, the use of 10ns SRAMs would increase both code execution and even instruction decoding if I use classic designs.
To implement code in SRAM, the boot process would need to backfill the SRAM on reset from an EEPROM. The result, Microcode code access goes from 50ns+ to 10ns depending on chip selection. My initial sketchs appear to be valid, but I will know when I bread board it and decided on the best way to do this on reset.

### Theory

Implemented on reset, using a special sequencer to perform SP->RD->WR->INC_SP-> repeat sequence, then when complete, switch the buffers for the EPROM to open collector and the SRAM's take effect.


# ISA #

The ISA has come under some sustained revisions to align with the Control Logic Decoding I am looking at implementing. Below is the new loaction of the ISA docuemtnation including a spreadsheet.

See: [Instruction set Architecture](/ISA/README.MD)

See below for a description of the key points:

![SS16-TTL-CPU](2026-03-13-ISA-Table.png?raw=true)


Previous Version of the ISA is located here, https://github.com/z900collector/CPU32-Assembler/blob/main/isa.md this will be updated to point into the SS-16 project.


# Software 

As I narrowed down on the width of the CPU, I decided an assembler project would highlight any issues as well as clarify the hardware needed to support the ISA. So I developed an Assembler. 
Having written 8080/8085/z80 and 68020 assembler code back in the 70's and 80's as an Embedded Systems Engineer in my younger days, I decided I would borrow ideas from these CPU's as well as the MIPS and RISC-V designs.

You can find the "Soon to be Completed" Assembler here: https://github.com/z900collector/CPU32-Assembler

# Schematics 

Stillchematics are still being finalized as I build modules. They will be released in KiCad and PDF formats soon.

So far I have the PC, SP drawn up and am working on the registers and User RAM. Everything else is sketches on paper (lots!).

# Updates 

## Update - 2026-02-23

Currently designing the ALU and Pipeline Logic, ordered more IC's to breadboard and will start breadboarding the IR logic and first pipeline.

## Update - 2026-03-13

ISA looking fairly stable, circuit design underway in KiCAD and breadboarding commenced.

More to come!

## Update - 2026-04-14

* Uploaded XLS file and moved ISA readme into a new ISA folder. 
* Updated readme to reflect movement of instructions, some additions and some deleteions.


## Contacting Me

If you need to cotact me:

sid DOT young AT gmail DOT com

Use SS-16 at the start of the Subject line (put them in square brackets) as I get a lot of emails every day and I can filter these with priority.

*Updated 2026-09-09*

