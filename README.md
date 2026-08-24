# CRISP-DM Phase 4: Visualizing Netflix Weekly Data. Part 3: Pie Charts, Box Plots, and Scatter Plots

In the previous tutorial, we used bar charts and histograms to compare titles and examine frequency distributions. We will now analyze audience proportions, compare data distributions, identify outliers, and examine relationships between numerical variables.

## Learning objectives

By the end of this tutorial, you will be able to:

* create interactive pie charts, box plots, and scatter plots with Plotly Express;
* analyze proportions, distributions, outliers, and relationships; and
* generate business insights from the visualizations.

## Business question

Our merchandising company needs to understand how the Netflix audience is distributed among categories, identify typical and exceptional audience values, and examine the relationship between weekly views and viewing hours. This information can support the identification of promising merchandising opportunities.

---

## 1. Load the libraries

We will use Pandas to handle the data and Plotly Express to create interactive visualizations.

```python
import pandas as pd
import plotly.express as px
```

---

## 2. Load the cleaned dataset

Upload `netflix_weekly_clean.csv` to Google Colab and load it into a pandas DataFrame.

```python
df = pd.read_csv("netflix_weekly_clean.csv")
```

Change the format of the data:

```python
category_names = {
    1: "Films (English)",
    2: "Films (Non-English)",
    3: "TV (English)",
    4: "TV (Non-English)"
}
df["week"] = pd.to_datetime(df["week"])
df["year"] = df["week"].dt.year
df["category_name"] = df["category"].map(category_names)
df.info()
```

---

## 3. Pie chart: Audience share by category

A pie chart shows how a total is divided among a small number of categories.

We will calculate the accumulated `weekly_views` for each Netflix category. Plotly will use these values to calculate the percentage represented by every category.

* **Slices:** `category`
* **Values:** Total `weekly_views`
* **Business question:** What percentage of the total Netflix audience corresponds to each content category?

Group the records by `category` and calculate the sum of `weekly_views`:

```python
category_audience = df.groupby("category", as_index=False)["weekly_views"].sum()
print(category_audience)
```

For example, if the original DataFrame contains the following records:

```text
   category  show_title  weekly_views
0         1     Movie A            10
1         1     Movie B             8
2         2     Movie C             6
3         3      Show D            12
4         3      Show E             7
5         3      Show F             5
6         4      Show G             9
7         4      Show H             4
```

After executing the code, we will obtain:

```text
   category  weekly_views
0         1            18
1         2             6
2         3            24
3         4            13
```

Now, create the pie chart:

```python
fig_pie_1 = px.pie(
    category_audience,
    names="category",
    values="weekly_views",
    title="Netflix Audience Share by Category",
    labels={
        "category": "Category",
        "weekly_views": "Total Views"
    }
)
fig_pie_1.update_layout(title_x=0.5)
fig_pie_1.show()
```

### Tuning the graph

The `pie` function accepts the following optional arguments:

`hole=0.4` creates an empty space in the center and transforms the pie chart into a donut chart.

`color_discrete_sequence=px.colors.qualitative.Pastel` changes the color palette used for the category lines. Other options include `Plotly`, `D3`, `Bold`, `Set1`, `Set2`, `Set3`, `Dark24`, `Light24`, `Safe`, and `Vivid`.

`opacity=0.8` controls the transparency of the slices.

`width=900` and `height=600` control the dimensions of the chart.

`template="plotly_dark"` applies a visual theme. Available options include `'ggplot2'`, `'seaborn'`, `'simple_white'`, `'plotly'`, `'plotly_white'`, `'plotly_dark'`, `'presentation'`, `'xgridoff'`, `'ygridoff'`, `'gridon'`, and `'none'`.

Use the following instruction to display the category and percentage inside each slice:

```python
fig_pie_1.update_traces(textposition="inside", textinfo="percent+label", textfont_size=14,
    insidetextorientation="horizontal")
```

The `update_traces` function accepts the following optional arguments:

`textposition="inside"` places the text inside the slices. Other options are `"outside"`, `"auto"`, and `"none"`.

`textinfo="percent+label"` displays the percentage and category name. Other options include `"label"`, `"percent"`, `"value"`, and their combinations.

`insidetextorientation="horizontal"` controls the orientation of the text inside the slices. Other options are `"radial"`, `"tangential"`, and `"auto"`.

---

## 4. Multiple pie charts: Audience share by category and year

An pie chart shows how the proportion represented by each category changes between different periods.

We will calculate the accumulated `weekly_views` for every combination of `year` and `category`.

* **Slices:** `category`
* **Values:** Total `weekly_views`
* **Facets:** `year`
* **Business question:** How has the audience share of each Netflix category changed over the years?

Group the records by `year` and `category`, and calculate the sum of `weekly_views`:

```python
year_category_audience = df.groupby(["year", "category"],as_index=False)["weekly_views"].sum()
print(year_category_audience)
```

For example, if the original DataFrame contains the following records:

```text
         week  year  category  show_title  weekly_views
0  2025-08-03  2025         1     Movie A            10
1  2025-08-10  2025         1     Movie B             8
2  2025-08-03  2025         3      Show C            12
3  2026-08-02  2026         1     Movie D             6
4  2026-08-02  2026         3      Show E             7
5  2026-08-09  2026         3      Show F             5
```

After executing the code, we will obtain:

```text
   year  category  weekly_views
0  2025         1            18
1  2025         3            12
2  2026         1             6
3  2026         3            12
```

Now, create the pie charts:

```python
fig_pie_2 = px.pie(
    year_category_audience,
    names="category",
    values="weekly_views",
    facet_col="year",
    title="Netflix Audience Share by Category and Year",
    labels={
        "category": "Category",
        "weekly_views": "Total Views",
        "year": "Year"
    }
)

fig_pie_2.update_layout(title_x=0.5)
fig_pie_2.show()
```

### Tuning the graph

The faceted pie chart accepts the same optional arguments used in the previous pie chart.

`facet_col="year"` creates a separate pie chart for each year.

`facet_col_wrap=3` arranges the pie charts in rows containing a maximum of three charts.

`color_discrete_sequence=px.colors.qualitative.Pastel` changes the color palette used for the category lines. Other options include `Plotly`, `D3`, `Bold`, `Set1`, `Set2`, `Set3`, `Dark24`, `Light24`, `Safe`, and `Vivid`.

`template="plotly_dark"` applies a visual theme. Available options include `'ggplot2'`, `'seaborn'`, `'simple_white'`, `'plotly'`, `'plotly_white'`, `'plotly_dark'`, `'presentation'`, `'xgridoff'`, `'ygridoff'`, `'gridon'`, and `'none'`.

Personalize the graph using the `update_traces` function:

```python
fig_pie_2.update_traces(
    textposition="inside",
    textinfo="percent+label",
    textfont_size=14,
    insidetextorientation="horizontal",
    pull=[0.1, 0, 0, 0],       # Separates the first slice
    rotation=90,               # Rotates the chart 90 degrees
    direction="clockwise",     # Sets the slice direction
    sort=False,                # Preserves the original category order
    marker_line_color="black", # Adds black borders
    marker_line_width=2
)
```

---

## 5. Box plot: Distribution of weekly audience

A box plot summarizes the distribution of a numerical variable.

Each record represents the audience accumulated by one title during one week. The box plot will show the typical weekly audience, its dispersion, and possible unusually successful titles.

* **X-axis:** Not used
* **Y-axis:** `weekly_views`
* **Business question:** What is the typical weekly audience of Netflix Top 10 titles, and are there unusually high audience values?

Create the box plot using the original weekly records:

```python
fig_box_1 = px.box(
    df,
    y="weekly_views",
    title="Distribution of Weekly Netflix Audience",
    labels={
        "weekly_views": "Weekly Views"
    }
)

fig_box_1.update_layout(title_x=0.5)
fig_box_1.show()
```

The boxplot displays:

* **Q1:** the value below which 25% of the observations are located;
* **Median:** the central value of the distribution;
* **Q3:** the value below which 75% of the observations are located;
* **Lower and upper fences:** the lowest and highest values that are not considered outliers; and
* **Outliers:** unusually low or high observations located outside the fences.

### Tuning the graph

The `box` function accepts the following optional arguments:

`orientation="h"` creates a horizontal boxplot. You must change `y="weekly_views"` for `x="weekly_views"`.

`points="outliers"` displays only the values outside the fences. Other available options are `"all"` and `False`.

`width=900` and `height=600` control the dimensions of the chart.

`template="plotly_dark"` applies a visual theme. Available options include `'ggplot2'`, `'seaborn'`, `'simple_white'`, `'plotly'`, `'plotly_white'`, `'plotly_dark'`, `'presentation'`, `'xgridoff'`, `'ygridoff'`, `'gridon'`, and `'none'`.

Use the following instruction to display the mean and change the color:

```python
fig_box_1.update_traces(boxmean=True, fillcolor="#E50914", line_color="#B20710", marker_color="#AAAA14")
```

---

## 6. Grouped box plot: Weekly audience by category

A grouped box plot compares the distribution of a numerical variable among several categories.

We will compare the median, dispersion, and outliers of `weekly_views` across the four Netflix categories.

* **X-axis:** `category`
* **Y-axis:** `weekly_views`
* **Groups:** `category`
* **Business question:** Which Netflix categories typically attract larger weekly audiences, and which contain exceptionally successful titles?


Create the grouped box plot:

```python
fig_box_2 = px.box(
    df,
    x="category",
    y="weekly_views",
    color="category",
    title="Weekly Netflix Audience by Category",
    labels={
        "category": "Category",
        "weekly_views": "Weekly Views"
    },
    animation_frame="year",
)
fig_box_2.update_layout(title_x=0.5)
fig_box_2.show()
```

### Tuning the graph

The grouped box plot accepts the same optional arguments used in the previous box plot.

`color="category"` assigns a different color to each category.

`points="outliers"` displays only the values outside the fences. Other available options are `"all"` and `False`.

`color_discrete_sequence=px.colors.qualitative.Pastel` changes the color palette used for the category lines. Other options include `Plotly`, `D3`, `Bold`, `Set1`, `Set2`, `Set3`, `Dark24`, `Light24`, `Safe`, and `Vivid`.

`template="plotly_dark"` applies a visual theme. Available options include `'ggplot2'`, `'seaborn'`, `'simple_white'`, `'plotly'`, `'plotly_white'`, `'plotly_dark'`, `'presentation'`, `'xgridoff'`, `'ygridoff'`, `'gridon'`, and `'none'`.

`animation_frame="year"` creates one boxplot frame for each year.

`range_y=[0, df["weekly_views"].max() * 1.1]` maintains the same vertical scale throughout the animation.

Use the following instruction to display the mean in every box:

```python
fig_box_2.update_traces(boxmean=True)
```

---

## 7. Scatter plot: Weekly views and viewing hours

A scatter plot shows the relationship between two numerical variables.

* **X-axis:** `weekly_views`
* **Y-axis:** `weekly_hours_viewed`
* **Business question:** Do titles with more weekly views also accumulate more viewing hours?

```python
fig_scatter = px.scatter(
    df,
    x="weekly_views",
    y="weekly_hours_viewed",
    title="Weekly Views and Viewing Hours",
    labels={
        "weekly_views": "Weekly Views",
        "weekly_hours_viewed": "Weekly Hours Viewed"
    }
)

fig_scatter.update_layout(title_x=0.5)
fig_scatter.show()
```

---

## Expected result

At the end of this activity, you should be able to:

* create interactive pie charts, box plots, and scatter plots;
* analyze proportions, distributions, outliers, and relationships; and
* generate business insights from the visualizations.

---

## Sources

* Netflix, *Top 10*:
  https://www.netflix.com/tudum/top10
* Plotly, *Plotly Express Documentation*:
  https://plotly.com/python/plotly-express/
* CRISP-DM course material, Phase 4: Modeling.
