# OpenArcadeDB

Currently tracking 148,300 attributes of 10,925 games!

I wasn't able to find something I could easily query to generate configs/etc that I wanted... so I took a ton of data I found & normalized it.

The structure is very simple. Each game has a directory, and then there are several files within that describe aspects of that game.

Although I haven't tried yet, this should be very easy to import into an actual database like MariaDB, SQLite, MySQL, Oracle, etc.

The basic structure is pretty self-explanatory.  Here's an example tree for a game.  It's missing game.romof, which is typically a BIOS.

~~~~
yieartf
├── game.cloneof
├── game.coinslots
├── game.control.buttons
├── game.control.count
├── game.control.type
├── game.genre
├── game.name
├── game.year
├── monitor.freq
├── monitor.orientation
├── monitor.ratio.horizontal
├── monitor.ratio.vertical
├── monitor.resolution.horizontal
├── monitor.resolution.vertical
└── monitor.type
~~~~

And here's the contents of those individual files:

~~~~
game.cloneof			yiear
game.coinslots			2
game.control.buttons		2
game.control.count		2
game.control.type		joy8way
game.genre			Fighter / Versus
game.name			Yie Ar Kung-Fu (GX361 conversion)
game.year			1985
monitor.freq			60.000000
monitor.orientation		H
monitor.ratio.horizontal	4
monitor.ratio.vertical		3
monitor.resolution.horizontal	256
monitor.resolution.vertical	224
monitor.type			R
~~~~
