---
title: I miei appunti su Vim
excerpt: ""
date: 2019-12-15
tags: appunti
lang: it
ref: vim_notes

---

{:toc}

# Come ottenere, in VIM, la posizione corrente del cursore all'interno del documento

Questo permette di conseguenza di ottenere anche i valori totali di righe, colonne, parole, caratteri e byte.

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

Originale: [http://vim.wikia.com/wiki/Word_count](http://vim.wikia.com/wiki/Word_count){:target="_blank_"}



# Generare una tabella dei contenuti in Vim

Tramite il plugin **vim-markdown-toc**, ed il comando `GenTocGFM`.

Vedi: [https://github.com/mzlogin/vim-markdown-toc](https://github.com/mzlogin/vim-markdown-toc){:target="_blank_"}


# Cerca un codice esadecimale

Per cercare un carattere a partire dal suo codice esadecimale, ad esempio `0x53`, usa `/` per cercare, usando questo pattern: `\%x53`


# Folding in Vim

command	| effect
------- | -------------
zi      | switch folding on or off
za 	| toggle current fold open/closed
zc 	| close current fold
zR 	| open all folds
zM 	| close all folds
zv 	| expand folds to reveal cursor


# Uso dei plugin in Vim

Installazione via `pathogen`: [https://github.com/tpope/vim-pathogen](https://github.com/tpope/vim-pathogen){:target="_blank_"}

Poi i plugin vanno copiati nella cartella `~/.vim/bundle` clonando direttamente da github.

Plugin utili da installare:

* sensible https://github.com/tpope/vim-sensible
* fugitive https://github.com/tpope/vim-fugitive
* markdown-folding https://github.com/nelstrom/vim-markdown-folding


# Controllo ortografico in Vim

* abilita il controllo ortografico --> `:setlocal spell spelllang=en_us`
* disabilita il controllo ortografico  --> `:set nospell`


# Sospendi e riprendi Vim

* per sospendere Vim e andare nel terminale, mantenendo tutti i buffer aperti:  `CTRL - Z`
* per riprendere, digita da terminale e segui con INVIO:  `fg`

# Visualizza il risultato di un comando in una nuova finestra

Il comando `new` apre una nuova finestra, e seguito da un pipe `|` riempie la finestra con il risultato del comando che segue `r` che sta per "read". Il comando deve essere preceduto da `!`. Ad esempio, il comando qui sotto visualizza il numero di account gestiti da `ledger`.

`new|r!ledger accounts --count`

Vedi: [https://superuser.com/a/157999](https://superuser.com/a/157999){:target="_blank_"}


# Completamento automatico

In modalitá INSERT, per completare una parola digitare `CTRL-n`.

Per invece inserire file/directories, usare `CTRL-x CTRL-f`.

Vedi:

* [https://www.cs.oberlin.edu/~kuperman/help/vim/completion.html](https://www.cs.oberlin.edu/~kuperman/help/vim/completion.html){:target="_blank_"}
* [https://stackoverflow.com/a/1919511](https://stackoverflow.com/a/1919511){:target="_blank_"}


# Trova e sostituisci con newline

Per **cercare** il carattere di ritorno a capo, usa `\n`; per invece **sostituire** un carattere con un ritorno a capo, usa `\r`.

As esempio, questo comando sostituisce tutti i ritorni a capo con un doppio ritorno a capo:

```
:%s/\n/\r\r/g
```

Vedi: [https://stackoverflow.com/a/71334](https://stackoverflow.com/a/71334){:target="_blank_"}


# Trova e sostituisci in piú di un file alla volta

Ad esempio sostituisci 2018 con 2019 in tutti i files nella cartella corrente: 

```
:arg *.*       
:argdo %s/2018/2019/ge | update

```
Vedi: [https://vi.stackexchange.com/a/2342](https://vi.stackexchange.com/a/2342){:target="_blank_"}

# Trova e sostituisci mantenendo delle variabili

Usando il comando per trovare un pattern, è possibile usare una "wildcard" che sostituisce tutto quello che c'è intorno e mantiene la variabile. Bisogna includere il pattern tra parentesi, e nel testo che sostituisce usare `\1` e progressivi in base al numero delle variabili. Nell'esempio sottostante, sostituisco una stringa su una riga con una stringa, un ritorno a capo, ed un'altra stringa, mantenendo delle variabili. Il comando è su una riga sola, non ci sono ritorni a capo.

`%s/Stefano:Commissions:Paypal commissions\s*\(\d*.\d*\) EUR\n/Stefano:Commissions    \1 EUR\r  ;Which: Paypal\r/g`

Stringa precedente:

```
  Stefano:Commissions:Paypal commissions     2.50 EUR

```

Risultato dopo la sostituzione

```
  Stefano:Commissions    2.50 EUR
  ;Which: Paypal

```

Nota che per semplicita' non salvo il numero di spazi vuoti in una variabile, ma li sostituisco con quattro spazi, qualunque numero fosse prima, in quanto irrilevante.

# Inserire caratteri speciali

Per inserire caratteri non-ASCII come la `è` si possono utilizzare i digraphs: in modalitá INSERT, premere `CTRL-k` e seguire con la combinazione dei due caratteri per ottenere il simbolo desiderato.
Lista dei miei piú usati:

|Carattere|Combinazione|
|---------|------------|
|è|`CTRL-K e'`|
|è|`CTRL-K e!`|
|á|`CTRL-K a'`|
|backtick|`CTRL-K !'`|

Qualche nota dalla documentazione `help digraphs`:

```
To make it easy to remember the mnemonic, the second character has
a standard meaning:

	char name		char	meaning ~
	Exclamation mark	!	Grave
	Apostrophe		'	Acute accent

```

# Altri comandi per Vim

* copia il percorso del file corrente in un nuovo buffer --> `:let @" = expand("%:p") (for the general buffer @)`
* vedi gli schemi colore caricando lo script --> `source ~/Dropbox/myscript/setcolors.vim` (source [http://vim.wikia.com/wiki/Switch_color_scheme](http://vim.wikia.com/wiki/Switch_color_scheme){:target="_blank_"})
* apri un file in una nuova tab il cui nome è sotto il cursore: `<c-w>gf`

* inserire nel testo l'output di un comando da terminale

`read ! COMANDO`

Ad esempio,

```
read! ls -al ~ | grep vim

drwxrwxr-x  5 thename thename  4096 Dez 22 11:53 .vim
-rw-------  1 thename thename 38004 Dez 22 17:15 .viminfo
drwx------  3 thename thename  4096 Nov 29 22:29 .vimperator
lrwxrwxrwx  1 thename thename    22 Dez  8 13:41 .vimrc -> Dropbox/sys/vim/.vimrc

```


