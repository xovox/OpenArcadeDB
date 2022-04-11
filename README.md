# OpenArcadeDB

Open Arcade DataBase (c) 2021 (https://github.com/xovox/OpenArcadeDB)

The OpenArcadeDB is currently tracking
349,900
attributes of 
17,775
games, BIOS & platforms!

I wasn't able to find something I could easily query to generate configs/etc that I wanted...
so I took a ton of data I found & normalized it.

# Sources

* https://github.com/finalburnneo/FBNeo
* https://www.progettosnaps.net
* https://arcade-history.com

# Projects that use OpenArcadeDB

None that I'm aware of, but I am going to implement using it with one current and a few future projects

* https://github.com/xovox/RetroCRT/

I'm also going to start writing my own EmulationStation scraper that strictly utilizes my database for information and then downloads images/videos.  This should help alieviate people experiencing timeouts.

# Downloading Compiled Files

The [releases](https://github.com/xovox/OpenArcadeDB/releases) page has already generated coallated files for ease of use

* SQLite3
* tsv
* txt

# Querying from inside of SQLite3

Here are a few simple queries you can run against the database

## Get the arcade-history.com entry for Rolling Thunder

```
select game_history from history where game_rom = "rthunder";
```

## Find all games published by Capcom

```
select game_rom from arcade where game_publisher = "Capcom";
```

## Find all games published, licensed, or co-published by Capcom

```
select game_rom from arcade where game_publisher like "%Capcom%";
```

## Find the naughty games!

```
select game_rom from arcade where game_mature = "TRUE";
```

## Pull up some monitor specs for Ms Pac-Man

```
select monitor_resolution_horizontal from arcade where game_rom = "mspacman";
select monitor_freq from arcade where game_rom = "mspacman";
```

## What games came out between 1981-1982?

```
select game_name from arcade where game_year between 1981 and 1982;
```

## Or you can get more specific like vertical games with a 4 way joystick & one button

```
select game_rom from arcade where game_control_buttons = 1 and monitor_orientation = "V" and game_control_type = "joy4way";
```

## Find games where the history mentions Evil Otto!

```
select game_rom from history where game_history like "%Evil Otto%";
```

# Queries run from a bash shell

```
sqlite3 OpenArcadeDB.sqlite3 <<< "select game_rom from arcade where game_publisher = 'Capcom';"
echo "select game_rom from arcade where game_publisher = 'Capcom';" | sqlite3 OpenArcadeDB.sqlite3
```

## Simple shell function for queries

```
rominfo() {
	sqlite3 OpenArcadeDB.sqlite3 <<< "select $2 from arcade where game_rom = '$1';"
}

rominfo mspacman monitor_resolution_horizontal
```

# Speed on a Raspberry Pi 3 B+ is very respectable

```
(pi@RetroCRT:tmp)$ time sqlite3 OpenArcadeDB.sqlite3 <<< "select monitor_resolution_vertical from arcade where game_rom = 'mspacman';"
224

real    0m0.049s
user    0m0.019s
sys     0m0.028s
```

# Querying by HTTP GET

We also have a very limited and relatively slow "API" that's provided by simply using a curl

```
(pi@RetroCRT:~)$ time curl -sL "https://raw.githubusercontent.com/xovox/OpenArcadeDB/master/games/mspacman/monitor.resolution.vertical"
224

real    0m0.260s
user    0m0.065s
sys     0m0.013s
```

# Directory/File Structure

For ease of management & tracking changes/additions in GitHub.com, I elected to use separate files for each datapoint.
While this makes maintaining and tracking changes far easier, it takes ~8m on my i5-7500 @ 3.8ghz to generate all files.
About half of the run time is inserting the historical data (2/3rds of the entire database's size), a third is collating the separate files, and the rest is generating TSV/TXT output.

Each game has a directory, and then there are several files within that describe aspects of that game.

The basic structure is pretty self-explanatory.
Here's an example tree for a game:

~~~~
game.name
game.name.short
game.publisher
game.year
game.genre
game.romof
game.bios
game.cloneof
game.coinslots
game.control.buttons
game.control.count
game.control.type
game.mature
monitor.count
monitor.freq
monitor.orientation
monitor.ratio.horizontal
monitor.ratio.vertical
monitor.resolution.horizontal
monitor.resolution.vertical
monitor.type
~~~~

And here's an example of the contents of those individual files:

~~~~
game.cloneof			yiear
game.coinslots			2
game.control.buttons		2
game.control.count		2
game.control.type		joy8way
game.genre			Fighter / Versus
game.name			Yie Ar Kung-Fu (GX361 conversion)
game.publisher			Konami
game.year			1985
monitor.freq			60.000000
monitor.orientation		H
monitor.ratio.horizontal	4
monitor.ratio.vertical		3
monitor.resolution.horizontal	256
monitor.resolution.vertical	224
monitor.type			R
~~~~

# Sparse Checkout

If you just want the database to be updated whenever upstream is updated *and* you don't want the other 1.4GB of space used up (it's still like 100MB thanks to git history), do a sparse checkout of just the DB!

```
mkdir $HOME/OpenArcadeDB &&
cd $HOME/OpenArcadeDB &&
git init &&
git config core.sparsecheckout true &&
echo release/ >> .git/info/sparse-checkout &&
git remote add -f origin https://github.com/xovox/OpenArcadeDB &&
git pull --depth=1 origin master
```
