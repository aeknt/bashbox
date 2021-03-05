# bashbox

A single script that gives coreutils like things written in pure `bash`.

This isnt intended to be really posix, it is missing lot of things from posix and some things behave differently.

# bashbox-bin
merge things in bin/ to one executable script

needs git, and bash and coreutils (tested only with gnu and freebsd ones)

# bashbox-lib
merge things on bin/ to one sourceable library called "bxlib"

wip

# bashbox-include
same as bashbox-lib but include code at start of another selected script

wip

# contributing
if you want contribute, **do not edit "bashbox/bxlib" file, its just automerged file, edit things in /bin or bashbox-bin/-lib/-include.**

# thanks
in bashbox is used:

https://github.com/lijaesela/bim (unlicense)

and other random scripts i found on iternet and were without license:
	- curl
	- ord
	- chr
	- hd
	- tac (most of)

