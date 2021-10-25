+++
date = 2021-08-10T07:00:00Z
description = "Date Selection got a new update - simplified view!"
image = "/images/pexels-senne-hoekman-1178498.jpg"
title = "[Chefshop] Date Selection UI Update"

+++
I got feedback and some ideas for simplifying the view of the date selection.

Normally what I did was basically grab some code off the web to use TailwindCSS to render an entire month. It had code to traverse months, and basically had an entire set of features that a normal "calendar" UI would have.

However, my discussion with an UI expert friend of mine led me to see the light.

I need to just show what's only necessary to the user. Simplify. 

The user only needs to know the days in which the user can select, and not really see a whole lot of days in which user cannot select.

Now in addition to that, for simplicity I decided to show the entire week of the date of first availability. That way, it's easier to render a subset of the calendar - since they always start on the start day of the week.

So here's before:

![](https://i.imgur.com/avJysq4.jpg)

And here's after the UI update:

![](https://i.imgur.com/Ootv5Tq.png)

Much.Simpler. 