# Python Extras

## What is this?
The [Software Carpentry Python course](https://swcarpentry.github.io/python-novice-inflammation/) is, in my opinion, exemplary for getting people started with programming in Python - especially for data analytics work. However, there are some things that they don't cover there, which I cover in this document.

## 1. Dictionaries
The Software Carpentry Python materials cover [Lists](https://swcarpentry.github.io/python-novice-inflammation/05-lists/index.html), but not dictionaries. Actually, once you've grasped lists, dictionaries aren't too difficult.

A dictionary is a data structure for which there are _keys_ (typically meaningful text) that each look up some _value_. For example:

```python
english_to_french = {
	"hello": "bonjour"
	, "world": "monde"
	, "goodbye": "au revoir"
}

print(english_to_french["world"])
# prints "monde"
```

A common use case is to use dictionaries to represent instances of objects / records in a database:

```python
planets = []
planets.append(
	{
		"name": "Mercury"
		, "diameter_km": "4879"
	}
)
planets.append(
	{
		"name": "Venus"
		, "diameter_km": "12104"
	}
)

print(planets[1]["diameter_km"])
# prints "12104"
```

## 2. Loading non-numerical CSV data
The Software Carpentry Python materials teach you to import CSV data using `numpy.loadtxt(fname='file.csv', delimiter=',')`. This makes sense if your CSV data is essentially a mathematical matrix - just the numbers - but this kind of CSV file is quite rare. More often, you will have numbers and text mixed together, e.g.

Example file &mdash; `weather_report.csv`
```csv
Day,Temperature,Weather
Monday,16,Rainy
Tuesday,21,Sunny
Wednesday,25,Cloudy
Thursday,22,Rainy
Friday,23,Rainy
```

Let's say you wanted to find the average temperature for this week. You really only need `numpy` to work with the numbers for the "Temperature" column. So how do you get the data in?

```python
import csv
import numpy

temperatures = []
with open('weather_report.csv') as csvfile:
	reader = csv.reader(csvfile, delimiter=',')
	for row in reader:
		possible_temperature = row[1]
		if possible_temperature.isnumeric():
			this_temperature = float(possible_temperature)
			temperatures.append(this_temperature)
	
	# completed
	print(temperatures)
		# [16.0, 21.0, 25.0, 22.0, 23.0]
	print(numpy.mean(temperatures))
		# 21.4
```

There's a few things to unpack here:

1. **We didn't generate a 2D array.** We could have done this if we had declared an array like `csvdata = []` at the start, and then right after `for row in reader:` we had `csvdata.append(row)`.

2. **We had to check each "possible temperature" with `isnumeric()`**. This is a function that runs on every String that returns `True` if the String could be validly interpreted as a number, and `False` if the String couldn't be validly interpreted as a number. This is mostly to filter out the heading (note that we could have instead kept track of the row number to omit the first row).

3. **We had to convert each temperature back into a number.** Otherwise it is interpreted as a String and you won't be able to use it with `numpy.mean(..)`. I chose to use `float(..)` but equally you could have used `int(..)` to get the same result of 21.4.

## 3. Working with numbers: numpy vs pandas

That method of working with CSV files seems a bit messy... what if there were a Python library that could do all of that in one go?

As a matter of fact, there is - `pandas`.

```python
import pandas

df = pandas.read_csv("weather_report.csv")
print(df["Temperature"].mean())
	# 21.4
```