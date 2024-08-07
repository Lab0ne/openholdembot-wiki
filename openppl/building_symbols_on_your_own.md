# Building Symbols On Your Own

The philosophy of (standard) PPL is: provide poker-logical symbols, that
can be used by any poker-playing person, no matter how much (or less)
programming experience they have. Examples are e.g.
MaxOpponentStackSize, StartingStackSize

However this approach has some drawbacks: it shifts all work to the
developers and limits the users, who might need additional symbols for
their bot-logic. Staying with the example above, they might need:
SmallBlindStacksize, BigBlindStacksize, UTGStacksize, ...,
ButtonStacksize, OpenRaiserStacksize, LimpRaiserStacksize,
ThreeBetterStacksize, FlopCheckRaiserStacksize, TurnDonkerStacksize,
etc. No matter how busy the development team is, they will never be able
to satisfy all needs. Therefore OpenHoldem’s (and partially OpenPPLs)
philosophy is slightly different: provide technical symbols like
balance0..balance9 (the stacksizes for chair0..chair9) and let the user
figure out the rest. This way the end-user is far more flexible; however
at the cost of a bit more work.

As an example we develop a symbol BigBlindStackSize. As a first step we
need to know the chair of the big blind. Then as a second step we will
be able to return the stacksize for this chair. To solve the first
problem we use the OpenHoldem symbol ac_dealposX which returns the
deal-position of chair X. The big blind is (with the very rare exception
of a missing small blind) always the second player to be dealt, so we
search for a the chair, that got dealt as second player.

```c
    ##f$BigBlindChair##
        WHEN ac_dealpos0 = 2 RETURN 0 FORCE
        WHEN ac_dealpos1 = 2 RETURN 1 FORCE
        WHEN ac_dealpos2 = 2 RETURN 2 FORCE
        WHEN ac_dealpos3 = 2 RETURN 3 FORCE
        WHEN ac_dealpos4 = 2 RETURN 4 FORCE
        WHEN ac_dealpos5 = 2 RETURN 5 FORCE
        WHEN ac_dealpos6 = 2 RETURN 6 FORCE
        WHEN ac_dealpos7 = 2 RETURN 7 FORCE
        WHEN ac_dealpos8 = 2 RETURN 8 FORCE
        WHEN ac_dealpos9 = 2 RETURN 9 FORCE
        // Other cases should not happen
        WHEN Others RETURN -1 FORCE
```

Having this information we can continue with the second step (the dull
part). We return the stacksize for the chair of the big blind, making
use of OpenHoldem’s stacksize symbols balance0..balance9. As you can see
it is possible to use OpenHoldem Symbols in your OpenPPL code. Of
course - we nearly forgot to mention it.

```c
    ##f$BigBlindStacksize##
        WHEN (f$BigBlindChair = 0) RETURN balance0 FORCE
        WHEN (f$BigBlindChair = 1) RETURN balance1 FORCE
        WHEN (f$BigBlindChair = 2) RETURN balance2 FORCE
        WHEN (f$BigBlindChair = 3) RETURN balance3 FORCE
        WHEN (f$BigBlindChair = 4) RETURN balance4 FORCE
        WHEN (f$BigBlindChair = 5) RETURN balance5 FORCE
        WHEN (f$BigBlindChair = 6) RETURN balance6 FORCE
        WHEN (f$BigBlindChair = 7) RETURN balance7 FORCE
        WHEN (f$BigBlindChair = 8) RETURN balance8 FORCE
        WHEN (f$BigBlindChair = 9) RETURN balance9 FORCE
        // Other cases should not happen.
        // But if you forget about "WHEN Others"
        // there always is an implicit "RETURN 0 FORCE".
        WHEN Others RETURN 0 FORCE
```

You see: it is not that difficult to extend OpenPPL on your own. The
possibilites are nearly endless. For the moment we skipped some details,
but creating new symbols is self-explaining: each new symbol starts with
a function header, that defines its name. The name of user-defined
symbols traditionally begines with f\$. For example
\##f\$BigBlindStacksize##. Thereafter follows the function’s code, which
usually is in a sequence of (optionally open-ended) when-conditions.
These when-conditions usually define actions (in the case of
f\$preflop... f\$river) or they contain return-statements like in the
example above.

*That’s all. OpenPPL is easy.*

## Advantages of Symbols

In our early versions of the manual we didn’t talk about the advantages
of structured code (especially functions AKA symbols), because they were
too obvious for us. This caused some confusions for newbies. Symbols are
great:

- to get understandable and self-documenting code (good naming;
  <http://en.wikipedia.org/wiki/Information_hiding>)

- to get reusabel code (named code-snippets)

- to get small code (no code clones)

- to get maintainable code (change and fix one location only)

- to get fast code (because of OpenHoldem’s symbol-caching: evaluates
  only once, use the value often)

- to get readable log-files (because you see all the symbol-names and
  their values)

- ...

So how often should you make use of symbols?

*All day. Everywhere. As much as possible. Enjoy them!*
