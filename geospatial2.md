# 2 Introduction to R for geospatial data

## 1 Introduction to RStudio

### Exercise 1.1 
What will be the value of each variable after each statement in the following program?
```R
mass <- 47.5
age <- 122
mass <- mass * 2.3
age <- age - 20
```

<details>
<summary>Solution
</summary>

Mass - 47.5   
Age - 122   
Mass - 47.5*2.3 = 109.25   
Age - 122-20 = 102   

</details>

<br>
<br>
<br>
<br>

### Exercise 1.2 
Run the code from the previous challenge, and write a command to compare if mass is larger than age.

```R
mass <- 47.5
age <- 122
mass <- mass * 2.3
age <- age - 20
```

<details>
<summary>Solution
</summary>

`mass > age  `

</details>

<br>
<br>
<br>
<br>

### Exercise 1.3
Which of the following are valid R variable names?

```
min_height
max.height
_age
.mass
MaxLength
min-length
2widths
celsius2kelvin
```

<details>
<summary>Solution
</summary>

`.mass` creates a hidden variable. We're not covering this here, so best not to use a full stop at the start of a variable name.

`_age`, `min-length`, and `2widths` are not valid.

</details>

<br>
<br>
<br>
<br>

### Exercise 3.1 
Given what you now know about type conversion, look at the class of data in `nordic_2$lifeExp` (using `str(nordic_2$lifeExp)`) and compare it with `nordic$lifeExp`. Why are these columns different classes?
<details>
<summary>Solution
</summary>

The data in `nordic_2$lifeExp` is stored as factors rather than numeric. This is because of the “or” character string in the third data point. “Factor” is R’s special term for categorical data. We will be working more with factor data later in this workshop.

</details>

<br>
<br>
<br>
<br>
 
 ### Exercise 3.2 
 There are several subtly different ways to call variables, observations and elements from data frames:
 
 ```
1. nordic[1]
 2. nordic[[1]]
 3. nordic$country
 4. nordic["country"]
 5. nordic[1, 1]
 6. nordic[, 1]
 7. nordic[1, ]
```
 Try out these examples and explain what is returned by each one.
 
 Hint: Use the function `class()` to examine what is returned in each case.

<details>
<summary>Solution
</summary>

1. `nordic[1]` =  We can think of a data frame as a list of vectors. The single brace [1] returns the first slice of the list, as another list. In this case it is the first column of the data frame.

2. `nordic[[1]]` = The double brace [[1]] returns the contents of the list item. In this case it is the contents of the first column, a vector of type factor.

3. `nordic$country` = This example uses the $ character to address items by name. coat is the first column of the data frame, again a vector of type factor. 

4. `nordic["country"]` = Here we are using a single brace ["country"] replacing the index number with the column name. Like example 1, the returned object is a list.

5. `nordic[1, 1]` = This example uses a single brace, but this time we provide row and column coordinates. The returned object is the value in row 1, column 1. The object is an integer but because it is part of a vector of type factor, R displays the label “Denmark” associated with the integer value.

6. `nordic[, 1]` = Like the previous example we use single braces and provide row and column coordinates. The row coordinate is not specified, R interprets this missing value as all the elements in this column vector.

7. nordic[1, ] = Again we use the single brace with row and column coordinates. The column coordinate is not specified. The return value is a list containing all the values in the first row.

</details>

<br>
<br>
<br>
<br>

### Exercise 4.1

You can create a new data frame right from within R with the following syntax:

`df <- data.frame(id = c("1", "b", "c"),
                 age = c("12", "18", "21"),
                 likesPizza = c(TRUE, TRUE, FALSE),
                 stringsAsFactors = FALSE)`
                 
Make a data frame that holds the following information for yourself:

* first name
* last name
* lucky number

Then use `rbind` to add a new entry.
Finally, use `cbind` to add a column with each person’s answer to the question, “Do you like pizza?”

<details>
<summary>Solution
</summary>

```
df <- data.frame(first = c("Grace"),
                 last = c("Hopper"),
                 lucky_number = c(0),
                 stringsAsFactors = FALSE)
df <- rbind(df, list("Marie", "Curie", 238) )
df <- cbind(df, likesPizza = c(TRUE, TRUE))
```

</details>

<br>
<br>
<br>
<br>

### Exercise 5.1
Given the following code:

```
x <- c(5.4, 6.2, 7.1, 4.8, 7.5)
names(x) <- c('a', 'b', 'c', 'd', 'e')
print(x)
```

Come up with at least 3 different commands that will produce the following output:
```
  b   c   d 
6.2 7.1 4.8 
```

<details>
<summary>Solution
</summary>

```
x[2:4]
x[-c(1,5)]
x[c("b", "c", "d")]
x[c(2,3,4)]

```

</details>

<br>
<br>
<br>
<br>

### Combining logical conditions

* `&` AND, returns `TRUE` if left and right are `TRUE`
* `|` OR, returns `TRUE` if left or right (or both) are `TRUE` 
* `all` compares all elements within a vector, returns `TRUE` if every element is `TRUE`
* `any` compares all elements within a vector, returns `TRUE` if one or more element is `TRUE`
<br>
<br>

(`&&` and `||` only compare first element of a vector and ignore the rest.)

<br>
<br>
<br>
<br>

### Exercise 5.2
Given the following code:
```R
x <- c(5.4, 6.2, 7.1, 4.8, 7.5)
names(x) <- c('a', 'b', 'c', 'd', 'e')
print(x)
```
Write a subsetting command to return the values in x that are greater than 4 and less than 7.

<details>
<summary>Solution
</summary>

```
x_subset <- x[x<7 & x>4]
print(x_subset)

```

</details>

<br>
<br>
<br>
<br>

### Handling special values
* `is.na` will return all positions in a vector, matrix, or data frame containing `NA` (or `NaN`)
* likewise, `is.nan`, and `is.infinite` will do the same for `NaN` and `Inf`
* `is.finite` will return all positions in a vector, matrix, or data.frame that do not contain `NA`, `NaN` or `Inf`.
* `na.omit` will filter out all missing values from a vector

<br>
<br>
<br>
<br>

### Exercise 5.3 
Fix each of the following common data frame subsetting errors:

1. Extract observations collected for the year 1957: `gapminder[gapminder$year = 1957, ]`    
2. Extract all columns except 1 through to 4: `gapminder[, -1:4]`   
3. Extract the rows where the life expectancy is longer the 80 years: `gapminder[gapminder$lifeExp > 80]`   
4. Extract the first row, and the fourth and fifth columns (`lifeExp` and `gdpPercap`): `gapminder[1, 4, 5]`     

<details>
<summary>Solution
</summary>

```
1. gapminder[gapminder$year == 1957, ]
2. gapminder[,-c(1:4)]
3. gapminder[gapminder$lifeExp > 80,]
4. gapminder[1, c(4, 5)]
 
```

</details>

<br>
<br>
<br>
<br>

### Exercise 6.1
Write a single command (which can span multiple lines and includes pipes) that will produce a dataframe that has the African values for `lifeExp`, `country` and `year`, but not for other Continents. 
<details>
<summary>Solution
</summary>

```
year_country_lifeExp_Africa <- gapminder %>%
                           filter(continent=="Africa") %>%
                           select(year,country,lifeExp)
 
```

</details>

<br>
<br>
<br>
<br>

![](/figures/13-dplyr-fig2.png)
<br>
<br>
<br>
<br>

### Exercise 7.1
Modify the example so that the figure shows the distribution of gdp per capita (`gdpPercap`), rather than life expectancy. 

<details>
<summary>Solution
</summary>

```
ggplot(data = gapminder, aes(x = gdpPercap)) +   
  geom_histogram()
 
```

</details>

<br>
<br>
<br>
<br>

![](/figures/13-dplyr-fig3.png)
<br>
<br>
<br>
<br>

### Exercise 8.1
Subset the gapminder data to include only data points collected since 1990. Write out the new subset to a file.
<details>
<summary>Solution
</summary>

```
gapminder_after_1990 <- filter(gapminder, year > 1990)

write.csv(gapminder_after_1990,
  file = "gapminder-after-1990.csv",
  row.names = FALSE)
 
```

</details>

<br>
<br>
<br>
<br>
