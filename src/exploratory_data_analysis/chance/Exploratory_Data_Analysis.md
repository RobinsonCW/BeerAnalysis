Exploratory Data Analysis
================
Chance Robinson
10/12/2019

  - [Exploratory Data Analysis](#exploratory-data-analysis)
      - [Library Imports](#library-imports)
      - [Load the csv data](#load-the-csv-data)
      - [Default Output](#default-output)
          - [Beers](#beers)
          - [Breweries](#breweries)
      - [State Lookup](#state-lookup)
      - [Brewery Count by State](#brewery-count-by-state)
      - [Merge beer and breweries](#merge-beer-and-breweries)
          - [First 6 observations of merged data
            sets](#first-6-observations-of-merged-data-sets)
          - [Last 6 observations of merged data
            sets](#last-6-observations-of-merged-data-sets)
      - [Missing Values](#missing-values)
      - [Barplot of median values](#barplot-of-median-values)
          - [ABV](#abv)
          - [IBU](#ibu)
      - [Highest Median Values](#highest-median-values)
          - [ABV](#abv-1)
          - [IBU](#ibu-1)
      - [ABV Summaary](#abv-summaary)
          - [Histogram](#histogram)
          - [Summary Statistics](#summary-statistics)
      - [ABV vs IBU](#abv-vs-ibu)
          - [Scatterplot](#scatterplot)

# Exploratory Data Analysis

## Library Imports

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.2.0     v purrr   0.3.2
    ## v tibble  2.1.3     v dplyr   0.8.3
    ## v tidyr   0.8.3     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Load the csv data

``` r
beers <- read.csv("../../../data/Beers.csv", stringsAsFactors=FALSE)
breweries <- read.csv("../../../data/Breweries.csv", stringsAsFactors=FALSE)
```

## Default Output

### Beers

``` r
head(beers)
```

    ##                  Name Beer_ID   ABV IBU Brewery_id
    ## 1            Pub Beer    1436 0.050  NA        409
    ## 2         Devil's Cup    2265 0.066  NA        178
    ## 3 Rise of the Phoenix    2264 0.071  NA        178
    ## 4            Sinister    2263 0.090  NA        178
    ## 5       Sex and Candy    2262 0.075  NA        178
    ## 6        Black Exodus    2261 0.077  NA        178
    ##                            Style Ounces
    ## 1            American Pale Lager     12
    ## 2        American Pale Ale (APA)     12
    ## 3                   American IPA     12
    ## 4 American Double / Imperial IPA     12
    ## 5                   American IPA     12
    ## 6                  Oatmeal Stout     12

### Breweries

``` r
head(breweries)
```

    ##   Brew_ID                      Name          City State
    ## 1       1        NorthGate Brewing    Minneapolis    MN
    ## 2       2 Against the Grain Brewery    Louisville    KY
    ## 3       3  Jack's Abby Craft Lagers    Framingham    MA
    ## 4       4 Mike Hess Brewing Company     San Diego    CA
    ## 5       5   Fort Point Beer Company San Francisco    CA
    ## 6       6     COAST Brewing Company    Charleston    SC

``` r
# The state column has an extra white space at the end of the string
breweries <- breweries %>%
  mutate(State = trimws(State))
```

## State Lookup

  - Washington D.C. added as an
entry

<!-- end list -->

``` r
lookup_df = data.frame(state_abb = state.abb, state_name = state.name, stringsAsFactors=FALSE) #makes a data frame with State name and abbreviation. 
# lookup_df

# add the District of Columbia as a lookup value
lookup_df <- rbind(lookup_df, 'DC' = c("DC", "Washington D.C."))
lookup_df
```

    ##    state_abb      state_name
    ## 1         AL         Alabama
    ## 2         AK          Alaska
    ## 3         AZ         Arizona
    ## 4         AR        Arkansas
    ## 5         CA      California
    ## 6         CO        Colorado
    ## 7         CT     Connecticut
    ## 8         DE        Delaware
    ## 9         FL         Florida
    ## 10        GA         Georgia
    ## 11        HI          Hawaii
    ## 12        ID           Idaho
    ## 13        IL        Illinois
    ## 14        IN         Indiana
    ## 15        IA            Iowa
    ## 16        KS          Kansas
    ## 17        KY        Kentucky
    ## 18        LA       Louisiana
    ## 19        ME           Maine
    ## 20        MD        Maryland
    ## 21        MA   Massachusetts
    ## 22        MI        Michigan
    ## 23        MN       Minnesota
    ## 24        MS     Mississippi
    ## 25        MO        Missouri
    ## 26        MT         Montana
    ## 27        NE        Nebraska
    ## 28        NV          Nevada
    ## 29        NH   New Hampshire
    ## 30        NJ      New Jersey
    ## 31        NM      New Mexico
    ## 32        NY        New York
    ## 33        NC  North Carolina
    ## 34        ND    North Dakota
    ## 35        OH            Ohio
    ## 36        OK        Oklahoma
    ## 37        OR          Oregon
    ## 38        PA    Pennsylvania
    ## 39        RI    Rhode Island
    ## 40        SC  South Carolina
    ## 41        SD    South Dakota
    ## 42        TN       Tennessee
    ## 43        TX           Texas
    ## 44        UT            Utah
    ## 45        VT         Vermont
    ## 46        VA        Virginia
    ## 47        WA      Washington
    ## 48        WV   West Virginia
    ## 49        WI       Wisconsin
    ## 50        WY         Wyoming
    ## 51        DC Washington D.C.

## Brewery Count by State

1.  How many breweries are present in each state?

<!-- end list -->

  - Note that all 50 states have counts and that the District of
    Columbia is also included to make the total of
51

<!-- end list -->

``` r
df_breweries <- merge(breweries, lookup_df, by.x = "State", by.y = "state_abb", all.x = TRUE)


df_breweries_count_by_state <- df_breweries %>%
  count(state_name, sort = TRUE)
# 
df_breweries_count_by_state
```

    ## # A tibble: 51 x 2
    ##    state_name        n
    ##    <chr>         <int>
    ##  1 Colorado         47
    ##  2 California       39
    ##  3 Michigan         32
    ##  4 Oregon           29
    ##  5 Texas            28
    ##  6 Pennsylvania     25
    ##  7 Massachusetts    23
    ##  8 Washington       23
    ##  9 Indiana          22
    ## 10 Wisconsin        20
    ## # ... with 41 more rows

``` r
df_merged <- merge(beers, df_breweries, by.x = "Brewery_id", by.y = "Brew_ID")
```

## Merge beer and breweries

2.  Merge beer data with the breweries data. Print the first 6
    observations and the last six observations to check the merged file.
    (RMD only, this does not need to be included in the presentation or
    the deck.)

### First 6 observations of merged data sets

``` r
head(df_merged)
```

    ##   Brewery_id        Name.x Beer_ID   ABV IBU
    ## 1          1  Get Together    2692 0.045  50
    ## 2          1 Maggie's Leap    2691 0.049  26
    ## 3          1    Wall's End    2690 0.048  19
    ## 4          1       Pumpion    2689 0.060  38
    ## 5          1    Stronghold    2688 0.060  25
    ## 6          1   Parapet ESB    2687 0.056  47
    ##                                 Style Ounces State             Name.y
    ## 1                        American IPA     16    MN NorthGate Brewing 
    ## 2                  Milk / Sweet Stout     16    MN NorthGate Brewing 
    ## 3                   English Brown Ale     16    MN NorthGate Brewing 
    ## 4                         Pumpkin Ale     16    MN NorthGate Brewing 
    ## 5                     American Porter     16    MN NorthGate Brewing 
    ## 6 Extra Special / Strong Bitter (ESB)     16    MN NorthGate Brewing 
    ##          City state_name
    ## 1 Minneapolis  Minnesota
    ## 2 Minneapolis  Minnesota
    ## 3 Minneapolis  Minnesota
    ## 4 Minneapolis  Minnesota
    ## 5 Minneapolis  Minnesota
    ## 6 Minneapolis  Minnesota

### Last 6 observations of merged data sets

``` r
tail(df_merged)
```

    ##      Brewery_id                    Name.x Beer_ID   ABV IBU
    ## 2405        556             Pilsner Ukiah      98 0.055  NA
    ## 2406        557  Heinnieweisse Weissebier      52 0.049  NA
    ## 2407        557           Snapperhead IPA      51 0.068  NA
    ## 2408        557         Moo Thunder Stout      50 0.049  NA
    ## 2409        557         Porkslap Pale Ale      49 0.043  NA
    ## 2410        558 Urban Wilderness Pale Ale      30 0.049  NA
    ##                        Style Ounces State                        Name.y
    ## 2405         German Pilsener     12    CA         Ukiah Brewing Company
    ## 2406              Hefeweizen     12    NY       Butternuts Beer and Ale
    ## 2407            American IPA     12    NY       Butternuts Beer and Ale
    ## 2408      Milk / Sweet Stout     12    NY       Butternuts Beer and Ale
    ## 2409 American Pale Ale (APA)     12    NY       Butternuts Beer and Ale
    ## 2410        English Pale Ale     12    AK Sleeping Lady Brewing Company
    ##               City state_name
    ## 2405         Ukiah California
    ## 2406 Garrattsville   New York
    ## 2407 Garrattsville   New York
    ## 2408 Garrattsville   New York
    ## 2409 Garrattsville   New York
    ## 2410     Anchorage     Alaska

## Missing Values

3.  Address the missing values in each column.

<!-- end list -->

  - The ABV and IBU columns have missing values

  - 62 for ABV

  - 1005 for IBU (this is a very high percentage of the overall rows)

<!-- end list -->

``` r
df_has_na_values <- any(is.na(df_merged))
df_has_na_values
```

    ## [1] TRUE

``` r
# which columns have missing values
df_na_columns = which(colSums(is.na(df_merged)) > 0)
df_na_columns
```

    ## ABV IBU 
    ##   4   5

``` r
df_na_abv <- df_merged %>%
  filter(is.na(ABV))

dim(df_na_abv)[1]
```

    ## [1] 62

``` r
df_na_ibu <- df_merged %>%
  filter(is.na(IBU))

dim(df_na_ibu)[1]
```

    ## [1] 1005

## Barplot of median values

4.  Compute the median alcohol content and international bitterness unit
    for each state. Plot a bar chart to compare.

### ABV

``` r
df_merged_abv_clean <- df_merged %>%
  filter(!is.na(ABV)) %>%
  group_by(state_name) %>%
  summarize(ABV.MEDIAN = median(ABV), count = n()) %>%
  arrange(desc(ABV.MEDIAN))
  
# df_merged_abv_clean

df_merged_abv_clean %>%
  ggplot(aes(x = state_name, y=as.numeric(ABV.MEDIAN))) +
  geom_bar(stat = "identity", position = "dodge") +
  ggtitle("Bar Plot of median ABV per State") +
  labs(x = "State", y = "Median") +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![](Exploratory_Data_Analysis_files/figure-gfm/merged-median-abv-barplot-1.png)<!-- -->

### IBU

``` r
df_merged_ibu_clean <- df_merged %>%
  filter(!is.na(IBU)) %>%
  group_by(state_name) %>%
  summarize(IBU.MEDIAN = median(IBU), count = n()) %>%
  arrange(desc(IBU.MEDIAN))
  
# df_merged_ibu_clean


df_merged_ibu_clean %>%
  ggplot(aes(x = state_name, y=as.numeric(IBU.MEDIAN))) +
  geom_bar(stat = "identity", position = "dodge") +
  ggtitle("Bar Plot of median IBU per State") +
  labs(x = "State", y = "Median") +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![](Exploratory_Data_Analysis_files/figure-gfm/merged-median-ibu-barplot-1.png)<!-- -->

## Highest Median Values

5.  Which state has the maximum alcoholic (ABV) beer? Which state has
    the most bitter (IBU) beer?

### ABV

``` r
head(df_merged_abv_clean)
```

    ## # A tibble: 6 x 3
    ##   state_name      ABV.MEDIAN count
    ##   <chr>                <dbl> <int>
    ## 1 Kentucky            0.0625    20
    ## 2 Washington D.C.     0.0625     8
    ## 3 Michigan            0.062    151
    ## 4 New Mexico          0.062     13
    ## 5 West Virginia       0.062      2
    ## 6 Colorado            0.0605   250

### IBU

``` r
head(df_merged_ibu_clean)
```

    ## # A tibble: 6 x 3
    ##   state_name    IBU.MEDIAN count
    ##   <chr>              <dbl> <int>
    ## 1 Maine               61       7
    ## 2 West Virginia       57.5     2
    ## 3 Florida             55      37
    ## 4 Georgia             55       7
    ## 5 Delaware            52       1
    ## 6 New Mexico          51       6

## ABV Summaary

### Histogram

6.  Comment on the summary statistics and distribution of the ABV
    variable.

<!-- end list -->

  - The histogram is right-skewed with the majority of the data hovering
    around the 0.05 range

<!-- end list -->

``` r
df_merged_abv_clean2 <- df_merged %>%
  filter(!is.na(ABV))
  # mutate(min = min(ABV, median = median(ABV), mean = mean(ABV), max = max(ABV)))

# summary(df_merged_abv_clean2)

abv_min <- min(df_merged_abv_clean2$ABV)
abv_max <- max(df_merged_abv_clean2$ABV)
abv_median <- median(df_merged_abv_clean2$ABV)
abv_mean <- mean(df_merged_abv_clean2$ABV)
abv_sd <- sd(df_merged_abv_clean2$ABV)


df_merged_abv_clean2 %>%
  ggplot(aes(as.numeric(ABV))) +
  geom_histogram() +
  ggtitle("Histogram of median ABV per State") +
  labs(x = "Median", y = "Count") +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](Exploratory_Data_Analysis_files/figure-gfm/merged-median-abv-histogram-1.png)<!-- -->

### Summary Statistics

``` r
abv_min 
```

    ## [1] 0.001

``` r
abv_max 
```

    ## [1] 0.128

``` r
abv_median 
```

    ## [1] 0.056

``` r
abv_mean 
```

    ## [1] 0.05977342

``` r
abv_sd 
```

    ## [1] 0.01354173

## ABV vs IBU

7.  Is there an apparent relationship between the bitterness of the beer
    and its alcoholic content? Draw a scatter plot. Make your best
    judgment of a relationship and EXPLAIN your answer.

### Scatterplot

``` r
### TODO
```
