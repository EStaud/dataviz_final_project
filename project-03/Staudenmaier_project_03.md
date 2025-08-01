---
title: "Data Visualization for Exploratory Data Analysis"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---

# Data Visualization Project 03


In this exercise you will explore methods to create different types of data visualizations (such as plotting text data, or exploring the distributions of continuous variables).


## PART 1: Density Plots

Using the dataset obtained from FSU's [Florida Climate Center](https://climatecenter.fsu.edu/climate-data-access-tools/downloadable-data), for a station at Tampa International Airport (TPA) for 2022, attempt to recreate the charts shown below which were generated using data from 2016. You can read the 2022 dataset using the code below: 


``` r
library(tidyverse)
weather_tpa <- read_csv("https://raw.githubusercontent.com/aalhamadani/datasets/master/tpa_weather_2022.csv")
# random sample 
sample_n(weather_tpa, 4)
```

```
## # A tibble: 4 × 7
##    year month   day precipitation max_temp min_temp ave_temp
##   <dbl> <dbl> <dbl>         <dbl>    <dbl>    <dbl>    <dbl>
## 1  2022     4     8             0       76       66     71  
## 2  2022    11    15             0       81       68     74.5
## 3  2022     9    24             0       92       76     84  
## 4  2022     3    29             0       86       64     75
```

See Slides from Week 4 of Visualizing Relationships and Models (slide 10) for a reminder on how to use this type of dataset with the `lubridate` package for dates and times (example included in the slides uses data from 2016).

Using the 2022 data: 

(a) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_facet.png" width="80%" style="display: block; margin: auto;" />


``` r
library(tidyverse)
library(lubridate)

weather <- weather_tpa %>%
  mutate(date = make_date(year, 
                     month, 
                     day),
         month_name = factor(month(date, 
                              label = TRUE, 
                              abbr = FALSE), 
                        levels = month.name))

ggplot(data = weather, 
       mapping = aes(x = max_temp, 
                     fill = month_name)) +
  geom_histogram(binwidth = 3, 
                 color = "white", 
                 show.legend = FALSE) +
  facet_wrap(~ month_name, 
             ncol = 4) +
  scale_y_continuous(breaks = seq(0, 20, 5), limits = c(0, 20)) +
  scale_x_continuous(breaks = c(60, 70, 80, 90), limits = c(55, 95)) +
  labs(
    x = "Maximum temperatures",
    y = "Number of Days"
  ) +
  theme_bw() +
  theme(axis.title.x = element_text(size = 16),
        axis.title.y = element_text(size = 16),
        axis.text = element_text(size = 13.5),
        strip.text = element_text(size = 14, face = "plain", color = "black",margin = margin(6, 0, 6, 0)))
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-3-1.png" width="80%" style="display: block; margin: auto;" />
Hint: the option `binwidth = 3` was used with the `geom_histogram()` function.

(b) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_density.png" width="80%" style="display: block; margin: auto;" />


``` r
library(ggplot2)
ggplot(data = weather, 
       mapping = aes(x = max_temp)) +
  geom_density(fill = "gray47",
               size = .9,
               bw = 0.5, 
               kernel = "epanechnikov") +
  scale_y_continuous(breaks = seq(0, .08, .02), limits = c(0, .08)) +
  scale_x_continuous(breaks = c(60, 70, 80, 90), limits = c(56, 97)) +
  labs(x = "Maximum temperature", 
       y = "density") +
  theme_minimal() +
  theme(axis.title.x = element_text(size = 16),
        axis.title.y = element_text(size = 16),
        axis.text = element_text(size = 13.5),
        panel.grid.major = element_line(size = 1.1),
        panel.grid.minor = element_line(size = 0.6))
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-5-1.png" width="80%" style="display: block; margin: auto;" />
Hint: check the `kernel` parameter of the `geom_density()` function, and use `bw = 0.5`.

(c) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_density_facet.png" width="80%" style="display: block; margin: auto;" />


``` r
weather <- weather_tpa %>%
  mutate(date = make_date(year, 
                     month, 
                     day),
         month_name = factor(month(date, 
                              label = TRUE, 
                              abbr = FALSE), 
                        levels = month.name))

ggplot(data = weather, 
       mapping = aes(x = max_temp,
                     fill = factor(month_name))) +
  geom_density(alpha = 0.7,
               size = .9,
               show.legend = FALSE) +
  scale_fill_viridis_d() +
  scale_y_continuous(breaks = seq(0, .25, .05), limits = c(0, .25)) +
  scale_x_continuous(breaks = c(50, 60, 70, 80, 90), limits = c(56, 97)) +
  facet_wrap(~ month_name, 
             ncol = 4) +
  labs(title = "Density plots for each month in 2022",
       x = "Maximum temperatures",
       y = NULL) +
  theme_bw() +
  theme(plot.title = element_text(size = 20, face = "plain"),
        axis.title.x = element_text(size = 16),
        axis.title.y = element_text(size = 16),
        axis.text = element_text(size = 13.5),
        strip.text = element_text(size = 14, face = "plain", color = "black",margin = margin(6, 0, 6, 0)))
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-7-1.png" width="80%" style="display: block; margin: auto;" />
Hint: default options for `geom_density()` were used. 

(d) Generate a plot like the chart below:


<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_ridges_plasma.png" width="80%" style="display: block; margin: auto;" />


``` r
library(ggridges)
library(viridis)
library(dplyr)
library(lubridate)
library(ggplot2)

ggplot(data = weather, 
       mapping = aes(x = max_temp,
                     y = month_name,
                     fill = ..x..)) +
  geom_density_ridges_gradient(scale = 1.5,
                               rel_min_height = 0.01,
                               alpha = 0.8,
                               linewidth = 1,
                               quantile_lines = TRUE,
                               quantiles = c(0.5)) +
  scale_fill_viridis(option = "plasma", breaks = c(60, 70, 80, 90, 100)) +
  scale_x_continuous(breaks = seq(50, 100, 10), limits = c(50, 100)) +
  labs(x = "Maximum temperature (in Fahrenheit degrees)",
       y = NULL) +
  theme_minimal() +
  theme(axis.title.x = element_text(size = 16),
        axis.title.y = element_text(size = 16),
        axis.text = element_text(size = 13.5),
        panel.grid.major = element_line(size = 1.1),
        panel.grid.minor = element_line(size = 0.6),
        legend.title = element_blank())
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-9-1.png" width="80%" style="display: block; margin: auto;" />

Hint: use the`{ggridges}` package, and the `geom_density_ridges()` function paying close attention to the `quantile_lines` and `quantiles` parameters. The plot above uses the `plasma` option (color scale) for the _viridis_ palette.


(e) Create a plot of your choice that uses the attribute for precipitation _(values of -99.9 for temperature or -99.99 for precipitation represent missing data)_.


``` r
wearher <- weather %>%
  filter(precipitation != -99.99) %>%
  mutate(date = make_date(year,
                          month,
                          day))

ggplot(data = weather,
       mapping = aes(x = date, 
                     y = precipitation)) +
  geom_line(color = "blue", 
            size = .7) +  
  geom_smooth(method = "loess", 
              color = "red", 
              size = 1) +  
  labs(title = "Daily Precipitation in 2022", 
       x = "Date", 
       y = "Precipitation (inches)") +
  theme_minimal() +
  theme(plot.title = element_text(size = 20, face = "plain"),
        axis.title.x = element_text(size = 14),
        axis.title.y = element_text(size = 14),
        axis.text = element_text(size = 8),
        panel.grid.major = element_line(size = 1.1),
        panel.grid.minor = element_line(size = 0.6))
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-10-1.png" width="80%" style="display: block; margin: auto;" />

## PART 2 

### Option (A): Visualizing Text Data

Review the set of slides (and additional resources linked in it) for visualizing text data: Week 6 PowerPoint slides of Visualizing Text Data. 

Choose any dataset with text data, and create at least one visualization with it. For example, you can create a frequency count of most used bigrams, a sentiment analysis of the text data, a network visualization of terms commonly used together, and/or a visualization of a topic modeling approach to the problem of identifying words/documents associated to different topics in the text data you decide to use. 

Make sure to include a copy of the dataset in the `data/` folder, and reference your sources if different from the ones listed below:

- [Billboard Top 100 Lyrics](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/BB_top100_2015.csv)

- [RateMyProfessors comments](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/rmp_wit_comments.csv)

- [FL Poly News Articles](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/flpoly_news_SP23.csv)


(to get the "raw" data from any of the links listed above, simply click on the `raw` button of the GitHub page and copy the URL to be able to read it in your computer using the `read_csv()` function)


``` r
library(tidyverse)
library(tidytext)

lyric <- read_csv("https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/BB_top100_2015.csv")

words <- lyric %>%
  unnest_tokens(word, Lyrics) %>%
  anti_join(stop_words)  # Remove common stop words

bing_sentiment <- get_sentiments("bing")

sentiment_analysis <- words %>%
  inner_join(bing_sentiment, by = "word") %>%
  count(sentiment, sort = TRUE)

ggplot(data = sentiment_analysis, 
       mapping = aes(x = sentiment, 
                     y = n, 
                     fill = sentiment)) +
  geom_col(show.legend = FALSE) +
  labs(title = "Sentiment Analysis of Song Lyrics in 2015",
       x = "Sentiment",
       y = "Word Count") +
  theme_minimal() +
  theme(plot.title = element_text(size = 20, face = "plain"),
        axis.title.x = element_text(size = 14),
        axis.title.y = element_text(size = 14),
        axis.text = element_text(size = 8),
        panel.grid.major = element_line(size = 1.1),
        panel.grid.minor = element_line(size = 0.6))
```

<img src="Staudenmaier_project_03_files/figure-html/unnamed-chunk-11-1.png" width="80%" style="display: block; margin: auto;" />

