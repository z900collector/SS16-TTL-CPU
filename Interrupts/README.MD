# Experiments with Interrupts

One area that has been bugging me in this design has been the lack of interrupts. I was so absorbed in trying to structure the control logic that I missed a crucial mechanism - interrupt logic is normally checked at the end of the current instruction.

Keeping this in mind, I quickly realized in my design that I simply needed to have an additonal "T" State to do a check for an interrupt, and if present start an "INT_State_0" state counter to clock through the logic needed to do what an intrupt handler needs in place before the first instruction of the Interupt subroutine is executed.

In terms of the types of Interrupts, I want to implement something similar to what most CPU designs have:

- An NMI (Non-maskable interrupt);
- A maskable INT (controlled via the EI and DI instructions).
- A vectored interupt using an offset register.
- A "Fast Interupt", just save the PC and Flags, leave the Registers to the code

Once the basic logic is in place, each can be added to the design later provided the initial design is done well.

## Getting Started

The first thing to do was work out how to latch the signals for the different types of interupts. Logisim Evolution seamed like the tool to use. Below is some experiments in simple latching to demonstrate this.

## NMI Pending logic using Logisim Evolution

This circuit captures the NMI and generates a latched signal. The RETI instruction can be used to reset the latch, this avoids retriggers.

![NMI latch in Logisim](nmi-latching.png)


## Using a TTL 74LS74 Chip

As I've done before, I decided to see if I could implement it in TTL, this circuit under went a few design changes as the signal to some of the pins showed errors in the circuit that did not appear using the basic "D" Flip-Flops.

![TTL Version of NMI latch](NMI_TTL_Experiment.png)


## Implementing EI / DI logic 

The logic for the EI and DI turned out to be surprisingly easy, pulsing the CLK (clock) & CLR (Reset) pins of the 74LS74 to set and clear a latch that has the "Q" output AND'd to the INT latch results in masking or enabling the INT signal to propagate through.

![INT with EI and DI Logic](Latched_INT.png)

## Step 2 - Checking the last state

At this stage I am still prototyping the next stage but it needs to OR togther all the INT signals and then AND the T7 state to determin if an INT/NMI has occured. Then either the NMI control logic can be activated or if not set, the INT control logic.

The basic workflow will be:

- PUSH PC
- PUSH R0 to R7
- PUSH Flags
- Set PC to INT/NMI vector
- reset "T" states back to T0


 More to come....
