-   [ropenaq](#ropenaq)
-   [Installation](#installation)
-   [Introduction](#introduction)
-   [Finding measurements availability](#finding-measurements-availability)
    -   [The `aq_countries` function](#the-aq_countries-function)
    -   [The `aq_cities` function](#the-aq_cities-function)
    -   [The `locations` function](#the-locations-function)
-   [Getting measurements](#getting-measurements)
    -   [The `aq_measurements` function](#the-aq_measurements-function)
    -   [The `aq_latest` function](#the-aq_latest-function)
-   [Paging and limit](#paging-and-limit)
-   [Other packages of interest for getting air quality data](#other-packages-of-interest-for-getting-air-quality-data)
    -   [Meta](#meta)

ropenaq
=======

[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/ropenaq)](http://cran.r-project.org/package=ropenaq) [![Build Status](https://travis-ci.org/ropenscilabs/ropenaq.svg?branch=master)](https://travis-ci.org/ropenscilabs/ropenaq) [![Build status](https://ci.appveyor.com/api/projects/status/qurhlh0j8ra3qors?svg=true)](https://ci.appveyor.com/project/ropenscilabs/ropenaq) [![codecov.io](https://codecov.io/github/ropenscilabs/ropenaq/coverage.svg?branch=master)](https://codecov.io/github/ropenscilabs/Ropenaq?branch=master)

Installation
============

Install the package with:

``` r
install.packages("ropenaq")
```

Or install the development version using [devtools](https://github.com/hadley/devtools) with:

``` r
library("devtools")
install_github("ropenscilabs/ropenaq")
```

If you experience trouble using the package on a Linux machine, please run

``` r
url::curl_version()$ssl_version
```

If it answers `GnuTLS`, run

``` r
apt-get install libcurl4-openssl-dev
```

And desinstall then re-install `curl`.

``` r
install.packages("curl")
```

If it still doesn't work, please open a new issue!

Introduction
============

This R package is aimed at accessing the openaq API. OpenAQ is a community of scientists, software developers, and lovers of open environmental data who are building an open, real-time database that provides programmatic and historical access to air quality data. See their website at <https://openaq.org/> and see the API documentation at <https://docs.openaq.org/>. The package contains 5 functions that correspond to the 5 different types of query offered by the openaq API: cities, countries, latest, locations and measurements. The package uses the `dplyr` package: all output tables are data.frame (dplyr "tbl\_df") objects, that can be further processed and analysed.

Finding measurements availability
=================================

Three functions of the package allow to get lists of available information. Measurements are obtained from *locations* that are in *cities* that are in *countries*.

The `aq_countries` function
---------------------------

The `aq_countries` function allows to see for which countries information is available within the platform. It is the easiest function because it does not have any argument. The code for each country is its ISO 3166-1 alpha-2 code.

``` r
library("ropenaq")
countriesTable <- aq_countries()
library("knitr")
kable(countriesTable$results)
```

| name                   | code |  cities|  locations|    count|
|:-----------------------|:-----|-------:|----------:|--------:|
| Australia              | AU   |      11|         28|   780882|
| Bosnia and Herzegovina | BA   |       4|         11|   156253|
| Bangladesh             | BD   |       1|          2|     5525|
| Brazil                 | BR   |      73|        112|  1205286|
| Canada                 | CA   |      11|        157|   539109|
| Chile                  | CL   |      95|        104|  1773896|
| China                  | CN   |       5|          6|    48921|
| Colombia               | CO   |       1|          1|     4001|
| Ethiopia               | ET   |       1|          1|      298|
| United Kingdom         | GB   |     105|        152|  1548897|
| Indonesia              | ID   |       2|          3|    14761|
| Israel                 | IL   |       1|          1|     1826|
| India                  | IN   |      33|         78|  1153897|
| Mongolia               | MN   |       1|         12|   887959|
| Mexico                 | MX   |       5|         48|   537968|
| Nigeria                | NG   |       1|          1|     2541|
| Netherlands            | NL   |      63|         93|  2079571|
| Peru                   | PE   |       1|         11|   196747|
| Philippines            | PH   |       1|          1|      958|
| Poland                 | PL   |      10|         15|   426909|
| Singapore              | SG   |       1|          1|     1275|
| Thailand               | TH   |      33|         61|  1067139|
| United States          | US   |     687|       1749|  8099365|
| Viet Nam               | VN   |       2|          3|    11481|
| Kosovo                 | XK   |       1|          1|     3467|

``` r
kable(countriesTable$meta)
```

| name       | license   | website                    |  page|  limit|  found|
|:-----------|:----------|:---------------------------|-----:|------:|------:|
| openaq-api | CC BY 4.0 | <https://docs.openaq.org/> |     1|    100|     25|

``` r
kable(countriesTable$timestamp)
```

| lastModif           | queriedAt           |
|:--------------------|:--------------------|
| 2016-08-31 09:11:54 | 2016-08-31 09:15:47 |

The `aq_cities` function
------------------------

Using the `aq_cities` functions one can get all cities for which information is available within the platform. For each city, one gets the number of locations and the count of measures for the city, the URL encoded string, and the country it is in.

``` r
citiesTable <- aq_cities()
kable(head(citiesTable$results))
```

| city      | country |  locations|  count| cityURL   |
|:----------|:--------|----------:|------:|:----------|
| 76t       | TH      |          1|      4| 76t       |
| ABBEVILLE | US      |          1|   2487| ABBEVILLE |
| Aberdeen  | GB      |          3|  24756| Aberdeen  |
| Aberdeen  | US      |          2|   5906| Aberdeen  |
| ADA       | US      |          1|   7527| ADA       |
| ADAIR     | US      |          1|  10926| ADAIR     |

The optional `country` argument allows to do this for a given country instead of the whole world.

``` r
citiesTableIndia <- aq_cities(country="IN")
kable(citiesTableIndia$results)
```

| city          | country |  locations|   count| cityURL       |
|:--------------|:--------|----------:|-------:|:--------------|
| Agra          | IN      |          1|   19595| Agra          |
| Ahmedabad     | IN      |          1|    9604| Ahmedabad     |
| Aurangabad    | IN      |          1|    2665| Aurangabad    |
| Barddhaman    | IN      |          1|    1158| Barddhaman    |
| Bengaluru     | IN      |          5|   64292| Bengaluru     |
| Chandrapur    | IN      |          2|   26716| Chandrapur    |
| Chennai       | IN      |          4|   43049| Chennai       |
| Chittoor      | IN      |          1|    2013| Chittoor      |
| Delhi         | IN      |         15|  273256| Delhi         |
| Faridabad     | IN      |          1|   33146| Faridabad     |
| Gaya          | IN      |          1|   11819| Gaya          |
| Gurgaon       | IN      |          1|   35622| Gurgaon       |
| Haldia        | IN      |          1|   23405| Haldia        |
| Howrah        | IN      |          1|    3845| Howrah        |
| Hyderabad     | IN      |          3|   55065| Hyderabad     |
| Jaipur        | IN      |          1|   47364| Jaipur        |
| Jodhpur       | IN      |          1|   44625| Jodhpur       |
| Kanpur        | IN      |          2|   50575| Kanpur        |
| Kolkata       | IN      |          3|   21193| Kolkata       |
| Lucknow       | IN      |          4|   37534| Lucknow       |
| Mumbai        | IN      |          3|   69482| Mumbai        |
| Muzaffarpur   | IN      |          1|   31442| Muzaffarpur   |
| Nagpur        | IN      |          5|   13141| Nagpur        |
| Nashik        | IN      |          4|    8395| Nashik        |
| Panchkula     | IN      |          1|   28463| Panchkula     |
| Patna         | IN      |          1|   22650| Patna         |
| Pune          | IN      |          1|   26990| Pune          |
| Rohtak        | IN      |          1|    3531| Rohtak        |
| Solapur       | IN      |          1|   56878| Solapur       |
| Thane         | IN      |          3|    2058| Thane         |
| Tirupati      | IN      |          3|   18853| Tirupati      |
| Varanasi      | IN      |          1|   48983| Varanasi      |
| Visakhapatnam | IN      |          4|   16490| Visakhapatnam |

If one inputs a country that is not in the platform (or misspells a code), then an error message is thrown.

``` r
#aq_cities(country="PANEM")
```

The `locations` function
------------------------

The `aq_locations` function has far more arguments than the first two functions. On can filter locations in a given country, city, location, for a given parameter (valid values are "pm25", "pm10", "so2", "no2", "o3", "co" and "bc"), from a given date and/or up to a given date, for values between a minimum and a maximum, for a given circle outside a central point by the use of the `latitude`, `longitude` and `radius` arguments. In the output table one also gets URL encoded strings for the city and the location. Below are several examples.

Here we only look for locations with PM2.5 information in India.

``` r
locationsIndia <- aq_locations(country="IN", parameter="pm25")
kable(locationsIndia$results)
```

| location                                      | city          | country | sourceName          |  count| lastUpdated         | firstUpdated        |  latitude|  longitude| pm25 | pm10  | no2   | so2   | o3    | co    | bc    | cityURL       | locationURL                                   |
|:----------------------------------------------|:--------------|:--------|:--------------------|------:|:--------------------|:--------------------|---------:|----------:|:-----|:------|:------|:------|:------|:------|:------|:--------------|:----------------------------------------------|
| AAQMS Karve Road Pune                         | Pune          | IN      | CPCB                |   6565| 2016-08-31 08:45:00 | 2016-03-21 08:00:00 |  18.49748|   73.81349| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Pune          | AAQMS+Karve+Road+Pune                         |
| Anand Vihar                                   | Delhi         | IN      | CPCB                |   7972| 2016-08-31 08:35:00 | 2015-06-29 14:30:00 |  28.65080|   77.31520| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | Anand+Vihar                                   |
| AP Tirumala                                   | Chittoor      | IN      | CPCB                |    493| 2016-07-04 06:15:00 | 2016-06-23 05:30:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Chittoor      | AP+Tirumala                                   |
| Ardhali Bazar                                 | Varanasi      | IN      | CPCB                |   9860| 2016-08-31 08:55:00 | 2016-03-22 00:05:00 |  25.35056|   82.97833| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Varanasi      | Ardhali+Bazar                                 |
| Central School                                | Lucknow       | IN      | CPCB                |   3944| 2016-08-31 08:30:00 | 2016-03-22 10:00:00 |  26.85273|   80.99633| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Lucknow       | Central+School                                |
| Chandrapur                                    | Chandrapur    | IN      | CPCB                |   3959| 2016-08-31 08:55:00 | 2016-03-22 00:25:00 |  19.95000|   79.30000| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Chandrapur    | Chandrapur                                    |
| Civil Lines                                   | Delhi         | IN      | CPCB                |      1| 2015-07-10 08:15:00 | 2015-07-10 08:15:00 |  28.67870|   77.22620| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | Civil+Lines                                   |
| Collectorate - Gaya - BSPCB                   | Gaya          | IN      | CPCB                |   2857| 2016-08-31 09:05:00 | 2016-03-21 16:35:00 |  24.74897|   84.94384| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Gaya          | Collectorate+-+Gaya+-+BSPCB                   |
| Collectorate Jodhpur - RSPCB                  | Jodhpur       | IN      | CPCB                |   6420| 2016-06-24 10:45:00 | 2016-03-21 18:30:00 |  26.29206|   73.03791| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Jodhpur       | Collectorate+Jodhpur+-+RSPCB                  |
| Collectorate - Muzaffarpur - BSPCB            | Muzaffarpur   | IN      | CPCB                |   6248| 2016-08-31 08:10:00 | 2016-03-19 09:20:00 |  26.07620|   85.41150| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Muzaffarpur   | Collectorate+-+Muzaffarpur+-+BSPCB            |
| GVM Corporation                               | Visakhapatnam | IN      | CPCB                |    263| 2016-07-01 05:30:00 | 2016-06-20 18:30:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Visakhapatnam | GVM+Corporation                               |
| GVMC Ram Nagar                                | Visakhapatnam | IN      | CPCB                |      1| 2016-07-08 05:00:00 | 2016-07-08 05:00:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Visakhapatnam | GVMC+Ram+Nagar                                |
| GVMC Ram Nagar-APPCB                          | Visakhapatnam | IN      | CPCB                |   2412| 2016-08-31 09:00:00 | 2016-07-08 05:00:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Visakhapatnam | GVMC+Ram+Nagar-APPCB                          |
| IGI Airport                                   | Delhi         | IN      | CPCB                |      1| 2015-07-10 06:30:00 | 2015-07-10 06:30:00 |  28.56000|   77.09400| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | IGI+Airport                                   |
| IGSC Planetarium Complex - Patna - BSPCB      | Patna         | IN      | CPCB                |   5503| 2016-08-31 09:05:00 | 2016-03-21 19:30:00 |  25.36360|   85.07550| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Patna         | IGSC+Planetarium+Complex+-+Patna+-+BSPCB      |
| Maharashtra Pollution Control Board Bandra    | Mumbai        | IN      | CPCB                |   7516| 2016-08-31 08:45:00 | 2016-03-21 16:15:00 |  19.04185|   72.86551| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Mumbai        | Maharashtra+Pollution+Control+Board+Bandra    |
| Maharashtra Pollution Control Board - Solapur | Solapur       | IN      | CPCB                |   9073| 2016-08-31 09:00:00 | 2016-03-21 18:30:00 |  17.65992|   75.90639| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Solapur       | Maharashtra+Pollution+Control+Board+-+Solapur |
| Mandir Marg                                   | Delhi         | IN      | CPCB                |  10726| 2016-07-26 13:25:00 | 2015-06-29 14:30:00 |  28.63410|   77.20050| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | Mandir+Marg                                   |
| Navi Mumbai Municipal Corporation Airoli      | Mumbai        | IN      | CPCB                |   8928| 2016-08-31 09:15:00 | 2016-03-21 18:30:00 |  19.14940|   72.99860| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Mumbai        | Navi+Mumbai+Municipal+Corporation+Airoli      |
| Nehru Nagar                                   | Kanpur        | IN      | CPCB                |   9793| 2016-08-31 09:05:00 | 2016-03-21 22:45:00 |  26.47031|   80.32517| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Kanpur        | Nehru+Nagar                                   |
| Punjabi Bagh                                  | Delhi         | IN      | CPCB                |  12825| 2016-08-26 09:50:00 | 2015-06-29 00:30:00 |  28.66830|   77.11670| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | Punjabi+Bagh                                  |
| RBU - WBSPCB                                  | Kolkata       | IN      | CPCB                |      1| 2016-06-28 04:32:00 | 2016-06-28 04:32:00 |  22.62787|   88.38040| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Kolkata       | RBU+-+WBSPCB                                  |
| R K Puram                                     | Delhi         | IN      | CPCB                |   3839| 2016-08-31 08:35:00 | 2016-03-21 23:55:00 |  28.56480|   77.17440| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | R+K+Puram                                     |
| RK Puram                                      | Delhi         | IN      | RK Puram            |   8593| 2016-03-22 00:10:00 | 2015-06-29 14:30:00 |  28.56480|   77.17440| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | RK+Puram                                      |
| Sanjay Palace                                 | Agra          | IN      | CPCB                |   6191| 2016-08-31 08:45:00 | 2016-03-22 00:20:00 |  27.19866|   78.00598| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Agra          | Sanjay+Palace                                 |
| Sector 6 Panchkula - HSPCB                    | Panchkula     | IN      | CPCB                |   5821| 2016-08-31 09:15:00 | 2016-03-21 18:30:00 |  30.70578|   76.85318| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Panchkula     | Sector+6+Panchkula+-+HSPCB                    |
| SPARTAN - IIT Kanpur                          | Kanpur        | IN      | Spartan             |   1684| 2014-09-26 00:30:00 | 2013-12-14 10:30:00 |  26.51900|   80.23300| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Kanpur        | SPARTAN+-+IIT+Kanpur                          |
| Tirumala                                      | Tirupati      | IN      | CPCB                |    554| 2016-07-11 10:45:00 | 2016-07-03 18:30:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Tirupati      | Tirumala                                      |
| Tirumala-APPCB                                | Tirupati      | IN      | CPCB                |   2403| 2016-08-31 08:00:00 | 2016-07-10 18:30:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Tirupati      | Tirumala-APPCB                                |
| US Diplomatic Post: Chennai                   | Chennai       | IN      | StateAir\_Chennai   |   6147| 2016-08-31 08:30:00 | 2015-12-11 21:30:00 |  13.05237|   80.25193| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Chennai       | US+Diplomatic+Post%3A+Chennai                 |
| US Diplomatic Post: Hyderabad                 | Hyderabad     | IN      | StateAir\_Hyderabad |   6147| 2016-08-31 08:30:00 | 2015-12-11 21:30:00 |  17.44346|   78.47489| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Hyderabad     | US+Diplomatic+Post%3A+Hyderabad               |
| US Diplomatic Post: Kolkata                   | Kolkata       | IN      | StateAir\_Kolkata   |   6147| 2016-08-31 08:30:00 | 2015-12-11 21:30:00 |  22.54714|   88.35105| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Kolkata       | US+Diplomatic+Post%3A+Kolkata                 |
| US Diplomatic Post: Mumbai                    | Mumbai        | IN      | StateAir\_Mumbai    |   6147| 2016-08-31 08:30:00 | 2015-12-11 21:30:00 |  19.06602|   72.86870| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Mumbai        | US+Diplomatic+Post%3A+Mumbai                  |
| US Diplomatic Post: New Delhi                 | Delhi         | IN      | StateAir\_NewDelhi  |   6197| 2016-08-31 08:30:00 | 2015-12-11 21:30:00 |  28.59810|   77.18907| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Delhi         | US+Diplomatic+Post%3A+New+Delhi               |
| Vikas Sadan Gurgaon - HSPCB                   | Gurgaon       | IN      | CPCB                |   6281| 2016-08-30 03:30:00 | 2016-03-25 07:15:00 |  28.45013|   77.02631| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Gurgaon       | Vikas+Sadan+Gurgaon+-+HSPCB                   |
| Visakhapatnam                                 | Visakhapatnam | IN      | CPCB                |      1| 2016-06-21 11:30:00 | 2016-06-21 11:30:00 |        NA|         NA| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Visakhapatnam | Visakhapatnam                                 |
| VK Industrial Area Jaipur - RSPCB             | Jaipur        | IN      | CPCB                |   8299| 2016-08-04 03:00:00 | 2016-03-21 18:30:00 |  26.97388|   75.77388| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Jaipur        | VK+Industrial+Area+Jaipur+-+RSPCB             |
| ZooPark                                       | Hyderabad     | IN      | CPCB                |   5462| 2016-08-31 08:45:00 | 2016-03-21 18:30:00 |  17.34969|   78.45144| TRUE | FALSE | FALSE | FALSE | FALSE | FALSE | FALSE | Hyderabad     | ZooPark                                       |

Getting measurements
====================

Two functions allow to get data: `aq_measurement` and `aq_latest`. In both of them the arguments city and location needs to be given as URL encoded strings.

The `aq_measurements` function
------------------------------

The `aq_measurements` function has many arguments for getting a query specific to, say, a given parameter in a given location or for a given circle outside a central point by the use of the `latitude`, `longitude` and `radius` arguments. Below we get the PM2.5 measures for Anand Vihar in Delhi in India.

``` r
tableResults <- aq_measurements(country="IN", city="Delhi", location="Anand+Vihar", parameter="pm25")
kable(head(tableResults$results))
```

| location    | parameter |  value| unit  | country | city  | dateUTC             | dateLocal           |  latitude|  longitude| cityURL | locationURL |
|:------------|:----------|------:|:------|:--------|:------|:--------------------|:--------------------|---------:|----------:|:--------|:------------|
| Anand Vihar | pm25      |     41| µg/m³ | IN      | Delhi | 2016-08-31 08:35:00 | 2016-08-31 14:05:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |
| Anand Vihar | pm25      |     27| µg/m³ | IN      | Delhi | 2016-08-31 08:05:00 | 2016-08-31 13:35:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |
| Anand Vihar | pm25      |     27| µg/m³ | IN      | Delhi | 2016-08-31 07:35:00 | 2016-08-31 13:05:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |
| Anand Vihar | pm25      |     29| µg/m³ | IN      | Delhi | 2016-08-31 07:05:00 | 2016-08-31 12:35:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |
| Anand Vihar | pm25      |     29| µg/m³ | IN      | Delhi | 2016-08-31 06:35:00 | 2016-08-31 12:05:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |
| Anand Vihar | pm25      |     20| µg/m³ | IN      | Delhi | 2016-08-31 06:05:00 | 2016-08-31 11:35:00 |   28.6508|    77.3152| Delhi   | Anand+Vihar |

``` r
kable(tableResults$timestamp)
```

| lastModif           | queriedAt           |
|:--------------------|:--------------------|
| 2016-08-31 09:11:54 | 2016-08-31 09:15:53 |

``` r
kable(tableResults$meta)
```

| name       | license   | website                    |  page|  limit|  found|
|:-----------|:----------|:---------------------------|-----:|------:|------:|
| openaq-api | CC BY 4.0 | <https://docs.openaq.org/> |     1|    100|   7972|

One could also get all possible parameters in the same table.

The `aq_latest` function
------------------------

This function gives a table with all newest measures for the locations that are chosen by the arguments. If all arguments are `NULL`, it gives all the newest measures for all locations.

``` r
tableLatest <- aq_latest()
kable(head(tableLatest$results))
```

| location          | city                 | country |  latitude|  longitude| parameter |    value| lastUpdated         | unit  | cityURL              | locationURL       |
|:------------------|:---------------------|:--------|---------:|----------:|:----------|--------:|:--------------------|:------|:---------------------|:------------------|
| 100 ail           | Ulaanbaatar          | MN      |  47.93291|  106.92138| co        |  488.000| 2016-08-31 09:00:00 | µg/m³ | Ulaanbaatar          | 100+ail           |
| 100 ail           | Ulaanbaatar          | MN      |  47.93291|  106.92138| no2       |   19.000| 2016-08-31 09:00:00 | µg/m³ | Ulaanbaatar          | 100+ail           |
| 100 ail           | Ulaanbaatar          | MN      |  47.93291|  106.92138| o3        |   60.000| 2016-08-31 09:00:00 | µg/m³ | Ulaanbaatar          | 100+ail           |
| 100 ail           | Ulaanbaatar          | MN      |  47.93291|  106.92138| pm10      |   84.000| 2016-08-31 09:00:00 | µg/m³ | Ulaanbaatar          | 100+ail           |
| 100 ail           | Ulaanbaatar          | MN      |  47.93291|  106.92138| so2       |    1.000| 2016-08-31 09:00:00 | µg/m³ | Ulaanbaatar          | 100+ail           |
| 16th and Whitmore | Omaha-Council Bluffs | US      |  41.32247|  -95.93799| o3        |    0.008| 2016-08-31 07:00:00 | ppm   | Omaha-Council+Bluffs | 16th+and+Whitmore |

Below are the latest values for Anand Vihar at the time this vignette was compiled (cache=FALSE).

``` r
tableLatest <- aq_latest(country="IN", city="Delhi", location="Anand+Vihar")
kable(head(tableLatest$results))
```

| location    | city  | country |  latitude|  longitude| parameter |   value| lastUpdated         | unit  | cityURL | locationURL |
|:------------|:------|:--------|---------:|----------:|:----------|-------:|:--------------------|:------|:--------|:------------|
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| co        |  1300.0| 2016-03-21 14:45:00 | µg/m³ | Delhi   | Anand+Vihar |
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| no2       |    52.5| 2016-08-31 08:35:00 | µg/m³ | Delhi   | Anand+Vihar |
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| o3        |    15.8| 2016-08-31 08:35:00 | µg/m³ | Delhi   | Anand+Vihar |
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| pm10      |    48.0| 2016-08-31 08:35:00 | µg/m³ | Delhi   | Anand+Vihar |
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| pm25      |    41.0| 2016-08-31 08:35:00 | µg/m³ | Delhi   | Anand+Vihar |
| Anand Vihar | Delhi | IN      |   28.6508|    77.3152| so2       |    18.0| 2016-03-21 14:45:00 | µg/m³ | Delhi   | Anand+Vihar |

Paging and limit
================

For all endpoints/functions, there a a `limit` and a `page` arguments, which indicate, respectively, how many results per page should be shown and which page should be queried. Based on this, how to get all results corresponding to a query? First, look at the number of results, e.g.

``` r
how_many <- aq_measurements(city = "Delhi",
                            parameter = "pm25")$meta
knitr::kable(how_many)
```

| name       | license   | website                    |  page|  limit|  found|
|:-----------|:----------|:---------------------------|-----:|------:|------:|
| openaq-api | CC BY 4.0 | <https://docs.openaq.org/> |     1|    100|  50154|

``` r
how_many$found
```

    ## [1] 50154

Then one can write a loop over pages. Note that the maximal value of `limit` is 1000.

``` r
meas <- NULL
for (page in 1:(ceiling(how_many$found/1000))){
  meas <- dplyr::bind_rows(meas,
                aq_measurements(city = "Delhi",
                                parameter = "pm25",
                                page = page,
                                limit = 1000))
  }
```

Other packages of interest for getting air quality data
=======================================================

-   The [`rdefra` package](https://github.com/kehraProject/r_rdefra), also part of the rOpenSci project, allows to to interact with the UK AIR pollution database from DEFRA, including historical measures.

-   The [`openair` package](https://github.com/davidcarslaw/openair) gives access to the same data as `rdefra` but relies on a local and compressed copy of the data on servers at King's College (UK), periodically updated.

-   The [`usaqmindia` package](https://github.com/masalmon/usaqmindia) provides data from the US air quality monitoring program in India for Delhi, Mumbai, Chennai, Hyderabad and Kolkata from 2013.

Meta
----

-   Please [report any issues or bugs](https://github.com/ropenscilabs/ropenaq/issues).
-   License: GPL
-   Get citation information for `ropenaq` in R doing `citation(package = 'ropenaq')`
-   Please note that this project is released with a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.

[![ropensci\_footer](http://ropensci.org/public_images/github_footer.png)](http://ropensci.org)
