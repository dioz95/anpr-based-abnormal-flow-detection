# ANPR-Based Abnormal Flow Detection System
**This repo is made to show case my final research project in Msc Smart Systems Engineering program at Newcastle Univeristy**

## Research Abstract
Automatic Number-Plate Recognition (ANPR) is a commonly used technology that aims to capture the images of vehicle plate numbers. Along with the development of information technology, the raw ANPR data now can be utilised to answer the challenges for detecting abnormal traffic flows in urban transportation system. This research is conducted by running a data analysis framework, harnessing the advantage of EP-MEANS clustering algorithm to extract the typical traffic flows for a particular road segment across the weekdays in the year of 2018. Then, the median flow of the typical traffics is extracted and used as the baseline parameter for median-based abnormal traffic flow detection algorithm. The performance of EP-MEANS clustering is evaluated by comparing the quadratic fitting result of the average speed against the traffic flow between typical and non-clustered data according to the Greenshields speed-flow fundamental diagram. The performance of median-based abnormal flow detection algorithm in detecting abnormal flows using both non-clustered and typical traffic flow is also compared. The result shows that the inclusion of EP-MEANS clustering in the research framework is advantageous in clearing the speed-flow relation on the data and also able to detail the detection result of median-based abnormal flow detection algorithm.

## The Core of The Code
The main code of this project is on the `statistical_data_analysis.Rmd` file. You can open it on the RStudio and don't forget to install the required package and dependencies.

As described on the abstract section, the detection system is built on the EP-MEANS clustering as it's core algorithm. Chiefly, you can imagine EP-MEANS as an algorithm that works like K-MEANS on the time series instead of point data. EP-MEANS utilize Earth Mover Distance (EMD) to separate time series into several clusters. However, in fact EP-MEANS doesn't directly work separate the time series. It need the Empirical Cumulative Distribution Function (ECDF) of the flow time series to be feed into the algorithm as an input.

The following code is used to plot the flow time series along the 2-3 corridor from the `corridor_A184_WEST_3cameras.csv` file,
``` r
p_daily_flow <-
  flows_23_weekday %>%
  ggplot() +
  geom_line(
    aes(x = hms::as_hms(t), y = flow, group = as_date(t)),
    alpha = .5
  ) + 
  scale_x_time(
    name = "Time",
    breaks = hms::hms(hours = seq(2, 22, 4)),
    labels = scales::label_time("%Hh")
  ) + 
  theme_bw()
p_daily_flow
```
<p align="center"><img src="https://github.com/dioz95/anpr-based-abnormal-flow-detection/blob/main/flows.png" width=700/></p>

The main task is we need to cluster the flow time series into typical and atypical group in order to find the daily flow threshold. This is where the EP-MEANS clustering algorithm shows it's magic. But before that, we need to convert the flow time series into the ECDF value,
``` r
p_daily_ecdf <-
  flow_with_expected %>%
  ggplot() +
  stat_ecdf(
    aes(x = flow, group = as_date(t)),
    alpha = .7
  ) +
  xlab("Vehicle count per time period (15min)") +
  ylab("Cumulative probability") +
  theme_bw()
p_daily_ecdf
```
<p align="center"><img src="https://github.com/dioz95/anpr-based-abnormal-flow-detection/blob/main/daily-ecdf.png" width=700/></p>

The code to execute the EP-MEANS is quite simple. The algorithm is implemented in R by using the maotai packacge (https://cran.r-project.org/web/packages/maotai/index.html),
``` r
epout_k2 <- flows_ecd23 %>%
  group_map(~ { maotai::epmeans(.x$ecd, k = 2) })
epout_k2
```
After a bunch of processes and data wrangling, then we could see the insight that EP-MEANS brings to us,
<p align="center"><img src="https://github.com/dioz95/anpr-based-abnormal-flow-detection/blob/main/atypical-vs-typical.png" width=700/></p>

In a more vivid way, we could see the differences between the median of the typical and the typical flows,
<p align="center"><img src="https://github.com/dioz95/anpr-based-abnormal-flow-detection/blob/main/typical-atypical-median.png" width=700/></p>
The dotted `....` line is the typical, the dashed `- - - -` line is the atypical, and the solid `_____` line is the combined median flows before clustered by the EP-MEANS algorithm
