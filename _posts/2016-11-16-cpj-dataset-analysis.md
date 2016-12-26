---
title: "An analysis of the Cpj (Committee to Protect Journalists) database of journalists killed from 1992"
date: 2016-11-16

---

Main findings
=============

**Impunity**: there is an irrisory percentage (2.7%) of cases when everyone responsible of journalist's murders is convicted, including both perpetrators and masterminds.

**Deaths in Iraq not covering war**: 51% of journalists dead in Iraq after 2003 were not covering the war.

Introduction
============

Cpj, and the dataset
--------------------

The Committee to Protect Journalists is an independent, nonprofit organization that promotes press freedom worldwide.

This dataset contains records of journalists deaths since 1992 where an investigation has been done, or it is in progress, to define whether a journalist's death was work-related.

Dataset, methodology and full terminology are available at [Cpj website](https://cpj.org/killed/).

Weaknesses of this analysis
---------------------------

More cleaning of data would be required to pursue other in-depth analysis; as example:

-   How is data accuracy over years? Are figures related to recent years comparable with those of earlier years?

-   Only from 2003 CPJ started collecting data on media support workers: this can skew the numbers toward following years.

-   In my analysis I considered "Unconfirmed" cases part of the population, differently than the analysis available on cpj website.

Getting and cleaning data
-------------------------

Dataset is available in Kaggle <https://www.kaggle.com/cpjournalists/journalists-killed-worldwide-since-1992/downloads/journalists-killed-worldwide-since-1992.zip>.

I used **R** as analysis software, alongside the **tidyverse** packages and the **RStudio** platform.

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

What were journalists covering?
===============================

In many cases journalists were not covering a single topic, therefore I disaggregated the variable; sum of bars is not equal to number of deaths.

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/coverages-1.png)

Killed by country/year not in war coverage
------------------------------------------

I focus on cases where the journalist was not covering war.

    ## Source: local data frame [450 x 3]
    ## Groups: Country_killed [101]
    ## 
    ##    Country_killed  Year Count
    ##            <fctr> <dbl> <int>
    ## 1     Philippines  2009    38
    ## 2            Iraq  2007    32
    ## 3            Iraq  2006    25
    ## 4            Iraq  2004    21
    ## 5          Rwanda  1994    20
    ## 6         Algeria  1995    19
    ## 7         Algeria  1994    12
    ## 8      Tajikistan  1993    12
    ## 9     Philippines  2004    11
    ## 10        Somalia  2012    11
    ## # ... with 440 more rows

Note the Philippines first result: this can be seen as [the worst loss of life of media professionals in one day in the history of journalism](http://www.gmanetwork.com/news/story/178575/news/nation/arroyo-declares-martial-law-in-maguindanao-province)

But it is interesting to note that, from this subset, 78 journalists in Iraq in 2004, 2006 and 2007 were not covering the war, that was [ongoing from 2003](https://en.wikipedia.org/wiki/Iraq_War)

Iraq case: war coverage by year
-------------------------------

The following graph plots deaths by year in Iraq: we can see that most of cases occur after the start of the war. We also see that there is a relevant percentage of cases where journalists weren't cover war.

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/coverage-in-iraq-1.png)

This can be better analysed by stacking the bars and then standardising each one to have the same height.

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/proportions-coverage-iraq-1.png)

Frequency table provides percentages by year ( but note that we lose here the weights on total number of deaths by year)

    ## Source: local data frame [15 x 3]
    ## Groups: Year [15]
    ## 
    ##     Year `Was covering war` `Was not covering war`
    ##    <dbl>              <chr>                  <chr>
    ## 1   1994               100%                     0%
    ## 2   2003              66.7%                  33.3%
    ## 3   2004              44.7%                  55.3%
    ## 4   2005              81.5%                  18.5%
    ## 5   2006              54.5%                  45.5%
    ## 6   2007              37.3%                  62.7%
    ## 7   2008              43.8%                  56.2%
    ## 8   2009                50%                    50%
    ## 9   2010              11.1%                  88.9%
    ## 10  2011                 0%                   100%
    ## 11  2012                 0%                   100%
    ## 12  2013              33.3%                  66.7%
    ## 13  2014                50%                    50%
    ## 14  2015                60%                    40%
    ## 15  2016                75%                    25%

Total share of deaths while covering war in all years in Iraq is showed in this table:

    ## # A tibble: 2 × 3
    ##                    War     n   freq
    ##                  <chr> <int>  <chr>
    ## 1     Was covering war   129 49.05%
    ## 2 Was not covering war   134 50.95%

From this table we conclude that `51%` of journalists were not covering war in Iraq at the time of their death.

Who / What are causes of death?
===============================

From [Cpj's methodology](https://cpj.org/killed/terminology.php): "**Type of Death**: CPJ further categorizes each death in which the motive is confirmed. The categories are: **crossfire/combat** (a killing on a battlefield or in a military context); **dangerous assignment** (deaths while covering a demonstration, riot, clashes between rival groups, and mob situations); and **murder** (the targeted killing of a journalist, whether premeditated or spontaneous, in direct relation to the journalist's work)"

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/typedeath-if-war-1.png)

Not surprisingly, when war was covered a big cause of death were combat-related actions.

I will now focus on cases of murder.

Murder perpetrators
-------------------

Who are the murderers?

From [Cpj's methodology](https://cpj.org/killed/terminology.php): "**Suspected Perpetrators in Murder Cases**: This refers to the person or entity CPJ has identified as most likely responsible. Categories include: **political groups** (antigovernment parties or combatants, including insurgents and terrorists); **government officials** (civilian government officials, including police); **military officials** (members of the government's military); **paramilitary groups** (irregular armed forces allied with the government); **criminal groups** (criminals or members of criminal gangs); **mob violence** (crowds of people acting together but not otherwise organized); and **local residents** (individuals inspired to violence by news coverage).".

Since these are not always defined, in some cases there are multiple suspect perpretrators for a record; I will then create a list with all perpetrators with weights in case for a record there are multiple possibilities.

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/murderers-1.png)

Political groups are the biggest source of deaths for journalists.

Impunity
========

From [Cpj's methodology](https://cpj.org/killed/terminology.php): "**Impunity in Murder Cases**: CPJ monitors the law enforcement and judicial process for each confirmed murder case, and we categorize the status of the investigation. The categories are: **complete impunity** (no convictions have been obtained); **partial justice** (some but not all of those responsible have been convicted; typically, assassins are convicted but not masterminds); and **full justice** (everyone responsible is convicted, including both perpetrators and masterminds)".

Impunity analysis on all countries may be little significative due to the amount of other variables outside this database: I will instead focus on countries with the higher number of murders.

In this dataset there are `796` cases of murder.

I will analyze them in relation to geographic area.

Over `89` countries where cases of murder are present, only `20` contribute to the 80% of total cases. I will focus on these.

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/impunity-by-country-1.png)

From this data there is `2.69%` possibility to have justice after a murder case.

Is there variation of impunity over time? Focus on the 3 deadliest countries.
-----------------------------------------------------------------------------

Is the impunity changing over time? I will focus on the 3 deadliest countries for clarity of representation.

``` r
plot_impunity <- function(country_index){
    c <- murder_countries[country_index, "Country_killed"][[1]] %>% as.character()
    d %>%
        tbl_df() %>%
        filter(Country_killed == c,
               !is.na(Impunity_for_murder),
               !is.na(Year)) %>%
        select(Country_killed, Year, Impunity_for_murder) %>%
        ggplot(aes(x = Year)) +
        geom_bar(aes(fill = Impunity_for_murder)) +
        scale_fill_brewer(palette = "RdYlGn",
                          direction = -1,
                          guide = guide_legend(title = "Impunity")) +
        theme_minimal() +
        labs(title = paste("Impunity for murders in", c),
             y = "Number of murders",
             x = "Year")
}
```

### Iraq

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/impunity-iraq-1.png)

### Philippines

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/impunity-philippines-1.png)

### Algeria

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/impunity-algeria-1.png)

There are so few solved murderes that a plot over time is not meaningful.

Impunity related to suspect perpetrators
----------------------------------------

How is impunity related to who is suspected to have committed the crime?

![]({{ site.url }}/assets/images/2016-11-16-cpj/figure-markdown_github/impunity-by-perpetrator-1.png)

It seems that impunity is lowest when the murders come from local residents, and is max when there are political groups or unknown fire as source of death.

Conclusion
==========

While I focused my analysis on impunity for murder and on coverage in Iraq, the datasets can offer several other analysis ideas, some of them I started in my exploratory analysis.

I also highlighted some possible flaws of my analysis, therefore data methodology and terminology must be carefully ponderated when extrapolating results, since can lead to wrong interpretations.

# Session info

``` r
sessionInfo()
```

    ## R version 3.3.2 (2016-10-31)
    ## Platform: x86_64-pc-linux-gnu (64-bit)
    ## Running under: Ubuntu 16.04.1 LTS
    ## 
    ## locale:
    ##  [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
    ##  [3] LC_TIME=de_DE.UTF-8        LC_COLLATE=en_US.UTF-8    
    ##  [5] LC_MONETARY=de_DE.UTF-8    LC_MESSAGES=en_US.UTF-8   
    ##  [7] LC_PAPER=de_DE.UTF-8       LC_NAME=C                 
    ##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
    ## [11] LC_MEASUREMENT=de_DE.UTF-8 LC_IDENTIFICATION=C       
    ## 
    ## attached base packages:
    ##  [1] stats     graphics  grDevices utils     datasets  methods  
    ##  [7] base     
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_0.12.8      tidyr_0.6.0      assertthat_0.1   digest_0.6.10   
    ##  [5] dplyr_0.5.0      rprojroot_1.1    R6_2.2.0         plyr_1.8.4      
    ##  [9] DBI_0.5-1        backports_1.0.4  magrittr_1.5     scales_0.4.1    
    ## [13] evaluate_0.10    stringi_1.1.2    rmarkdown_1.3    tools_3.3.2     
    ## [17] servr_0.5        stringr_1.1.0    munsell_0.4.3    httpuv_1.3.3    
    ## [21] yaml_2.1.14      colorspace_1.3-2 htmltools_0.3.5  knitr_1.15.1    
    ## [25] tibble_1.2   

