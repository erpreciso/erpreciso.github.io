---
title: I miei appunti su Linux
excerpt: Una raccolta dei comandi che ho trovato utile memorizzare per l'uso relativamente frequente che ne faccio
date: 2019-12-15
tags: [command-line,linux,non-original]
lang: it
ref: linux_notes

---

# Struttura delle cartelle in Linux

Origine: [http://www.thegeekstuff.com/2010/09/linux-file-system-structure/](http://www.thegeekstuff.com/2010/09/linux-file-system-structure/){:target="_blank_"}

```
   --- /bin   --> User Binaries
   |-- /sbin  --> System Binaries
   |-- /etc   --> Configuration Files
   |-- /dev   --> Device Files
   |-- /proc  --> Process Information
   |-- /var   --> Variable Files
/ -|-- /tmp   --> Temporary Files
   |-- /usr   --> User Programs
   |-- /home  --> Home Directories
   |-- /boot  --> Boot loader files
   |-- /lib   --> System libraries
   |-- /opt   --> Optional add-ons Apps
   |-- /mnt   --> Mount Directory
   |-- /media --> Removable Devices
   --- /srv   --> Service Data
```


# Comandi utili per terminale

* trova un file, as esempio .muttrc --> `locate .muttrc`

* mostra un calendario --> `cal`

* crea un alias --> `alias mutt='mutt -y'`

* crea un link simbolico nella directory corrente --> `ln -s TARGET`

* cancella directory e contenuto --> `[sudo] rm -r <dirname>`

* decomprimi un .gz file --> `gzip -d <filename>`

* decomprimi un .tar.bz2 file --> `tar -xjf <filename>`

* comprimi una jpeg --> `jpegoptim --size=250k tecmint.jpeg`


# Installare una macchina virtuale con linux e condividere una cartella col sistema operativo principale

* install `sudo apt-get install build-essentials linux-headers-`uname -r` dkms`
* install guest additions in virtual box options under Devices
* share folder with dropbox
* add current user to vboxsf users `sudo adduser xxxxxx vboxsf`
* logout and login
* you see folder in `/media`


# Usare Vim come editor per Git (e altro)


Set the GIT_EDITOR environment variable: `export GIT_EDITOR=vim`

Reference: [https://stackoverflow.com/a/2596835](https://stackoverflow.com/a/2596835){:target="_blank_"}


# Installare Sketchup
Now obsolete since there is sketchup in browser

* install `wine` from synaptic
* install `winetricks` from wget http://www.kegel.com/wine/winetricks
* execute winetricks by executing from where you downloaded `sudo sh ./winetricks`
* install sketchup 8


# Come copiare testo dal terminale, ad esempio da file di testo, direttamente negli appunti accessibili da Ctrl + v.

Usando il comando `xclip`

```
less <nome del file> | xclip -selection=clipboard
```

oppure

```
xclip -in <nome del file> -selection clipboard
```

dopodiché è possibile utilizzare `Ctrl + v` oppure tasto destro del mouse per incollare in un editor di testo o in una pagina web.
Si puó utilizzare il comando anche per scrivere un file di testo con il contenuto degli appunti.

```
xclip -out <nome del file> -selection clipboard
```

È possibile anche abbreviare "clipboard" in "c".

Originale: [https://linuxtidbits.wordpress.com/2008/02/22/command-line-to-clipboard/](https://linuxtidbits.wordpress.com/2008/02/22/command-line-to-clipboard/){:target="_blank_"}


# Come visualizzare i files di una certa dimensione

In questi esempi, mostro i files piú grandi di 50 megabytes presenti nella mia home folder e nelle cartelle contenute. Tre comandi simili per risultati simili.

```
find ~ -xdev -type f -size +50M

/home/thename/Dropbox/mail/mbox
/home/thename/Dropbox/mail/sent
/home/thename/Downloads/forest-trees/covtype.csv
/home/thename/.rvm/src/ruby-2.3.0/libruby-static.a
/home/thename/.rvm/rubies/ruby-2.3.0/lib/libruby-static.a
```


```
find ~ -type f -size +50M -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

/home/thename/Dropbox/mail/mbox: 211M
/home/thename/Dropbox/mail/sent: 74M
/home/thename/Downloads/forest-trees/covtype.csv: 72M
/home/thename/.rvm/src/ruby-2.3.0/libruby-static.a: 51M
/home/thename/.rvm/rubies/ruby-2.3.0/lib/libruby-static.a: 51M
```


```
find ~ -type f -size +50M | xargs du -sh

211M	/home/thename/Dropbox/mail/mbox
74M	/home/thename/Dropbox/mail/sent
72M	/home/thename/Downloads/forest-trees/covtype.csv
51M	/home/thename/.rvm/src/ruby-2.3.0/libruby-static.a
51M	/home/thename/.rvm/rubies/ruby-2.3.0/lib/libruby-static.a
```
Originale:[http://unix.stackexchange.com/questions/140367/finding-all-large-files-in-the-root-filesystem](http://unix.stackexchange.com/questions/140367/finding-all-large-files-in-the-root-filesystem){:target="_blank_"}


Questo comando invece stampa le cartelle più grandi

```
du -a ~ |sort -n -r|head -n 10

5423428	/home/thename
2479748	/home/thename/Dropbox
834536	/home/thename/Dropbox/books-school-knowledge
683716	/home/thename/.rvm
568612	/home/thename/.local
568608	/home/thename/.local/share
563604	/home/thename/.local/share/Trash
563232	/home/thename/.local/share/Trash/files
451952	/home/thename/.cache
412020	/home/thename/Downloads
```


Questo comando stampa le dimensioni delle cartelle

```
du -sh .

55M	2008-09-20_cornera
92M	2008-09-27_barba_ferrero
7,4M	2008-10-04_valletta
76M	2008-10-05_cinghiale
3,7M	2008-10-10_festa_pavia
24M	2008-10-12_gravellona


```

# Conoscere quali dispositivi sono collegati, e lo spazio libero disponibile


```
df -h

Filesystem      Size  Used Avail Use% Mounted on
udev            1,4G     0  1,4G   0% /dev
tmpfs           290M  5,0M  285M   2% /run
/dev/sda8       590G   12G  549G   3% /
tmpfs           1,5G  1,4M  1,5G   1% /dev/shm
tmpfs           5,0M  4,0K  5,0M   1% /run/lock
tmpfs           1,5G     0  1,5G   0% /sys/fs/cgroup
/dev/sda1       256M   33M  224M  13% /boot/efi
tmpfs           290M   40K  290M   1% /run/user/1000

```

Il parametro `-h` serve per avere lo spazio disponibile in multipli di 1024, ovvero megabytes or gigabytes.

```
df --help

Usage: df [OPTION]... [FILE]...
Show information about the file system on which each FILE resides,
or all file systems by default.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all             include pseudo, duplicate, inaccessible file systems
  -B, --block-size=SIZE  scale sizes by SIZE before printing them; e.g.,
                           '-BM' prints sizes in units of 1,048,576 bytes;
                           see SIZE format below
  -h, --human-readable  print sizes in powers of 1024 (e.g., 1023M)
  -H, --si              print sizes in powers of 1000 (e.g., 1.1G)
  -i, --inodes          list inode information instead of block usage
  -k                    like --block-size=1K
  -l, --local           limit listing to local file systems
      --no-sync         do not invoke sync before getting usage info (default)
      --output[=FIELD_LIST]  use the output format defined by FIELD_LIST,
                               or print all fields if FIELD_LIST is omitted.
  -P, --portability     use the POSIX output format
      --sync            invoke sync before getting usage info
      --total           elide all entries insignificant to available space,
                          and produce a grand total
  -t, --type=TYPE       limit listing to file systems of type TYPE
  -T, --print-type      print file system type
  -x, --exclude-type=TYPE   limit listing to file systems not of type TYPE
  -v                    (ignored)
      --help     display this help and exit
      --version  output version information and exit

Display values are in units of the first available SIZE from --block-size,
and the DF_BLOCK_SIZE, BLOCK_SIZE and BLOCKSIZE environment variables.
Otherwise, units default to 1024 bytes (or 512 if POSIXLY_CORRECT is set).

The SIZE argument is an integer and optional unit (example: 10K is 10*1024).
Units are K,M,G,T,P,E,Z,Y (powers of 1024) or KB,MB,... (powers of 1000).

FIELD_LIST is a comma-separated list of columns to be included.  Valid
field names are: 'source', 'fstype', 'itotal', 'iused', 'iavail', 'ipcent',
'size', 'used', 'avail', 'pcent', 'file' and 'target' (see info page).

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Full documentation at: <http://www.gnu.org/software/coreutils/df>
or available locally via: info '(coreutils) df invocation'

```


# Usare il tasto di blocco maiuscole come Control

Se lavorate in Ubuntu, installate `gnome-tweaks` e selezionate la voce appropriata.

Se invece siete su una distribuzione per cui i tweaks non sono disponibili:

* vai in `Settings` --> `Setting Manager` --> `Session and Startup`
* aggiungi alla voce `autostart`:

```
/usr/bin/setxkbmap -option "ctrl:nocaps"
```

Origine:

* [serverfault](http://serverfault.com/questions/10437/how-do-you-swap-the-caps-lock-to-control-in-xfce#10537){:target="_blank_"}
* [askubuntu](http://askubuntu.com/questions/77066/modify-xfce-startup-applications#77147){:target="_blank_"}


# Sincronizzare una cartella locale con cartella remota

```
rsync -rv . /media/etcetcetc
```

Origine: [tecmint](http://www.tecmint.com/rsync-local-remote-file-synchronization-commands/){:target="_blank_"}


# Uso del comando grep

Uso: `grep OPTS EXPR FILE`

```
-l    : print only filenames containing
-n    : print also line number
-v    : negative, aka print lines that don't match (useful to pipe to another grep to exclude results)
-c    : count occurrences
-i    : ignore case
-x    : look for exact matches only
fgrep : fast command to look for exact matches only
egrep : extended regular expressions use
-C 6  : print 6 lines around
-r    : search recursively in subdirs
```


# Uso del terminale `tmux`

* start tmux --> `tmux`
* exit tmux --> `exit`
* start named session --> `tmux new-session -s <session name>`
* prefix (command to prefix to other commands) --> remapped in `~/.tmux.conf to `C-a`
* detach from session --> `P d` (PREFIX then d)
* kill session --> `tmux kill-session -t <name>`
* list sessions --> `tmux ls` (list-sessions)
* attach to a session --> `tmux attach` (if more than one session open, -t and session name)
* create a new window --> `P c`
* move between windows --> `P n, P p`
* rename window --> `P ,`
* jump to a window by index --> `P 0`, `P 1` etc
* find a window by name --> `P f`
* list all windows --> `P w`
* rename session --> `P $`
* vertical split --> `P %`
* horizontal split --> `P "`
* change layout template --> `P spacebar`
* closing panel --> `P x` or type exit
* enter command mode --> `P :`
* create a "Master" session with "Top" first window --> `tmus new -s Master -n Top`
* modify tmux config --> `~/.tmux.conf`


# Installa Digikam
download the appimage, maxe it exec using chmod a+x, then run the app.


# Install simple-scan
=============================

since the version from Ubuntu SOftware Center doesn't work, I rebuilt from source following instr. here

https://gitlab.gnome.org/GNOME/simple-scan

then, to launch, from terminal hit

> XDG_DATA_DIRS=install/share:$XDG_DATA_DIRS ./install/bin/simple-scan





# [How to re-install Ubuntu keeping your data and settings](https://ubuntuforums.org/showthread.php?t=2057342)


# Session info

```
uname -mrs

> Linux 4.8.0-12.2-liquorix-amd64 x86_64

lsb_release -a

> Distributor ID:	Ubuntu
> Description:	Ubuntu 16.04.1 LTS
> Release:	16.04
> Codename:	xenial
```

