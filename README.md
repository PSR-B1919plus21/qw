# qwp

This is a version of the bot under development by me. I called it 'qwp' for a few fun reasons:
* The postfix notations ++/+/pp/p are common ways of denoting incremental progress in programming.
* It makes the word symmetric with the 'p' mirroring the 'q', and kind of looks like a little face.
* It allows a fun phonetic pronunciation: "qwip".

I am not a strong crawl player, not nearly as strong as most, and I think much of success in crawl can be summarized as, 
a) avoid getting into dangerous situations, and
b) know when to get out of a dangerous situation
What I lack in Crawl skill, I make up for with expertise in artificial intelligence and machine learning. After watching the bot play for a while, I think it does a surprising amount of things really well, but it struggles to make sense of the game board (e.g. monsters, features, and terrain in view) in a way that is generic, consistent, and accessible to the decision making logic, instead relying on a bespoke behaviors tied to different actions.

If I were to approach this as a research project it would be really interesting to work with some deep learning models, but I think that is both less fun and kind of overkill. I am more interested in building a system that allows a "player" to sort of specify a character build and play style then see how the bot does. That sounds more fun to me as a player, letting me make high level strategic decisions and see how they unfold during the game. I also think it would be really useful to build an "assist mode" that does less to play the game automatically and more to support the player by providing information/recommendations and reducing tedious tasks.

Anyways, with all that in mind I have a I have a few main design goals:
1. Build a generic board state evaluation framework
- A first pass at this is already in place in the form of a "threat model" which calculates current visible melee/ranged threats, which is then used by kiting/buffing/escape logic.
- The board state model should provide a lot of options for strengthening the bot's skill at (a) and (b) above.
2. Overhaul the "plan" system into a "gambit" system which essentially adds a priority component to the plans. An implicit priority value for plans already exists in the form of their list ordering, but by adding explicit priorities we can then define separate collections of plans/gambits and then merge them into a single plan list for the bot to execute. This also makes the next goal much easier...
3. Modularize the gambit collections for different religions, backgrounds, and playstyles. Refactor existing plan/gambit code to use a common structure, with the goal of making gambit definitions more accessible to people who want to use the bot and roll their own strategies.
4. Build a casting framework and gambit model. This is a big lift and has been deliberately avoided in past development, for good reason. It would really advance the bot's potential in a lot of ways though, and I am cautiously optimistic that it could be feasible with the improvements in the lua API over the years and some inspiration from ToME4's Automatic Talent Gambits (https://te4.org/games/addons/tome/auto-talent-gambits).

I am not a developer so my code is pretty sloppy and hacky, I will do my best to try and clean things up regularly but no promises. I also can't promise this project won't be abandonded once something more interesting comes along.

# qw

This rcfile is elliptic's DCSS bot "qw", the first bot to win DCSS with no
human assistance. A substantial amount of code here was contributed by elliott
or borrowed from N78291's bot "xw", and many others have contributed as well.
The bot is now maintained by the dcss devteam. Please post bug reports as issues
on the official crawl qw repository:

https://github.com/crawl/qw/issues/new

The current version of qw can start as any species/background combination
and has some basic grasp of how many of the gods work (see qw.rc
for configuration details). Note though that most spells and racial
abilities aren't used, and qw is not very good at most combos! Its best
3-rune combo at the moment is DDFi^Makhleb, which manages a winrate of about
15% in offline testing. It can optionally try to get more runes, with its
preferred combo for that being GrFi^Okawaru, converting to TSO after Slime
(15-rune winrate for this build is about 1%).

## running on remote DCSS server
* Please make sure you have permission to run a bot on your server of choice!
  Misconfigured (or even well-configured) bots can eat up server CPU from
  actual players.
* go to e.g. http://crawl.akrasiac.org:8080/
* click "(edit rc)" link for DCSS trunk
* replace text that were here with contents of qw.rc file from this repo
* in ": DELAYED = false" and ": AUTO_START = false" lines change "false" to "true"
* you may also want to edit some of the configuration lines near the top
  of qw.rc (for instance to choose which combos qw will play)
* save and run DCSS trunk (either in webtiles or in console)
* enjoy!

Since clua works on the server side, webtiles drawing can lag behind things
actually happening, so the IRC bot [Sequell](https://github.com/greensnark/dcss_sequell) may tell you your character killed Sigmund or died to him before you see that with your own eyes. To see more current events just refresh the page and press "Tab". Alternatively, run or watch the bot in console (via ssh).

If you are familiar with Sequell, please add the name of the account that
you are using for qw to the "bot" nick with "!nick bot <accountname>" so
that games on the account can be easily filtered out of queries. (Also, please
don't run qw on the same account that you use for your own personal games!)

## running locally
* clone this repo
* run crawl locally with command like "./crawl -rc qw/qw.rc"
* enter name if necessary and start game. If you did not change "AUTO_START" variable, press "Tab"
* enjoy!

The file qw.exp is a simple expect script that automates running qw for many games in a row. The "AUTO_START" variable should be left at false when when using this. (With minor modifications, this can also be used to run games on a remote server over ssh.)

## miscellaneous tips for coding/testing qw
* run qw locally with the DCSS command-line option -seed <n> to use a seeded RNG for (mostly) reproducible testing
* uncomment the "say(plandata[2])" line in the cascade function to track what the bot is doing (very spammy)
* put code you want to test in the "ttt()" funtion on the bottom; make it run by macroing some key to "===ttt"
