---
title: "Global Terrorism Database: un'analisi esplorativa"
excerpt: Per familiarizzare col database in previsione di analisi future.
date: 2016-08-09
lang: it
ref: terrorism
tags: analisi-esplorative-di-dati
    
---

-   [Introduzione](#introduzione)
-   [Preparazione dell'ambiente di lavoro](#preparazione-dellambiente-di-lavoro)
-   [Preparazione dei dati](#preparazione-dei-dati)
-   [Esplorazione](#esplorazione)
    -   [Dimensioni del dataset](#dimensioni-del-dataset)
    -   [Nomi e struttura delle variabili](#nomi-e-struttura-delle-variabili)
    -   [Uno sguardo ai dati](#uno-sguardo-ai-dati)
        -   [Caso dell'Undici Settembre](#caso-dellundici-settembre)
        -   [Caso della strage alla stazione di Bologna](#caso-della-strage-alla-stazione-di-bologna)
    -   [Conversione nel formato Table Data Frame](#conversione-nel-formato-table-data-frame)
    -   [Tipologie delle variabili](#tipologie-delle-variabili)
    -   [Approfondimento su alcune variabili](#approfondimento-su-alcune-variabili)
        -   [Geolocalizzazioni mancanti](#geolocalizzazioni-mancanti)
        -   [Variabile "numero persone uccise" `nkill` mancante](#variabile-numero-persone-uccise-nkill-mancante)
        -   [Creazione variabile "data" `date`](#creazione-variabile-data-date)
    -   [Gruppi di variabili](#gruppi-di-variabili)
-   [Analisi per gruppi di variabili](#analisi-per-gruppi-di-variabili)
    -   [ID e variabili temporali](#id-e-variabili-temporali)
    -   [Informazioni generali sull'evento](#informazioni-generali-sullevento)
    -   [Localizzazione](#localizzazione)
        -   [Mappa di frequenza](#mappa-di-frequenza)
        -   [Choropleth](#choropleth)
        -   [Grafico mediante `ggmap`](#grafico-mediante-ggmap)
    -   [Tipo di attacco](#tipo-di-attacco)
        -   [Attacco suicida?](#attacco-suicida)
    -   [Armi](#armi)
    -   [Tipo di obiettivo e vittime](#tipo-di-obiettivo-e-vittime)
        -   [Grafico per tipi di obiettivo](#grafico-per-tipi-di-obiettivo)
        -   [Nazionalita della vittima](#nazionalita-della-vittima)
    -   [Informazioni sull'attentatore](#informazioni-sullattentatore)
    -   [Vittime e conseguenze](#vittime-e-conseguenze)
        -   [Densita' degli attacchi in base al numero di persone uccise](#densita-degli-attacchi-in-base-al-numero-di-persone-uccise)
        -   [Conseguenze economiche](#conseguenze-economiche)
-   [Alcuni esempi di analisi multivariate](#alcuni-esempi-di-analisi-multivariate)
    -   [Paese con il piu elevato numero di vittime](#paese-con-il-piu-elevato-numero-di-vittime)
    -   [Nazionalita dei morti per paese dove l'attacco e' avvenuto](#nazionalita-dei-morti-per-paese-dove-lattacco-e-avvenuto)
    -   [Evento col piu' elevato numero di vittime](#evento-col-piu-elevato-numero-di-vittime)
    -   [Eventi avvenuti in Italia](#eventi-avvenuti-in-italia)

![Giuseppe Memeo in Via De Amicis a Milano il 14 Maggio 1977, e l'interno della banca di Piazza Fontana a Milano dopo la strage del 12 Dicembre 1969. Credits: [Wikipedia](https://it.wikipedia.org/wiki/Anni_di_piombo)]({{ site.url }}/assets/images/terrorism/anni_di_piombo.jpg)

Introduzione
============

Il GTD e' un database open source contenente informazioni su eventi di terrorismo dal 1970 al 2015. In queste pagine mi limito all'esplorazione delle informazioni contenute come preparazione per una futura analisi. Non ci saranno quindi analisi approfondite, commenti, e quindi nemmeno tesi e conclusioni.

Riporto la descrizione dettagliata del database dalla pagina introduttiva presente nel portale web <http://start.umd.edu/gtd/about/>

> Overview of the GTD
>
> The Global Terrorism Database (GTD) is an open-source database including information on terrorist events around the world from 1970 through 2015 (with additional annual updates planned for the future). Unlike many other event databases, the GTD includes systematic data on domestic as well as transnational and international terrorist incidents that have occurred during this time period and now includes more than 150,000 cases. For each GTD incident, information is available on the date and location of the incident, the weapons used and nature of the target, the number of casualties, and--when identifiable--the group or individual responsible.
>
> Statistical information contained in the Global Terrorism Database is based on reports from a variety of open media sources. Information is not added to the GTD unless and until we have determined the sources are credible. Users should not infer any additional actions or results beyond what is presented in a GTD entry and specifically, users should not infer an individual associated with a particular incident was tried and convicted of terrorism or any other criminal offense. If new documentation about an event becomes available, an entry may be modified, as necessary and appropriate.
>
> The National Consortium for the Study of Terrorism and Responses to Terrorism (START) makes the GTD available via this online interface in an effort to increase understanding of terrorist violence so that it can be more readily studied and defeated.

Preparazione dell'ambiente di lavoro
====================================

{% highlight r %}
library(tidyverse)
db_original_path <- "~/Downloads/globalterrorismdb_0616dist.csv"
{% endhighlight %}

Preparazione dei dati
=====================

Scarico i dati da [Kaggle](https://www.kaggle.com/START-UMD/gtd){:target="_blank"} e li importo in R tramite il pacchetto [`readr`](http://readr.tidyverse.org/){:target="_blank"}. Alcune variabili sono automaticamente lette come formato `integer` (`readr` deriva i formati colonne dalle prime 1000 osservazioni) ma cio non e' corretto e quindi fornisco manualmente i formati, almeno per queste osservazioni errate.

{% highlight r %}
db <-
  readr::read_csv(db_original_path,
                  col_types = readr::cols(nkill = readr::col_double(),
                                          nwound = readr::col_double(),
                                          propvalue = readr::col_double(),
                                          nhours = readr::col_double(),
                                          nperpcap = readr::col_double(),
                                          nhostkid = readr::col_double(),
                                          nreleased = readr::col_double(),
                                          nkillter = readr::col_double(),
                                          nkillus = readr::col_double(),
                                          nwoundus = readr::col_double(),
                                          ransomamt = readr::col_double(),
                                          nwoundte = readr::col_double(),
                                          ransompaid = readr::col_double()))
{% endhighlight %}

Esplorazione
============

Dimensioni del dataset
----------------------

Numero di righe e colonne

{% highlight r %}
db %>% dim()
{% endhighlight %}

    ## [1] 156772    138

Nomi e struttura delle variabili
--------------------------------

{% highlight r %}
dplyr::glimpse(db)
{% endhighlight %}

    ## Observations: 156,772
    ## Variables: 137
    ## $ eventid            <dbl> 197000000001, 197000000002, 197001000001, 1...
    ## $ iyear              <int> 1970, 1970, 1970, 1970, 1970, 1970, 1970, 1...
    ## $ imonth             <int> 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1...
    ## $ iday               <int> 0, 0, 0, 0, 0, 1, 2, 2, 2, 3, 1, 6, 8, 9, 9...
    ## $ approxdate         <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ extended           <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ resolution         <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ country            <int> 58, 130, 160, 78, 101, 217, 218, 217, 217, ...
    ## $ country_txt        <chr> "Dominican Republic", "Mexico", "Philippine...
    ## $ region             <int> 2, 1, 5, 8, 4, 1, 3, 1, 1, 1, 1, 1, 8, 1, 1...
    ## $ region_txt         <chr> "Central America & Caribbean", "North Ameri...
    ## $ provstate          <chr> NA, NA, "Tarlac", "Attica", NA, "Illinois",...
    ## $ city               <chr> "Santo Domingo", "Mexico city", "Unknown", ...
    ## $ latitude           <dbl> 18.45679, 19.43261, 15.47860, 37.98377, 33....
    ## $ longitude          <dbl> -69.95116, -99.13321, 120.59974, 23.72816, ...
    ## $ specificity        <int> 1, 1, 4, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1...
    ## $ vicinity           <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ location           <chr> NA, NA, NA, NA, NA, NA, NA, "Edes Substatio...
    ## $ summary            <chr> NA, NA, NA, NA, NA, "1/1/1970: Unknown Afri...
    ## $ crit1              <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1...
    ## $ crit2              <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1...
    ## $ crit3              <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1...
    ## $ doubtterr          <int> 0, 0, 0, 0, -9, 0, 0, 1, 0, 0, 1, 1, -9, 0,...
    ## $ alternative        <int> NA, NA, NA, NA, NA, NA, NA, 2, NA, NA, 1, 2...
    ## $ alternative_txt    <chr> ".", ".", ".", ".", ".", ".", ".", "Other C...
    ## $ multiple           <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ success            <int> 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1...
    ## $ suicide            <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ attacktype1        <int> 1, 6, 1, 3, 7, 2, 1, 3, 7, 7, 3, 7, 4, 7, 7...
    ## $ attacktype1_txt    <chr> "Assassination", "Hostage Taking (Kidnappin...
    ## $ attacktype2        <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ attacktype2_txt    <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ attacktype3        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ attacktype3_txt    <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ targtype1          <int> 14, 7, 10, 7, 7, 3, 3, 21, 4, 2, 4, 4, 6, 2...
    ## $ targtype1_txt      <chr> "Private Citizens & Property", "Government ...
    ## $ targsubtype1       <int> 68, 45, 54, 46, 46, 22, 25, 107, 28, 21, 27...
    ## $ targsubtype1_txt   <chr> "Named Civilian", "Diplomatic Personnel (ou...
    ## $ corp1              <chr> NA, "Belgian Ambassador Daughter", "Voice o...
    ## $ target1            <chr> "Julio Guzman", "Nadine Chaval, daughter", ...
    ## $ natlty1            <int> 58, 21, 217, 217, 217, 217, 218, 217, 217, ...
    ## $ natlty1_txt        <chr> "Dominican Republic", "Belgium", "United St...
    ## $ targtype2          <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ targtype2_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ targsubtype2       <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ targsubtype2_txt   <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ corp2              <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ target2            <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ natlty2            <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ natlty2_txt        <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ targtype3          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ targtype3_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ targsubtype3       <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ targsubtype3_txt   <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ corp3              <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ target3            <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ natlty3            <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ natlty3_txt        <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ gname              <chr> "MANO-D", "23rd of September Communist Leag...
    ## $ gsubname           <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ gname2             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ gsubname2          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ gname3             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ingroup            <int> 3629, 3330, -9, -9, -9, 2373, 623, -9, 1000...
    ## $ ingroup2           <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ingroup3           <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ gsubname3          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ motive             <chr> NA, NA, NA, NA, NA, "To protest the Cairo I...
    ## $ guncertain1        <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1...
    ## $ guncertain2        <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ guncertain3        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ nperps             <int> NA, 7, NA, NA, NA, -99, 3, -99, 1, 1, NA, -...
    ## $ nperpcap           <dbl> NA, NA, NA, NA, NA, -99, NA, -99, 1, 1, NA,...
    ## $ claimed            <int> NA, NA, NA, NA, NA, 0, NA, 0, 1, 0, NA, 0, ...
    ## $ claimmode          <int> NA, NA, NA, NA, NA, NA, NA, NA, 1, NA, NA, ...
    ## $ claimmode_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "Le...
    ## $ claim2             <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ claimmode2         <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ claimmode2_txt     <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ claim3             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ claimmode3         <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ claimmode3_txt     <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ compclaim          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weaptype1          <int> 13, 13, 13, 6, 8, 5, 5, 6, 8, 8, 6, 8, 5, 8...
    ## $ weaptype1_txt      <chr> "Unknown", "Unknown", "Unknown", "Explosive...
    ## $ weapsubtype1       <int> NA, NA, NA, 16, NA, 5, 2, 16, 19, 20, 16, 1...
    ## $ weapsubtype1_txt   <chr> ".", ".", ".", "Unknown Explosive Type", "....
    ## $ weaptype2          <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weaptype2_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weapsubtype2       <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weapsubtype2_txt   <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weaptype3          <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weaptype3_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weapsubtype3       <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weapsubtype3_txt   <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weaptype4          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weaptype4_txt      <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weapsubtype4       <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ weapsubtype4_txt   <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ weapdetail         <chr> NA, NA, NA, "Explosive", "Incendiary", "Sev...
    ## $ nkill              <dbl> 1, 0, 1, NA, NA, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ nkillus            <dbl> NA, NA, NA, NA, NA, 0, NA, 0, 0, 0, NA, 0, ...
    ## $ nkillter           <dbl> NA, NA, NA, NA, NA, 0, NA, 0, 0, 0, NA, 0, ...
    ## $ nwound             <dbl> 0, 0, 0, NA, NA, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ nwoundus           <dbl> NA, NA, NA, NA, NA, 0, NA, 0, 0, 0, NA, 0, ...
    ## $ nwoundte           <dbl> NA, NA, NA, NA, NA, 0, NA, 0, 0, 0, NA, 0, ...
    ## $ property           <int> 0, 0, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1...
    ## $ propextent         <int> NA, NA, NA, NA, NA, 3, NA, 3, 3, 3, 3, 3, N...
    ## $ propextent_txt     <chr> ".", ".", ".", ".", ".", "Minor (likely < $...
    ## $ propvalue          <dbl> NA, NA, NA, NA, NA, NA, NA, 22500, 60000, N...
    ## $ propcomment        <chr> NA, NA, NA, NA, NA, NA, NA, "Three transfor...
    ## $ ishostkid          <int> 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ nhostkid           <dbl> NA, 1, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ nhostkidus         <int> NA, 0, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ nhours             <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ndays              <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ divert             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ kidhijcountry      <chr> NA, "Mexico", NA, NA, NA, NA, NA, NA, NA, N...
    ## $ ransom             <int> 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0...
    ## $ ransomamt          <dbl> NA, 8e+05, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ ransomamtus        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ransompaid         <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ransompaidus       <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ransomnote         <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ hostkidoutcome     <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ hostkidoutcome_txt <chr> ".", ".", ".", ".", ".", ".", ".", ".", "."...
    ## $ nreleased          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ addnotes           <chr> NA, NA, NA, NA, NA, "The Cairo Chief of Pol...
    ## $ scite1             <chr> NA, NA, NA, NA, NA, "\"Police Chief Quits,\...
    ## $ scite2             <chr> NA, NA, NA, NA, NA, "\"Cairo Police Chief Q...
    ## $ scite3             <chr> NA, NA, NA, NA, NA, "Christopher Hewitt, \"...
    ## $ dbsource           <chr> "PGIS", "PGIS", "PGIS", "PGIS", "PGIS", "He...
    ## $ INT_LOG            <int> 0, 0, -9, -9, -9, -9, 0, -9, 0, 0, 0, -9, -...
    ## $ INT_IDEO           <int> 0, 1, -9, -9, -9, -9, 0, -9, 0, 0, 0, -9, -...
    ## $ INT_MISC           <int> 0, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0...
    ## $ INT_ANY            <int> 0, 1, 1, 1, 1, -9, 0, -9, 0, 0, 0, -9, 1, -...
    ## $ related            <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...

I nomi delle variabili sono in un formato consistente, non necessitano di revisione e modifica.

Le variabili presentano delle classi adeguate ai dati che rappresentano; tutte le specifiche sono nel [codebook](http://www.start.umd.edu/gtd/downloads/Codebook.pdf).

Uno sguardo ai dati
-------------------

### Caso dell'Undici Settembre

{% highlight r %}
db %>% 
  filter(iyear == 2001,
         imonth == 9,
         iday == 11,
         country_txt == "United States") %>% 
  print.data.frame
{% endhighlight %}

    ##        eventid iyear imonth iday approxdate extended resolution country
    ## 1 200109110004  2001      9   11       <NA>        0       <NA>     217
    ## 2 200109110005  2001      9   11       <NA>        0       <NA>     217
    ## 3 200109110006  2001      9   11       <NA>        0       <NA>     217
    ## 4 200109110007  2001      9   11       <NA>        0       <NA>     217
    ##     country_txt region    region_txt    provstate          city latitude
    ## 1 United States      1 North America     New York New York City 40.71278
    ## 2 United States      1 North America     New York New York City 40.71278
    ## 3 United States      1 North America     Virginia     Arlington 38.88078
    ## 4 United States      1 North America Pennsylvania   Shanksville 40.01846
    ##   longitude specificity vicinity
    ## 1 -74.00594           1        0
    ## 2 -74.00594           1        0
    ## 3 -77.10827           1        0
    ## 4 -78.90720           1        1
    ##                                                                                                          location
    ## 1                                                                                                            <NA>
    ## 2                                                                                                            <NA>
    ## 3                                                                                                            <NA>
    ## 4 The hijacked plane crash-landed 150 miles northwest of Washington D.C. in a field near the town of Shanksville.
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                summary
    ## 1                                                                                                                                                                              09/11/2001: This was one of four related terrorist attacks that occurred in the U.S.A. and that are known collectively as the 9/11 Terrorist Attacks. In the first attack, which occurred at 8:46 am local time, American Airlines Flight 11 crashed into the North Tower of the World Trade Center Complex in New York City. Five hijackers belonging to the terrorist organization, Al-Qa`ida, took control of the Boeing 767 aircraft on a flight originating from Logan International Airport in Boston, Massachusetts, and destined for Los Angeles International Airport. On the airplane were 76 passengers, 11 crew members and 5 hijackers, all of whom died upon impact. The North Tower collapsed at 10:28 am local time after the South Tower had collapsed at 9:59 am local time.  A combined total of 2603 people died in the World Trade Center attacks, not counting the Flight 11 hijack victims.  Although not immediately known at the time of the attack, the Al-Qa`ida terrorist network, lead by Osama bin Laden, was responsible for all four of these attacks. Al-Qa`ida leaders such as bin Laden and Khalid Shaikh Mohammed claimed responsibility for the attacks in multiple videotaped interviews over the next few years.
    ## 2                                                                                     09/11/2001: This was one of four related terrorist attacks that occurred in the U.S.A. and that are known collectively as the 9/11 Terrorist Attacks. In the second of these attacks, which occurred at 9:03 am local time, United Airlines Flight 175 crashed into the South Tower of the World Trade Center Complex in New York City. Five hijackers belonging to the terrorist organization, Al-Qa`ida, hijacked the Boeing 767 aircraft on a flight originating from Logan International Airport in Boston, Massachusetts, and destined for Los Angeles International Airport. On the plane were 56 passengers, 9 crew members and 5 hijackers, all of whom died upon impact.  The South Tower collapsed at 9:59 am local time, killing all who were trapped inside or located nearby.  The North Tower collapsed, after having been struck by American Airlines Flight 11, at 10:28 am local time.  2603 people died in the two World Trade Center attacks not counting the Flight 175 hijack victims.  Although not immediately known at the time of the attack, the Al-Qa`ida terrorist network, lead by Osama bin Laden, was responsible for all four of these attacks. Al-Qa`ida leaders such as bin Laden and Khalid Shaikh Mohammed claimed responsibility for the attacks in multiple videotaped interviews over the next few years.
    ## 3                                                                                                                                                                                                                                                                                                         09/11/2001: This was one of four related terrorist attacks that occurred in the U.S.A. and that are known collectively as the 9/11 Terrorist Attacks. In the third of these attacks, which occurred at 9:37 am local time, American Airlines Flight 77 crashed into the Pentagon in Arlington, Virginia.  Five hijackers belonging to the terrorist organization, Al-Qa`ida, hijacked the Boeing 757 aircraft on a flight from originating from Washington Dulles International Airport destined for Los Angeles International Airport and proceeded to fly the plane into the western side of the Pentagon.  On the plane were 53 passengers, 6 crew members and 5 hijackers, all of whom died upon impact. 125 people died in the Pentagon due to the impact or in the resulting fire. Although not immediately known at the time of the attack, the Al-Qa`ida terrorist network, lead by Osama bin Laden, was responsible for all four of these attacks. Al-Qa`ida leaders such as bin Laden and Khalid Shaikh Mohammed claimed responsibility for the attacks in multiple videotaped interviews over the next few years.
    ## 4 09/11/2001: This was one of four related terrorist attacks that occurred in the U.S.A. and that are known collectively as the 9/11 Terrorist Attacks. In the fourth of these attacks, occurring at 10:03 am local time, United Airlines Flight 93, a Boeing 757 passenger airplane, crashed en route from Newark, New Jersey to San Francisco, California. The airliner crashed in a rural area of Pennsylvania near the town of Shanksville following a struggle for control of the plane between the passengers and four hijackers. 29 passengers, seven crew members, and four hijackers died in the crash. Al-Qa`ida leader, Khalid Shaikh Mohammed, indicated in a 2002 interview with the Doha based Al Jazeera News Network that the intended target of Flight 93 had been the U.S.A. Capitol Building located in Washington D.C. The Al-Qa`ida hijackers had planned on flying the airplane into the Capitol Building just as the other planes in the coordinated strike had been flown into the two World Trade Center towers in New York City and the Pentagon in Arlington, Virginia. Although not immediately known at the time of the attack, the Al-Qa`ida terrorist network, lead by Osama bin Laden, was responsible for all four of these attacks. Al-Qa`ida leaders such as bin Laden and Khalid Shaikh Mohammed claimed responsibility for the attacks in multiple videotaped interviews over the next few years.
    ##   crit1 crit2 crit3 doubtterr alternative alternative_txt multiple success
    ## 1     1     1     1         0          NA               .        1       1
    ## 2     1     1     1         0          NA               .        1       1
    ## 3     1     1     1         0          NA               .        1       1
    ## 4     1     1     1         0          NA               .        1       1
    ##   suicide attacktype1 attacktype1_txt attacktype2 attacktype2_txt
    ## 1       1           4       Hijacking           2   Armed Assault
    ## 2       1           4       Hijacking           2   Armed Assault
    ## 3       1           4       Hijacking           2   Armed Assault
    ## 4       1           4       Hijacking           2   Armed Assault
    ##   attacktype3 attacktype3_txt targtype1               targtype1_txt
    ## 1        <NA>               .        14 Private Citizens & Property
    ## 2        <NA>               .        14 Private Citizens & Property
    ## 3        <NA>               .         2        Government (General)
    ## 4        <NA>               .        14 Private Citizens & Property
    ##   targsubtype1                    targsubtype1_txt     corp1
    ## 1           67        Unnamed Civilian/Unspecified Civilians
    ## 2           67        Unnamed Civilian/Unspecified Civilians
    ## 3           21 Government Building/Facility/Office Civilians
    ## 4           67        Unnamed Civilian/Unspecified Civilians
    ##                                                                                                                                           target1
    ## 1 Passengers and crew members on American Airlines Flight 11 and the people working in the North Tower of the World Trade Center in New York City
    ## 2  Passengers and crew members on United Airlines Flight 175 and the people working in the South Tower of the World Trade Center in New York City
    ## 3                                                               Passengers and crew members on American Airlines Flight 77 and Pentagon personnel
    ## 4                  Airline passengers and crew members on board American Airlines Flight 93 and personnel at the intended Capitol Building target
    ##   natlty1   natlty1_txt targtype2        targtype2_txt targsubtype2
    ## 1     217 United States         1             Business            3
    ## 2     217 United States         1             Business            3
    ## 3     217 United States         4             Military           27
    ## 4     217 United States         2 Government (General)           21
    ##                                targsubtype2_txt
    ## 1                                 Bank/Commerce
    ## 2                                 Bank/Commerce
    ## 3 Military Barracks/Base/Headquarters/Checkpost
    ## 4           Government Building/Facility/Office
    ##                                 corp2
    ## 1          World Trade Center Complex
    ## 2          World Trade Center Complex
    ## 3 United States Department of Defense
    ## 4              United States Congress
    ##                                       target2 natlty2   natlty2_txt
    ## 1 Businesses operating inside the Twin Towers     217 United States
    ## 2 Businesses operating inside the Twin Towers     217 United States
    ## 3                                The Pentagon     217 United States
    ## 4          The United States Capitol Building     217 United States
    ##   targtype3       targtype3_txt targsubtype3             targsubtype3_txt
    ## 1         6 Airports & Aircraft           42 Aircraft (not at an airport)
    ## 2         6 Airports & Aircraft           42 Aircraft (not at an airport)
    ## 3         6 Airports & Aircraft           42 Aircraft (not at an airport)
    ## 4         6 Airports & Aircraft           42 Aircraft (not at an airport)
    ##               corp3
    ## 1 American Airlines
    ## 2   United Airlines
    ## 3 American Airlines
    ## 4   United Airlines
    ##                                                     target3 natlty3
    ## 1 American Airlines-owned Boeing 767 passenger jet aircraft     217
    ## 2   United Airlines-owned Boeing 767 passenger jet aircraft     217
    ## 3 American Airlines-owned Boeing 757 passenger jet aircraft     217
    ## 4   United Airlines-owned Boeing 757 passenger jet aircraft     217
    ##     natlty3_txt    gname gsubname gname2 gsubname2 gname3 ingroup ingroup2
    ## 1 United States Al-Qaida     <NA>   <NA>      <NA>   <NA>   20029       NA
    ## 2 United States Al-Qaida     <NA>   <NA>      <NA>   <NA>   20029       NA
    ## 3 United States Al-Qaida     <NA>   <NA>      <NA>   <NA>   20029       NA
    ## 4 United States Al-Qaida     <NA>   <NA>      <NA>   <NA>   20029       NA
    ##   ingroup3 gsubname3  motive guncertain1 guncertain2 guncertain3 nperps
    ## 1     <NA>      <NA> Unknown           0          NA        <NA>      5
    ## 2     <NA>      <NA> Unknown           0          NA        <NA>      5
    ## 3     <NA>      <NA> Unknown           0          NA        <NA>      5
    ## 4     <NA>      <NA> Unknown           0          NA        <NA>      4
    ##   nperpcap claimed claimmode claimmode_txt claim2 claimmode2
    ## 1        0       1         6         Video     NA       <NA>
    ## 2        0       1         6         Video     NA       <NA>
    ## 3        0       1         6         Video     NA       <NA>
    ## 4        0       1         6         Video     NA       <NA>
    ##   claimmode2_txt claim3 claimmode3 claimmode3_txt compclaim weaptype1
    ## 1              .   <NA>       <NA>              .        -9        10
    ## 2              .   <NA>       <NA>              .        -9        10
    ## 3              .   <NA>       <NA>              .        -9        10
    ## 4              .   <NA>       <NA>              .        -9        10
    ##                                                                 weaptype1_txt
    ## 1 Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)
    ## 2 Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)
    ## 3 Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)
    ## 4 Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)
    ##   weapsubtype1 weapsubtype1_txt weaptype2 weaptype2_txt weapsubtype2
    ## 1           NA                .         8    Incendiary           20
    ## 2           NA                .         8    Incendiary           20
    ## 3           NA                .         8    Incendiary           20
    ## 4           NA                .         8    Incendiary           20
    ##      weapsubtype2_txt weaptype3 weaptype3_txt weapsubtype3
    ## 1 Gasoline or Alcohol         9         Melee           23
    ## 2 Gasoline or Alcohol         9         Melee           23
    ## 3 Gasoline or Alcohol         9         Melee           23
    ## 4 Gasoline or Alcohol         9         Melee           23
    ##              weapsubtype3_txt weaptype4 weaptype4_txt weapsubtype4
    ## 1 Knife or Other Sharp Object      <NA>             .         <NA>
    ## 2 Knife or Other Sharp Object      <NA>             .         <NA>
    ## 3 Knife or Other Sharp Object      <NA>             .         <NA>
    ## 4 Knife or Other Sharp Object      <NA>             .         <NA>
    ##   weapsubtype4_txt
    ## 1                .
    ## 2                .
    ## 3                .
    ## 4                .
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                        weapdetail
    ## 1 The attackers reportedly gained control of the plane using sharp objects resembling knives or other sharpened metal objects. The attackers turned the airplane into a missile when flying it into the North Tower of the World Trade Center Complex. The airplane's jet fuel ignited the building and resulted in a massive fire that contributed to the collapse of the North Tower. Mace may have also been used in subduing the passengers and crew members.
    ## 2 The attackers reportedly gained control of the plane using sharp objects resembling knives or other sharpened metal objects. The attackers turned the airplane into a missile when flying it into the South Tower of the World Trade Center Complex. The airplane's jet fuel ignited the building and resulted in a massive fire that contributed to the collapse of the South Tower. Mace may have also been used in subduing the passengers and crew members.
    ## 3                                                                         The attackers reportedly gained control of the plane using sharp objects resembling knives or other sharpened metal objects. The attackers turned the airplane into a missile when flying it into the west side of the Pentagon. The airplane's jet fuel ignited the building and resulted in a massive fire. Mace may have also been used in subduing the passengers and crew members.
    ## 4                                                                                                                        The attackers reportedly gained control of the plane using sharp objects, possibly knives or other sharpened metal objects. The attackers intended to turn the airplane into a missile by flying it into the United States Capital Building in Washington D.C. Mace may have also been used in subduing the passengers and crew members.
    ##    nkill nkillus nkillter nwound nwoundus nwoundte property propextent
    ## 1 1381.5  1357.5        5     NA       NA        0        1          1
    ## 2 1381.5  1325.5        5     NA       NA        0        1          1
    ## 3  189.0   181.0        5    106       NA        0        1          1
    ## 4   44.0    38.0        4      0        0        0        1          1
    ##                       propextent_txt propvalue
    ## 1 Catastrophic (likely > $1 billion)        NA
    ## 2 Catastrophic (likely > $1 billion)        NA
    ## 3 Catastrophic (likely > $1 billion)        NA
    ## 4 Catastrophic (likely > $1 billion)        NA
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               propcomment
    ## 1 The crash resulted in the destruction of an American Airlines Boeing 767 aircraft. All personal belongings stowed on the plane were destroyed as well. Also destroyed was the North Tower of the World Trade Center complex in New York City, which collapsed as a result of the impact and subsequent fire. The building's collapse undoubtedly damaged the foundation of nearby buildings, though the South Tower had already fallen. Businesses located in the North Tower suffered massive economic losses. Air travel was restricted or prevented in the United States for several months following the attacks. Total losses to the United States Airline industry are unknown, but extremely high. Several major United States airlines were nearly bankrupted in the aftermath of the attacks. The attacks had a negative impact on the U.S. and world economy.
    ## 2                    The crash resulted in the destruction of an United Airlines Boeing 767 aircraft. All personal belongings stowed on the plane were destroyed as well. Also destroyed was the South Tower of the World Trade Center Complex in New York City, which collapsed as a result of the impact and subsequent fire. The building's collapse undoubtedly damaged the foundation of nearby buildings, including the North Tower. Businesses located in the South Tower suffered massive economic losses. Air travel was restricted or prevented in the United States for several months following the attacks. Total losses to the United States Airline industry are unknown, but extremely high. Several major United States airlines were nearly bankrupted in the aftermath of the attacks. The attacks had a massive impact on the U.S. and world economy.
    ## 3                                                                                                                                                                                                                                                                                              The crash resulted in the destruction of an American Airlines Boeing 757 aircraft. All personal belongings stowed on the plane were destroyed as well. Also destroyed was the western facing wall of the Pentagon. Air travel was restricted or prevented in the United States for several months following the attacks. Total losses to the United States Airline industry are unknown, but extremely high. Several major United States airlines were nearly bankrupted in the aftermath of the attacks. The attacks had a negative impact on the U.S. and world economy.
    ## 4                                                                                                                                                                                                                                                                                                                                                      The crash resulted in the destruction of an United Airlines owned Boeing 757 airplane. All personal belongings stowed on the plane were destroyed as well. Air travel was restricted or prevented in the United States for several months following the attacks. Total losses to the United States Airline industry are unknown, but extremely high. Several major United States airlines were nearly bankrupted in the aftermath of the attacks. The attacks had a negative impact on the U..S and world economy.
    ##   ishostkid nhostkid nhostkidus nhours ndays divert kidhijcountry ransom
    ## 1         1       88         86      1    NA   <NA>          <NA>      0
    ## 2         1       59         54      1    NA   <NA>          <NA>      0
    ## 3         1       59         56      1    NA   <NA>          <NA>      0
    ## 4         1       40         38      1    NA   <NA>          <NA>      0
    ##   ransomamt ransomamtus ransompaid ransompaidus ransomnote hostkidoutcome
    ## 1        NA        <NA>         NA         <NA>       <NA>              4
    ## 2        NA        <NA>         NA         <NA>       <NA>              4
    ## 3        NA        <NA>         NA         <NA>       <NA>              4
    ## 4        NA        <NA>         NA         <NA>       <NA>              4
    ##                              hostkidoutcome_txt nreleased
    ## 1 Hostage(s) killed (not during rescue attempt)         0
    ## 2 Hostage(s) killed (not during rescue attempt)         0
    ## 3 Hostage(s) killed (not during rescue attempt)         0
    ## 4 Hostage(s) killed (not during rescue attempt)         0
    ##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 addnotes
    ## 1                                                                                                                                                                                                                        This attack was one of four related incidents (cf. 200109110004-07). Three people including two attendants, Karen Martin and Barbara Arestegui, were stabbed or had their throats slashed by the hijackers. American Airlines Flight 11 departed from Boston's Logan International Airport at 7:59 am local time. The 9/11 Commission that officially investigated the attacks estimated that the hijacking began at 8:14 am. Since the aircraft crashed into the North Tower at 8:46 am, the hijacking lasted 32 minutes.  24 people still remain missing from the World Trade Center attacks.
    ## 2 This attack was one of four related incidents (cf. 200109110004-07). Flight attendant, Robert Fangman, called a United Airlines office in San Francisco and spoke with Marc Policastro. He reported the hijacking and said that both pilots had been killed. He also reported that a flight attendant was stabbed and said that the hijackers were flying the plane. United Flight 175 departed from Boston's Logan International Airport at 8:14 am local time. At 8:47 am, air traffic control received the first indications that the plane was hijacked. Since the airplane impacted the South Tower at 9:03 am, the length of the hijacking was at least 16 minutes. The second aircraft seized was United Airlines flight 175, which was scheduled to depart from Logan at 8:00 and fly directly to Los Angeles.
    ## 3                                                                                                                                         This attack was one of four related incidents (cf. 200109110004-07). As the aircraft approached the Pentagon it clipped several street lamps, one of which fell onto a taxi cab, injuring the driver, Lloyd England. The aircraft's right wing hit a portable generator that provided backup power for the Pentagon and the left engine hit an external steam vault before it slammed into the Pentagon. American Airlines Flight 77 departed Washington Dulles International Airport at 8:20 am local time. The airplane was hijacked sometime between 8:51 and 8:54 am. Since the aircraft crashed into the Pentagon at 9:37 am, the hijacking lasted between 40-43 minutes.
    ## 4                                                                               This attack was one of four related incidents (cf. 200109110004-07). The passengers attempted to retake control of the airplane after receiving word that other hijacked aircraft had been crashed into the World Trade Center buildings in New York City and the Pentagon in Arlington, Virginia. The attackers evidentially gave the order to crash the plane once they realized they could not maintain control of the vehicle to carry out their mission. United Airlines Flight 93, departing from Newark International Airport at 8:42 am local time, crashed at 10:03 am. Air traffic controllers believed that the hijackers took over the airplane at 9:28 am, so the hijacking lasted for 35 minutes before Flight 93 crashed.
    ##                                                                                                                                                                                         scite1
    ## 1 United States Government, The 9/11 Commission Report: Final Report of the National Commission on Terrorist Attacks Upon the United States. Washington, DC: Government Printing Office, 2004.
    ## 2 United States Government, The 9/11 Commission Report: Final Report of the National Commission on Terrorist Attacks Upon the United States. Washington, DC: Government Printing Office, 2004.
    ## 3 United States Government, The 9/11 Commission Report: Final Report of the National Commission on Terrorist Attacks Upon the United States. Washington, DC: Government Printing Office, 2004.
    ## 4 United States Government, The 9/11 Commission Report: Final Report of the National Commission on Terrorist Attacks Upon the United States. Washington, DC: Government Printing Office, 2004.
    ##                                                                                                                                                                                                                                scite2
    ## 1 Lindsay Kines, \x93United States on high alert after thousands feared dead: Airlines initially blame hijackers for attacks on the Pentagon in Washington, New York's World Trade Center,\x94 The Vancouver Sun, September 11, 2001.
    ## 2 Lindsay Kines, \x93United States on high alert after thousands feared dead: Airlines initially blame hijackers for attacks on the Pentagon in Washington, New York's World Trade Center,\x94 The Vancouver Sun, September 11, 2001.
    ## 3 Lindsay Kines, \x93United States on high alert after thousands feared dead: Airlines initially blame hijackers for attacks on the Pentagon in Washington, New York's World Trade Center,\x94 The Vancouver Sun, September 11, 2001.
    ## 4 Lindsay Kines, \x93United States on high alert after thousands feared dead: Airlines initially blame hijackers for attacks on the Pentagon in Washington, New York's World Trade Center,\x94 The Vancouver Sun, September 11, 2001.
    ##                                                                                                                                                                                                                     scite3
    ## 1 Joe Frolick, \x93Hijackers Ram Two Airliners Into World Trade Center, Third Plane Slams Into Pentagon, Fourth Crashes Near Pittsburgh; Thousands Die; Bush Vows Swift Revenge,\x94 The Plain Dealer, September 12, 2001.
    ## 2 Joe Frolick, \x93Hijackers Ram Two Airliners Into World Trade Center, Third Plane Slams Into Pentagon, Fourth Crashes Near Pittsburgh; Thousands Die; Bush Vows Swift Revenge,\x94 The Plain Dealer, September 12, 2001.
    ## 3                                                                                                                        "Plane Crashes Into Pentagon; Troops Deployed in Response," Associated Press, September 11, 2001.
    ## 4                                                                                                                  \x93Plane Crashes Into Pentagon; Troops Deployed in Response,\x94 Associated Press, September 11, 2001.
    ##   dbsource INT_LOG INT_IDEO INT_MISC INT_ANY
    ## 1    CETIS       0        1        0       1
    ## 2    CETIS       0        1        0       1
    ## 3    CETIS       0        1        0       1
    ## 4    CETIS       0        1        0       1
    ##                                    related                date
    ## 1 200109110005, 200109110006, 200109110007 2001-09-11 12:00:00
    ## 2 200109110004, 200109110006, 200109110007 2001-09-11 12:00:00
    ## 3 200109110004, 200109110005, 200109110007 2001-09-11 12:00:00
    ## 4 200109110004, 200109110005, 200109110006 2001-09-11 12:00:00

### Caso della strage alla stazione di Bologna

{% highlight r %}
db %>% 
  filter(iyear == 1980,
         imonth == 8,
         iday == 2,
         country_txt == "Italy") %>%
  print.data.frame
{% endhighlight %}

    ##       eventid iyear imonth iday approxdate extended resolution country
    ## 1 1.98008e+11  1980      8    2       <NA>        0       <NA>      98
    ##   country_txt region     region_txt      provstate    city latitude
    ## 1       Italy      8 Western Europe Emilia-Romagna Bologna 44.49419
    ##   longitude specificity vicinity location summary crit1 crit2 crit3
    ## 1  11.34652           1        0     <NA>    <NA>     1     1     1
    ##   doubtterr alternative alternative_txt multiple success suicide
    ## 1         0          NA               .        0       1       0
    ##   attacktype1   attacktype1_txt attacktype2 attacktype2_txt attacktype3
    ## 1           3 Bombing/Explosion          NA               .        <NA>
    ##   attacktype3_txt targtype1  targtype1_txt targsubtype1
    ## 1               .        19 Transportation          100
    ##             targsubtype1_txt corp1              target1 natlty1
    ## 1 Train/Train Tracks/Trolley  <NA> Central Station (RR)      98
    ##   natlty1_txt targtype2 targtype2_txt targsubtype2 targsubtype2_txt corp2
    ## 1       Italy        NA             .           NA                .  <NA>
    ##   target2 natlty2 natlty2_txt targtype3 targtype3_txt targsubtype3
    ## 1    <NA>      NA           .      <NA>             .         <NA>
    ##   targsubtype3_txt corp3 target3 natlty3 natlty3_txt
    ## 1                .  <NA>    <NA>    <NA>           .
    ##                              gname gsubname gname2 gsubname2 gname3
    ## 1 Armed Revolutionary Nuclei (NAR)     <NA>   <NA>      <NA>   <NA>
    ##   ingroup ingroup2 ingroup3 gsubname3 motive guncertain1 guncertain2
    ## 1     303       NA     <NA>      <NA>   <NA>           1          NA
    ##   guncertain3 nperps nperpcap claimed claimmode claimmode_txt claim2
    ## 1        <NA>     NA       NA      NA        NA             .     NA
    ##   claimmode2 claimmode2_txt claim3 claimmode3 claimmode3_txt compclaim
    ## 1       <NA>              .   <NA>       <NA>              .      <NA>
    ##   weaptype1             weaptype1_txt weapsubtype1 weapsubtype1_txt
    ## 1         6 Explosives/Bombs/Dynamite           28     Dynamite/TNT
    ##   weaptype2             weaptype2_txt weapsubtype2       weapsubtype2_txt
    ## 1         6 Explosives/Bombs/Dynamite           16 Unknown Explosive Type
    ##   weaptype3 weaptype3_txt weapsubtype3 weapsubtype3_txt weaptype4
    ## 1        NA             .           NA                .      <NA>
    ##   weaptype4_txt weapsubtype4 weapsubtype4_txt
    ## 1             .         <NA>                .
    ##                           weapdetail nkill nkillus nkillter nwound
    ## 1 Plastic explosive; Explosive - TNT    76      NA       NA    188
    ##   nwoundus nwoundte property propextent propextent_txt propvalue
    ## 1       NA       NA        1         NA              .        NA
    ##   propcomment ishostkid nhostkid nhostkidus nhours ndays divert
    ## 1        <NA>         0       NA         NA     NA    NA   <NA>
    ##   kidhijcountry ransom ransomamt ransomamtus ransompaid ransompaidus
    ## 1          <NA>      0        NA        <NA>         NA         <NA>
    ##   ransomnote hostkidoutcome hostkidoutcome_txt nreleased addnotes scite1
    ## 1       <NA>             NA                  .        NA     <NA>   <NA>
    ##   scite2 scite3 dbsource INT_LOG INT_IDEO INT_MISC INT_ANY related
    ## 1   <NA>   <NA>     PGIS       0        0        0       0    <NA>

Conversione nel formato Table Data Frame
----------------------------------------

Un formato conveniente per essere visualizzato in quanto limita la stampa a schermo ad un numero di righe e colonne adatto ad una console.

{% highlight r %}
db <- db %>% tbl_df()
{% endhighlight %}

Tipologie delle variabili
-------------------------

Suddivido le variabili a seconda dei vari tipi (testo, numero) per successive analisi differenziate in base a questo.

{% highlight r %}
num_vars <- names(db)[sapply(db, class) %in% c("numeric", "integer")]
text_vars <- names(db)[sapply(db, class) == "character"]

# controllo che tutte le variabili sono categorizzate
c(num_vars, text_vars) %>% length() == length(names(db))
{% endhighlight %}

    ## [1] TRUE

Approfondimento su alcune variabili
-----------------------------------

### Geolocalizzazioni mancanti

{% highlight r %}
db %>% distinct(region_txt)
{% endhighlight %}

    ## # A tibble: 12 × 1
    ##                     region_txt
    ##                          <chr>
    ## 1  Central America & Caribbean
    ## 2                North America
    ## 3               Southeast Asia
    ## 4               Western Europe
    ## 5                    East Asia
    ## 6                South America
    ## 7               Eastern Europe
    ## 8           Sub-Saharan Africa
    ## 9   Middle East & North Africa
    ## 10       Australasia & Oceania
    ## 11                  South Asia
    ## 12                Central Asia

Ci sono 4519 geolocalizzazioni mancanti, ma la regione (suddivisione geografica piu' vasta) e lo stato sono sempre popolati.

{% highlight r %}
db %>% 
  filter(is.na(latitude)) %>% 
  ggplot(aes(x = iyear, y = region_txt)) +
  geom_jitter() + 
  labs(title = "Geolocalizzazioni mancanti per regione",
       x = "Anno",
       y = "Regione")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-7-1.png)

### Variabile "numero persone uccise" `nkill` mancante

{% highlight r %}
db %>% 
  filter(is.na(nkill)) %>% 
  ggplot(aes(x = iyear, y = weaptype1_txt)) +
  geom_jitter() + 
  labs(x = "Anno",
       y = "Tipo di arma",
       title = "Informazione mancante sul numero delle persone uccise")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-8-1.png)

### Creazione variabile "data" `date`

Dato che una variabile "data" non esiste, la creo partendo da anno, mese e giorno.

{% highlight r %}
db <- db %>% 
  mutate(date = ISOdate(iyear, imonth, iday))
date_vars <- "date" # tipo di variabili
db %>% 
  ggplot(aes(x = date)) +
  stat_count() +
  theme_minimal() +
  labs(title = "Distribuzione degli attacchi per data",
       x = "Giorno",
       y = "Conteggio")
{% endhighlight %}

    ## Warning: Removed 894 rows containing non-finite values (stat_count).

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-9-1.png)

Gruppi di variabili
-------------------

Da codebook, posso dividere le variabili in 9 gruppi:

1.  ID e variabili temporali: GTD ID and Date
2.  Informazioni generali sull'evento: Incident Information
3.  Localizzazione: Incident Location
4.  Tipo di attacco: Attack Information
5.  Armi: Weapon Information
6.  Tipo di obiettivo e vittime: Target/Victim Information
7.  Informazioni sull'attentatore: Perpetrator Information
8.  Vittime e conseguenze: Casualties and Consequences
9.  Altre informazioni e provenienza: Additional Information and Sources

{% highlight r %}
gtd_id_date <-
  c("eventid",
    "iyear",
    "imonth",
    "iday",
    "approxdate",
    "extended",
    "resolution")
incident_info <-
  c(
    "summary",
    "crit1",
    "crit2",
    "crit3",
    "doubtterr",
    "alternative_txt",
    "multiple",
    "related"
  )
incident_location <-
  c(
    "country_txt",
    "region_txt",
    "provstate",
    "city",
    "vicinity",
    "location",
    "latitude",
    "longitude",
    "specificity"
  )
attack_info <-
  c("attacktype1_txt",
    "attacktype2_txt",
    "attacktype3_txt",
    "success",
    "suicide")
weapon_info <-
  c("weaptype1_txt",
    "weaptype2_txt",
    "weaptype3_txt",
    "weaptype4_txt",
    "weapdetail")
target_info <-
  c(
    "targtype1_txt",
    "targsubtype1_txt",
    "corp1",
    "target1",
    "natlty1_txt",
    "targtype2_txt",
    "targsubtype2_txt",
    "corp2",
    "target2",
    "natlty2_txt",
    "targtype3_txt",
    "targsubtype3_txt",
    "corp3",
    "target3",
    "natlty3_txt"
  )
perpetrator_info <-
  c(
    "gname",
    "gsubname",
    "gname2",
    "gsubname2",
    "gname3",
    "gsubname3",
    "guncertain1",
    "guncertain2",
    "guncertain3",
    "nperps",
    "nperpcap",
    "claimed",
    "claimmode_txt",
    "compclaim",
    "claim2",
    "claimmode2",
    "claim3",
    "claimmode3",
    "motive"
  )
causalties <-
  c(
    "nkill",
    "nkillter",
    "nwound",
    "nwoundte",
    "ishostkid",
    "nhostkid",
    "nhours",
    "ndays",
    "divert",
    "kidhijcountry",
    "hostkidoutcome_txt",
    "nreleased"
  )
consequences <-
  c(
    "property",
    "propextent_txt",
    "propvalue",
    "propcomment",
    "ransom",
    "ransompaid",
    "ransomnote"
  )
{% endhighlight %}

Analisi per gruppi di variabili
===============================

ID e variabili temporali
------------------------

Il primo gruppo di variabili comprende l'ID univoco dell'evento, e la data in cui esso e' avvenuto. Di seguito una semplice distribuzione degli eventi per anno.

{% highlight r %}
db %>% 
  select_(.dots = gtd_id_date) %>% 
  ggplot(aes(x = iyear)) +
  stat_count() +
  labs(title = "Numero di eventi per anno",
       x = "Anno",
       y = "Numero di eventi") +
  scale_y_continuous(labels = scales::comma) +
  theme_minimal()
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/id-and-date-1.png)

Come riportato nel codebook, l'anno 1993 non contiene dati.

Informazioni generali sull'evento
---------------------------------

Il secondo gruppo di variabili contiene informazioni sulla classificazione dell'evento. In particolare, analizzo la distribuzione dei criteri di inclusione:

-   **Criterio 1**: l'atto deve essere finalizzato al raggiungimento di un obiettivo politico, economico, religioso o sociale. Il criterio non e\` soddisfatto se il perpetratore agisce per puro profitto personale.

-   **Criterio 2**: deve esserci evidenza di un'intenzione di sottomettere, intimidare o consegnare un messaggio ad un pubblico piu\` grande delle vittime dirette. Manifestazioni di questo possono essere dichiarazioni prima o dopo l'attacco, o scelta particolare di vittime, armi e strategie di attacco.

-   **Criterio 3**: l'azione deve essere considerata come un attacco non convenzionale a civili al di fuori delle convenzioni di guerra, come ad esempio la convenzione di Ginevra del 1949.

{% highlight r %}
freq <- db %>% 
  select_(.dots = incident_info) %>% 
  select(crit1, crit2, crit3) %>% 
  group_by(crit1, crit2, crit3) %>% 
  count() %>% 
  ungroup()
area1 <- freq %>% filter(crit1 == 1) %>% summarize(a = sum(n))
area2 <- freq %>% filter(crit2 == 1) %>% summarize(a = sum(n))
area3 <- freq %>% filter(crit3 == 1) %>% summarize(a = sum(n))
n12 <- freq %>%
  filter(crit1 == 1, crit2 == 1) %>%
  summarize(a = sum(n))
n23 <- freq %>%
  filter(crit2 == 1, crit3 == 1) %>%
  summarize(a = sum(n))
n13 <- freq %>%
  filter(crit1 == 1, crit3 == 1) %>%
  summarize(a = sum(n))
n123 <- freq %>%
  filter(crit1 == 1, crit2 == 1, crit3 == 1) %>%
  summarize(a = sum(n))
vplot <- VennDiagram::draw.triple.venn(area1[[1]],
                              area2[[1]],
                              area3[[1]],
                              n12[[1]],
                              n23[[1]],
                              n13[[1]],
                              n123[[1]],
                              category = c("Criterio 1",
                                           "Criterio 2",
                                           "Criterio 3"),
                              euler.d = TRUE,
                              scaled = TRUE,
                              lwd = c(1, 1, 1),
                              fill = c("blue", "red", "green"))
grid::grid.draw(vplot)
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/incident-info-1.png)

Localizzazione
--------------

Il 90% degli avvenimenti accade in 37 stati. Qui riporto la tabella di frequenza relativa a questi, ed una mappa di tipo choropleth comprendente invece tutto il mondo.

### Mappa di frequenza

{% highlight r %}
db %>% 
  select(Regione = region_txt) %>% 
  sapply(table)
{% endhighlight %}

    ##                             Regione
    ## Australasia & Oceania           246
    ## Central America & Caribbean   10337
    ## Central Asia                    538
    ## East Asia                       786
    ## Eastern Europe                 4892
    ## Middle East & North Africa    40422
    ## North America                  3268
    ## South America                 18628
    ## South Asia                    37841
    ## Southeast Asia                10360
    ## Sub-Saharan Africa            13434
    ## Western Europe                16020

{% highlight r %}
db %>% 
  select_(.dots = incident_location) %>% 
  group_by(country_txt) %>% 
  count() %>% 
  arrange(desc(n)) %>% 
  mutate(freq = prop.table(n), cumul = cumsum(freq)) %>% 
  filter(cumul < 0.9) %>% 
  select(Paese = country_txt, n, cumul) %>% 
  mutate(`Percentuale cumulata` = scales::percent(cumul),
         Conteggio = scales::comma(n)) %>% 
  select(-cumul, -n)
{% endhighlight %}

    ## # A tibble: 37 × 3
    ##             Paese `Percentuale cumulata` Conteggio
    ##             <chr>                  <chr>     <chr>
    ## 1            Iraq                  12.0%    18,770
    ## 2        Pakistan                  20.1%    12,768
    ## 3           India                  26.5%     9,940
    ## 4     Afghanistan                  32.6%     9,690
    ## 5        Colombia                  37.8%     8,077
    ## 6            Peru                  41.7%     6,085
    ## 7     Philippines                  45.2%     5,576
    ## 8     El Salvador                  48.6%     5,320
    ## 9  United Kingdom                  51.8%     4,992
    ## 10         Turkey                  54.1%     3,557
    ## # ... with 27 more rows

### Choropleth

Mappa choropleth che mostra una scala di colori in base al numero di eventi accaduti per ogni stato.

{% highlight r %}
library(maps)
world_map <- map_data("world")
attacks_by_country <- db %>% 
  select_(.dots = incident_location) %>% 
  group_by(country_txt) %>% 
  count() %>% 
  rename(country = country_txt)

attacks_by_country %>% 
  ggplot(aes(map_id = country)) +
  geom_map(aes(fill = n), map = world_map) +
  expand_limits(x = world_map$long, y = world_map$lat) +
  labs(title = "Numero di attacchi per paese")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/incident-location-choropleth-1.png)

Il risultato e' pero' parziale in quanto dei nomi di nazioni non sono consistenti tra il database e la mappa. Ad esempio, nella mappa gli Stati Uniti sono etichettati come "USA", mentre nel database sono "United States".

{% highlight r %}
countries <- world_map %>% 
  select(country = region) %>% 
  distinct(country)

attacks_by_country %>% 
  anti_join(countries, by = "country") %>% 
  arrange(desc(n))
{% endhighlight %}

    ## # A tibble: 29 × 2
    ##                     country     n
    ##                       <chr> <int>
    ## 1            United Kingdom  4992
    ## 2             United States  2693
    ## 3  West Bank and Gaza Strip  1990
    ## 4        West Germany (FRG)   541
    ## 5                Yugoslavia   203
    ## 6        Bosnia-Herzegovina   159
    ## 7                  Rhodesia    83
    ## 8              Soviet Union    78
    ## 9                     Zaire    48
    ## 10       East Germany (GDR)    38
    ## # ... with 19 more rows

Pulisco manualmente due casi.

{% highlight r %}
attacks_by_country[attacks_by_country$country == "United States", "country"] <- "USA"
attacks_by_country[attacks_by_country$country == "United Kingdom", "country"] <- "UK"

attacks_by_country %>% 
  ggplot(aes(map_id = country)) +
  geom_map(aes(fill = n), map = world_map) +
  expand_limits(x = world_map$long, y = world_map$lat) +
  labs(title = "Numero di attachi per paese (dati parzialmente puliti)")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/choropleth-cleaned-1.png)

Meglio, ma ci sono dei limiti dovuti alla natura del dataset: alcuni stati non esistono piu, come ad esempio la Germania dell'Est o la Yugoslavia. Riportare questi dati nelle nazioni attuali potrebbe essere impreciso, o addirittura errato.

### Grafico mediante `ggmap`

Usando la geolocalizzazione si possono ottenere risultati piu precisi in quanto non esiste il vincolo dei confini nazionali nel definire le mappe.

Di seguito, un estratto del dato riguardante l'Europa.

{% highlight r %}
library(ggmap)
europe <- ggmap::get_map(location = "Berlin", zoom = 4, maptype = "terrain")
geo <- db %>% 
  select_(.dots = incident_location) %>% 
  filter(region_txt == "Western Europe" | region_txt == "Eastern Europe") %>% 
  filter(!is.na(latitude) & !is.na(longitude)) %>% 
  group_by(latitude, longitude) %>% 
  count() 
ggmap::ggmap(europe, extent = "panel") +
  geom_point(aes(x = longitude,
                 y = latitude,
                 size = n,
                 alpha = n), data = geo,
             colour = "red") +
  labs(title = "Geolocalizzazione degli attacchi in Europa")
{% endhighlight %}

    ## Warning: Removed 555 rows containing missing values (geom_point).

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/ggmap-europe-1.png)

{% highlight r %}
geo2 <- db %>% 
  select_(.dots = incident_location) %>% 
  filter(region_txt == "Western Europe" | region_txt == "Eastern Europe") %>% 
  filter(!is.na(latitude) & !is.na(longitude))
ggmap::ggmap(europe, extent = "panel") +
  stat_density2d(aes(x = longitude,
                     y = latitude,
                     fill = ..level..,
                     alpha = ..level..),
             size = 2,
             bins = 4,
             geom = "polygon",
             data = geo2) +
  labs(title = "Densita' degli attacchi in Europa")
{% endhighlight %}

    ## Warning: Removed 1966 rows containing non-finite values (stat_density2d).

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/ggmap-2-1.png)

In Europa, gli eventi sono in numero limitato e molto sparsi geograficamente. Ripeto l'esercizio in Israele.

{% highlight r %}
me <- ggmap::get_map(location = "Jerusalem", zoom = 9, maptype = "terrain")
geo3 <- db %>% 
  select_(.dots = incident_location) %>% 
  filter(!is.na(latitude) & !is.na(longitude)) %>% 
  filter(latitude > 31.1 ,
           latitude < 32.5 ,
           longitude > 34.4 ,
           longitude < 36) 
ggmap::ggmap(me, extent = "device", legend = "topleft") +
  stat_density2d(aes(x = longitude,
                     y = latitude,
                     fill = ..level..,
                     alpha = ..level..),
             # size = 2,
             bins = 15,
             geom = "polygon",
             data = geo3) +
  scale_fill_gradient(low = "black",
                      high = "red") +
  labs(title = "Densita' degli attacchi in Israele")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/ggmap-middleeast-1.png)

Sull'uso del pacchetto `ggmap`, vedere:
> D. Kahle and H. Wickham. [ggmap: Spatial Visualization with ggplot2](http://journal.r-project.org/archive/2013-1/kahle-wickham.pdf){:target="_blank"}. The R Journal, 5(1), 144-161

Tipo di attacco
---------------

{% highlight r %}
db %>% 
  select_(.dots = attack_info) %>% 
  select(`Tipo di attacco` = attacktype1_txt) %>% 
  sapply(table)
{% endhighlight %}

    ##                                     Tipo di attacco
    ## Armed Assault                                 37554
    ## Assassination                                 17582
    ## Bombing/Explosion                             75963
    ## Facility/Infrastructure Attack                 8849
    ## Hijacking                                       556
    ## Hostage Taking (Barricade Incident)             835
    ## Hostage Taking (Kidnapping)                    9115
    ## Unarmed Assault                                 828
    ## Unknown                                        5490

### Attacco suicida?

{% highlight r %}
db %>% 
  select(suicide) %>% 
  mutate(suicide = ifelse(suicide == 1, "vero", "falso")) %>% 
  table
{% endhighlight %}

    ## .
    ##  falso   vero 
    ## 152001   4771

{% highlight r %}
db %>% 
  select(suicide, attacktype1_txt) %>% 
  mutate(suicide = suicide == 1) %>% 
  ggplot(aes(x = attacktype1_txt)) +
  geom_bar(aes(fill = suicide)) +
  coord_flip() + 
  theme_minimal() + 
  labs(x = "Tipo di attacco",
       y = "Conteggio",
       title = "Conteggi dei tipi di attacco, con evidenza sul suicidio")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unordered-attacktype-bars-1.png)

Riordino le barre per migliore visualizzazione.

{% highlight r %}
levs <- db %>%
  select(attacktype1_txt) %>% 
  group_by(attacktype1_txt) %>% 
  count() %>% 
  arrange(n)

db %>% 
  select(suicide, attacktype1_txt) %>% 
  mutate(suicide = suicide == 1) %>% 
  ggplot(aes(x = factor(attacktype1_txt, levels = levs[[1]]))) +
  geom_bar(aes(fill = suicide)) +
  coord_flip() + 
  theme_minimal() + 
  scale_y_continuous(labels = scales::comma) +
  labs(x = "Tipo di attacco",
       y = "Count",
       title = "Conteggi dei tipi di attacco, con evidenza sul suicidio")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/ordered-attacktype-bars-1.png)

Armi
----

Questa sezione include informazioni sul tipo di arma utilizzata.

{% highlight r %}
weapon_info
{% endhighlight %}

    ## [1] "weaptype1_txt" "weaptype2_txt" "weaptype3_txt" "weaptype4_txt"
    ## [5] "weapdetail"

Ci sono quattro diversi livelli di informazione in base alla macrotipologia (`weaptype`) ed un campo di dettaglio (`weapdetail`).

{% highlight r %}
sapply(weapon_info, function(x) distinct_(db, x) %>% nrow())
{% endhighlight %}

    ## weaptype1_txt weaptype2_txt weaptype3_txt weaptype4_txt    weapdetail 
    ##            12            12            11             6         16988

Dato che il livello piu' dettagliato ha un conteggio troppo elevato, mostrero i livelli solo per ii campi della gerarchia.

{% highlight r %}
sapply(weapon_info[grepl("weaptype", weapon_info)], function(x) distinct_(db, x))
{% endhighlight %}

    ## $weaptype1_txt.weaptype1_txt
    ##  [1] "Unknown"                                                                    
    ##  [2] "Explosives/Bombs/Dynamite"                                                  
    ##  [3] "Incendiary"                                                                 
    ##  [4] "Firearms"                                                                   
    ##  [5] "Chemical"                                                                   
    ##  [6] "Fake Weapons"                                                               
    ##  [7] "Melee"                                                                      
    ##  [8] "Sabotage Equipment"                                                         
    ##  [9] "Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)"
    ## [10] "Radiological"                                                               
    ## [11] "Other"                                                                      
    ## [12] "Biological"                                                                 
    ## 
    ## $weaptype2_txt.weaptype2_txt
    ##  [1] "."                                                                          
    ##  [2] "Firearms"                                                                   
    ##  [3] "Melee"                                                                      
    ##  [4] "Incendiary"                                                                 
    ##  [5] "Explosives/Bombs/Dynamite"                                                  
    ##  [6] "Other"                                                                      
    ##  [7] "Sabotage Equipment"                                                         
    ##  [8] "Chemical"                                                                   
    ##  [9] "Fake Weapons"                                                               
    ## [10] "Unknown"                                                                    
    ## [11] "Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)"
    ## [12] "Biological"                                                                 
    ## 
    ## $weaptype3_txt.weaptype3_txt
    ##  [1] "."                                                                          
    ##  [2] "Unknown"                                                                    
    ##  [3] "Firearms"                                                                   
    ##  [4] "Melee"                                                                      
    ##  [5] "Incendiary"                                                                 
    ##  [6] "Explosives/Bombs/Dynamite"                                                  
    ##  [7] "Other"                                                                      
    ##  [8] "Fake Weapons"                                                               
    ##  [9] "Sabotage Equipment"                                                         
    ## [10] "Vehicle (not to include vehicle-borne explosives, i.e., car or truck bombs)"
    ## [11] "Chemical"                                                                   
    ## 
    ## $weaptype4_txt.weaptype4_txt
    ## [1] "."                         "Explosives/Bombs/Dynamite"
    ## [3] "Melee"                     "Firearms"                 
    ## [5] "Incendiary"                "Other"

Ora un grafico per mostrare il numero di attentati in base all'arma utilizzata.

{% highlight r %}
levels_weapons <- db %>%
  select(weaptype1_txt) %>% 
  group_by(weaptype1_txt) %>% 
  count() %>% 
  arrange(n) %>% 
  mutate(weaptype1_txt = ifelse(grepl("Vehicle", weaptype1_txt),
                                "Vehicle",
                                weaptype1_txt))

db %>% 
  select(weaptype1_txt) %>% 
  mutate(weaptype1_txt = ifelse(grepl("Vehicle", weaptype1_txt),
                                "Vehicle",
                                weaptype1_txt)) %>% 
  ggplot(aes(x = factor(weaptype1_txt, levels = levels_weapons[[1]]))) +
  geom_bar() +
  coord_flip() + 
  theme_minimal() + 
  scale_y_continuous(labels = scales::comma) +
  labs(x = "Tipo di arma",
       y = "Conteggio",
       title = "Conteggio dei tipi di arma")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/weapon-info-1.png)

Tipo di obiettivo e vittime
---------------------------

{% highlight r %}
db %>% 
  select_(.dots = target_info)
{% endhighlight %}

    ## # A tibble: 156,772 × 15
    ##                  targtype1_txt
    ##                          <chr>
    ## 1  Private Citizens & Property
    ## 2      Government (Diplomatic)
    ## 3          Journalists & Media
    ## 4      Government (Diplomatic)
    ## 5      Government (Diplomatic)
    ## 6                       Police
    ## 7                       Police
    ## 8                    Utilities
    ## 9                     Military
    ## 10        Government (General)
    ## # ... with 156,762 more rows, and 14 more variables:
    ## #   targsubtype1_txt <chr>, corp1 <chr>, target1 <chr>, natlty1_txt <chr>,
    ## #   targtype2_txt <chr>, targsubtype2_txt <chr>, corp2 <chr>,
    ## #   target2 <chr>, natlty2_txt <chr>, targtype3_txt <chr>,
    ## #   targsubtype3_txt <chr>, corp3 <chr>, target3 <chr>, natlty3_txt <chr>

Prima uno sguardo ai conteggi per ogni variabile.

{% highlight r %}
sapply(target_info, function(x) distinct_(db, x) %>% nrow())
{% endhighlight %}

    ##    targtype1_txt targsubtype1_txt            corp1          target1 
    ##               22              111            29297            79948 
    ##      natlty1_txt    targtype2_txt targsubtype2_txt            corp2 
    ##              213               23              104             2345 
    ##          target2      natlty2_txt    targtype3_txt targsubtype3_txt 
    ##             4563              155               21               86 
    ##            corp3          target3      natlty3_txt 
    ##              352              632              102

Ora uno sguardo ai livelli dei tipi di obiettivo per capire cosa riportare graficamente.

{% highlight r %}
sapply(target_info[grepl("targtype", target_info)], function(x) distinct_(db, x))
{% endhighlight %}

    ## $targtype1_txt.targtype1_txt
    ##  [1] "Private Citizens & Property"    "Government (Diplomatic)"       
    ##  [3] "Journalists & Media"            "Police"                        
    ##  [5] "Utilities"                      "Military"                      
    ##  [7] "Government (General)"           "Airports & Aircraft"           
    ##  [9] "Business"                       "Educational Institution"       
    ## [11] "Violent Political Party"        "Religious Figures/Institutions"
    ## [13] "Unknown"                        "Transportation"                
    ## [15] "Tourists"                       "NGO"                           
    ## [17] "Telecommunication"              "Food or Water Supply"          
    ## [19] "Terrorists/Non-State Militia"   "Other"                         
    ## [21] "Maritime"                       "Abortion Related"              
    ## 
    ## $targtype2_txt.targtype2_txt
    ##  [1] "."                              "Police"                        
    ##  [3] "Educational Institution"        "Unknown"                       
    ##  [5] "Business"                       "Private Citizens & Property"   
    ##  [7] "Government (General)"           "Telecommunication"             
    ##  [9] "Utilities"                      "Airports & Aircraft"           
    ## [11] "Military"                       "Government (Diplomatic)"       
    ## [13] "Religious Figures/Institutions" "Terrorists/Non-State Militia"  
    ## [15] "Transportation"                 "Journalists & Media"           
    ## [17] "Tourists"                       "Violent Political Party"       
    ## [19] "NGO"                            "Maritime"                      
    ## [21] "Abortion Related"               "Other"                         
    ## [23] "Food or Water Supply"          
    ## 
    ## $targtype3_txt.targtype3_txt
    ##  [1] "."                              "Business"                      
    ##  [3] "Private Citizens & Property"    "Government (General)"          
    ##  [5] "Police"                         "Airports & Aircraft"           
    ##  [7] "Journalists & Media"            "Violent Political Party"       
    ##  [9] "Government (Diplomatic)"        "Military"                      
    ## [11] "Religious Figures/Institutions" "Utilities"                     
    ## [13] "NGO"                            "Tourists"                      
    ## [15] "Transportation"                 "Educational Institution"       
    ## [17] "Terrorists/Non-State Militia"   "Telecommunication"             
    ## [19] "Maritime"                       "Other"                         
    ## [21] "Food or Water Supply"

### Grafico per tipi di obiettivo

Creo un solo grafico per la suddivisione del target di livello piu' elevato.

{% highlight r %}
levels_target <- db %>%
  select(targtype1_txt) %>% 
  group_by(targtype1_txt) %>% 
  count() %>% 
  arrange(n)

db %>% 
  select(targtype1_txt) %>% 
  ggplot(aes(x = factor(targtype1_txt, levels = levels_target[[1]]))) +
  geom_bar() +
  coord_flip() + 
  theme_minimal() + 
  scale_y_continuous(labels = scales::comma) +
  labs(x = "Tipo di obiettivo 1",
       y = "Conteggio",
       title = "Conteggio dei tipi di obiettivo")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/target1-info-graph-1.png)

### Nazionalita della vittima

{% highlight r %}
db %>% 
  filter(!is.na(natlty1_txt)) %>%
  group_by(natlty1_txt) %>%
  count() %>% 
  arrange(desc(n)) %>%
  mutate(freq = prop.table(n),
         cumul = cumsum(freq)) %>%
  filter(cumul < 0.8) %>%
  arrange(desc(cumul)) %>%

  ggplot(aes(x = reorder(natlty1_txt, n), y = n)) +
  geom_point() +
  theme_minimal() +
  scale_y_continuous(labels = scales::comma) +
  coord_flip() +
  labs(x = "Nazionalita'",
       y = "Numero di persone uccise",
       title = "Numero di persone uccise per nazionalita'")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-17-1.png)

Informazioni sull'attentatore
-----------------------------

{% highlight r %}
db %>% 
  select_(.dots = perpetrator_info) %>% 
  glimpse()
{% endhighlight %}

    ## Observations: 156,772
    ## Variables: 19
    ## $ gname         <chr> "MANO-D", "23rd of September Communist League", ...
    ## $ gsubname      <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ gname2        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ gsubname2     <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ gname3        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ gsubname3     <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ guncertain1   <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, ...
    ## $ guncertain2   <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ guncertain3   <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ nperps        <int> NA, 7, NA, NA, NA, -99, 3, -99, 1, 1, NA, -99, 1...
    ## $ nperpcap      <dbl> NA, NA, NA, NA, NA, -99, NA, -99, 1, 1, NA, -99,...
    ## $ claimed       <int> NA, NA, NA, NA, NA, 0, NA, 0, 1, 0, NA, 0, NA, 0...
    ## $ claimmode_txt <chr> ".", ".", ".", ".", ".", ".", ".", ".", "Letter"...
    ## $ compclaim     <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ claim2        <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ claimmode2    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ claim3        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ claimmode3    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ...
    ## $ motive        <chr> NA, NA, NA, NA, NA, "To protest the Cairo Illino...

Queste sembrano tutte variabili con un elevato numero di livelli per poter essere visualizzate. Verranno usate dopo per analisi multivariate.

{% highlight r %}
db %>% 
  select(gname) %>% 
  distinct() %>% 
  nrow()
{% endhighlight %}

    ## [1] 3290

Vittime e conseguenze
---------------------

{% highlight r %}
causalties
{% endhighlight %}

    ##  [1] "nkill"              "nkillter"           "nwound"            
    ##  [4] "nwoundte"           "ishostkid"          "nhostkid"          
    ##  [7] "nhours"             "ndays"              "divert"            
    ## [10] "kidhijcountry"      "hostkidoutcome_txt" "nreleased"

### Densita' degli attacchi in base al numero di persone uccise

{% highlight r %}
db %>% 
  ggplot(aes(x = nkill)) +
  geom_density() + 
  theme_minimal()
{% endhighlight %}

    ## Warning: Removed 8945 rows containing non-finite values (stat_density).

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-20-1.png)

### Conseguenze economiche

Uno sguardo alle variabili.

{% highlight r %}
db %>% 
  select_(.dots = consequences) %>% 
  glimpse()
{% endhighlight %}

    ## Observations: 156,772
    ## Variables: 7
    ## $ property       <int> 0, 0, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 0,...
    ## $ propextent_txt <chr> ".", ".", ".", ".", ".", "Minor (likely < $1 mi...
    ## $ propvalue      <dbl> NA, NA, NA, NA, NA, NA, NA, 22500, 60000, NA, 0...
    ## $ propcomment    <chr> NA, NA, NA, NA, NA, NA, NA, "Three transformers...
    ## $ ransom         <int> 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ ransompaid     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
    ## $ ransomnote     <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...

{% highlight r %}
db %>% 
  select_(.dots = consequences) %>% 
  summary()
{% endhighlight %}

    ##     property       propextent_txt       propvalue         
    ##  Min.   :-9.0000   Length:156772      Min.   :       -99  
    ##  1st Qu.: 0.0000   Class :character   1st Qu.:       -99  
    ##  Median : 1.0000   Mode  :character   Median :       -99  
    ##  Mean   :-0.4341                      Mean   :    249621  
    ##  3rd Qu.: 1.0000                      3rd Qu.:      5000  
    ##  Max.   : 1.0000                      Max.   :2700000000  
    ##                                       NA's   :125460      
    ##  propcomment            ransom        ransompaid        ransomnote       
    ##  Length:156772      Min.   :-9.00   Min.   :     -99   Length:156772     
    ##  Class :character   1st Qu.: 0.00   1st Qu.:     -99   Class :character  
    ##  Mode  :character   Median : 0.00   Median :       0   Mode  :character  
    ##                     Mean   :-0.14   Mean   :  431972                     
    ##                     3rd Qu.: 0.00   3rd Qu.:    4277                     
    ##                     Max.   : 1.00   Max.   :41000000                     
    ##                     NA's   :81680   NA's   :156149

{% highlight r %}
db %>% 
  ggplot(aes(x = propextent_txt)) +
  geom_bar() +
  coord_flip() +
  theme_minimal() +
  labs(x = "Danni alle proprieta'",
       y = "Conteggio",
       title = "Conteggi delle categorie di danni alle proprieta'")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-23-1.png)

Alcuni esempi di analisi multivariate
=====================================

Paese con il piu elevato numero di vittime
------------------------------------------

{% highlight r %}
db %>% 
  filter(!is.na(nkill)) %>%                # remove all NAs
  select(country_txt, nkill) %>%           # select relevant vars
  group_by(country_txt) %>%                # group by country
  summarise(nkill_tot = sum(nkill)) %>%    # create a column with sum by country
  arrange(desc(nkill_tot)) %>%             # sort descending by death by country
  mutate(freq = prop.table(nkill_tot),     # create share by country, 
         cumul = cumsum(freq)) %>%         # create cumulated share
  filter(cumul < 0.8) %>%                  # keep only countries with 80% of total death
  arrange(desc(cumul)) %>%                 # inverse rank

  ggplot(aes(x = reorder(country_txt, nkill_tot), y = nkill_tot)) +
  geom_point() +
  theme_minimal() +
  scale_y_continuous(labels = scales::comma) +
  coord_flip() +
  labs(x = "Paese",
       y = "Numero di vittime",
       title = "Vittime per paese")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-24-1.png)

Nazionalita dei morti per paese dove l'attacco e' avvenuto
----------------------------------------------------------

{% highlight r %}
deadliest_countries <- db %>% 
  filter(!is.na(country_txt),
         !is.na(nkill)) %>%
  group_by(country_txt) %>%
  # count() %>% 
  summarise(n = sum(nkill)) %>% 
  arrange(desc(n)) %>%
  mutate(freq = prop.table(n),
         cumul = cumsum(freq)) %>%
  filter(cumul < 0.8) %>% 
  arrange(n)

deadliest_nationalities <- db %>% 
  filter(!is.na(natlty1_txt),
         !is.na(nkill)) %>%
  group_by(natlty1_txt) %>%
  # count() %>% 
  summarise(n = sum(nkill)) %>% 
  arrange(desc(n)) %>%
  mutate(freq = prop.table(n),
         cumul = cumsum(freq)) %>%
  filter(cumul < 0.8) %>% 
  arrange(n)

db %>% 
  filter(country_txt %in% deadliest_countries[,1][[1]],
         natlty1_txt %in% deadliest_nationalities[,1][[1]]) %>% 
  group_by(country_txt, natlty1_txt) %>% 
  count() %>% 
  rename(Paese = country_txt,
         Provenienza = natlty1_txt) %>% 
  ggplot(aes(x = factor(Paese, levels = deadliest_countries[,1][[1]]),
             y = factor(Provenienza, levels = deadliest_nationalities[,1][[1]]))) +
  geom_point(aes(size = n)) +
  theme_minimal() +
  theme(axis.text.x=element_text(angle = 90)) +
  labs(x = "Paese",
       y = "Nazionalita'",
       title = "Nazionalita' dei coinvolti in un evento in base al paese di accadimento")
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/unnamed-chunk-25-1.png)

Evento col piu' elevato numero di vittime
-----------------------------------------

{% highlight r %}
db %>%
  filter(nkill == max(nkill, na.rm = TRUE)) %>%
  select(nkill, summary) %>% 
  knitr::kable()
{% endhighlight %}

|  nkill| summary                                                                                                                                                                                                                                                                                                                                                                                                      |
|------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   1500| 06/12/2014: Assailants abducted approximately 1686 soldiers from Camp Speicher in Tikrit city, Saladin governorate, Iraq. Two captives escaped custody and at least 1500, if not all of the remaining victims, are presumed dead. The Islamic State of Iraq and the Levant (ISIL) claimed responsibility and stated that the attacks were in revenge for the killing of ISIL leader Abdul-Rahman al-Beilawy. |

Eventi avvenuti in Italia
-------------------------

{% highlight r %}
db %>% 
  filter(country_txt == "Italy") %>% 
  ggplot(aes(x = iyear)) +
  stat_count() +
  labs(title = "Eventi accaduti in Italia per anno",
       y = "Numero di attacchi terroristici",
       x = "Anno") +
  theme_minimal()
{% endhighlight %}

![]({{ site.url }}/assets/images/terrorism/figure-markdown_github/italian-events-by-year-1.png)


{% include rsource.html %}
