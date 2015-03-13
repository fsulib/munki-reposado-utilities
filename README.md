# munki-reposado-utilities

Some python scripts for manipulating munki repos and pkginfo files.

Script that creates munki apple_update_metadata pkginfos from a list of Apple update ids. The pkginfos are output to files in the directory from which you run the script.

It pulls a description and display name for each update from the output of repoutil.
Although there are options for each of these, I haven't implemented them yet.

Usage:
./bulk-pkginfo.py --catalog testing --catalog production --unattended_install input_list

input_list should be a file looking like:

031-17163<br/>
031-13757<br/>
031-17882<br/>
...

Don't forget to makecatalogs!
