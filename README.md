## Project Objective
The source of the dataset was from the World Development Indicators (WDI) database. The objective of the project was to compose visualisations to shed light on conflict and food aid in 
Sub-Saharan Africa. Additionally, the visualizations had to demonstrate whether there is an association between food aid and conflict.

## What was done
- Libraries from the tidyverse package were used for the most part in this project
- Extracted more variables from the WDI site that are deemed relevant to the objectives and combined them into the main CSV dataset. This was achieved using the JOIN function.
- Data cleaning and manipulation steps were performed such as abbreviating variable names, variables data type conversion, and extracting year values from dates.
- Plotted visualizations (bar charts, scatterplots) between variables using the ggplot2 package to understand the relationship between them and ultimately form insights that fulfil the objectives

#### Abbreviation of column names:
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

#### Converting dates into just 'year' values:
```
df$year <- format(as.Date(df$year, format="%d/%m/%Y"),"%Y")
View(df)
```

#### Converting variable data types:
```
a <- c(2:25)
df[a] <- lapply(df[a],as.numeric)
lapply(df , class)
View(df)
```

#### Extracting relevant variables from WDI package:
```
wdi_dat <- WDI(indicator = c("IS.RRS.TOTL.KM", "MS.MIL.TOTL.P1", "IS.AIR.GOOD.MT.K1", "IS.SHP.GOOD.TU"), start = 2002, end = 2020, extra = TRUE)
wdi_dat <- subset(wdi_dat, region %in% "Sub-Saharan Africa")
```

#### Joining WDI dataset and food aid dataset using LEFT JOIN:
```
food_WDI <- left_join(df,wdi_dat)
View(food_WDI)
```

Scatterplot showing relationship between food aid and people affected by disasters:
![Image 1](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%201.png?raw=true) <br> <br>

Bar plot summary of countries with major conflicts:
![Image 2](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%202.png?raw=true) <br> <br>

Bar plot summary of countries with minor conflicts:
![Image 3](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%203.png?raw=true) <br> <br>

Bar plot summary of emergency aid received by Sub-Saharan countries:
![Image 4](https://github.com/bayyangjie/Data-Visualization-and-Storytelling/blob/main/Images/Picture%204.png?raw=true)
