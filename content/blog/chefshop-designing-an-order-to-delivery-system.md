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

Fourth, why share all of this? I'm a huge proponent of building in the open. I think it causes interesting conversations. I'm not that popular in the Internet. Only my friends read this blog. Not much is gained by copying this structure because it's pretty simple. You can't really start a successful business with a cottage industry anyways. The goal was to be ramen profitable. The concept isn't new - in fact, it's hashed, rehashed countless times. I hope to add to the Elixir trove of text content in efforts to broaden the adaptation of Elixir.

And my final reason - I like the language (Elixir), and I am using this to learn its inner workings.

## Diagram Overview

Let's look at a diagram I drew up. Yes it's hand-drawn. Close your eyes a bit and think like you are in a startup. It's half of a business complex floor. Glass walled rooms with whiteboards ordain the walls as you walk. You see me. I wave you in. You look across at the whiteboards. And there you see this:

![Order and Delivery System Diagram.png](/images/chefshop_order_delivery_flow.png "Order and Delivery System Diagram")

Being the CTO, but oblivious to the inner workings of Elixir, you cringe. You wince. You look at me, and being the seasoned veteran of the software industry - you start thinking out loud.

### "Too many lines and too many boxes and circles. What is this and why is it too complicated looking?"

I start out explaining.

### Go and read some basics of Elixir

No I won't go into the details of Elixir and its main concepts, which are basic core concepts of its underlying language and system, Erlang and the OTP. Please [look](https://elixir-lang.org/getting-started/mix-otp/genserver.html) at [these](https://ferd.ca/the-zen-of-erlang.html) materials if you really want to know, but I will share one video by Saša Jurić who will summarize things very nicely:

{{< youtube [JvBT4XBdoUE](https://www.youtube.com/watch?v=JvBT4XBdoUE&fbclid=IwAR2NKJoVNH_8Atoo3MyN74rtE9Uqrf-oMG0URo8qxOsvnfhXJs-CAmwnJRQ) >}}

Watch that and let's continue - I'm gonna assume now from now on you kinda know how it works. Just understand what GenServers/Processes are, and how it behaves in Elixir. I'll add in the rest below.

### What it does

Over the flow of a user ordering some food, you will end up creating an Order. Then, the order will somehow turn into a Delivery. Well during cooking and delivering, you know you can handle only a set quote of food. You can't make gazillion tacos when your grill can only make six per hour. So there's the concept of delivery slots. 

Delivery slots show that during time range X, I can only take Y orders. I can set a weekly schedule of time slots and max cap for each time slots. Users who order has to select that. Let's call this **delivery time slots**.

Orders and Deliveries have state fields in its concept or schema or columns (at this level of discussion they all mean the same thing - attributes of the concept).

### Desired Interactions/Goals of the system

* Order gets created
  * This decrements available time slots count for that time where order is set to deliver.
* Order status goes from **"start prepping" to "prepped"**
  * This will in turn create a new Delivery (its status is **"pending"** )
* Owner of restaurant looks at the Deliveries dashboard, finds the new pending delivery. Owner then assigns it to a driver and sets its status to  **"assigned".**
* Driver picks up the delivery, drops it off to the destination, and updates the status in the delivery updating portal, to **"completed"**.
  * This increments available time slots count for that time slot.
* User cancels an order
  * This increments available time slots count for that time slot.
* Time slots have a "latest time to order" time limit. After that time limit, user can't order for that time slot.
  * Available time slots have to update based on time so users can always select from the appropriate time slots

In addition, some aspects of the business flow are crucial and should be interactive in the browser - as in data should update without refreshing the page. They are:

* Ordering process, time slots selection phase
  * Because user can be slow, hit the threshold for "latest time to order". But you have to prevent that - so whenever the time slot updates, the ordering view in the web page has to interactively update, without triggering a web refresh.
* Shop owner has two dashboards that he uses, that has to be interactive and show updated information in "soft real-time" (term in Erlang which means it aims for fault-tolerant, not faultless in its system). So this has to be updated without page refresh.

### How'd you design it in non-Elixir, OOP-based systems approach?

Let's use Golang as the main language, because it's one of the most bare-metal languages I know.

As you see above, there are concepts that has to reliably update based on changes of other concepts. For example, Order state change triggers Delivery / Delivery Time Slot selections. Delivery state changes triggers Delivery Time Slot selections. Time - literally time - also updates Delivery Time Slot selections. 

Then, we have the frontend issue of web pages having to update reliably and near real time whenever these concept updates happen.

There are two ways to build this. Let me explain and their pros/cons:

* Create microservices, and in each of them call other related microservices directly via some communications protocol (REST / gRPC as the most common for internal communications)
  * Pros: simpler than the approach below, less working pieces
  * Cons: too interlocked. If those services fail, ripple effect and eventual systems diagnostics/ recovery is not going to be fun. If Order microservice fails, we don't know where it failed - 
* 