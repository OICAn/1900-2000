<h1 style="text-align: center">
    Assignment 1
</h1>

<p style="text-align: right">201618130120 刘杰</p>

## What i want to know?

​	I want know the difference between 1900 and 2000 years at the population structure of the United States. And at the same time, I want to present more information as far as possible.

## How can you get information?

​	Most instructions are listing on the picture, but one thing should pay attention to is the bar of an age is from the bottom to the top. For example,  the bars of 90 years old is the two top-most bars, not the two below them.Broadly, you can have a general comparison between two years. If you want to know more detailed information, like the population of 25 years old women in 1900, you can see at the field of **Female** and look at the **25** on the Y axis, and you will find it is 324 ten thousand(don't forget this "ten thousand").

![1554203948639](C:\Users\OIC-An\AppData\Roaming\Typora\typora-user-images\1554203948639.png)By the way, the data in the picture is all approximate.

## What story i want to tell?

​	Most importantly, i want to have a comparison between 1900 and 2000 on the big side, in another word, a general comparison. So i bring them at a single picture. As you can see, the total population of 2000 is more than twice that of 1900, especially for people over 30 years old.

​	We can see this separately. In 1900, while the red-blue bars in the picture, the population structure is like a Pyramid, which is called "Growing type". As you can see, the younger, the more population. The birth rate is much higher than the mortality rate, and the proportion of adolescents in the population is very large. This type of social population will increase rapidly in a relatively short period of time, so there is no need to worry about the labor force at all. Now this population structure is most seen in developing country, while in 1900, the United States is a rapidly developing country.While in 2000, while the purple-green bars in the picture, the situation is very different. Broadly, it is like a "Pentagon". The most people are middle age.This population structure is between growth and stability. Cause the young population still accounts for a large part, its birth rate is still larger than death rate, and the population is still growing at a faster rate. When you search the Internet for more information, you will find that the United States is the only developed country with a rapidly developing population. But compared with 1900, 2000 is more inclined to a stable demographic structure.

​	Let's see something else. As we can see, in 1900, which are the red-blue bars in the picture, the red bars and the blue bars are almost the same length, which means that the same age group of male and female is almost equal. But when we look at 2000, which are the purple-green bars in the picture, it turns different. As we can see, in the 0-25 age, male are always more than female with a number that isn't too small. But when after 35 years old, female are always more than male at the same age, especially after 70, the population of female is nearly 1.5 times that of male  by average. So we can draw a conclusion that the US population has a certain gender imbalance in the same age. Newborns are more likely male, maybe it has something to do with environment, culture, male physical condition or something else. Want to avoid this situation getting worse, people in the US have to pay attention to it, do some research and take some actions. While most long-lived people are more likely to be female, maybe because of physical differences, but it may be more relevant to other reasons like pressure of life, job categories, lifestyle or more important, the value of health. In a word, male should pay attention to this phenomenon and pay attention to their own health!

​	Finally, we can see the bar charts in the same row as a whole, which means to the whole population at an age in 1900 or 2000. And we can easily find that in each age, the population of 2000 is more than 3 times that of 1900, and this corresponds to our previous analysis named "Growing type" population.

## How i program this picture?

​	In this picture, I create 3 linear scales and axis, they are vertical, left and right. The vertical one is for age, the left one is for female and the right one is for male. Using the right one as an example, its domain is as follows:

```javascript
.domain([d3.min(dataset, function (d) {
								return d.People;
							}), d3.max(dataset, function (d) {
								return d.People;
							})])
```

``dataset`` is the data from ``.csv`` file, which is saved as an object array and ``d.People`` is the population.

Its range is :

```javascript
.range([0, (width/2 - 2 * padding)])
```

``width`` is the width of my ``svg`` and ``padding`` is for blank.Last i use ``nice()`` to make it beautiful.And the left one is opposite.

​	For the most important elements——bar chars, i use 

```javascript
svg.selectAll("rect")
				.data(dataset)
				.enter()
				.append("rect")
```

to bind my dataset.For each bar chart, it has a different ``x``coordinate

```javascript
.attr("x", function (d) {
					return d.Sex==1 ? (width/2) : 2*padding + xScale_left(d.People);
				})
```

``d.Sex==1``means this is the population of male, so i let its ``x``be``width/2``, and this bar chart will grow to the right, male field.The female is more complex, i will explain it later.

​	Its ``y``coordinate:

```javascript
.attr("y", function (d) {
					return d.Year==1900 ? yScale(d.Age) - 28 : yScale(d.Age) - 48;
				})
```

​	I set the ``height`` to 18

```javascript
.attr("height", 18)
```

​	And for its ``width``, if it is for male, then map it to right scale directly. But when it is for female, it turns to be more different, cause the left scale's direction is opposite to the right one. For each left bar chars, it should be aligned right at ``width/2``.So its ``width + x`` should be equal to ``width/2``. So we can get the width:

```javascript
.attr("width", function (d) {
					return d.Sex==1 ? xScale_right(d.People) : width/2-2*padding-xScale_left(d.People);
				})
```

​	As for its color, it is relevant to its year and sex:

```javascript
.attr("fill", function (d) {
					return d.Sex==1 ? d.Year==1900 ? "blue" : "green" : d.Year==1900 ? "red" : "purple"; 
				});
```

​	The bar charts are finished.Then i should add some text tags for more information, and each text's position, content are relevant to the data:

```javascript
svg.selectAll("text")
				.data(dataset)
				.enter()
				.append("text")
```

The content of the text can be the population itself:

```javascript
.text(function (d) {
					return Math.round(d.People);
				})
```

The ``x``coordinate of text, when it is for the male, can directly be ``width/2``(but for some very small number, the rectangle's width will be so small that can's be put into the rectangle, so it needs some operations else), but when it is for the female it is more complex. Every text should align right end at  ``width/2`` , but every letter has a width, if the number is big which include more than three letters, its whole width is big, while the number is small, it turns to be small. Fortunately, after many trials, i get the width of a letter, so i can determine the ``x`` of each text, like follows:

```javascript
.attr("x", function (d) {
					//较复杂的逻辑判断，主要是不同情况的x值不同
					return d.Sex==1 ? d.People<40 ? width/2+xScale_right(d.People) : width/2 : d.People<40 ? 2*padding+xScale_left(d.People)-16 : d.People<100 ? width/2 - 16 : d.People<1000 ? width/2 - 24 : width/2 - 32;
				})
```

and for ``y`` :

```javascript
.attr("y", function (d) {
					return d.Year==1900 ? (yScale(d.Age) - 28 + 14) : (yScale(d.Age) - 48 + 14);
				})
```

Finally set the style of font:

```javascript
.attr("font-family", "sans-serif")
			   	.attr("font-size", "13px")
			   	.attr("fill", function (d) {
			   		return d.People >= 40 ? "white" : "black";
			   	});
```

​	Above is the main and complex work i have done for this project, the other is basal and i don's want to spreed time explaining it!