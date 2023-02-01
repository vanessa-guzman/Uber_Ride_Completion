Code–Uber Ride Completion
================
2022-10-11

# Uber Request Data

## Installing Packages

``` r
install.packages('tidyverse', repos = "http://cran.us.r-project.org")
```

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/21/536vjlp524v0yj99lmnbb4z00000gn/T//Rtmp5sq77D/downloaded_packages

``` r
install.packages('lubridate', repos = "http://cran.us.r-project.org") 
```

    ## 
    ## The downloaded binary packages are in
    ##  /var/folders/21/536vjlp524v0yj99lmnbb4z00000gn/T//Rtmp5sq77D/downloaded_packages

## Loading Packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.0      ✔ purrr   1.0.1 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.3.0      ✔ stringr 1.5.0 
    ## ✔ readr   2.1.3      ✔ forcats 1.0.0 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(lubridate) #for working with dates
```

    ## 
    ## Attaching package: 'lubridate'
    ## 
    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

## Importing the Data

The data can be found on
[kaggle](https://www.kaggle.com/datasets/vatsalraicha/uber-request-data).
The was collected between July 11, 2016 and July 16, 2016.

``` r
uber_data <- read_csv("Uber Request Data.csv")
```

    ## Rows: 6745 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (4): Pickup point, Status, Request timestamp, Drop timestamp
    ## dbl (2): Request id, Driver id
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Inspecting the Data

``` r
dim(uber_data)
```

    ## [1] 6745    6

``` r
colnames(uber_data)
```

    ## [1] "Request id"        "Pickup point"      "Driver id"        
    ## [4] "Status"            "Request timestamp" "Drop timestamp"

``` r
head(uber_data)
```

    ## # A tibble: 6 × 6
    ##   `Request id` `Pickup point` `Driver id` Status         Request times…¹ Drop …²
    ##          <dbl> <chr>                <dbl> <chr>          <chr>           <chr>  
    ## 1          619 Airport                  1 Trip Completed 11/7/2016 11:51 11/7/2…
    ## 2          867 Airport                  1 Trip Completed 11/7/2016 17:57 11/7/2…
    ## 3         1807 City                     1 Trip Completed 12/7/2016 9:17  12/7/2…
    ## 4         2532 Airport                  1 Trip Completed 12/7/2016 21:08 12/7/2…
    ## 5         3112 City                     1 Trip Completed 13-07-2016 08:… 13-07-…
    ## 6         3879 Airport                  1 Trip Completed 13-07-2016 21:… 13-07-…
    ## # … with abbreviated variable names ¹​`Request timestamp`, ²​`Drop timestamp`

``` r
str(uber_data)
```

    ## spc_tbl_ [6,745 × 6] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ Request id       : num [1:6745] 619 867 1807 2532 3112 ...
    ##  $ Pickup point     : chr [1:6745] "Airport" "Airport" "City" "Airport" ...
    ##  $ Driver id        : num [1:6745] 1 1 1 1 1 1 1 1 1 2 ...
    ##  $ Status           : chr [1:6745] "Trip Completed" "Trip Completed" "Trip Completed" "Trip Completed" ...
    ##  $ Request timestamp: chr [1:6745] "11/7/2016 11:51" "11/7/2016 17:57" "12/7/2016 9:17" "12/7/2016 21:08" ...
    ##  $ Drop timestamp   : chr [1:6745] "11/7/2016 13:00" "11/7/2016 18:47" "12/7/2016 9:58" "12/7/2016 22:03" ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   `Request id` = col_double(),
    ##   ..   `Pickup point` = col_character(),
    ##   ..   `Driver id` = col_double(),
    ##   ..   Status = col_character(),
    ##   ..   `Request timestamp` = col_character(),
    ##   ..   `Drop timestamp` = col_character()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

## Cleaning the Data and Adding New Columns

### Converting Timestamps to Date Time

The functions parse_date_time() and guess_formats() were used because
the date was stored as a string and in two different formats.

``` r
uber_data$`Request timestamp` <- parse_date_time(uber_data$`Request timestamp` , guess_formats(x = uber_data$`Request timestamp`, c("dmY HM", "dmY HMS")))

uber_data$`Drop timestamp` <- parse_date_time(uber_data$`Drop timestamp` , guess_formats(x = uber_data$`Drop timestamp`, c("dmY HM", "dmY HMS")))

str(uber_data)
```

    ## spc_tbl_ [6,745 × 6] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ Request id       : num [1:6745] 619 867 1807 2532 3112 ...
    ##  $ Pickup point     : chr [1:6745] "Airport" "Airport" "City" "Airport" ...
    ##  $ Driver id        : num [1:6745] 1 1 1 1 1 1 1 1 1 2 ...
    ##  $ Status           : chr [1:6745] "Trip Completed" "Trip Completed" "Trip Completed" "Trip Completed" ...
    ##  $ Request timestamp: POSIXct[1:6745], format: "2016-07-11 11:51:00" "2016-07-11 17:57:00" ...
    ##  $ Drop timestamp   : POSIXct[1:6745], format: "2016-07-11 13:00:00" "2016-07-11 18:47:00" ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   `Request id` = col_double(),
    ##   ..   `Pickup point` = col_character(),
    ##   ..   `Driver id` = col_double(),
    ##   ..   Status = col_character(),
    ##   ..   `Request timestamp` = col_character(),
    ##   ..   `Drop timestamp` = col_character()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

``` r
# the make sure the dates were converted properly
```

### Calculating the Ride Duration

``` r
uber_data$ride_duration <- round(difftime(uber_data$`Drop timestamp`, uber_data$`Request timestamp`), 2)
# rounded to hundredths place
```

### New Columns for Weekdays and Hours

``` r
uber_data$weekday <- format(as.Date(uber_data$`Request timestamp`), "%A")

uber_data$weekday <- ordered(uber_data$weekday, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday"))
# to place weekdays in the correct order

uber_data$hour <- format(as.POSIXct(uber_data$`Request timestamp`), "%H")

is.numeric(uber_data$hour)
```

    ## [1] FALSE

``` r
# to check if hour column is numeric

uber_data$hour <- as.numeric(uber_data$hour)
# to convert hour column to numeric

str(uber_data)
```

    ## spc_tbl_ [6,745 × 9] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ Request id       : num [1:6745] 619 867 1807 2532 3112 ...
    ##  $ Pickup point     : chr [1:6745] "Airport" "Airport" "City" "Airport" ...
    ##  $ Driver id        : num [1:6745] 1 1 1 1 1 1 1 1 1 2 ...
    ##  $ Status           : chr [1:6745] "Trip Completed" "Trip Completed" "Trip Completed" "Trip Completed" ...
    ##  $ Request timestamp: POSIXct[1:6745], format: "2016-07-11 11:51:00" "2016-07-11 17:57:00" ...
    ##  $ Drop timestamp   : POSIXct[1:6745], format: "2016-07-11 13:00:00" "2016-07-11 18:47:00" ...
    ##  $ ride_duration    : 'difftime' num [1:6745] 69 50 41 55 ...
    ##   ..- attr(*, "units")= chr "mins"
    ##  $ weekday          : Ord.factor w/ 5 levels "Monday"<"Tuesday"<..: 1 1 2 2 3 3 4 5 5 1 ...
    ##  $ hour             : num [1:6745] 11 17 9 21 8 21 6 5 17 6 ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   `Request id` = col_double(),
    ##   ..   `Pickup point` = col_character(),
    ##   ..   `Driver id` = col_double(),
    ##   ..   Status = col_character(),
    ##   ..   `Request timestamp` = col_character(),
    ##   ..   `Drop timestamp` = col_character()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

``` r
# the make sure the hour was converted properly
```

### Using Case When for the Time of Day and Work Hours

``` r
uber_data$time <- case_when(uber_data$hour >=5  & uber_data$hour <=11 ~"Morning" , uber_data$hour >=12 & uber_data$hour <= 17 ~"Afternoon", uber_data$hour >= 18 & uber_data$hour <=21 ~'Evening', uber_data$hour >= 22 | uber_data$hour<=4 ~'Night')

uber_data$time <- ordered(uber_data$time, levels = c("Morning", "Afternoon", "Evening" ,"Night"))
# to order the times of day

uber_data$work <- case_when(uber_data$hour >= 8 & uber_data$hour <=17 ~"typical work hours", TRUE ~ "off hours")
```

## Analyzing and Exploring the Data

### Date Range

``` r
uber_data %>% filter(Status == "Trip Completed") %>% summarize(first_request = min(`Request timestamp`), last_dropoff = max(`Drop timestamp`))
```

    ## # A tibble: 1 × 2
    ##   first_request       last_dropoff       
    ##   <dttm>              <dttm>             
    ## 1 2016-07-11 00:00:00 2016-07-16 01:09:24

### Number of Rides and Drivers

``` r
uber_data %>% summarize(total_rides = n()) 
```

    ## # A tibble: 1 × 1
    ##   total_rides
    ##         <int>
    ## 1        6745

``` r
uber_data %>% summarize(unique_drivers = n_distinct(`Driver id`))
```

    ## # A tibble: 1 × 1
    ##   unique_drivers
    ##            <int>
    ## 1            301

### Summary of Statuses

``` r
uber_data %>% group_by(Status) %>% summarize(stat_perc = round(100 * n()/ nrow(uber_data), 2))
```

    ## # A tibble: 3 × 2
    ##   Status            stat_perc
    ##   <chr>                 <dbl>
    ## 1 Cancelled              18.7
    ## 2 No Cars Available      39.3
    ## 3 Trip Completed         42.0

``` r
# rounded
# percentages were used instead of counts to make comparisons simpler
```

### Summary of Pickup Points

``` r
uber_data %>% group_by(`Pickup point`) %>% summarize(pickup_perc = round(100 * n()/ nrow(uber_data), 2))
```

    ## # A tibble: 2 × 2
    ##   `Pickup point` pickup_perc
    ##   <chr>                <dbl>
    ## 1 Airport               48.0
    ## 2 City                  52.0

``` r
# rounded
```

### Status by Pickup Point

``` r
uber_data %>% group_by(`Pickup point`, Status) %>% summarize(pickup_status = round(100 * n()/ nrow(uber_data), 2)) %>% spread(Status, pickup_status)
```

    ## `summarise()` has grouped output by 'Pickup point'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 2 × 4
    ## # Groups:   Pickup point [2]
    ##   `Pickup point` Cancelled `No Cars Available` `Trip Completed`
    ##   <chr>              <dbl>               <dbl>            <dbl>
    ## 1 Airport             2.94                25.4             19.7
    ## 2 City               15.8                 13.9             22.3

### Summary of Ride Duration

``` r
uber_data %>% filter(Status == "Trip Completed") %>% summarize(avgerage_ride = round(mean(ride_duration), 2), shortest_ride = round(min(ride_duration), 2), longest_ride = round(max(ride_duration),2))
```

    ## # A tibble: 1 × 3
    ##   avgerage_ride shortest_ride longest_ride
    ##   <drtn>        <drtn>        <drtn>      
    ## 1 52.41 mins    20.78 mins    83 mins

### Ride Duration by Pickup Point

``` r
uber_data %>% filter(Status == "Trip Completed") %>% group_by(`Pickup point`) %>% summarize(avgerage_ride = round(mean(ride_duration),2), shortest_ride = round(min(ride_duration),2), longest_ride = round(max(ride_duration),2))
```

    ## # A tibble: 2 × 4
    ##   `Pickup point` avgerage_ride shortest_ride longest_ride
    ##   <chr>          <drtn>        <drtn>        <drtn>      
    ## 1 Airport        52.24 mins    21.18 mins    82.07 mins  
    ## 2 City           52.57 mins    20.78 mins    83.00 mins

### Rides by weekday

``` r
uber_data %>% group_by(weekday) %>% summarize(wd_perc = round(100 * n()/ nrow(uber_data), 2)) 
```

    ## # A tibble: 5 × 2
    ##   weekday   wd_perc
    ##   <ord>       <dbl>
    ## 1 Monday       20.3
    ## 2 Tuesday      19.4
    ## 3 Wednesday    19.8
    ## 4 Thursday     20.1
    ## 5 Friday       20.5

### Ride Duration by weekday

``` r
uber_data %>% filter(Status == "Trip Completed") %>% group_by(weekday) %>% summarize(average_ride = round(mean(ride_duration), 2), shortest_ride = round(min(ride_duration), 2), longest_ride = round(max(ride_duration), 2))
```

    ## # A tibble: 5 × 4
    ##   weekday   average_ride shortest_ride longest_ride
    ##   <ord>     <drtn>       <drtn>        <drtn>      
    ## 1 Monday    52.37 mins   22.00 mins    81.00 mins  
    ## 2 Tuesday   52.59 mins   21.00 mins    83.00 mins  
    ## 3 Wednesday 52.11 mins   21.18 mins    81.18 mins  
    ## 4 Thursday  52.36 mins   20.78 mins    82.90 mins  
    ## 5 Friday    52.65 mins   24.30 mins    82.07 mins

### Rides by the time of day

``` r
uber_data %>% group_by(time) %>% summarize( rides_per_day = round(100* n()/ nrow(uber_data), 2))
```

    ## # A tibble: 4 × 2
    ##   time      rides_per_day
    ##   <ord>             <dbl>
    ## 1 Morning            37.3
    ## 2 Afternoon          18.2
    ## 3 Evening            28.5
    ## 4 Night              16.0

### Status by time of day

``` r
uber_data %>% group_by(time, Status) %>% summarize(ds_perc = round(100* n() / nrow(uber_data), 2)) %>% spread(Status, ds_perc)
```

    ## `summarise()` has grouped output by 'time'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 4 × 4
    ## # Groups:   time [4]
    ##   time      Cancelled `No Cars Available` `Trip Completed`
    ##   <ord>         <dbl>               <dbl>            <dbl>
    ## 1 Morning       13.6                 7.59            16.1 
    ## 2 Afternoon      1.87                6.82             9.52
    ## 3 Evening        1.94               17.2              9.38
    ## 4 Night          1.29                7.68             6.98

### Status by work hours

``` r
uber_data %>% group_by(work, Status) %>% summarize(ws_perc = round(100* n() / nrow(uber_data), 2)) %>% spread(Status, ws_perc)
```

    ## `summarise()` has grouped output by 'work'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 2 × 4
    ## # Groups:   work [2]
    ##   work               Cancelled `No Cars Available` `Trip Completed`
    ##   <chr>                  <dbl>               <dbl>            <dbl>
    ## 1 off hours              10.5                 28.3             24.2
    ## 2 typical work hours      8.24                11.0             17.8

### Cancellations by driver

``` r
#amount of drivers who have cancelled requests

drivers_who_cancelled <- uber_data %>% filter(Status == "Cancelled") %>% summarize(drivers_who_cancelled = n_distinct(`Driver id`))

# total drivers 

total_drivers <- uber_data %>% summarize(total_drivers = n_distinct(`Driver id`))

# percentage of drivers who have cancelled rides
round(drivers_who_cancelled/total_drivers *100, 2)
```

    ##   drivers_who_cancelled
    ## 1                 98.01

``` r
# 98.01

# top 3 amount of cancellations
uber_data %>% filter(Status == "Cancelled") %>% group_by(`Driver id`) %>% summarize(cancelations = n())  %>% top_n(3, cancelations) %>% arrange(desc(cancelations)) 
```

    ## # A tibble: 4 × 2
    ##   `Driver id` cancelations
    ##         <dbl>        <int>
    ## 1          84           12
    ## 2          54           11
    ## 3         142           10
    ## 4         206           10

``` r
# top 3 amount of cancellations- without driver id
uber_data %>% filter(Status == "Cancelled") %>% group_by(`Driver id`) %>% summarize(cancelations = n())  %>% top_n(3, cancelations) %>% arrange(desc(cancelations)) %>% select(cancelations)
```

    ## # A tibble: 4 × 1
    ##   cancelations
    ##          <int>
    ## 1           12
    ## 2           11
    ## 3           10
    ## 4           10

``` r
# cancellations in ascending order
uber_data %>% filter(Status == "Cancelled") %>% group_by(`Driver id`) %>% count()
```

    ## # A tibble: 295 × 2
    ## # Groups:   Driver id [295]
    ##    `Driver id`     n
    ##          <dbl> <int>
    ##  1           1     4
    ##  2           2     4
    ##  3           3     4
    ##  4           4     5
    ##  5           5     2
    ##  6           6     4
    ##  7           7     3
    ##  8           8     3
    ##  9           9     6
    ## 10          10     3
    ## # … with 285 more rows

## Initial Visualizations

### Status

``` r
uber_data %>% ggplot() + geom_bar(aes(x= Status))
```

![](Code--Uber-Ride-Completion_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

### Pickup Point

``` r
uber_data %>% ggplot() + geom_bar(aes(x= `Pickup point`))
```

![](Code--Uber-Ride-Completion_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

### Status by Pickup Point

``` r
uber_data %>% ggplot() + geom_bar(aes(x= Status, fill = `Pickup point`))
```

![](Code--Uber-Ride-Completion_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

### Rides by weekday

``` r
uber_data %>% ggplot() + geom_bar(aes(x= weekday))
```

![](Code--Uber-Ride-Completion_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

### Average Ride Length by Pickup Point

``` r
uber_data %>% filter(Status == "Trip Completed") %>% group_by(`Pickup point`) %>% summarize(average_length = mean(ride_duration)) %>% ggplot(aes(x= `Pickup point`, y= average_length)) + geom_col()
```

    ## Don't know how to automatically pick scale for object of type <difftime>.
    ## Defaulting to continuous.

![](Code--Uber-Ride-Completion_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

## Exporting Data

Data will be visualized in Tableau.

### Assigning Variables

``` r
status_percentage <- uber_data %>% group_by(Status) %>% summarize(stat_perc = round(100 * n()/ nrow(uber_data), 2))

pickup_points_perc <- uber_data %>% group_by(`Pickup point`) %>% summarize(pickup_perc = round(100 * n()/ nrow(uber_data), 2))

status_by_pickup_point <- uber_data %>% group_by(`Pickup point`, Status) %>% summarize(pickup_status = round(100 * n()/ nrow(uber_data), 2)) 
```

    ## `summarise()` has grouped output by 'Pickup point'. You can override using the
    ## `.groups` argument.

``` r
status_by_weekday <-uber_data %>% group_by(weekday) %>% summarize(wd_perc = round(100 * n()/ nrow(uber_data), 2)) 

duration_summary <- uber_data %>% filter(Status == "Trip Completed") %>% summarize(avgerage_ride = round(mean(ride_duration), 2), shortest_ride = round(min(ride_duration), 2), longest_ride = round(max(ride_duration), 2))

duration_by_pickup_point <- uber_data %>% filter(Status == "Trip Completed") %>% group_by(`Pickup point`) %>% summarize(avgerage_ride = round(mean(ride_duration), 2), shortest_ride = round(min(ride_duration), 2), longest_ride = round(max(ride_duration), 2))

duration_by_weekday <- uber_data %>% filter(Status == "Trip Completed") %>% group_by(weekday) %>% summarize(average_ride = mean(ride_duration), shortest_ride = min(ride_duration), longest_ride = max(ride_duration))

status_by_timeOf_day <- uber_data %>% group_by(time, Status) %>% summarize(ds_perc = round(100* n() / nrow(uber_data), 2))
```

    ## `summarise()` has grouped output by 'time'. You can override using the
    ## `.groups` argument.

``` r
status_by_work_hours <- uber_data %>% group_by(work, Status) %>% summarize(ws_perc = round(100* n() / nrow(uber_data), 2))
```

    ## `summarise()` has grouped output by 'work'. You can override using the
    ## `.groups` argument.

### Exporting the Data

``` r
write.csv(status_percentage, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/status_percentage.csv")

write.csv( pickup_points_perc, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/pickup_points_perc.csv")

write.csv(status_by_pickup_point,  "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/status_by_pickup_point.csv")

write.csv(status_by_weekday, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/status_by_weekday.csv")

write.csv(duration_summary, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/duration_summary.csv")

write.csv(duration_by_pickup_point, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/duration_by_pickup_point.csv")

write.csv(duration_by_weekday, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/duration_by_weekday.csv")

write.csv(status_by_timeOf_day, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/status_by_timeOf_day.csv")

write.csv(status_by_work_hours, "~/Desktop/Projects- PUBLIC/Uber_Ride_Completion/status_by_work_hours.csv")
```

## Links to Tableau and Presentation

Tableau
[link](https://public.tableau.com/app/profile/vanessa4875/viz/UberRequestData_16655330440490/StatusandPickupLocation#1)

Presentation on [Google
Slides](https://docs.google.com/presentation/d/1yHXhxyyKh1Vcir4K0L_VVsf27Od4JmK0AM_05leICsk/edit?usp=sharing)
