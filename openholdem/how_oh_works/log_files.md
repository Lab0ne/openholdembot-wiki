# Log-Files

Sometimes the bot might take an unexpected decision, more rarely for
bots in production, but for sure regularly for bots in development. The
reason can be mis-scrapes (Table Map or improper system settings); it
can be bad bot-logic; it can even be a bug in OpenHoldem.

Many newbies will automatically assume the latter case. Whenever you
encounter a strange behaviour it is a good decision to make use of
OpenHoldems logging capabilities. OpenHoldem will generate a log file as
it operates, less detailed logs for production, more detailed logs for
debugging. The verbosity of the log can be set in the Preferences tabs
*Logging* and *Debugging*. The log file can be found at
*OpenHoldem\logs\oh_NN.log* where NN is the session-ID.

## Basic Info

On every autoplayer action OpenHolldem will create a section with some
basic info about the table-state, the decision taken and the action
executed.

    2015-09-10 08:17:35 - Version: 7.8.0 
    2015-09-10 08:17:35 - Chairs: 9 
    2015-09-10 08:17:35 - Userchair: 4 
    2015-09-10 08:17:35 - Holecards: 7d9c 
    2015-09-10 08:17:35 - Community: .......... 
    2015-09-10 08:17:35 - Handrank: 2 
    2015-09-10 08:17:35 - Hand: hcard 
    2015-09-10 08:17:35 - My balance: 2135.00
    2015-09-10 08:17:35 - My currentbet: 80.00 
    2015-09-10 08:17:35 - To call: 0.00 
    2015-09-10 08:17:35 - Pot: 280.00
    2015-09-10 08:18:19 - Big blind: 80.00 
    2015-09-10 08:18:19 - Big bet (FL): 160.00 
    2015-09-10 08:18:19 - f$betsize: 0.00 
    2015-09-10 08:18:19 - Formulas: FCK.. 
    2015-09-10 08:18:19 - Buttons: F.KRA 
    2015-09-10 08:18:19 - Best action: f$call 
    2015-09-10 08:18:19 - Action taken: f$check

As you can see in this example the bot-logic decided to call, but there
was no bet to call and no call button either. So OpenHoldem finally
checked (backup-action).

## Autoplayer Trace

If you want to go into detail the most useful part of the log is clearly
the autoplayer-trace. It helps you to investigateö the entire evaluation
that caused a decision. Let’s assume we have a f\$allin-function that
looks like that:

    ##f$allin##
       f$HaveUnbeatableMonster
    OR f$HaveSuperDuperDraw
    OR f$PoorHeroIsPotCommitted

On evaluation of f\$allin OpenHoldem will look up the three user-defined
functions (f\$HaveUnbeatableMonster, ...), evaluate them and combine
their return-values to a final result. This leads to a simplified
call-tree:

<figure>
<img src="images/openholdem/how_oh_works/log_simple_call_tree.jpg" />
</figure>

Each function that is one level indented got called by the next function
above that is one level less indented. Things would be easy (or a
night-mare) if all code was in one oversized function and all called
symbols were black boxes. However one of the advantages of OH-script and
OpenPPL is that you can structure your code and create functions on your
own. Indeed OpenPPL provides several hundred poker-logical functions
that work out of the box. They are all available as plain text, so that
you can have a look at their code and OpenHoldem can log their
evaluation in detail. Let’s assume your user-defined function
f\$HaveUnbeatableMonster is an easy one and just considers the winning
percentage (the built-in symbol prwin), f\$HaveSuperDuperDraw uses the
OpenPPL-symbols HaveStraightDraw and HaveFlushDraw, whereas
f\$PoorHeroIsPotCommitted uses Balance and Pot. Then the
autoplayer-trace could look like this:

<figure>
<img src="images/openholdem/how_oh_works/log_detailed_call_tree.jpg" />
</figure>

So the implementation and the evaluation of each symbol use some other
symbols that are one level deeper indented in the autoplayer-trace. In
practice the autoplayer-trace will get even more detailed. For example
even a simple symbol like Balance might need the user-chair, the size of
the big-blind and some more. But the principle is always the same and
with a little bit experience the logs become easy to read.

## OpenPPL

OpenPPL works very similar to OH-script. Basic info and the
autoplayer-trace are the same.

### Initializations

Most OpenPPL-symbols get calculated on the fly when you need them. But
some symbols require special initialization, exactly once per orbit when
it is our turn and the scraper saw enough stable frames. This is the
case mainly for counters like “Raises”, “Calls” and some more.

    InitMemorySymbols = 0.000 [Line 18/3976]
      TimeToInitMemorySymbols = 1.000 [Line 8/3921]
        isfinalanswer = 1.000
        GameStateChanged = 1.000 [Line 2/3928]
          dealerchair = 4.000
          me_re_MemOldGameStateDealerChair = 3.000
      False = 0.000 [Line 1/230]
      InitMissingSmallBlind = 0.000 [Line 2/772]
        betround = 1.000
        ConstBetRoundPreflop = 1.000 [Line 1/199]
    ...

You will see these initializations on top of the autoplayer-trace, i.e.
below the summary, but above f\$preflop.

### Main evaluations

After everything got initialized OpenHoldem will evaluate the main
OpenPPL-function for the current betround: f\$preflop, f\$flop, f\$turn
or f\$river.

    f$preflop = 11.000 [Line 93/2726]
      StackSize = 26.688 [Line 1/1422]
      StackSize = 26.688 [cached]
      BigBlindSize = 80.000 [Line 1/1373]
        bet1 = 80.000
      BigBlindSize = 80.000 [cached]
      listGp1 = 1.000
      listGp2 = 0.000
      listGp3 = 0.000
      user_EarlyZone = 1.000
      Raises = 1.000 [cached]
      Calls = 0.000 [Line 1/630]
        me_re_MemCalls = 0.000
      Calls = 0.000 [cached]
      BotsLastAction = -1000007.000 [Line 2/573]
        None = -1000007.000 [cached]

### Return Values

You might wonder, what the numbers mean:

    f$preflop = 11.000
    ...
    BotsLastAction = -1000007.000 [Line 2/573]
        None = -1000007.000

The evaluator-engine needs to handle three different type of final
actions:

- betsizes (in big-blinds), encoded as positive numbers, so f\$preflop =
  11.000 in the example above means RaiseTo 11 (big blinds).

- percentaged potsized bets, encoded as small negative numbers. -0.666
  means bet 2/3 pot.

- fixed actions, encoded as large negative numbers.

      ##Beep## 
      -1000000

      ##Fold##
      -1000001

      ##Undefined_BetSize_Probably_Due_To_Misscraped_Pot_Defaulting_To_Minraise##
      -1000002

      ##RaiseMin##
      Raise

  Altogether 25 named action-constants – too many to mention them here.
  But they are in the OpenPPL-library which is a plain-text-file. Feel
  welcome to have a look – there are many interesting things to
  discover.

### Undefined Actions and Return Values

Whenever OpenHoldem reaches the end of a function it will evaluate a
special symbol
“empty_expression\_\_false\_\_zero\_\_when_others_fold_force”. So it
will no longer continue silently with an “undefined” value like in the
past. Instead it will create a verbose log-file. This symbol always
evaluates to zero, which is also the encoding of “False” and “Fold”.

    f$flop = 0.000 [Line 27/73]
       ...
       empty_expression__false__zero__when_others_fold_force = 0.000
    f$check = 1.000 [cached] 
    f$fold = 1.000 [cached]

So whenever you see this symbol in your log-files you know that your
profile does not handle the current situation. This regularly happens
for so-called “tweaks”, i.e. incomplete profiles, meant to be used on
top of another one. Folding a set of kings? No wonder, nothing to be
tweaked, playing sets of kings is easy. In such cases you have to
complete your profile. Tweaking, completing and combining profiles is
explained in the OpenPPL-manual.

### Autoplayer Actions

After the OpenPPL-decision gets calculated OpenHoldem translates it to
OH-script autoplayer-actions (plural because of backup-actions) and
thereafter the autoplayer works the normal way. That’s why you can see
some OH-script autoplayer-functions at the end of the autoplayer-trace,
each one with a pre-computed (“*\[cached\]*”) value. Please be aware
that OpenHoldem – contrary to OpenPPL – doesn’t use big-blinds for its
calculations but dollars. So “RaiseTo 11” in the example above leads to
a f\$betsize of \$880.

    f$betsize = 880.000 [cached] 
    f$raise = 1.000 [cached] 
    f$call = 1.000 [cached] 
    f$check = 1.000 [cached]
    f$allin_on_betsize_balance_ratio = 0.000 [Line 5/1864]
    f$fold = 1.000 [cached]

## Errors and Warnings

Besides of the autoplayer-trace OpenHoldem logs all errors and warnings,
i.e.:

- all shown error-messages

- all message-boxes that got suppressed by your preferences

- some more errors and warnings where an error-dialog would not be
  appropriate.

## Debug Messages

Debug messages are mainly meant for developers and for experienced users
who want to get more info about the way how OpenHoldem works internally.
These messages can be turned on with the Debugging-tab of the
Preferences dialog. They are very useful for finding and fixing bugs,
but they are usually not needed for most end-users. Below an example
about blind-detection, blind-locking (for stability) and detecting
hand-resets.

    2015-08-15 16:00:24 - [CHandresetDetector] Setting handnumber to [] 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by dealerchair: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by cards: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by handnumber: true 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by community cards: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by potsize: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by nopponentsplaying: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by increasing balance: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by changing blind level: false 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset by new small blind: true 
    2015-08-15 16:00:24 - [CHandresetDetector] Methods firing this heartbeat: 0b001000001 
    2015-08-15 16:00:24 - [CHandresetDetector] Methods firing last 3 heartbeat2: 0b1000001 
    2015-08-15 16:00:24 - [CHandresetDetector] Number of methods firing last 3 heartbeat2: 2 
    2015-08-15 16:00:24 - [CHandresetDetector] Handreset found 
    2015-08-15 16:00:24 - [CHandresetDetector] Setting last dealerchair to [-1] 
    2015-08-15 16:00:24 - [CSymbolEngineTableLimits] CalcTableLimits() 
    2015-08-15 16:00:24 - [CSymbolEngineTableLimits] ResetOnHeartbeat() 
    2015-08-15 16:00:24 - [CSymbolEngineTableLimits] TableLimitsNeedToBeComputed() true, because not locked 
    2015-08-15 16:00:24 - [CBlindGuesser] Data from scraper (ttlimits, c0limits): 5.00 / 10.00 / 0.00 
    2015-08-15 16:00:24 - [CBlindLevels] Trying to find best matching blind-level for 5.00 / 10.00 / 0.00 
    2015-08-15 16:00:24 - [CBlindLevels] Perfect match found 
    2015-08-15 16:00:24 - [CBlindLevels] Blinds recognized as 5.00 / 10.00 / 20.00 
    2015-08-15 16:00:24 - [CSymbolEngineTableLimits] AutoLockBlinds() 
    2015-08-15 16:00:24 - [CSymbolEngineTableLimits] blinds_locked_for_current_hand: false

## Logging for DLL

Usually OpenHoldem will stop going into more detail when it reaches a
DLL symbol, because DLL-functions and C-functions are a black-box for
OpenHoldem. However it is possible to enable DLL-logging, then
OpenHoldem will continue like in the axample above if the DLL-function
calls back to OH-script. Furthermore it is possible to log any
user-defined messages from the DLL to the standard OpenHoldem-log with a
function from the DLL-interface:

    EXE_IMPLEMENTS void   __stdcall WriteLog(char* format, ...);

Now if your head hurts be assured: things are half as hard once you have
gained a little bit experience.

*The log-file knows the truth. True experts know the log-file.*
