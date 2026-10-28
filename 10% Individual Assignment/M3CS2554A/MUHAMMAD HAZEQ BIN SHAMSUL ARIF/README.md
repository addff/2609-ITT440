# MUHAMMAD HAZEQ BIN SHAMSUL ARIF

**NAME:** MUHAMMAD HAZEQ BIN SHAMSUL ARIF<br>
**STUDENT ID:** 2025130551<br>
**CLASS:** CDCS2554A<br>
**TOPIC:** Fundamental Of Computer Graphic And Visualisation<br>
**COURSE CODE:** ITT 440 - Network Programming<br>
**YOUTUBE LINK:** https://youtu.be/W6HWPMuuwTo<br>

# Basic Data Visualisation Simulator

A Python program that generates sample data and displays it as four basic charts (bar, line, scatter and pie). It was built for **Fundamentals of Computer Graphics and Visualisation**.

## Problem Statement

In computer graphics and visualisation, raw data in tables or lists is hard to read and interpret. Patterns, trends and comparisons are difficult to spot when values are only shown as numbers, which slows down analysis and decision making.

This project applies the fundamentals of computer graphics to present data visually. It generates sample data and converts it into basic charts (bar chart, line chart, scatter plot and pie chart). The aim is to show how each chart type suits a different kind of data, and how graphical representation makes data easier to understand than raw values.

## Literature Review

According to *Computer Graphics: Principles and Practice* (Hughes et al., 2013), computer graphics converts data and geometric descriptions into images on a display. The same pipeline underlies how coordinates and values become the lines, points and shapes of a chart.

Furthermore, Tufte (2001) in *The Visual Display of Quantitative Information* argues that graphical displays should communicate complex ideas clearly and precisely, and that charts should avoid unnecessary decoration. Munzner (2014) in *Visualization Analysis and Design* adds that the choice of visual form (position, length, colour and shape) should match the type of data and the task the viewer needs to perform.

## System Design

The system generates sample data and displays it as basic visualisations. It consists of the following modules:

| Module | Description |
|---|---|
| **Data Generation** | Creates sample datasets: category sales totals, a monthly sales trend, and paired study hours and scores. |
| **Bar Chart** | Compares values across categories using bar length. |
| **Line Chart** | Shows how a value changes over time, with a trend line to make the direction clear. |
| **Scatter Plot** | Plots pairs of values as points to reveal relationships between two variables. |
| **Pie Chart** | Shows each category's share of a whole as a percentage. |

Each chart is given a title, axis labels, a legend and colours so that it can be read without extra explanation. The charts are displayed on screen and saved as image files. The system then prints a short analytics report summarising the data that was visualised.

## System Requirements

| Requirement | Details |
|---|---|
| Operating System | Windows, Linux, or macOS |
| Programming Language | Python 3.8 or above |
| Libraries | Matplotlib and NumPy |
| Processor | Any modern CPU |
| RAM | Minimum 4GB |
| Storage | At least 500MB free space |
| Display | Required to show the charts |

## Installation Steps

### A. Install the libraries

```bash
pip install matplotlib numpy
```

### B. Launch the program

Navigate to the project folder:

```bash
cd data_visualisation_sim
```

Run the program:

```bash
python visualisation_main.py
```

### C. Provide desired input

When prompted, enter the required values. For example:

```
Enter number of data points to generate (example: 12): 12
Choose chart type (bar / line / scatter / pie / all): all
```

Pressing Enter without typing anything uses the defaults (12 data points, all charts).

## Sample Output & Analytic

```
========== DATA VISUALISATION REPORT ==========

Charts generated:
Bar Chart     : Sales by Category (5 categories)
Line Chart    : Monthly Sales Trend (12 months)
Scatter Plot  : Study Hours vs Score (12 points)
Pie Chart     : Category Share (100%)

Total data points visualised: 34
Charts saved as: bar.png, line.png, scatter.png, pie.png

========== ANALYSIS ==========
Highest category : Clothing (8191)
Lowest category  : Electronics (2714)
Total sales      : 29116
Sales trend      : increased by 1132 units over the period
Correlation (r)  : 0.97 (strong positive relationship)
```

### Analysis

- The bar chart makes the highest and lowest categories obvious at a glance.
- The line chart shows the overall trend and any seasonal increase or decrease.
- The scatter plot shows whether two variables are related, in this case a strong positive correlation.
- The pie chart shows each category's percentage of the total, but it becomes hard to read with many categories.

## Conclusion

In conclusion, this project successfully developed a simulator that demonstrates basic data visualisation using computer graphics principles. The results show that graphical representation makes data much easier to understand than raw numbers, because patterns, comparisons and trends become visible immediately.

Each chart type has its own strength: bar charts for comparison, line charts for trends, scatter plots for relationships and pie charts for proportions. This highlights the importance of choosing the right visualisation for the nature of the data, so that information is communicated clearly and accurately.

## References

- Hughes, J. F., et al. (2013). *Computer Graphics: Principles and Practice* (3rd ed.). Addison-Wesley. A standard textbook covering the fundamentals of computer graphics, including how data is turned into images.
- Tufte, E. R. (2001). *The Visual Display of Quantitative Information* (2nd ed.). Graphics Press. A classic book on the principles of clear and effective data graphics.
- Munzner, T. (2014). *Visualization Analysis and Design*. CRC Press. Explains how to choose visual representations that suit the data and the task.
- Hunter, J. D. (2007). Matplotlib: A 2D Graphics Environment. *Computing in Science & Engineering*, 9(3), 90-95. Documentation available at: [https://matplotlib.org/](https://matplotlib.org/)
