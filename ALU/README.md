# ALU Design #

Draft Notice - This is some thoughts on a 16-Bit ALU for the SS16 TTL CPU

I've been looking at how a typical ALU works but without using the 74LS181 or similar style chip. One of thesites I have been studying is the Multiplexor ALU design by Dieter Muller from around 2016. The URL is here for those inclined to do more research:

http://www.6502.org/users/dieter/

In addition to this research I have also determined I want to add some additional logic for:

* 16 bit Comparator using 74HC85 chips
* Shifter using both Multiplexors (74HC153) with either a physically shifted latch/buffer or a dedicated chip (slower). See: http://www.6502.org/users/dieter/tarch/tarch_4.htm for some ideas.
* Shifter and MUX's would select MSB/LSB, 0, 1 or Carry into the appropriate end of the shift latch
* AND, OR, XOR, NOR logic via combinational logic and selectable into the ALU via more Multiplexors.

