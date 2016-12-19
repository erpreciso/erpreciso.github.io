---
title: Comandi frequenti Linux
date: 2016-12-13 17:16 +01:00
tags: [command-line,linux,non-original]

---


<!-- vim-markdown-toc GFM -->
* [Come copiare testo dal terminale, ad esempio da file di testo, direttamente negli appunti accessibili da Ctrl + v.](#come-copiare-testo-dal-terminale-ad-esempio-da-file-di-testo-direttamente-negli-appunti-accessibili-da-ctrl--v)
* [Conoscere quali dispositivi sono collegati, e lo spazio libero disponibile](#conoscere-quali-dispositivi-sono-collegati-e-lo-spazio-libero-disponibile)
* [Come ottenere, in VIM, la posizione corrente del cursore all'interno del documento, e di conseguenza i valori totali di righe, colonne, parole, caratteri e byte.](#come-ottenere-in-vim-la-posizione-corrente-del-cursore-allinterno-del-documento-e-di-conseguenza-i-valori-totali-di-righe-colonne-parole-caratteri-e-byte)
* [Come visualizzare i files di una certa dimensione](#come-visualizzare-i-files-di-una-certa-dimensione)
* [Generare una tabella dei contenuti in Vim](#generare-una-tabella-dei-contenuti-in-vim)
* [Session info](#session-info)

<!-- vim-markdown-toc -->

# Come copiare testo dal terminale, ad esempio da file di testo, direttamente negli appunti accessibili da Ctrl + v.

```
less NOME_DEL_FILE | xclip -selection=clipboard
```
oppure

```
xclip -in NOME_DEL_FILE -selection clipboard
```
dopodiché è possibile utilizzare `Ctrl + v` oppure tasto destro del mouse per incollare in un editor di testo o in una pagina web.
Si puó utilizzare il comando anche per scrivere un file di testo con il contenuto degli appunti.

```
xclip -out NOME_DEL_FILE -selection clipboard
```

È possibile abbreviare "clipboard" in "c".
Esempio:

```
man xclip | xclip -selection c


XCLIP(1)                                                      General Commands Manual                                                     XCLIP(1)

NAME
       xclip - command line interface to X selections (clipboard)

SYNOPSIS
       xclip [OPTION] [FILE]...

DESCRIPTION
       Reads  from  standard in, or from one or more files, and makes the data available as an X selection for pasting into X applications. Prints
       current X selection to standard out.

       -i, -in
              read text into X selection from standard input or files (default)

       -o, -out
              print the selection to standard out (generally for piping to a file or program)

       -f, -filter
              when xclip is invoked in the in mode with output level set to silent (the defaults), the filter option will cause xclip to print the
              text piped to standard in back to standard out unmodified

       -l, -loops
              number of X selection requests (pastes into X applications) to wait for before exiting, with a value of 0 (default) causing xclip to
              wait for an unlimited number of requests until another application (possibly another invocation of xclip)  takes  ownership  of  the
              selection

       -t, -target
              specify  a  particular data format using the given target atom.  With -o the special target atom name "TARGETS" can be used to get a
              list of valid target atoms for this selection.  For more information about target atoms refer to ICCCM section 2.6.2

       -d, -display
              X display to use (e.g. "localhost:0"), xclip defaults to the value in $DISPLAY if this option is omitted

       -h, -help
              show quick summary of options

       -selection
              specify which X selection to use, options are "primary" to use XA_PRIMARY (default), "secondary" for XA_SECONDARY or "clipboard" for
              XA_CLIPBOARD

       -version
              show version information

       -silent
              fork into the background to wait for requests, no informational output, errors only (default)

       -quiet show informational messages on the terminal and run in the foreground

       -verbose
              provide a running commentary of what xclip is doing

       -noutf8
              operate in legacy (i.e. non UTF-8) mode for backwards compatibility (Use this option only when really necessary, as the old behavior
              was broken)

       xclip reads text from standard in or files and makes it available to other X applications for pasting as an X selection (traditionally with
       the  middle  mouse button). It reads from all files specified, or from standard in if no files are specified. xclip can also print the con‐
       tents of a selection to standard out with the -o option.

       xclip was designed to allow tighter integration of X applications and command line programs. The default action is to silently wait in  the
       background for X selection requests (pastes) until another X application places data in the clipboard, at which point xclip exits silently.
       You can use the -verbose option to see if and when xclip actually receives selection requests from other X applications.

       Options can be abbreviated as long as they remain unambiguous. For example, it is possible to use -d or -disp instead of -display. However,
       -v couldn't be used because it is ambiguous (it could be short for -verbose or -version), so it would be interpreted as a filename.

       Note  that  only  the  first  character  of the selection specified with the -selection option is important. This means that "p", "sec" and
       "clip" would have the same effect as using "primary", "secondary" or "clipboard" respectively.

EXAMPLES
       I hate man pages without examples!

       uptime | xclip

       Put your uptime in the X selection. Then middle click in an X application to paste.

       xclip -loops 10 -verbose /etc/motd

       Exit after /etc/motd (message of the day) has been pasted 10 times. Show how many selection requests (pastes) have been processed.

       xclip -o > helloworld.c

       Put the contents of the selection into a file.

       xclip -t text/html index.html

       Middle click in an X application supporting HTML to paste the contents of the given file as HTML.

ENVIRONMENT
       DISPLAY
              X display to use if none is specified with the -display option.

REPORTING BUGS
       Please report any bugs, problems, queries, experiences, etc. directly to the author.

AUTHORS
       Kim Saunders <kims@debian.org> Peter Åstrand <astrand@lysator.liu.se>

                                                                                                                                          XCLIP(1)

```

Originale: [https://linuxtidbits.wordpress.com/2008/02/22/command-line-to-clipboard/](https://linuxtidbits.wordpress.com/2008/02/22/command-line-to-clipboard/){:target="_blank"}


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


# Come ottenere, in VIM, la posizione corrente del cursore all'interno del documento, e di conseguenza i valori totali di righe, colonne, parole, caratteri e byte.

Premere `g`, e subito dopo `<Ctrl> + g`

```
Col 1-8 of 94-108; Line 12 of 178; Word 135 of 3591; Char 1663 of 25334; Byte 1665 of 25471
```

Per invece contare il numero di volte che una specifica parola (o regex) compare nel documento, usare

`:%s/PATTERN//gn`

* `:` per iniziare il comando;
* `%` per cercare nell'intero documento; si puó sostituire con numeri di linee, oppure visuale`'<,'>`
* `s` il comando per sostituire;
* `PATTERN` la parola o regex da cercare;
* `spazio vuoto` dato che non sostituiamo con nulla;
* `g` per cercare tutte le occorrenze, non solo la prima di ogni linea;
* `n` per non sostituire nulla.

Originale: [http://vim.wikia.com/wiki/Word_count](http://vim.wikia.com/wiki/Word_count){:target="_blank"}


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
Originale:[http://unix.stackexchange.com/questions/140367/finding-all-large-files-in-the-root-filesystem](http://unix.stackexchange.com/questions/140367/finding-all-large-files-in-the-root-filesystem){:target="_blank"}


# Generare una tabella dei contenuti in Vim

Tramite il plugin **vim-markdown-toc**, ed il comando `GenTocGFM`.

Risultato in cima a questa pagina.


Originale:[https://github.com/mzlogin/vim-markdown-toc](https://github.com/mzlogin/vim-markdown-toc){:target="_blank"}


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
