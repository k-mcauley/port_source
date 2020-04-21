Title: Data Analysis with pandas
Author: Kieran McAuley
Date: 2020-04-14
Category: blogging
Tags: pandas, data analysis, python, annotation

# ``Introduction to Data Analysis with pandas``
To become familiarised with the **pandas library** for use in analysis of data I undertook this small project. As most data seen clinically from previous reports and new dose monitoring software will collate data in a *.csv* or *.dat* file, I examined methods of pulling this data to python utilising pandas. 

## Formatting data
---  
Data obtained from the Met Office Hadley Centre Central England Temperature Data reserve (1772-present), available at Met Office [downloads](https://www.metoffice.gov.uk/hadobs/hadcet/data/download.html), was used in this analysis. 

    :::python3
    import pandas as pd
    from pandas import Series
    import matplotlib.pyplot as plt

    df = pd.read_csv(
        "C:/Users/Kieran/Pandas tutorial/cetml1659on.dat",
        skiprows=4, #only numerical data remains
        delim_whitespace=True, # whitespace separated sep='\s+'
        # unrecorded years use variation of -99 as placeholder
        # must be removed
        # df[df < -50] = None (alternative method)
        na_values=['-99.9', '-99.99'] 
    ) 
    df.tail() #display end of dataset

By examining the average temperature ($^\circ$C) of each year, the following plot was obtained:

![Year_Temp]({static}/img/average_year.png)

While this data does show an obvious increase in the average temperature ($^\circ$C) by year, a clearer result could be obtained by grouping the data.

## Groupby module
---
String manipulation was performed to generate a new column titled 'decade'. Using the groupby module, the mean temperature of each decade was obtained. 

    :::python3
    years = Series(df.index, index=df.index).apply(str)
    decade = years.apply(lambda x: x[:3]+'0')

    df['decade'] = decade
    by_decade = df.groupby('decade').mean()

Plotting the average temperatures by decade produces the following figure: 

![Average Temperature by Decade]({static}/img/average_decade.png)

The results obtained by grouping the data by decade have become considerably clearer. The manipulation of the data was also quite minimal.

## Annotation module
---  

As annotation within python can become complex, the year plot was used to test certain aspects of annotating figures. The plots of January and June average temperatures by year were included. The aim was to annotate the year which contained the January with highest average temperature ("Warmest Winter"). 

    :::python3
    fig, ax = plt.subplots()

    #plotting various average annual and monthly temps
    year_plot = df['JAN'].plot(color="steelblue", ax=ax)
    year_plot = df['JUN'].plot(color="firebrick", ax=ax)
    year_plot = df['YEAR'].plot(color="green", linestyle="--", ax=ax)

    year_plot.set_ylabel(r"Average Temperature ($^\circ$C)")
    year_plot.set_xlabel("Year")
    ax.legend(loc='upper left')
    year_plot.grid(color='black', linestyle='-', linewidth=0.25)
    #LaTex required for use of symbols
    year_plot.set_title(r"Average Temperature ($^\circ$C) vs. Year")

    # Return index of first occurrence of maximum over requested axis
    warm_winter_year = df['JAN'].idxmax()
    warm_winter_temp = df['JAN'].max()

    ax.annotate('Warmest winter',
            xy=(warm_winter_year, warm_winter_temp), xycoords='data',
            xytext=(-100, +30), textcoords='offset points', fontsize=12,
            arrowprops=dict(arrowstyle="->", connectionstyle="arc3,rad=-.2"))

    plt.show()

This considerable block of code produced the annotated figure displayed below:

![Annotation Example]({static}/img/annotation.png)

## Conclusions
---

The above data all appear to show a local increase of the average temperatures by year and decade. This would be expected, as we know there is a global increase in the average temperatures observed worldwide.

The use of python in selection and manipulation in this data analysis was quite simple and removed the need for manual manipulation of data compared to another available software. Although the *annotation module* commonly used to annotate figures can be difficult to implement and may produce large blocks of code, the results are markedly better than what can be achivived in programs such as **Excel**. 

Future work to improve this analysis should examine areas such as error analysis with python and further annotation examples. 