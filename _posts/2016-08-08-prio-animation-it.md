---
title: Un'anticipazione del dataset PRIO
excerpt: Semplice animazione ottenuta con il pacchetto `gganimate`
date: 2016-08-08
lang: it
tags: analisi-esplorative-di-dati
ref: prio-animation

---

Scopo di questa pagina e' la creazione di un'animazione utilizzando il pacchetto [`gganimate`](https://github.com/dgrtwo/gganimate){:target="_blank"}.

Usero' dati provenienti dal [PRIO](https://www.prio.org/){:target="_blank"}, il **Peace Research Institute Oslo** sui conflitti armati.

> Gleditsch, Nils Petter; Peter Wallensteen, Mikael Eriksson, Margareta Sollenberg & Håvard Strand, 2002. ‘Armed Conflict 1946–2001: A New Dataset’, Journal of Peace Research 39(5): 615–637.

Carico i dati
-------------

Scarico il dataset dalla pagina del [Uppsala Conflict Data Program](http://www.pcr.uu.se/research/UCDP/){:target="_blank"} in formato `Rdata`.

{% highlight r %}
library(tidyverse)
{% endhighlight %}

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

{% highlight r %}
load("~/Downloads/ged50.Rdata")
db <- tbl_df(ged50)
{% endhighlight %}

    ## Loading required package: sp

{% highlight r %}
world <- map_data("world")
{% endhighlight %}

    ## 
    ## Attaching package: 'maps'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     map

Mantengo solo data e coordinate
-------------------------------

{% highlight r %}
names(db)
{% endhighlight %}

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

{% highlight r %}
sub <- db %>% 
  select(latitude,
         longitude,
         year,
         nkill = best_est) %>% 
  # sample_n(10) %>%
  arrange(year) 
summary(sub)
{% endhighlight %}

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

{% highlight r %}
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
{% endhighlight %}

    ## Warning: Ignoring unknown aesthetics: frame, cumulative

{% highlight r %}
gganimate::gganimate(p)
{% endhighlight %}

<video controls loop>
<source src="{{ site.url }}/assets/animations/prio/prio.webm" />
<p>
video of chunk anim
</p>
</video>

Altre risorse
-------------

-   il pacchetto `animation` in [Cran](https://cran.r-project.org/web/packages/animation/animation.pdf){:target="_blank"}

-   utilizzare correttamente le opzioni in `knitr`, da [stackoverflow](http://stackoverflow.com/questions/12038893/combining-r-markdown-and-animation-package#12039871){:target="_blank"}

{% include rsource.html %}
