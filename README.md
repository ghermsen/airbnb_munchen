[![author](https://img.shields.io/badge/author-ghermsen-red.svg)](https://www.linkedin.com/in/gabrielhermsen/)
[![](https://img.shields.io/badge/python-3.9-blue.svg)](https://docs.python.org/3.9/)
[![GPLv3 license](https://img.shields.io/badge/License-GPLv3-blue.svg)](http://perso.crans.org/besson/LICENSE.html)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/ghermsen/airbnb_munchen/issues)


# Data Analysis from Airbnb - Munich

The IPO (Initial Public Offering, that is, the process in which a private company goes public by selling its shares to the general public) of [Airbnb](https://www.airbnb.com) took place on December 9, 2020, and its shares started trading on December 10, 2020. On that day, the company's shares were worth USD144.71, an incredible 112.00% more than the USD68.00 value set by the company in its IPO, making the company reach the mark of USD100 Billion in market value, surpassing giants from hotel industries like Marriott International, Hilton, and Hyatt.

The Startup, founded in 2008, offers an attractive and easy-to-use platform for its users, connecting travelers or people who want to stay in a property and hosts (people who prefer to rent their property for a short time). Airbnb is present in more than 80,000 cities worldwide and has hosted more than 300 million people, and it is considered by many to be the largest hotel company today, and the main detail is: Airbnb does not have any hotels!

One of Airbnb's initiatives is to provide website data for some of the world's major cities. Through the [Inside Airbnb](http://insideairbnb.com/get-the-data.html), it is possible to download a large amount of data to develop Data Science projects and solutions.

**In this project, we will analyze the data for the city of Munich and see which insights can be extracted from raw data.**


<br><center><img alt="Airbnb München" width="100%" src="https://www.sueddeutsche.de/image/sz.1.4251859/1840x772?v=1544705249&format=webp"></center>
<div align="center"><em>Image Source: <a href:"https://www.sueddeutsche.de/muenchen/airbnb-kommentar-1.4251629"> Süddeutsche Zeitung</a></em></div>

---

## Imports

Below there are all the libraries used in this project.

* `Functools`
* `GeoPandas`
* `GeoPy`
* `Matplotlib`
* `Pandas`
* `Seaborn`


## Obtaining the Data

All data used in this project was obtained from the website [Inside Airbnb](http://insideairbnb.com/get-the-data.html).

For this data analysis, the following files will be downloaded:

* Exploratory Data Analysis
    * City - Munich, Bavaria, Germany
    * Data Compiled - 24 December, 2020
    * File - `listings.csv` - *Summary information and metrics for listings in Munich (good for visualisations)*.
    
    
* Map visualization
    * City - Munich, Bavaria, Germany
    * Data Compiled - N/A
    * File - `neighbourhoods.geojson` - *GeoJSON file of neighbourhoods of the city.*.
    

### Understanding the Dataset

Understanding the data is a fundamental step in any data science project. It will show us the variables available in our dataset and how these variables are structured. This step helps us understand the type of problem and how we will solve it.

**Variables' Dictionary**:

* `id` - property id number
* `name` - property name announced
* `host_id` - host id number
* `host_name` - host name
* `neighbourhood_group` - there are no values and will be deleted
* `neighbourhood` - neighbourhood name
* `latitude` - property latitude
* `longitude` - property longitude
* `room_type` - property type announced
* `price` - daily rate
* `minimum_nights` - minimum number of nights to book
* `number_of_reviews` - total number of reviews for the property
* `last_review` - date of the last review
* `reviews_per_month` - number of reviews per month
* `calculated_host_listings_count` - number of properties from the same host
* `availability_365` - number of days available in the calendar year
    * **in München there is a rule where entire properties can be rented for only 8 weeks during the calendar year, however, this restriction is not valid for rooms**.


### Dataset Size and Types of Variables

Below, it is possible to see the size of our dataset and the type of its variables.

```
# Dataset Size

    Number of Entries:	 4815
    Number of Features:	 16

# Type of Variables

    id                                  int64
    name                               object
    host_id                             int64
    host_name                          object
    neighbourhood_group               float64
    neighbourhood                      object
    latitude                          float64
    longitude                         float64
    room_type                          object
    price                               int64
    minimum_nights                      int64
    number_of_reviews                   int64
    last_review                        object
    reviews_per_month                 float64
    calculated_host_listings_count      int64
    availability_365                    int64
    dtype: object
```


### Cleaning the Dataset

Looking at the Variables Dictionary and also the types of variables present in the dataset, it is possible to see the presence of variables that will not be used in our analysis, such as:

- `id`
- `host_id`
- `host_name`
- `neighbourhood_group`
- `number_of_reviews`
- `last_review`
- `reviews_per_month`
- `calculated_host_listings_count`

For a cleaner and more objective analysis, these variables will be deleted.

Below, there is a sample of our dataset after cleaning.

![Dataset sample](img/data_sample.png)


### Missing Values

The quality of a dataset is directly related to the number of missing values. It is important to understand early on whether these null values are significant concerning the total number of entries to avoid future problems in our analysis.

* The variable `name` has approximately 0.02% of the null values, which is considered as a non-significant amount in the case that these variables are to be used for analysis.

As the variables that will not be used previously have been cleaned up, this dataset has practically no null values, therefore no null values treatment will be performed.

```
# Checking Missing Values

    name                0.000208
    neighbourhood       0.000000
    latitude            0.000000
    longitude           0.000000
    room_type           0.000000
    price               0.000000
    minimum_nights      0.000000
    availability_365    0.000000
    dtype: float64
```


### Outliers Detection and Treatment

Outliers are data that are very far from your dataset, that is, data out of "normal". They are problematic due to distortions in the actual values of the dataset caused by them, resulting in the loss of information.

For this, a histogram will be plotted to check for the presence of outliers.
    
![Histogram of variables](img/histogram.png)

Through the histogram, it is possible to verify the presence of outliers in the variables `price` and `minimum_nights`.

The values do not follow a distribution and distort the entire graphical representation. To confirm it, there are two quick ways to help detect outliers:

* Summary statistics using the `describe()` method;
* Plot `boxplots` for the variables.

![Summary Statistics 1](img/summary_statistics.png)

Looking at the statistical summary above, it is possible to obtain valuable information for the analysis and about the presence of outliers.

* The variable `prices` has 75% of its values below 120, but its maximum value is 8255, which is well above the values obtained up to 75% of the dataset, which proves the presence of outliers.
* The variable `prices` presents minimum values equal to 0. Understanding the Airbnb business, it is known that no one rents any property on Airbnb for free.
* The variable `minimum_nights` has 75% of its values below 3, but its maximum value is 800, which is well above the values obtained up to 75% of the dataset, which proves the presence of outliers.


#### Boxplot for "prices"

Below, the boxplot for the variable `price` visually shows the information contained in the summary statistics.

As a parameter, all data greater than 1000 will be considered an outlier in this analysis. Below, you will see the quantity and ratio of these outliers and the values where `price` are equal to 0.

![Boxplot Prices](img/box_plot_prices.png)
    

#### Boxplot for "minimum_nights"

Below, the boxplot for the variable `minimum_nights` visually shows the information contained in the summary statistics.

As a parameter, all data greater than 30 will be considered an outlier in this analysis. Below, you will see the quantity and ratio of these outliers.
    
![Boxplot Minimum Nights](img/boxplot_minimum_nights.png)
    

#### Removing Outliers and Creating Data Frame for Analysis

Now with the outliers identified and treated, we shall plot new boxplots analyze them.
    
![Boxplot Price and Minimum Nights](img/boxplot_price_and_minimum_nights.png)
    
Finally, with the clean data frame created and treated, the analysis begins.

![Summary Statistics 2](img/summary_statistics_2.png)


## Data Analysis

This project aims to answer the following questions:

**Questions**

1. What is the proportion of the property types in Munich on Airbnb? And what is the proportion by neighbourhood?
2. What is the average property price in the different neighbourhoods of Munich?
3. How are property prices distributed in the 2 most expensive neighbourhoods?
4. How are property prices distributed in the 2 cheapest neighbourhoods?
5. Can tourist attractions represent high prices when choosing properties in Munich on Airbnb?

### Q1 - What is the proportion of the property types in Munich on Airbnb? And what is the proportion by neighbourhood?

It can be seen that the proportion of property types *'Entire home/apt'* and *'Private room'* in Munich is close, with **52.95%** and **44.09%** respectively.

```
# Number of Property Types

    Entire home/apt    2462
    Private room       2050
    Shared room          80
    Hotel room           58
    Name: room_type, dtype: int64


# Ratio of Property Types

    Entire home/apt    52.95%
    Private room       44.09%
    Shared room         1.72%
    Hotel room          1.25%
    Name: room_type, dtype: object
```


As the property types *'Shared room'* and *'Hotel room'* are not relevant to the number of properties being rented on Airbnb in the city of Munich, I will continue this analysis in the neighbourhoods using only the property types *'Entire home/apt'* and *'Private room'*.

![Data frame neighbourhoods / property tipe](img/df_question1_1.png)

It can be seen that the distribution of property types in Munich is well balanced in most neighbourhoods.

Below you can see the neighbourhoods where the proportion of property type *'Entire home/apt'* is higher, so if the Airbnb user wants to stay in one of the neighbourhoods listed below, there is a greater chance that this user will find offers of this type property available.

![Data frame more Entire Home/Apt](img/df_question1_2.png)

Now, you can see the neighbourhoods where the proportion of property type *'Private room'* is higher, so if the Airbnb user wants to stay in one of the neighbourhoods listed below, there is a greater chance that this user will find offers for this type of property available.

![Data frame more Private Room](img/df_question1_3.png)


### Q2 -  What is the average property price in the different neighbourhoods of Munich?


It has already been seen in the summary statistics that the average property price in Munich is **€ 105.23**. Below is the average property price per neighbourhood in Munich. We can see that there is a big price difference, where the average price in the **most expensive neighbourhood is € 149.36 and in the cheapest neighbourhood is 74.94**.

![Data frame Average Price](img/df_question2_1.png)

To continue this analysis, I will extract the 2 most expensive and cheapest neighbourhoods in Munich concerning the average price of all properties.

![Data frame Higher and Lower Average Price](img/df_question2_2.png)


### Q3 - How are property prices distributed in the 2 most expensive neighbourhoods?

When analyzing the price per property type in the two most expensive neighbourhoods of Munich concerning the average price, we shall observe that:

**Ludwigsvorstadt-Isarvorstadt**

1. ***'Entire room/apt'***
    * The average price is far from the median (159.98 x 110.00 respectively), which indicates the presence of outliers.
    * It is possible to rent a property of this type in this neighbourhood for **€ 30.00**.
    
    
2. ***'Private room'***
    * The average price is far from the median (131.86 x 76.50 respectively), which indicates the presence of outliers.
    * It is possible to rent a property of this type in this neighbourhood for **€ 25.00**.

---
    
**Altstadt-Lehel**

1. ***'Entire room/apt'***
    * The average price is far from the median (173.19 x 145.00 respectively), which indicates the presence of outliers.
    * It is possible to rent a property of this type in this neighbourhood for **€ 10.00**.
    
    
2. ***'Private room'***
    * The average price is far from the median (104.91 x 80.00 respectively), which indicates the presence of outliers.
    * It is possible to rent a property of this type in this neighbourhood for **€ 35.00**.

![Data frame Expensive Neighbourhoods](img/df_question3.png)

In order to have a better view of prices and confirmation of the presence of outliers, we will plot the boxplot again for properties in these neighbourhoods.
    
![Boxplot Expensive Neighbourhoods](img/boxplot_question3.png)

Through the boxplot chart, the presence of outliers in the price of different property types in these neighbourhoods is clear. For this reason, it is better to consider the median price, as the median is not as sensitive to outlier values as the average.

With that, we can conclude that:

* The price of both property types is **higher in Altstadt-Lehel** than in Ludwigsvorstadt-Isarvorstadt when we start to consider the median as the base price.

* Although Altstadt-Lehel is the most expensive location based on the median price for the *'Entire home/apt'* property type, it is possible to find this type of property for the lowest minimum price, **€ 10.00**.

* It is possible to find *'Private room'* properties at minimum price lower **in Ludwigsvorstadt-Isarvorstadt** than in Altstadt-Lehel.


### Q4 - How are property prices distributed in the 2 cheapest neighbourhoods?

When analyzing the price per property type in the two most expensive neighbourhoods of Munich concerning the average price, we shall observe that:

**Moosach**

1. ***'Entire room/apt'***

    * The average price is far from the median (88.69 x 62.50 respectively), which indicates the presence of outliers.
    
    * It is possible to rent a property of this type in this neighbourhood for **€ 33.00**.


2. ***'Private room'***
    * The average price is far from the median (60.19 x 59.50 respectively), which indicates the presence of outliers.
    
    * It is possible to rent a property of this type in this neighbourhood for **€ 20.00**.


---
    
**Milbertshofen-Am Hart**

1. ***'Entire room/apt'***
    * The average price is far from the median (93.89 x 69.00 respectively), which indicates the presence of outliers.
    
    * It is possible to rent a property of this type in this neighbourhood for **€ 16.00**.
    
    
2. ***'Private room'***
    * The average price is far from the median (61.19 x 50.00 respectively), which indicates the presence of outliers.
    
    * It is possible to rent a property of this type in this neighbourhood for **€ 23.00**.

![Data frame Cheap Neighbourhoods](img/df_question4.png)

In order to have a better view of prices and confirmation of the presence of outliers, we will plot the boxplot again for properties in these neighbourhoods.

![Boxplot Cheap Neighbourhoods](img/boxplot_question4.png)

Through the boxplot chart, the presence of outliers in the price of different property types in these neighbourhoods is clear. For this reason, it is better to consider the median price, as the median is not as sensitive to outlier values as the average.

With that, we can conclude that:

* The price for *'Entire home/apt'* is **lower in Moosach** than in Milbertshofen-Am Hart.

* Although Moosach is the cheapest location based on the median price for the *'Entire home/apt'* property type, it is possible to find this type of property for the lowest minimum price of **€ 16.00 in Milbertshofen-Am Hart**.

* The price of  property type *'Private room'* is **lower in Milbertshofen-Am Hart** than in Moosach.

* It is possible to find *'Private room'* properties at a minimum price lower **in Moosach** than in Milbertshofen-Am Hart.


### Q5 - Can tourist spots represent high prices when choosing properties in Munich on Airbnb?

We know that in most urban centers, the central regions usually have the largest number of sights, places for nightlife, restaurants, offices, and more. Consequently, this increases the demand for people looking for apartments and rooms in these areas, making prices in these areas commonly higher than in other regions.

A final analysis will be conducted to identify tourist spots in Munich, and then confirm whether the location of these tourist spots is closer to the most expensive or cheapest neighbourhoods in the city.

For this, the [Inside Airbnb](http://insideairbnb.com/get-the-data.html) website has another dataset on which we can make this analysis.

This data frame has already been generated in the **'Obtaining the Data'* part of this notebook.

```
# expensive and cheap neighbourhoods

    array(['Altstadt-Lehel', 'Ludwigsvorstadt-Isarvorstadt', 'None',
           'Moosach', 'Milbertshofen-Am Hart'], dtype=object)
```


With the `Nominatim`, we can create a locator using the name of these tourist spots and get their latitudes and longitudes. These tourist spots were selected on the [official website of Munich](https://www.muenchen.de/sehenswuerdigkeiten/top-20-sehenswuerdigkeiten.html). 

After generating a dictionary of tourist spots in Munich, we can start plotting our map of Munich, with the chosen neighbourhoods and tourist spots.

```
# Dictionary of Tourist Spots

    {'Frauenkirche München': (11.573592008043118, 48.1385821),
     'Schloss Nymphenburg': (11.5032967, 48.1582569),
     'Marienplatz': (11.575924590567384, 48.137031750000006),
     'Alter Peter': (11.576030455223243, 48.13644495),
     'Allianz Arena': (11.624664151799873, 48.21880845),
     'Karlsplatz (Stachus)': (11.565622, 48.1394857),
     'Odeonsplatz': (11.5763171, 48.1427658),
     'Residenz': (11.57904463904056, 48.1411608),
     'Deutsches Museum': (11.582908990594504, 48.1300409),
     'Olympiapark': (11.55032660696114, 48.16875265),
     'BMW Welt': (11.556375779693298, 48.1772189),
     'Alte Pinakothek': (11.569970907138872, 48.1482861),
     'Neue Pinakothek': (11.570937445883057, 48.1499278),
     'Pinakothek der Moderne': (11.572227101401982, 48.14716025),
     'Tierpark Hellabrunn': (11.554196865190177, 48.096281149999996),
     'Bayerische Staatsoper': (11.579513288504806, 48.1396162),
     'Hofbräuhaus': (11.57998140312025, 48.13757095),
     'Alter Hof': (11.5780147, 48.1382036),
     'Viktualienmarkt': (11.576255684942478, 48.135575200000005)}
```

On the map, we can confirm that most of the tourist spots in Munich are located in the most expensive neighbourhoods of the city.

![Munich Map](img/map_question5.png)   


## Conclusion

Even working with a non-complete dataset, it was possible to see many outliers in the price variable, which was the fundamental variable used for this analysis. Treatment of outliers was carried out at the beginning of the project, using the entire city as a parameter. However, when the analysis of the most expensive and cheapest neighbourhoods concerning the average price began, it was possible to observe the existence of outliers in neighbourhood prices, which means that the analysis with the average price can be a non-viable alternative for a better representation of reality.

The analysis of the Airbnb in Munich showed us that the property types 'Entire room/apt' and 'Private room' are well balanced, probably it is a result of the policies applied regarding the restriction of the period of days for renting entire properties in the city.

For future iterations in this project, I will use the complete dataset available on [Inside Airbnb](http://insideairbnb.com/get-the-data.html) website. This dataset offers a possibility of analyzing important variables that can explain the price, such as square meter, amenities among others.

Questions, suggestions for improvements, and corrections are welcome, and thank you for analyzing the data with me so far!