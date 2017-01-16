---
title: Un'anticipazione del dataset PRIO
excerpt: Semplice animazione ottenuta con il pacchetto `gganimate`
header:
  teaser: assets/images/prio/anim-1.png
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
    
---

Scopo di questa pagina e' la creazione di un'animazione utilizzando il pacchetto `gganimate`.

Usero' dati provenienti dal [PRIO](https://www.prio.org/), il **Peace Research Institute Oslo** sui conflitti armati.

> Gleditsch, Nils Petter; Peter Wallensteen, Mikael Eriksson, Margareta Sollenberg & Håvard Strand, 2002. ‘Armed Conflict 1946–2001: A New Dataset’, Journal of Peace Research 39(5): 615–637.

Carico i dati
-------------

Scarico il dataset dalla pagina del [Uppsala Conflict Data Program](http://www.pcr.uu.se/research/UCDP/) in formato `Rdata`.

``` r
library(tidyverse)
```

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

``` r
load("~/Downloads/ged50.Rdata")
db <- tbl_df(ged50)
```

    ## Loading required package: sp

``` r
world <- map_data("world")
```

    ## 
    ## Attaching package: 'maps'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     map

Mantengo solo data e coordinate
-------------------------------

``` r
names(db)
```

    ##  [1] "id"         "relid"      "year"       "active_yea" "type_of_vi"
    ##  [6] "conflict_d" "conflict_n" "conflict_1" "dyad_dset_" "dyad_new_i"
    ## [11] "dyad_name"  "side_a_dse" "side_a_new" "side_a"     "side_b_dse"
    ## [16] "side_b_new" "side_b"     "number_of_" "source_art" "source_off"
    ## [21] "source_dat" "source_hea" "source_ori" "where_prec" "where_coor"
    ## [26] "where_desc" "adm_1"      "adm_2"      "latitude"   "longitude" 
    ## [31] "geom_wkt"   "priogrid_g" "country"    "region"     "event_clar"
    ## [36] "date_prec"  "date_start" "date_end"   "deaths_a"   "deaths_b"  
    ## [41] "deaths_civ" "deaths_unk" "best_est"   "high_est"   "low_est"   
    ## [46] "isocc"      "gwno"       "gwab"       "coords.x1"  "coords.x2"

``` r
sub <- db %>% 
  select(latitude,
         longitude,
         year,
         nkill = best_est) %>% 
  # sample_n(10) %>%
  arrange(year) 
summary(sub)
```

    ##     latitude        longitude            year          nkill         
    ##  Min.   :-34.60   Min.   :-117.05   Min.   :1989   Min.   :     0.0  
    ##  1st Qu.:  8.00   1st Qu.:  29.47   1st Qu.:1998   1st Qu.:     1.0  
    ##  Median : 28.17   Median :  46.08   Median :2005   Median :     2.0  
    ##  Mean   : 21.59   Mean   :  45.73   Mean   :2004   Mean   :    14.8  
    ##  3rd Qu.: 34.11   3rd Qu.:  74.67   3rd Qu.:2011   3rd Qu.:     6.0  
    ##  Max.   : 60.00   Max.   : 160.28   Max.   :2015   Max.   :300559.0

Non ci sono valori mancanti.

Animazione
----------

Il mio obiettivo e' un risultato simile a quello visto a questo link, <https://www.kaggle.com/tentotheminus9/d/tentotheminus9/good-morning-tweets/good-morning-twitter-animation/code>, ma utilizzando il pacchetto `gganimate` di [David Robinson](http://varianceexplained.org/), installato da [github](https://github.com/dgrtwo/gganimate)

``` r
p <- ggplot() +
  geom_path(data = world, aes(x = long,
                              y = lat,
                              group = group)) +
  coord_equal() +
  labs(title = "Persone uccise in conflitti armati") +
  scale_y_continuous(labels = scales::comma) +
  scale_colour_continuous(labels = scales::comma) +
  geom_point(data = sub,
             aes(x = longitude,
                 y = latitude,
                 size = nkill,
                 frame = year,
                 cumulative = TRUE),
             color = "red",
             alpha = 0.3)
```

    ## Warning: Ignoring unknown aesthetics: frame, cumulative

``` r
gganimate::gganimate(p)
```

<video   controls loop>
<source src="{{ site.url }}/assets/animations/prio/prio.webm" />
<p>
video of chunk anim
</p>
</video>

Altre risorse
-------------

-   il pacchetto `animation` <https://cran.r-project.org/web/packages/animation/animation.pdf>

-   utilizzare correttamente le opzioni in `knitr` <http://stackoverflow.com/questions/12038893/combining-r-markdown-and-animation-package#12039871>
