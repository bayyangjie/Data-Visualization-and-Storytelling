## Project Objective
The source of the dataset was from the World Development Indicators (WDI) database. The objective of the project was to compose visualisations to shed light on conflict and food aid in 
Sub-Saharan Africa. Additionally, the visualizations had to demonstrate whether there is an association between food aid and conflict.

## Learning points
- Libraries from the tidyverse package were used for the most part in this project
- Extracted more variables from the WDI site that are deemed relevant to the objectives and combined them into the main CSV dataset. This was achieved using the JOIN function.
- Data cleaning and manipulation steps were performed such as abbreviating variable names, variables data type conversion, and extracting year values from dates.
- Plotted visualizations (bar charts, scatterplots) between variables using the ggplot2 package to understand the relationship between them and ultimately form insights that fulfil the objectives <br>

## Data Cleaning

#### Abbreviation of column names
```
names(df) <- substr(names(df),1,9)
names(df)

## Renaming abbreviations due to replicated abbreviated names
df <- df %>%
  rename(othr_ctry_aff = 15, neigh_ctry_aff =16, non_neigh_aff=17)

## Correcting some values in 'country' column due to illegal symbols

df <- df %>% 
  mutate(country = str_replace(country, "C�te d'Ivoire", "Ivory Coast"))
```
Shortening the variable names for easier referencing. And also removing special characters in the variable names by renaming the country name (e.g from Cote d'Ivoire to "Ivory Coast).

<br>

#### Converting dates into just 'year' values
```
df$year <- format(as.Date(df$year, format="%d/%m/%Y"),"%Y")
View(df)
```
<br>

#### Converting variable data types
```
a <- c(2:25)
df[a] <- lapply(df[a],as.numeric)
lapply(df , class)
View(df)
```
<br>

## Data manipulation

#### Extracting relevant variables from WDI package
```
wdi_dat <- WDI(indicator = c("IS.RRS.TOTL.KM", "MS.MIL.TOTL.P1", "IS.AIR.GOOD.MT.K1", "IS.SHP.GOOD.TU"), start = 2002, end = 2020, extra = TRUE)
wdi_dat <- subset(wdi_dat, region %in% "Sub-Saharan Africa")
names(wdi_dat)[which(names(wdi_dat) == "MS.MIL.TOTL.P1")] <- "arm_person"
names(wdi_dat)[which(names(wdi_dat) == "IS.RRS.TOTL.KM")] <- "rail_lines"
names(wdi_dat)[which(names(wdi_dat) == "IS.AIR.GOOD.MT.K1")] <- "air_freight"
names(wdi_dat)[which(names(wdi_dat) == "IS.SHP.GOOD.TU")] <- "port_traffic"
View(wdi_dat)

## Removing unwanted columns
wdi_dat <- wdi_dat[-c(2:3, 5:6, 15:16)]
View(wdi_dat)

## Ordering the values in 'year' column in ascending order for each country
wdi_dat %>% arrange(factor(country, as.character(unique(country))), year) -> wdi_dat
View(wdi_dat)
str(wdi_dat)

## Converting data types of the WDI variables
i <- c(2:3 , 9:10)
wdi_dat[i] <- lapply(wdi_dat[i], as.numeric)
lapply(wdi_dat , class)
```
<br>

#### Joining WDI dataset and food aid dataset using LEFT JOIN
```
food_WDI <- left_join(df,wdi_dat)
View(food_WDI)
```
<br>

#### Grouping the dataframe by countries
```
food_WDI_group <- group_by(food_WDI , country)
tbl_sum(food_WDI_group)

## Creating a new dataframe of mean emergency aid of the countries after grouping
food_WDI_sum <- summarise(food_WDI_group , emergency.mean=mean(emergency, na.rm=TRUE))
food_WDI_sum
food_WDI_sum[order(food_WDI_sum$emergency.mean, decreasing = TRUE),]
```
Grouping by countries and calculating the mean emergency aid of each country.

<br>

## Plotting

#### Scatterplot showing relationship between food aid and people affected by disasters
The variable 'affected' which represents the number of people affected by disaster forms the x-axis and the variable 'Emergency Food Aid' which represents the amount of food aid delivered forms the y-axis.
The scatterplot shows that higher number of people affected by natural disasters suggests a more serious impact of the disaster. It also shows that even with a higher number of people affected, emergency aid does not increase proportionately.

![Image 1](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%201.png?raw=true) <br> <br>

#### Bar plot summary of countries with major conflicts
'Country' and 'major_con' columns were combined to form a separate dataframe 'food_WDI_mc' that contains only data related to the country names and major conflicts. 
The dataframe was then further filtered using filter() to form a secondary dataframe 'major_con_1' which only contains countries that suffered major conflicts.  
Functions group_by() and summarise() were then used to group the distinct countries and obtain the total count of major conflicts for each country.
The bar chart shows that Sudan had the highest count of major conflicts which tallies with it having one of the highest emergency aid received. This shows that major conflicts led to Ethiopia requiring a high amount of emergency aid received.

![Image 2](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%202.png?raw=true) <br> <br>

#### Bar plot summary of countries with minor conflicts
Filter() function was used again to filter only those countries that suffered minor conflicts and assigned a new variable 'minor_con_1' to the filtered dataframe.
Similarly, group_by() was used to group the distinct countries and then summarise() was used to retrieve the total count of minor conflicts that occurred for each country.
As compared to the major conflicts bar plot, more countries suffered minor conflicts as opposed to the list of countries in major conflicts. In terms of emergency aid, Ethiopia received the highest amount of emergency aid. This could be due to the high number of minor conflicts.

![Image 3](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%203.png?raw=true) <br> <br>

#### Bar plot summary of emergency aid received by Sub-Saharan countries
The bar chart below shows the countries that have emergency aid received above 10 million USD, and it shows that Sudan and Ethiopia have the highest amount of emergency aid received.
The data is filtered to only show those countries that have mean emergency aid of above 10 million USD (filter(emerg_high_mean>10)) and assigned to the variable 'emerg_high_mean'.

![Image 4](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%204.png?raw=true)

## Significant findings

This conclusion summarizes the factors that affect emergency aid and the variables that do not affect emergency aid. 
Emergency food aid is affected by conflicts, natural disasters, and GDP per capita. As inferred from the plots, high minor conflicts such as in Ethiopia can lead to high emergency aid while high major conflict such as in Sudan can also lead to requiring high emergency aid. So conflicts in general regardless of high or low could lead to an increase in aid needed by countries. Secondly, natural disasters also do play a part in high emergency food aid. Somalia being the most affected by natural disasters, is the country with the third highest amount of emergency food aid received. Lastly, countries that have a low GDP per capita such as Mozambique and Mauritania received higher amount of emergency food aid as compared to the other countries with higher GDP per capita. On the other hand, factors such as high armed personnel and inflation levels do not contribute to increased emergency food aid. In summary, conflicts are not the only contributing factors that lead to rise in emergency food aid needed in the SSA region.
