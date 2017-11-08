---
layout: post
categories: articles
title: Electric vehicles in Delaware
date: 2017-11-08
description: Exploring electric vehicle purchases in Delaware using pandas and folium
tags: data-science
---

<p><a href="http://www.exoticspotter.com/tesla-model-s-newark-delaware-130588">
<img src="\img\delaware-EVs\de_tesla.jpg" style="display:block; margin-left: auto; margin-right: auto;">
</a></p>

As I was looking for data to use for practicing data analysis,
I came across a dataset combining two things I love: Delaware and electric vehicles.
This dataset, [State Rebates for Alternative-Fuel Vehicles](https://data.delaware.gov/Energy-and-Environment/State-Rebates-for-Alternative-Fuel-Vehicles/8z8z-di7f),
contains information on successful applicants for rebates issued by the state under the
[Delaware Clean Transportation Incentive Program](http://www.dnrec.delaware.gov/energy/pages/clean-transportation-incentives-home.aspx),
a recent program designed to encourage the purchase of "alternative fuel vehicles".
The dataset is maintained by the [Delaware Open Data Portal](https://data.delaware.gov).

In this post, I'll discuss the highlights from my analysis.
My primary interest is understanding trends in electric vehicle adaptation in Delaware.
I have a special interest in Teslas, so I often compare broader trends in
alternative fuel vehicle (AFV) purchases to the Tesla subset.

### Population statistics

![Age histogram](\img\delaware-EVs\age.png)

![Age of Tesla purchasers](\img\delaware-EVs\age_tesla.png)

![Vehicle types](\img\delaware-EVs\vehicle_types.png)

![Makes](\img\delaware-EVs\make.png)

![Electric vehicle models](\img\delaware-EVs\electric_models.png)

### Spatial distribution of alternative fuel vehicles

![Counties](\img\delaware-EVs\counties.png)

![AFV normalized map](\img\delaware-EVs\afv_norm_map.png)

![Tesla map](\img\delaware-EVs\tesla_map.png)

### Change in rebate policy

![Monthly sales by type after change in policy](\img\delaware-EVs\policy_change_type.png)

![Monthly sales by make after change in policy](\img\delaware-EVs\policy_change_make.png)

![Purchasing trends after change in policy](\img\delaware-EVs\policy_change_trends.png)

### More information

The Jupyter notebooks for [data cleaning](http://nbviewer.jupyter.org/github/petermattia/Delaware-EVs/blob/master/Electric%20vehicles%20in%20Delaware%20-%20Data%20cleaning.ipynb?flush_cache=true)
and [data analysis](http://nbviewer.jupyter.org/github/petermattia/Delaware-EVs/blob/master/Electric%20vehicles%20in%20Delaware%20-%20Data%20analysis.ipynb?flush_cache=true), along with all files,
are in [this GitHub repository](https://github.com/petermattia/Delaware-EVs).
I used the
[numpy](http://numpy.org) (numerics),
[pandas](https://pandas.pydata.org) (data frames),
[folium](https://folium.readthedocs.io/en/latest/) (mapping),
[matplotlib](https://matplotlib.org) (plotting), and
[seaborn](https://seaborn.pydata.org) (plot formatting)
libraries extensively in this analysis.