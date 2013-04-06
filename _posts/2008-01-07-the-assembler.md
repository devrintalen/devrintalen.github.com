---
layout: post
title: The Assembler
---

My friend [Chris] and I recently finished our [USB project][476]. I
was trying to think of a good way to present this in a post, and
decided to highlight one small part of the project: the assembly that
drives the lowest layers of the software stack.

Old School
----------

Last semester I had hacked up the assembly for our project without
much foresight or care for elegance. We were more preoccupied with
trying to grasp the [650-page spec][usb] that is USB, and beautiful
code was the least of our worries.

Though the code didn't *look* good, it still had to *be* good, and
here's what it had to accomplish:

- Output one bit from a buffer in memory every 10 cycles (no more, no
  less).

- Keep track of the number of bits sent and stop when that is equal to
  a given bit count.

- Every eight bits sent load another byte from memory.

- Raise and lower the enable line so that the Mega32 can drive the bus
  when transmitting and read the bus when receiving.

This becomes a tall task when you only have 9 cycles to work with (one
cycle is needed to output on the I/O pins). Let's look at what the
assembly I wrote last semester for transmitting a packet looks like:

    #define SIE_TOKEN_BIT
        mov r20, r3
        andi r20,0x01
        add r20, r10
        out %1, r20
        lsr r3
        subi r16, 1
        brne .+4
        jmp .sie_send_token_eop
    
    /* Token: Send bit, and nop until next one */
    #define SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT
        NOP2
    
    /* Token: Send bit, and buffer another byte */
    #define SIE_TOKEN_BIT_BUFFER
        SIE_TOKEN_BIT
        ld r3, X+

The first thing to note is that *all loops were unrolled* in this
assembly. To send a byte, we had this macro:

    #define SIE_TOKEN_BYTE
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_NOP
        SIE_TOKEN_BIT_BUFFER

This would literally copy and paste in the assembly above about eight
times. And that wasn't even the worst of it: because the loops were
unrolled, we had to copy in enough loop iterations for the worst case
scenario. For the data packet transmit code --- which at most could
send 103 bits --- we had the byte macro copied in 13 times. This
equated to about *936 lines of code* --- for just one small part of
the SIE code. The sheer size of this hurt us; our code weighed in at
about 20 KB when compiled. On a device with only 32 KB of program
flash memory this becomes a bit of a problem (considering that our
code was intended as a companion library to an existing user program).

The assembly above shouldn't be too confusing, but a few notes are in
order.

- `r3` is used to store the byte buffered from memory.
- `r20` is used as a temporary register.
- `r10` holds the value `0x05`.
- `%1` is a compiler directive for `PORTA`.
- `r16` holds the bit count for the given packet.

Each of the lines are explained in order:

1. `mov r20, r3`

   Copies the buffer register into the temporary register.

2. `andi r20,0x01`

   Performs an AND operation on the temporary register with a bit mask
   to extract the lowest bit. This is the bit that will be sent on the
   bus.

3. `add r20, r10`

   Adds the bit to be sent with 5: what this is essentially doing is
   differentially encoding the signal and setting the enable pin high
   at the same time. The pin assignments were: enable on pin 2, D+ on
   pin 1, and D- on pin 0. Thus if the bit in the temporary register
   is 1, meaning that we should be sending a differential 1, adding 5
   will yield `0b00000110`. The enable line is set high, as is D+. D-
   is low.

4. `out %1, r20`

   Outputs the value of the temporary register on `PORTA`.

5. `lsr r3`

   Shifts the buffer register down one, getting it ready for when the
   next bit will be sent.

6. `subi r16, 1`

   Decrements the bit count by one.

7. `brne .+4`

   When the bit count hits zero, this branch will not be taken.

8. `jmp .sie_send_token_eop`

   If the above branch is not taken --- meaning that all bits have
   been sent --- then the code will jump to the end-of-packet handler.

Repeat this seven times, and add a load instruction on the eighth, and
you have the complete workings of last semester's assembly. It worked,
true, but it was gross; and with an entire semester to rework stuff I
decided to sit down and hammer out some nice code.

New School
----------

We came into this semester knowing that USB with a Mega32 is indeed
possible.  We also knew what USB *was*. We figured that a full code
overhaul would be in order, and there's no better place to start than
at the bottom.

The assembly, from above, was completely tossed. Little by little we
came up with our new assembly --- replete with rolled-up loops and
clever hacks.  These changes required some small modifications to the
hardware, but nothing major.

Here's the code that made it into our final revision:

    #define TX_PACKET(label,mem_pointer,bit_count_reg)
        mov     r5, __zero_reg__
        ldi     r20, 0x01
    
        /* buffer */
        .sie_#label_tx_buffer:
        ld      r10, #mem_pointer+
    
        /* bit tx */
        .sie_#label_tx_bit:
        lsl     r10
        rol     r20
        out     %4, r20
    
        /* completion checks */
        dec     #bit_count_reg
        breq    .sie_#label_tx_done
    
        add     r5, r3
        brcs    .sie_#label_tx_buffer
    
        ldi     r20, 0x01
        rjmp    .sie_#label_tx_bit
    
        /* done */
        .sie_#label_tx_done:

I could try to explain all the assembly here, but I'd be repeating an
entire chapter of our documentation on the project. Chapter 4 of [the
documentation][1] covers each line of the assembly and how it
works. Check it out, or try and figure out what the assembly is doing
on your own. Should you choose to do that, know that:

- The pin assignments are: enable on pins 1 & 2 (these get ORed
  together into one enable line) and transmit on pin 0 (this gets
  differentially encoded by the hardware).

- `%4` is a compiler directive for the USB port.

- `#label`, `#mem_pointer`, and `#label` are parameters passed to the
  macro and are pasted into the macro where needed before the code is
  compiled.

- `r3` holds the value `0x20`.

Explanation aside, the punchline is that nearly 1000 lines of assembly
in our previous project got replaced with just *12* lines of
cleverness.

[1]: http://aneviltrend.com/images/usb_documentation.pdf
[Chris]: http://cdleary.blogspot.com
[476]: http://instruct1.cit.cornell.edu/courses/ee476/FinalProjects/s2007/blh36_cdl28_dct23/blh36_cdl28_dct23/index.html
[usb]: http://www.usb.org/developers/docs
