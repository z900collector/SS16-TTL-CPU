
**DRAFT**

# Program Counter Overview #

The program counter (PC) is 16 bits wide and designed around 74F161 counter chips, this particular chip selection may change to 74F193 however the primary function is to count up.

### Key points ###

- On a reset the counters start at 0
- The counters are 16 bits wide, there for 4 counter chips are required.
- To jump to a new location, the PC is reloaded from a latch.
- For relative branches, 74F283 Adder chips add the displacement and reload the counters.
- Basic design built on a breadboard but under review.

<TODO> - More to come.

*Updated 2026-11-05 *
