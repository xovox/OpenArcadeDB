# OpenVGDB

Open Video Game Database (c) 2021 (https://github.com/xovox/OpenVGDB)

The Open Video Game DataBase is currently tracking 333,893 attributes of 16,750 games, BIOS & platforms!

I wasn't able to find something I could easily query to generate configs/etc that I wanted...
so I took a ton of data I found & normalized it.

Currently I'm only tracking arcade data, though console data is forthcoming

# Downloading

Check out https://github.com/xovox/OpenVGDB/releases

# Compiled Files

I've compiled several files for quick searches of the data:

* SQLite3
* tsv
* txt

# Querying SQLite3

Here are a few simple queries you can run against the database

```
select monitor_resolution_horizontal from arcade where game_rom = "mspacman";
select monitor_freq from arcade where game_rom = "mspacman";
select game_name from arcade where game_year between 1981 and 1982;
select game_rom from arcade where game_publisher = "Capcom";
select game_rom from arcade where game_mature = "TRUE";
```

Or you can get more specific

```
select game_rom from arcade where game_control_buttons = 1 and monitor_orientation = "V" and game_control_type = "joy4way";
```

Queries run from a bash shell

```
sqlite3 OpenVGDB.20210506.sqlite3 <<< "select game_rom from arcade where game_publisher = 'Capcom';"
echo "select game_rom from arcade where game_publisher = 'Capcom';" | sqlite3 OpenVGDB.20210506.sqlite3
```

Simple shell function for queries

```
rominfo() {
	sqlite3 OpenVGDB.20210506.sqlite3 <<< "select $2 from arcade where game_rom = '$1';"
}

rominfo mspacman monitor_resolution_horizontal
```

Speed on a Raspberry Pi 3 B+ is very respectable

```
(pi@RetroCRT:tmp)$ time sqlite3 OpenVGDB.20210506.sqlite3 <<< "select monitor_resolution_vertical from arcade where game_rom = 'mspacman';"
224

real    0m0.049s
user    0m0.019s
sys     0m0.028s
```

# Querying by HTTP GET

We also have a very limited and relatively slow "API" that's provided by simply using a curl

```
(pi@RetroCRT:~)$ time curl -sL "https://raw.githubusercontent.com/xovox/OpenVGDB/master/games/mspacman/monitor.resolution.vertical"
224

real    0m0.260s
user    0m0.065s
sys     0m0.013s
```

# Directory/File Structure

For ease of management & tracking changes/additions in GitHub.com, I elected to use separate files for each datapoint.
While this makes generation of the actual SQLite3 DB & TSV files, it takes 4m30s on my relatively old i5-4278U @ 2.6GHz.

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
