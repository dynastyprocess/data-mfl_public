
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Public MFL League Data

<!-- badges: start -->
<!-- badges: end -->

This repository contains MFL data gathered via
[ffscrapr](https://github.com/dynastyprocess/ffscrapr) and stored as
[Apache Arrow](https://arrow.apache.org/docs/r/articles/arrow.html)’s
Parquet files.

Usage instructions currently written out in R - if you use a different
programming language, I would gladly take a PR with parallel
instructions!

## Contents

There are currently two included datasets: SafeLeagues data (2019-2020)
and ScottFishBowl data (2016-2020).

Within each of these, you will find subfolders:

-   `league`: contains a table summarising features about the leagues
    within the dataset such as scoring\_flags, best\_ball, idp,
    qb\_type, player\_copies, league\_size.
-   `draft`: contains the most recent draft for that league year
-   `standings`: contains season-finish data, including H2H/All-Play win
    records, points for, potential points etc
-   `starters`: contains the starters for each week of the regular
    season
-   `transactions`: contains all public transaction data (adds, drops,
    trades, blind bidding etc)

## Install (R)

You will need to clone or download this repository to your machine in
order to use this data (as of this writing). You can do this via the
buttons above, or by running this command in a terminal.

    git clone https://www.github.com/dynastyprocess/data-mfl_public.git

The Arrow R package can be installed with:

``` r
install.packages("arrow")
# or, if that gives you problems, I usually can get the nightly version to work a little better
install.packages("arrow", repos = "https://arrow-r-nightly.s3.amazonaws.com")
```

If both of these give you problems, please see the official arrow help
for installation here:
<https://arrow.apache.org/docs/r/articles/install.html>)

## Usage (R)

Each subfolder (i.e. `scottfishbowl/draft`) is designed to be opened as
a multi-file Arrow dataset.

Here’s an example of accessing the league summary table:

``` r
suppressPackageStartupMessages({
  library(arrow)
  library(dplyr)
})
#> Warning: package 'arrow' was built under R version 4.0.5

safeleagues_leagues <- arrow::open_dataset("safeleagues/league/") %>% 
  dplyr::collect()

safeleagues_leagues
#> # A tibble: 1,838 x 14
#>    league_id league_name                               franchise_count qb_type
#>    <chr>     <chr>                                               <dbl> <chr>  
#>  1 10089     SafeLeagues Dynasty 143 - Basic (SF)                   12 2QB/SF 
#>  2 10108     SafeLeagues Napoleon 02 - CP Challenge #1              12 1QB    
#>  3 10124     SafeLeagues Dynasty 120 - Premium (SF/TE)              12 2QB/SF 
#>  4 10608     SafeLeagues Dynasty 078 - Rival (SF)                   12 2QB/SF 
#>  5 10627     SafeLeagues Dynasty 127 - Basic (SF)                   12 2QB/SF 
#>  6 10672     SafeLeagues Dynasty 090 - Basic (SF/TE)                12 2QB/SF 
#>  7 10974     SafeLeagues Dynasty 142 - Basic                        12 1QB    
#>  8 11122     SafeLeagues Redraft 09 - Starter (SF)                  12 2QB/SF 
#>  9 11205     SafeLeagues Redraft 07 - Starter (SF)                  12 2QB/SF 
#> 10 11360     SafeLeagues Dynasty 186 - Basic (SF/TE)                12 2QB/SF 
#> # ... with 1,828 more rows, and 10 more variables: idp <lgl>,
#> #   scoring_flags <chr>, best_ball <lgl>, salary_cap <lgl>,
#> #   player_copies <dbl>, years_active <chr>, qb_count <chr>, roster_size <dbl>,
#> #   league_depth <dbl>, year <int>
```

Arrow is very useful because you can pass filters, sorts, and selects to
the dataset `before` loading it into memory. For example, if you want to
look at waiver wire adds (and not drops) in Scott Fish Bowl’s 2020
leagues, you can do so like this:

``` r
sfb_adds <- arrow::open_dataset("scottfishbowl/transactions/") %>% 
  filter(type_desc == "added", year == 2020, sfb_type == "official") %>% 
  arrange(desc(bbid_spent)) %>% 
  select(league_id, player_id, player_name, pos, team, bbid_spent, franchise_id, franchise_name, timestamp) %>% 
  collect()

sfb_adds
#> # A tibble: 20,400 x 9
#>    league_id player_id player_name     pos   team  bbid_spent franchise_id
#>        <int> <chr>     <chr>           <chr> <chr>      <dbl> <chr>       
#>  1     13411 14811     Ahmed, Salvon   RB    MIA          100 0041        
#>  2     13411 10313     Dalton, Andy    QB    DAL          100 0063        
#>  3     13411 14265     Fulgham, Travis WR    PHI          100 0051        
#>  4     13411 10313     Dalton, Andy    QB    DAL          100 0055        
#>  5     16428 14096     Ozigbo, Devine  RB    JAC          100 0062        
#>  6     17910 14095     Johnson, Ty     RB    NYJ          100 0059        
#>  7     17910 10313     Dalton, Andy    QB    DAL          100 0057        
#>  8     17910 10313     Dalton, Andy    QB    DAL          100 0001        
#>  9     17910 14065     Rypien, Brett   QB    DEN          100 0093        
#> 10     17910 14813     Robinson, James RB    JAC          100 0032        
#> # ... with 20,390 more rows, and 2 more variables: franchise_name <chr>,
#> #   timestamp <dttm>
```

## Contributing

Many hands make light work, especially when maintaining open data! Here
are some ways you can contribute to this project:

-   You can [open an
    issue](https://github.com/DynastyProcess/data-mfl_public/issues/new)
    if you’d like to request specific data or report a bug/error.

-   You can [sponsor this project by donating to help with server
    costs](https://github.com/sponsors/tanho63)!

Please note that this project is released with a [Contributor Code of
Conduct](https://github.com/DynastyProcess/data/blob/master/CODE_OF_CONDUCT.md) - 
by participating, you agree to abide by these terms.

## Terms of Use

This data is released under CC0.
