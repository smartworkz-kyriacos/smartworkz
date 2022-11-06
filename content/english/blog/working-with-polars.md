+++
author = ""
bg_image = "/images/banner/banner-1.jpg"
categories = ["Pandas", "Polars"]
date = 2022-11-05T23:00:00Z
description = ""
image = "/images/polars.png"
tags = []
title = "Working with Polars"
type = "post"

+++
### **Installing Polars**

Polars can be installed using Pypi using the following code:

    pip install polars

### **Importing libraries**

Polars offers many functionalities that are similar to Pandas, so it won’t be a problem for anyone to switch over.

    import polars as pl
    import matplotlib.pyplot as plt
    %matplotlib inline

### **Loading Dataset**

    data = pl.read_csv("../winemag-data_first150k.csv")
    print(type(data))
    > <class 'polars.frame.DataFrame'>

Let us start with a basic Data Analysis.

### **Getting familiar with the dataset**

    data.shape
    > (150930, 11)

    data.columns

![columns ](https://editor.analyticsvidhya.com/uploads/96375p7.png =406x192)

    data.dtypes

![dtypes](https://editor.analyticsvidhya.com/uploads/29576p8.png =497x196)

    data.head()

![data.head pplars](https://editor.analyticsvidhya.com/uploads/75570p9.png =1078x573)

As you can see this is a huge dataset. with over 11 columns and 150k+ entries, we have a lot of data to analyze. The columns I am interested in are Country, points, and price. Let us see what we can find.

### **Null Values**

Before moving forward we have to take care of the null values if present. We can find the null values easily using _null_count()_.

    data.null_count()

![null values](https://editor.analyticsvidhya.com/uploads/63342p10.png =884x98)

Therefore around 13.5k entries are missing values for the price column. We can either drop these rows since it’s less than 10% of the whole dataset, but we can put some other value like the _mean_:

    data['price'] = data['price'].fill_none('mean')

### **Performing Analysis**

Now we dig a little deeper and look into some statistical analysis. This can help us gain some insightful knowledge of the dataset.

Our goal is to compare how price and points vary from country to country.

    # Analyses of wine prices
    print(f'Median price: {data["price"].median()}')
    print(f'Average price: {data["price"].mean()}')
    print(f'Maximum price: {data["price"].max()}')
    print(f'Minimum price: {data["price"].min()}')

![median price polars](https://editor.analyticsvidhya.com/uploads/19918p11.png =527x70)

    # Analyses of wine points
    print(f'Median points: {data["points"].median()}')
    print(f'Average points: {data["points"].mean()}')
    print(f'Maximum points: {data["points"].max()}')
    print(f'Minimum points: {data["points"].min()}')

![](https://editor.analyticsvidhya.com/uploads/69478p12.png)

Thus we can see that a wine can be as cheap as 4 dollars but still have great taste. Now let’s see which countries sell wine.

    countries = data['country'].unique().to_list()
    print(f'There are {len(countries)} countries in the list')
    >There are 49 countries in the list

Scrolling through the dataset, we can see that there are 2 strange values in the column _country_. These are an undefined country (“”) and another country called ‘US-France’:

    print(data[(data['country'] == '') | (data['country'] == 'US-France')])

![print data](https://editor.analyticsvidhya.com/uploads/53330p13.png =1015x692)

Since there are just 6 entries with these weird values, so I think it’s safe if we dropped the rows.

    data = data[(data['country'] != '') & (data['country'] != 'US-France')]

Now we look into countries which have the best and the costliest wines.

    #wines with high points
    print(data.groupby('country').select('points').mean().sort(by_column='points_mean', reverse=True))

![wines with high points](https://editor.analyticsvidhya.com/uploads/38521p14.png =646x446)

    #Wines which are costly
    print(data.groupby('country').select('price').max().sort(by_column='price_max', reverse=True))

![costly wines pypolars](https://editor.analyticsvidhya.com/uploads/35986p15.png =587x460)

Thus we can see that England has one of the best wines, but the costliest one is from France.

## Plotting with Polars

It is always nice to have some visualizations to have a better understanding of the data. Pandas have inbuilt plotting capabilities, but the majority use Matplotlib and Seaborn since it’s much easier to use and offers a wider variety of plots.

    top_15_countries = data.groupby('country').select('points').mean().sort(by_column='points_mean', reverse=True)[:15][0]
    df_top15 = pl.DataFrame({'country': top_15_countries}).join(data, on='country', how='left')
    fig, ax = plt.subplots(figsize=(15, 5))
    for i, x in enumerate(df_top15['country'].unique()):
        ax.boxplot(df_top15[df_top15['country'] == x]['points'], labels=[str(x)], positions=[i])
    plt.xticks(rotation=90)
    plt.xlabel('Countries')
    plt.ylabel('Average points')
    plt.show()

![plotting with polars](https://editor.analyticsvidhya.com/uploads/16050p16.png =981x360)

## Endnotes

Polars is still an ongoing project and in the early stages of development. If you are interested in having a more in-depth look at the workings of the library, I highly recommend you to read this article by the creator of Polars himself:

[I wrote one of the fastest DataFrame Libraries | Ritchie Vink](https://www.ritchievink.com/blog/2021/02/28/i-wrote-one-of-the-fastest-dataframe-libraries/)