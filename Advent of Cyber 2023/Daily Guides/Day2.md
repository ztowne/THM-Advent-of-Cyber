# Day 2
## Introduction
Here are our objectives for Day 2:
- Get an introduction to what data science involves and how it can be applied to Cybersecurity
- Get a gentle introduction to Python
- Get to work with some popular Python libraries such as Pandas and Matplotlib to crunch data
- Help McHoneyBell establish an understanding of AntartiCrafts' network

Let's get started:

First, I'm going to launch the VM provided by THM, which takes us to Jupyter.
We are given a brief overview (or refresher) on data science and the roles and descriptions of each phase. These include:
- Data Collection
- Data Processing
- Data Mining
- Analysis
- Communication

Some practical, real-world examples of data science in Cybersecurity include:
- SIEMs in order to give a wider understanding of the organizations landscape through the collection of data
- Threat trend analysis to track and understand new and emerging threats
- Predictive analysis to create and model the future threat landscape in order to prevent further attempts

## Jupyter Notebooks
Jupyter notebooks is a program I used quite frequently during my time in school, but having this refresher will be enjoyable. It is an open source documentation platform that can contain code, text, and even terminal functionality.

Our first task, is to read through and get a very basic understanding of Python. We learn different data types, variables, lists, and even importing libraries.

## Pandas
Pandas is a Python libray that is used to work with data sets. We can manipulate, analyze, explore, and clean data sets using Pandas.

### Series

A series is a data structure within Pandas that uses a key-value pair. The key acts as the indices and the values is data stored within the index:

![Alt text](/Advent%20of%20Cyber%202023/Resources/series.png)

First, we import pandas and refer to is using "pd".

```import pandas as pd```

Next, we create a list to contain our data:

```transportation = ['Train', 'Plane' , 'Car']```

We then turn this list into a series:

```transportation_series = pd.Series(transportation)```

### DataFrames
DataFrames can extend a series.DataFrames a grouping of series, like a spreadsheet or database. It is two-dimensional, and includes rows and columns.

First, we create a two-dimensional list:

```data = [['Ben', 24, 'United Kingdom], ['Jacob', 32, 'USA'], ['Alice', 19, 'Germany']]```

Now, we can create a variable to store the DataFrame:

```df= pd.DataFrame(data, columns=['Name', 'Age', 'Country of Residence'])```

Once printed, we get the following output:

![Alt text](/Advent%20of%20Cyber%202023/Resources/DataFrames.png)

### Grouping

Grouping in Pandas allows us to group our data into categories, such as:

- Grouping Columns
- Grouping Rows
- Comparing

Back in our lab environment, we can load the .csv file (awards.csv) provided in the VM:

```df = pd.read_csv("awards.csv)```

![Alt text](/Advent%20of%20Cyber%202023/Resources/awards.png)

Once loaded, we will group the columbs by "Department" and "Prize":

```df.groupby(['Department'])['Prize'].sum()```

We use sum to return the sum values of each column:

![Alt text](/Advent%20of%20Cyber%202023/Resources/sum.png)

We can also use the describe function to give a breakdown of each column by percentiles

```df.groupby(['Department'])['Prize'].describe()```

![Alt text](/Advent%20of%20Cyber%202023/Resources/describe.png)

## Matplotlib

Matplotlib allows us to create various plots and graphs. In this section, we will create our first plot. First, we need to import both pandas and matplotlib:

```import pandas as pd```
```import matplotlib.pyplot as plt```

We can create out first plot and add data into it below, remembering that we start with the X axis and then the Y axis:

```plt.plot(['January', 'February', 'March'], [8,14,23,40])```

![Alt text](/Advent%20of%20Cyber%202023/Resources/plot1.png)

However, our graph does not have labels on the Axis, so it's hard to tell what this graph means. Below, we will create a new plot with the axis labeled, as well as give our graph a title:

```plt.xlabel('Months of the Year)```
```plt.ylabel('Number of Toys Produced)```

```plt.title("A Line Graph Showing Toys Produced Between September and December)```

```plt.plot(['September','October,'November','December'],[8,14,80,160])```

![Alt text](/Advent%20of%20Cyber%202023/Resources/plot2.png)

### Bar Graph
 Using matplotlib, we can also create bar graphs in order to compare data points.

THM has given us a .csv file (drinks.csv) in order to create this bar graph.

We use pandas to import the spreadsheet:

```spreadsheet = pd.read_csv('drinks.csv')```

Next, we extract the columns 'Drinks' and 'Vote' and assign them to their own variables

```drinks = spreadsheet['Drink']```

```votes = spreadsheet['Vote']```

Next, we will use pyplot to determine the size of the figure:

```plt.figure(figsize=(10,6))```

Here, we will use plt.bar to tell pyplot to use a bargraph. We add an 'h' to use a horizontal bar graph.

```plt.barh(drinks, votes, color = 'skyblue')```

And finally, we will set the labels for our graph: 

```plt.xlabel('Number of Votes')```

```plt.ylabel('Name of Drink)```

```plt.title('A Bar Graph Showing the Employees Favourite Drinks')```

```plt.gca().invert_yaxis``` (Optional, inverts the y-axis for better readability)

![Alt text](/Advent%20of%20Cyber%202023/Resources/bargraph.png)



# Capstone

In this capstone, we will be using the skills and tools we learned to analyze a supplited packet captuer and answer the following questions:
- How many packets were captured?
- What IP address sent the most amount of traffic during the packet capture?
- What was the most commonly used Protocol?

To answer the first questiom, we simply use the Pandas count function on the dataframe:

```df.count()```

![Alt text](/Advent%20of%20Cyber%202023/Resources/day2q1.png)

We find our answer of 100 packets.

Next, we use a groupby and the Pandas size function to find the Source IP address who sent the most amount of traffic:

```df.groupby(['Source]).size()```

![Alt text](/Advent%20of%20Cyber%202023/Resources/day2q2.png)

We find our answer of 10.10.1.4.

Last, we use the Pandas values.counts on the Protocol column to find which protocol was the most commonly used:

```df['Protocol'].value_counts()```

![Alt text](/Advent%20of%20Cyber%202023/Resources/day2q3.png)

We find our last answer of ICMP.


# Conclusion

On Day 2, we got a hands on, brief explanation of Python for Data Science, as well as an introduction to Pandas and matplotlib. We processeced and analyzed data, as well as created a line and bar graph in order to make the data easily digestable. This was a fun way to refresh on these topics and use these tools in a real world setting.
