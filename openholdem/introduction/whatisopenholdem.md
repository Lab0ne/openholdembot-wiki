# What is OpenHoldem

OpenHoldem (and its supporting applications: OpenScrape, ManualMode and
OHReplay) is an open source screen scraping framework and programmable
logic engine for the online Texas Hold’em style poker game. This
framework provides the capabilities to allow you to build your own Texas
Hold’em robot (bot). There are two major parts to any poker bot: the
game state engine and the action engine.

There are a number of approaches for getting the game state from a poker
client, including directly reading the memory of the poker software,
injecting code into the client’s address space, parsing the client’s
chat box, and interpreting the pixels presented on the screen by the
client. OpenHoldem’s game state engine uses the last approach – it
provides a parameter driven method of interpreting the pixels (*screen
scraping*; <http://en.wikipedia.org/wiki/Screen_scraping>) presented by
the poker client to determine the state of the game at any given time.
Screen scraping is not a new approach to this kind of problem, poker
botting or otherwise. Screen scraping has been a recognized approach for
solving a specific set of computer problems almost since computers were
invented. Screen scraping has been around for a longer period of time
than most of you reading this have been alive!

The action engine in OpenHoldem is called the *Autoplayer*. The
Autoplayer will use logic that is provided by you to decide what poker
action to take based on the current game state. The Autoplayer will then
click the buttons and enter the text on the screen to make the poker
client execute this action.

OpenHoldem is a framework. What that means is that you need to provide
it with a couple of things to enable the game state engine and the
action engine to operate correctly.

The first is the parameters that instruct OpenHoldem how to interpret
the pixels presented by the casino that you play at. Each casino
presents these pixels in a slightly different manner, and thus these
parameters need to be specific for each casino.

Second is the logic to tell the action engine what to do – just like
every person plays live/human poker in a different style, this logic
will instruct OpenHoldem how to play the style that you want it to play.

OpenHoldem supports every casino in existence today, as far as we know.
If you find a casino that does not work with OpenHoldem’s game state or
action model, please hit the forums and let us know. The developer team
is very responsive to modifying OpenHoldem to work with the continually
changing landscape of Texas Hold’em casino client software.

OpenHoldem also supports every sub-flavor of Texas Hold’em poker known
to exist today. No-limit, fixed-limit, pot-limit, full ring, heads-up,
6-max, MTT, SNG, double or nothing, turbo, normal speed, whatever.
Someone else has already made it work – you can make it work too.
