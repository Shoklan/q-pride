---
aliases:
- /Data Vis/Data Visualization/Python/sreamlit/2021/02/04/Introduction-To-Streamlit
categories:
- Python
- Data Vis
- Data Visualization
- sreamlit
date: '2021-02-04'
description: You should use this framework too.
layout: post
title: Quick Introduction to Streamlit
toc: true

---

# What is Streamlit?
Streamlit is a Framework which is growing in popularity among Python Data Scientists. And the reason for this is rather obvious if you've every tried mixing building Web Applications with Data Exporation or Reporting for other people inside your company - or just in general for your projects.

We're going to build a little Web App with the Iris dataset. And yes, please don't groan; I am also sick of the Iris dataset but it's clean and simple and accessible.

So, first we start with actually downloading and it's just their namesake:
```bash
python -m pip install streamlit
```

Next we'll import and scaffold off their introduction application:
```python
import streamlit as st
# To make things easier later, we're also importing numpy and pandas for
# working with sample data.
import numpy as np
import pandas as pd

st.text("Hello World")
```
... and save that to a file called `irisExplore.py`. Once that's done, you'll run it from the console using `streamlit run irisExplore.py`:
![Hello World Streamlit!]({{ site.baseurl }}/images/intro-to-streamlit/hello-world-streamlit.png)

And there we have our first Web Application. Calling the HTML elements that you want and expect is just that simple. If you want a paragraph then you'll use `st.title()`:
```python
st.title("Turtles Turtles Turtles")
st.subheader("But, which movie reference is it?")
st.text("Hello World")
```
![Hello World, Now With Titles!]({{ site.baseurl }}/images/intro-to-streamlit/hello-world-with-titles.png)
And, there is even support for Latex too!:
```python
st.title("Turtles Turtles Turtles")
st.subheader("But, which movie reference is it?")
st.text("Hello World")
st.latex(r'''
    a + ar + a r^2 + a r^3 + \cdots + a r^{n-1} =
    \sum_{k=0}^{n-1} ar^k =
    a \left(\frac{1-r^{n}}{1-r}\right)
    ''')
```
![Hello World, Now With Latex!]({{ site.baseurl }}/images/intro-to-streamlit/hello-world-with-latex.png)

## Now with Data!
So, now we're going to take a quick look at how to add charts. First we're going to collect the iris dataset. If you have R or something like Tensorflow installed then you can import it from there. I'm going to collect it from someone random on the internet. You should probably download this dataset and store it somewhere; you'll never get away from it.
```python
import pandas as pd
iris = pd.read_csv("https://datahub.io/machine-learning/iris/r/iris.csv")
```
Adding the chart is just as easy and you would expect:
```python
st.line_chart(iris)
```
![Iris Squiggles]({{ site.baseurl }}/images/intro-to-streamlit/iris-squiggles.png)
Not exactly what we're looking but it's kind of neat.
This should serve as a reminder that when it comes to tools, it's **Garbage In, Garbage Out** for what you feed it.
Be careful what you're doing because our tools wont know any better.
