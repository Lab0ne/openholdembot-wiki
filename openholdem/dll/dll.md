# User-DLL

OpenHoldem has the ability to interface with a user-compiled DLL.
Microsoft Windows provides a feature whereby running code can load other
code, presuming that code is provided in the form of DLL files.
OpenHoldem utilizes this Windows DLL feature to allow you to extend the
functions within OpenHoldem to practically anything that you want,
provided you can write the code for it and compile it. Generally, people
who create DLLs write them in the C or C++, as those are the native
historical target languages for using DLL functions, as defined by
Microsoft. There are ways to have OpenHoldem use a C/C++ DLL shell which
then calls code written in other languages, such as managed languages
like .NET, but that topic is beyond the scope of this manual. If you are
interested in doing this, there are some topics in our C++ subforum that
talk about utilizing other languages.

OpenHoldem provides a number of preferences for how a user-DLL will be
loaded. It can be done on start-up, when a formula is loaded, or
manually. See the {Preferences} section for more information.

From this point on, the assumption is that, if you are creating a
user-DLL, it will be written in the C or C++ languages. To do this, you
need a C/C++ compiler – the express editions of Microsoft Visual Studio
will suffice for this, as these user-DLLs typically do not need the MFC
library, which is only present in the paid versions of Microsoft Visual
Studio. A starting framework for a user-DLL can be found in the source
code library on Google Code, but is also embedded just below this
paragraph. See the {References} section for links.

## How OpenHoldem and the DLL cooperate 

The OpenHoldem to DLL interface is small and clean. OpenHoldem exports
the following functions and the DLL gets linked to them at load-time.

    // Functions exported by OpenHoldem 
    extern "C" __declspec(dllimport) double __stdcall GetSymbol(const char* name); 
    extern "C" __declspec(dllimport) void __stdcall SendChatMessage(const char *msg); 
    extern "C" __declspec(dllimport) void* __stdcall GetPhl1kFromDll(); 
    extern "C" __declspec(dllimport) void* __stdcall GetPrw1326(); 
    extern "C" __declspec(dllimport) void __stdcall WriteLog(char* fmt, ...); extern "C" __declspec(dllexport) char* __stdcall GetHandnumberFromDll();

And the DLL will export a function too, namely

    __declspec(dllexport) double process_message( const char* message, const void* param );

Again linking happens at load-time, so the DLL-interface is ready for
cooperation right from the start. Contrary to WinHoldem and early
versions of OpenHoldem no function-pointer get exchanged at run-time, so
a very error-prone mechanism could get removed.

#### Game-States

Each scrape cycle, OpenHoldem will send the current game state to the
DLL. As the author of the DLL, you will decide what to do with this game
state information. The game state information is stored in a structure
within OpenHoldem, and a pointer to this structure is passed to your
User DLL in the “param” parameter. An example of how to cast this void
pointer to the game state structure is provided in the Reference User
DLL source code. The details of what is included in this game state
structure are in the header file in the Reference User DLL source code.

#### Querying dll\$-Symbols

Any symbol that begins with the characters “dll\$” is interpreted by the
OpenHoldem parser as requiring a call to the loaded User DLL in order to
determine its value. The “query” message is the mechanism for requesting
the value of this symbol. Any symbol name can be created for use by your
User DLL, as long as it begins with “dll\$”, and your DLL code knows how
to handle it. So, for example, if the OpenHoldem decision engine
encounters a symbol called “dll\$kill_phil” in the evaluation of the
“f\$allin” function, then a synchronous call will be made to the User
DLL to provide the value of dll\$kill_phil in order to complete the
evaluation of f\$allin. The evaluation of f\$allin will block until your
DLL handles this request and provides a result. The name of the symbol
(in this case, “dll\$kill_phil”) is provided in the “param” parameter,
and should be cast to a (const char\*). A simple example of how to
process this message is provided in the Reference User DLL source code.

#### load and unload Messages

There are two event messages that are passed to your DLL. Both of these
messages have NULL “param” parameters. “load” – this message is sent
once immediately after your DLL is loaded. Typically this is used for
some kind of initialization activity by your DLL. “unload” – this
message is sent once just prior to OpenHoldem unloading your DLL from
memory. This could be due to OpenHoldem shutdown, manual intervention
from the DLL menu, or new formula loading. Typically this is used to
clean up your DLL’s activities, such as connections to databases,
closure of threads, etc.

#### phl1k 

This is a pointer to OpenHoldem’s internal hand list array, for your DLL
to read and modify as needed. It points to an array of the following
type:

    bool inlist[1000][13][13];

OpenHoldem can store 1000 different hand lists, and the first dimension
of this array specifies the hand list number, the second and third
dimensions specify the rank of the first and second card, where
rank0\>=rank1 is interpreted to mean suited cards, and rank0\<rank1 is
interpreted to mean unsuited cards. This is a very sparsely populated
array, where the intersection of the three dimensions specifies if the
particular card pair is present in the hand list or not. A non-zero
value at this intersection indicates that the card pair is present; zero
indicates that the card pari is not present. prw1326 This message
provides a pointer to a prw1326 structure stored in OpenHoldem. This
prw1326 structure is used to describe the manner in which OpenHoldem
calculates its prwin, prtie and prlos symbols, based on the information
which the DLL developer places in this structure. Unless the DLL code
sets a specific enabling flag in this structure, the current prwin,
prtie and prlos calculation is unchanged. Please see the {prw1326}
section for more information.

<div class="rem*">

* 0.1*. OpenHoldems handlist-structure got rewritten and OpenHoldem is
no longer restricted to 1000 numbered lists. Therefore this paragraph
does currently not apply. A new machanism for manipulating handlists
will be developed in the near future.

</div>

### A Simple Example

An easy way to get started is to override all OH-Script processing with
DLL functions. There are a number of reasons to do this, including:

- Limitations of the OH-Script language (no assignments is the biggest
  limitation)

- Low level integration with other technologies

To override all OH-Script processing, simply instruct each primary
function to only have a call to a “dll\$” symbol. For example, use the
following code:

    ##f$allin##
    dll$alli

    ##f$raise##
    dll$rais

And so on. In your User DLL, handle each of these “dll\$” symbols as
described above. Now all of the decision logic has been moved from
OH-Script code to code that you provide in your DLL. It is important to
remember that even if you want to code up most of your bot logic in
C/C++, you still must use the OpenHoldem primary functions themselves to
call this logic.

### prw1326 

The prw1326 structure and associated individual chair structure have the
following specification:

    //prwin 1326 chair structure 
    struct sprw1326_chair 
    { 
        int level;  // indicates weighting level 
                    // for 'always consider' 
        int limit;  // max index into weight array  
                    // used for computational efficiency 
        int ignore; // if non-zero no weighting will 
                    // be applied to this chair 
        int rankhi[1326]; // higher ranked card number 
                          // in pocket cards 
        int ranklo[1326]; // lower ranked card number 
                          // in pocket cards 
        int weight[1326]; // the significance value for this hand 
    double scratch;   // for future reference
    };

    //prwin 1326 structure 
    struct sprw1326 
    { 
        int useme; // unless set to 1326 the normal 
                   // OH prwin will be used 
        int preflop; // unless set to 1326 the normal 
                     // OH prwin will be used pre-flop 
        int usecallback; // unless set to 1326 the callback 
                         // function will not be invoked 
        double (*prw_callback)(void); // if enabled will be 
                                      // invoked before the 
                                      // prwin calculation pass 
        double scratch; // for future reference 
        int bblimp; // if non-zero no weighting 
                    // will be applied if a chair has put 
                    // nothing in the pot 
        sprw1326_chair vanilla_chair; // will be precalculated 
                                      // by OH at startup. 
                                      // convenience values 
        sprw1326_chair chair[10]; // structures for each chair 
    };

To use the structure from a User DLL, the following would also need to
be added:

    typedef sprw1326* pp13; pp13 prw1326;

Also, in the process_message handling add:

    if (strcmp(pmessage,"prw1326")==0) { prw1326 = (pp13)param; return 0; }

During OpenHoldem initialization the prw1326.vanilla_chair structure is
set up so that .level is 1024, the .ranklo and .rankhi arrays contain
the card order used for the normal OpenHoldem prwin calculation (but
expanded from 169 to 1326 format), the weighting of the high third of
these cards is set to 1024, the middle third is sloped down from 1024 to
zero, and .limit is set so that the final third are never even
considered as possible opponent cards. The .vanilla_chair is then copied
to each of the ten .chair structures. This should mean that even if the
capability is activated by setting .useme to 1326, the prwin calculation
will not crash and will give an anodyne weighted result. The
vanilla_chair is not referenced or used by OpenHoldem other than at
start-up, and could be altered and used by the DLL developer to act as a
default profile for new players.

A more complete explanation of how the prw1326 approach works can be
found in the {Weighted prwin} section.

### isfinalanswer 

isfinalanswer is a calculated symbol, designed for User-DLL developers,
who want to make sure, that certain calculations get executed only once
per turn. This symbols is true (non-zero) just prior to the Autoplayer
acting, and false (zero) any other time. More precise, it is true
exactly when

- it is your turn

- you see stable frames, i.e. when N scrapes is a row are identical (N
  is set in Edit-\>Preferences-\>Autoplayer-\>Frame Delay)

### dll\$iswait 

Prior to the OpenHoldem Autoplayer taking action, it will query your
User DLL, if one is loaded, for the value of dll\$iswait. If this query
returns non-zero, then OpenHoldem will not execute the Autoplayer’s
action this scrape cycle, but rather will skip the action and continue
to the next screen scrape cycle. While dll\$iswait returns non-zero,
isfinalanswer will always be zero.

In the forum Matrix explained
(<http://www.maxinmontreal.com/forums/viewtopic.php?f=174&t=6885>):

> While dll\$iwait is true you don’t have to respond to formula
> requests. You do have to somehow make sure that by the time
> dll\$iswait is true that OpenHoldem has been returned the correct
> result. When you go down this route you may encounter the problem of
> OpenHoldem stop evaluating the formulas, because there has been no
> change in state and dll\$iswait is false. The best thing to do in this
> case is to disable all caching inside of OpenHoldem and always return
> a valid formula result and set dll\$iswait to false on the very first
> valid frame with buttons you see from OpenHoldem.

In short: dll\$iswait will be only needed if you do very time-consuming
computations in your DLL that get executed in another thread. Just
return true to tell OH: Hey buddy, I am really busy atm. Please come
back later and do something else in the meantime. Scrape some additional
frames, sitin/sitout/rebuy etc. Most DLLs should just return false all
the time.

*isfinalanswer*: an OH-symbol that becomes true, when all conditions are
met that are required that the autoplayer acts (enough stable frames,
dll\$iswait = false, balance stability, etc.) If you want to execute
your DLL-logic only once, you should wait for isfinal-answer = true and
then calculate it on the fly. OH will be blocked for a small moment
while your DLL is working.

### cmd\$recalc 

A User DLL also has the ability to send the request cmd\$recalc, which
will force OpenHoldem to recompute the versus tables and restart the
Iterator thread.
