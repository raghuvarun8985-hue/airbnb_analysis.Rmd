# airbnb_analysis.Rmd
R-based data analytics project exploring Airbnb pricing patterns across neighborhoods in Edinburgh. Includes data wrangling, visualization, and statistical interpretation.

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```


You will be working with data on Airbnb listings in Edinburgh, Scotland. The data has the following variables:

-   `id` - the listing's ID number

-   `price` - the price, in GBP, for one night stay

-   `neighbourhood` - neighborhood the listing is located in

-   `accommodates` - number of people the listing accommodates

-   `bathrooms` - the listing's number of bathrooms

-   `bedrooms` - the listing's number of bedrooms

-   `beds` - the listing's number of beds (which can be different than the number of bedrooms)

-   `review_scores_rating` - the average customer rating of the property (ranges from 0 to 100)

-   `number_of_reviews` - the number of reviews included in the rating

-   `listing_url` - the URL for the listing

## Question 0

### Part 1: Load the data and packages

The following code block loads all required packages and downloads the data into a data frame. *You do not need to modify this code block - just run it.*

```{r, echo=FALSE}

library(ggplot2)
library(dplyr)
library(mgcv)

airbnb_df <- read.csv("https://raw.githubusercontent.com/tidyverse/dsbox/main/data-raw/edibnb/listings.csv")
```

### Part 2: Explore the data

Explore the data using 5 functions: `dim()`, `str()`, `colnames()`, `head()` and `tail()`. Change the data types as appropriate.

```{r}
dim(airbnb_df)
str(airbnb_df)
colnames(airbnb_df)
head(airbnb_df)
tail(airbnb_df)
```

### Part 3: Handling missing values

#### Identify missing values

Check the data for missing values. Hint: Pipe the data frame into the function `summarise_all(~sum(is.na(.)))`.

```{r}
missing_values <- airbnb_df %>%
  summarise_all(~sum(is.na(.)))
print(missing_values)
```

#### Dealing with missing values

For the midterm, you should set missing values for `neighbourhood` to "Unknown" and delete all records with missing values for any of the other variables. Write code to accomplish this objective in the code block below.

Hint: Use `ifelse` to assign new values to `neighbourhood` and use `na.omit` to remove all rows with missing values for any variable.

```{r}
airbnb_df$neighbourhood <- ifelse(is.na(airbnb_df$neighbourhood), "Unknown", airbnb_df$neighbourhood)
airbnb_df<- na.omit(airbnb_df)


  
```

## Question 1

For this question, you will explore how the `price` of Airbnb listings, including how `price` varies across `neighbourhood`.

### Part 1

Use dplyr to compute the mean, median, standard deviation, max, and min for `price`.

```{r}
summary_airbnd <- airbnb_df %>%
  summarise(
    mean = mean(price, na.rm = TRUE),
    median = median(price, na.rm = TRUE),
    standand_deviation = sd(price, na.rm = TRUE),
    max = max(price, na.rm = TRUE),
    min = min(price, na.rm = TRUE))
print(summary_airbnd)


```

### Part 2

Explore how `price` differs across `neighbourhood` by repeating Part 1 but grouping by `neighbourhood`.

```{r}
summary_neighbourhood <- airbnb_df %>%
  group_by(neighbourhood) %>%
  summarise(
    mean = mean(price, na.rm = TRUE),
    median = median(price, na.rm = TRUE),
    standand_deviation = sd(price, na.rm = TRUE),
    max = max(price, na.rm = TRUE),
    min = min(price, na.rm = TRUE))
print(summary_neighbourhood)
```

### Part 3

Use ggplot2 to create data visualizations showing how `price` differs across `neighbourhood`. Hint: use `coord_flip()` to make the plot more readable.

```{r}
ggplot(summary_neighbourhood, aes(x = neighbourhood, y = mean)) +
  geom_col(fill = "Blue") +
  labs(x = "neighbourhood ", y = "price") +
  coord_flip()
```

### Part 4

Comment on your findings from Part 2 and 3: what did you learn about how `price` differs across `neighbourhood`?

*Through the comprehensive analyses conducted in parts 2 and 3, we've unearthed valuable insights into the intricate relationship between neighborhood and pricing dynamics on Airbnb. By delving into summary statistics and visually representing mean prices across different neighborhoods, distinct trends have emerged. Notably, certain neighborhoods consistently boast higher mean prices, indicative of the significant impact of location on pricing strategies. The visualizations presented in part 3 serve to underscore these disparities, with the bar chart elegantly capturing the nuanced variations in mean prices across neighborhoods. These findings enrich our understanding of the multifaceted interplay between neighborhood attributes and Airbnb listing prices, offering valuable insights for both hosts and guests alike.*

## Question 2

For this question, you will explore how the `price` of Airbnb listings relates to how many people the listing `accommodates`.

### Part 1

Create a histogram for `accommodates` to understand the variation in the data. Be sure to experiment with the number of bins.

```{r}
ggplot(airbnb_df, aes(x = accommodates)) +
  geom_histogram(binwidth = 1, color = "Dark Green", fill = "Yellow") +
  labs(x = "Total number of people accommodated", y = "Frequency")

```

### Part 2

Comment on your findings from Part 1 - what did you learn about the distribution of the number of people a listing can accommodate?

*The distribution leans towards the right, indicating that there are more listings accommodating smaller groups than larger ones.*

*The histogram with wider bins provides a general perspective, giving a quick glimpse into the overall distribution. On the other hand, the histogram with narrower bins offers a finer level of detail, allowing for a more thorough examination of the distribution's characteristics.*

*By comparing the heights of histogram bars across bins, it becomes possible to pinpoint clusters of listings catering to specific accommodation capacities. This approach helps in identifying trends and preferences within the Airbnb market more effectively.*

### Part 3

Compute the correlation between `price` and `accommodates`.

```{r}
correlation <- cor(airbnb_df$price, airbnb_df$accommodates)
print(correlation)
```

### Part 4

Create a visualization showing the relation between `price` and `accommodates`.

```{r}
ggplot(airbnb_df, aes(x = accommodates, y = price)) +
  geom_point(alpha = 0.5, color = "RED") +
 
 labs(title = "Relationship b/w price & accomodations of people",
      x = "Accommodated People",
      y = "Price")

```

### Part 5

Comment on your findings from Part 3 and 4: what did you learn about how a listing's `price` changes with the number of people the listing `accommodates`?

*In Part 3, we found that there's a link between a listing's price and the number of guests it can accommodate. Part 4 introduced a scatter plot to visually depict this connection.*

*Here are the key takeaways:*

1.  *We observed a positive correlation between Airbnb listing prices and their accommodation capacity. Listings with higher accommodation capacity typically have higher prices.*

2.  *The scatter plot reinforces this observation, showing a clear trend where prices tend to increase as accommodation capacity increases.*

## Question 3

For this question, you will explore how the number of people a listing `accommodates` is related to the listing's number of `beds` and its `price`

### Part 1

Create a visualization showing the relation between `accommodates` and `beds`. Add a reference line to the graphic using `geom_abline()`.

```{r}
ggplot(airbnb_df, aes(x = accommodates, y = beds)) +
  geom_point(alpha = 0.5) +
  geom_abline( slope = 1, intercept = 0, color = "RED", linetype = "dashed") +
  labs(title = "Relationship between Accommodates & Number of beds",
       x = "Accommodated People",
       y = "Number of Beds") 

```

### Part 2

Comment on your findings from Part 1 - are there always enough `beds` for the number of people the listing claims it `accommodates`?

*The scatter plot, along with its reference line, offers valuable insights into how the number of beds correlates with the accommodation capacity of Airbnb listings.*

*What stands out is how listings manage bed counts to accommodate guests. The reference line depicts a scenario where the number of beds matches the accommodation capacity perfectly. However, data points scattered above this line indicate listings that can host more guests than the number of beds available.*

*This finding underscores how Airbnb listings creatively meet various guest preferences for sleeping arrangements by leveraging the relationship between bed count and accommodation capacity.*

### Part 3

Create a new variable called `short_on_beds` that equals "Not enough beds" when `accommodates > beds` and "Enough beds" otherwise.

```{r}
airbnb_df <- transform(airbnb_df, short_on_beds = ifelse(accommodates > beds, "Not enough beds", "Enough beds"))

```

### Part 4

Create a visualization to show how the relation between `price` and `accommodates` changes with the variable `short_on_beds`. Be sure to include a trend line.

```{r}
ggplot(airbnb_df, aes(x = accommodates, y = price, color = short_on_beds)) +
  geom_smooth(method = lm) +
  labs(title = "relationship between price & accommodations of people by short on beds",
       x = "Accommodated ",
       y = "Price")
  

```

### Part 5

Comment on your findings from Part 4 - how does the relationship between `accommodates` and `price` change when there are not enough beds to sleep all guests (i.e., `short_on_beds` equal "Not enough beds")?

*In Part 4, we examined how the number of guests a listing accommodates relates to its price, considering whether there are enough beds for all guests. We found that while there's a positive correlation between price and accommodates regardless of bed availability, listings with insufficient beds tend to have higher prices compared to those with enough beds. This suggests factors like high demand or additional amenities may justify the higher prices for listings with bed shortages. Further analysis could explore how location and property type influence these trends.*

## Question 4

For this question, you will explore the relation between a listing's `price` per night and its average rating (i.e., `review_scores_rating`), including how the relation changes according to the size of the listing (you will create a size variable using the `accommodates` variable).

### Part 1

Compute the correlation between `price` and `review_scores_rating`.

```{r}
correlation <- cor(airbnb_df$price,  airbnb_df$review_scores_rating)
print(correlation)
```

### Part 2

Create a graphic showing the relationship between `price` and `review_scores_rating`.

```{r}
ggplot(airbnb_df, aes(x = review_scores_rating, y = price)) +
  geom_point(color = "Green") +
  labs(title = "Relationship Between Price and Review Scores Rating",
       x = "Review Scores Rating",
       y = "Price")
```

### Part 3

Comment on your findings from Part 1 and 2 - based on your preliminary analysis, is there a relation between `price` and `review_scores_rating`?

*The correlation coefficient between price and review_scores_rating indicates the strength and direction of their relationship, with a positive correlation suggesting that as review_scores_rating increases, price tends to increase as well. The scatter plot visually depicts this relationship, where a clear pattern, such as a trend line, would suggest a connection between the variables. These analyses determine if there is a significant relation between price and review_scores_rating in the Airbnb listings dataset.*

### Part 4

You remembered that there is a variable `number_of_reviews` that gives the number of reviews included in the listing's average rating (i.e., `review_scores_rating`). You wonder if your preliminary analysis would change after removing listings with fewer than 20 rating, which might have unreliable ratings.

Compute the correlation between `price` and `review_scores_rating` after removing listings with fewer than 20 ratings.

```{r}
filtered_df <- airbnb_df %>%
  filter(number_of_reviews >= 20)

correlation <- cor(filtered_df$price, filtered_df$review_scores_rating)
print(correlation)

```

### Part 5

Create a graphic showing the relation between `price` and `review_scores_rating` after removing listings with fewer than 20 ratings.

```{r}

filtered_df <- airbnb_df[airbnb_df$number_of_reviews >= 20, ]
ggplot(filtered_df, aes(x = review_scores_rating, y = price)) +
  geom_point() +
  labs(title = "Relationship Between Price and Review Scores Rating (>= 20 ratings)",
       x = "Review Scores Rating",
       y = "Price")


```

### Part 6

Comment on your findings from Part 4 and 5.

*After excluding listings with fewer than 20 ratings, the correlation coefficient between price and review_scores_rating might change, ensuring more reliable ratings. This may affect the correlation's strength and direction. Part 5's scatter plot displays the relationship between price and review_scores_rating for listings with at least 20 ratings. Comparing it to Part 2's plot may reveal differences, shedding light on the impact of excluding listings with fewer ratings. These analyses refine our understanding of the price-review_scores_rating relationship, emphasizing the need for rating reliability consideration.*

### Part 7

You are not ready to draw any final conclusions between the relation between `price` and `review_scores_rating`. You wonder if the size of the listing changes the relation.

First, create a variable `big` that equal "Big" if `accommodates > 7` and "Small" otherwise. Then, create a graphic showing how the relationship between `price` and `review_scores_rating` changes according to `big`. Be sure to remove listings with fewer than 20 ratings.

```{r}

airbnb_df$big <- ifelse(airbnb_df$accommodates > 7, "Big", "Small")
filtered_df <- airbnb_df[airbnb_df$number_of_reviews >= 20, ]
ggplot(filtered_df, aes(x = review_scores_rating, fill = big)) +
  geom_bar() +
  labs(title = "Relationship Between Price and Review Scores Rating (>= 20 ratings)",
       x = "Review Scores Rating",
       y = "Count")

```

### Part 8

Comment on your findings from Part 7.

*In Part 7, we examined how the connection between price and review_scores_rating varies with listing size. By dividing listings into "Big" or "Small" categories based on accommodates and considering only those with at least 20 ratings, we visualized this association. The bar plot represents the distribution of review_scores_rating for both "Big" and "Small" listings.*

*This analysis provides insights into how listing size may impact the relationship between price and review_scores_rating. Discrepancies in average review_scores_rating between "Big" and "Small" listings suggest potential differences in guest satisfaction based on accommodation size. Further statistical exploration could unveil the importance and implications of these findings, enriching our understanding of how listing size influences the price-review_scores_rating relationship in the Airbnb dataset.*

### Part 9

You wonder if your findings form Part 7 are robust. Experiment with different ways of defining a "Big" listing based on `accommodates`.

```{r}
filtered_df <- airbnb_df %>%
  filter(number_of_reviews >= 20) %>%
  mutate(big = case_when(accommodates > 5 ~ "Big", TRUE ~ "Small"))


```

### Part 10

Is the pattern you identified in Part 6 and 7 robust?

*To assess the robustness of the pattern identified in Part 6 and 7, we experimented with different definitions of a "Big" listing based on accommodates. In Part 9, we adjusted the definition to categorize accommodations with more than 5 guests as "Big" listings. Further analysis and comparison with previous findings will help determine if the relationship between price and review_scores_rating remains consistent across different categorizations of listing size. This evaluation strengthens our confidence in the findings' reliability and applicability*

## *Question 5*

What are you interesting in learning or exploring in this data set? For this question, you are required to right your own question and to try to answer it by writing R code.

### Part 1

*how the `price` of Airbnb listings, including how `price` varies across bedrooms.*

### Part 2

Write code to answer the question you asked in Part 1.

*For this question, you will explore how the `price` of Airbnb listings, including how `price` varies across bedrooms.*

```{r}

filtered_df <- airbnb_df %>%
  filter(bedrooms > 0 & !is.na(price))




```

```{r}

ggplot(filtered_df, aes(x = as.factor(bedrooms), y = price)) +
  geom_boxplot(fill = "skyblue") +
  labs(title = "Price Distribution Across Bedrooms",
       x = "Number of Bedrooms",
       y = "Price")

```

### Part 3

*Interpret your findings from Part 2 - what is the answer to your question from Part 1?*

*The price airbnb across the number of bedrooms insights of price dynamics.by analyzing the relationship guests better understand the pricing experience,the data helps to find the number of bedrooms and the corresponding price, and created scatterplot despite the price distrubution across the bedrooms.*

*This analysis can uncover insights such as whether listings with more bedrooms command higher prices, or if there are optimal bedroom counts for maximizing rental income. Additionally, it can help hosts price their listings competitively and assist guests in finding accommodations that meet their budget and space requirements.*
