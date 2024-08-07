# Gecko, Our Default Bot

## The Gecko story

We once had an old-school-member called Gecko from the Netherlands.
Gecko had no experience in the IT-business, but he was a really
dedicated guy; a hard worker and fast learner. Gecko worked round the
clock, contributed 500 posts per month to the forum, reported bugs
Sunday morning 4 am.Over the course of the next three years he rewrote
his bot at least 20 times from scratch, each time structuring it a bit
better and making it stronger. Hard work pays off and so Gecko finally
got a winning bot that made him a little fortune. Gecko traded his
well-earned botting-money for six renovated teeths, a twelf-man
internet-advertising-company in Pakistan and freedom from the Dutch
police.

One day Gecko decided to visit his employees in Pakistan. He took his
\$200-car (bought from all the money the Dutch police left him), found
the way through the snowy mountains of Austria and sunny Greece, missed
Pakistan by some miles but discovered Thailand where he now lives in
happiness.

Gecko finally quit botting; but as a true gentleman he generously
donated his pokerbot to the OpenHoldem community.

## Its playing style 

Lots of people used the Gecko-bot as a base for their own
poker-logic.Everybody praised its coding-style and its good play. Gecko
plays no-limit big-stack in a tight-aggressive (or maybe
semi-loose-aggressive) way. It uses PokerTracker to adapt to its
opponents, stealing more against tight blind-posters, value-betting
harder against calling-stations, being more cautious if a passive player
gives action and more. For example Gecko is prepared to play for full
stacks with mid-pair against hyper-maniacs. The Gecko-bot is by far the
best bot available to the public we have ever seen (demo-bots) or heard
of (commercials). It tries to squeeze the slightest edge in an
aggressive manner; so be prepared for some funny swings, hopefully more
often in the right direction.

## Does Gecko play tournaments?

To be honest: not really. Gecko is designed to play manly bigstack and
squeeze the slightest edge in an aggressive manner. But in tournaments
chip-EV and money-EV usually differ (except for winner-takes-it-all);
tournaments usually get played for survival and they often end with a
shortstacked push-fold-phase. Nothing Gecko is really designed for. The
Gecko-bot probably needs a little tweak on top of it that tempers its
aggression here and there a little bit and cares about ICM.

## Using the Gecko-bot

The Gecko-bot is now fully integrated and OpenHoldems default-bot. It
sits in the background and waits for situations where your bot-logic
does not specify an action. Then Gecko steps in. You can use Gecko in
several ways:

- play pure Gecko. Whenever your bot-logic is empty or not loaded or you
  click “New” then Gecko will care about everything and play all games
  from the beginning to the end. In other words: forget to load your
  bot-logic and increase your winnings.

- tweak Gecko. If Gecko satisfies you only 99% or if you want to adapt
  it to other game-types like tournaments, then you can easily tweak it.
  Don’t worry: you don’t have to change anything in Geckos code and
  possibly ruin it. All you have to do: create a new file with the
  exceptional situations that you want to play differently and leave the
  rest unspecified. Then OpenHoldem will play your bot-logic and Gecko
  will care about all the rest. We call this type of bot-logic a
  *Gecko-tweak*.

- use Gecko as a starting-point.for your own complete bot. Even if you
  want to create a bot completely from scratch Gecko will be useful. You
  can start your bot-logic as a Gecko-tweak and as long as your logic is
  incomplete Gecko will care about the forgotten situations. So you can
  for example test your half-made bot as if it was already finished.

- don’t use Gecko at all. If you are one of the fortunate
  old-school-members who already have a complete and better bot, then
  you don’t have to worry that Gecko ruins anything. You can safely
  delete the Gecko-file from the botlogic folder or just terminate all
  evaluations with  

      WHEN Others
         WHEN Others Fold FORCE
