+++
date = 2021-03-27T07:00:00Z
description = "Processes, PubSub, LiveView, oh my!"
draft = true
image = "/images/chefshop_order_delivery_flow.png"
title = "[Chefshop] Designing an order-to-delivery system"

+++
# Short overview of the Ordering and Delivery system design of ChefShop

> GASP< why disclose the inner workings of ChefShop?!

First of all, working name. ChefShop is just a codename for the product.

Second of all, it's not really the actual product. The actual business is my cottage shop of selling homemade snacks for remote workers who have to use their brain a lot. This is going to just drive that business because I'm intentionally obtuse in this area and dumb enough to spend the time to build my custom Shopify. Plus I'm not quite sure if it even supports the features I want it to do, in that I want to control the rate of ordering and space it out.

Third, why create this business entirely when COVID19 resolution is just around the corner? Kinda banking on the idea that more companies will offer more remote work.

And my final reason - I like the language (Elixir), and I am using this to learn its inner workings.

## Diagram Overview

Let's look at a diagram I drew up. Yes it's hand-drawn. Close your eyes a bit and think like you are in a startup. It's half of a business complex floor. Glass walled rooms with whiteboards ordain the walls as you walk. You see me. I wave you in. You look across at the whiteboards. And there you see this:

![Order and Delivery System Diagram.png](/images/chefshop_order_delivery_flow.png "Order and Delivery System Diagram")