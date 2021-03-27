+++
date = 2021-03-27T07:00:00Z
description = "Processes, PubSub, LiveView, oh my!"
image = "/images/chefshop_order_delivery_flow.png"
title = "Chefshop Designing an order-to-delivery system"

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

There are two ways to build this. 

#### Backend design. Let me explain and their pros/cons:

* Create microservices, and in each of them call other related microservices directly via some communications protocol (REST / gRPC as the most common for internal communications)
  * Pros: simpler than the approach below, less working pieces/microservices to worry about
  * Cons: microservices are coupled too much. For example, Order microservice has to do its order processing, while ALSO calling Delivery/DeliverySlots microservice whenever Order's status gets updated. What if those microservices decided to change their call signatures? Developers have to keep track of which code has to be updated in which microservices.
* Use messaging queue systems for broadcasting Order/Delivery events, by leveraging solutions on whichever cloud platform used - MSK/SQS (AWS), PubSub (GCP). Don't use Azure because why would you.
  * Pros: split responsibility. For example, Order microservice will just process orders, and it just needs to listen in (subscribe) to whichever topic it needs to listen in. Message formats are agreed upon standardized format - enabling a good middle abstract layer, loosely coupling microservices. This allows independent development work to progress without worrying too much about related dependency issues.
  * Cons: In addition to your microservices, now you have to worry about yet another platform resource and its quirks - uptime, latency, delivery SLA (for example GCP's PubSub guarantees delivery but not chronological ordering, but Kafka does both), what happens when delivered message never gets digested to the microservices in time (retry strategy), etc... Because you are offloading the messaging queue system to the platform, you are under the whim of the platform.

#### Frontend design's pros/cons:

So for this you have to use SPA (single page application), and probably one of the popular solutions: React/Vue/Angular. 

For the the "real-time" aspect of the data, you need to do either one of the two methods to update the data:

* Websockets
* Long polling: timed AJAX calls

Pros for this kind of solution is that this is entirely via Javascript based solution so it is easily approachable to get it up and running

Cons for this is that setting up a reliable websocket connection entails creating the call signatures, and you would have to start a websocket server in the backend for it. Even if you choose the long polling approach, you have to deal with connections lost - do you keep calling? What if the services are just dead and need to restart, and just keep hitting them won't give the room to recover? How can you implement backoff strategy?

### How it is done in Elixir

Hopefully you've seen Saša Jurić's Youtube video shared above so you have an idea of what a process is. 

The base of the discussion revolves around three key aspects of the Erlang/OTP philosophy. Elixir doesn't change it as it is built on top of the core Erlang structure. They are:

1. Erlang is a functional language. If you want to do work on some data, you pass the data in to a function and work on it. You keep state by looping it that data into a function over and over.
2. Each Process has its own memory to work on - memory is never shared across processes
3. Processes talk to each other using messages

On top of this, Erlang (or more correctly, OTP) adds the concept of Supervision. This means that you can control what to do when processes fail/stop, or if you want to dynamically start processes.

So on top of these building blocks, Erlang, and more exactly Elixir, has built blocks of modules that help out development:

* PubSub - its publisher/subscription framework, which isn't too big a jump from the main message-passing nature of Erlang processes
* Phoenix LiveView - Elixir's web framework is Phoenix, and LiveView is its solution on how to do SPA via sending updated DOM diffs through WebSocket. Really cool.

### Explanation of the diagram

Let's show the diagram so it's close by:

![](/images/chefshop_order_delivery_flow.png)

Let's start with 3 and 8 in the diagram - the OrderProcess and the DeliveryProcess. These are the main operator of the time-related aspects of Order and Delivery. You don't need to read too much into this, but just know that they are what updates / creates Orders and Deliveries. I know, I know - it seems like I'm not using processes correctly for this part, because processes are only for time-dependent operations. I promise you the real implementation got a little different as I wanted to make it right.

DeliverySlotsProcess (5) maintains a map of currently approved set of delivery time slots. Now I know I said functional languages don't hold state. But if you loop through the same data over and over and over, then you maintain state. Doesn't make sense right? I just explained the inner core workings of the Erlang language, and you are welcome to [read more](https://elixir-lang.org/getting-started/mix-otp/agent.html "Elixir guide on Agents") about it. This one needs to update based on time.

Then we have our PubSubs - items 1 and 9. These are abstractions of Elixir which mimic the Publish/Subscribe model of message queues systems, where you publish to a topic and other processes are welcome to subscribe to them to get the information.

Now you'll see that there are these "SiftX" processes (4,6,7). These are processes whose sole purpose is to subscribe to the PubSubs. Respectively they will get only the pertinent messages subscribed, to run some logic, with the end result being they will trigger the respective "core" processes to finally update Order/Delivery/DeliverySlots.

The "LV*" processes are Phoenix LiveView processes. They subscribe to the PubSub to re-trigger update of the pages, so that whenever any messages can come through, the pages will update to show the relevant updated data to the browser.

### Can you find an issue here?

There might be more but there is one issue with this design. Can you find it?

It's on the LiveView processes.

Did you find it?

Haha cool cool. That's right. If the messages trigger the re-rendering of the LiveView pages, then there might be the case where they would STILL show the previous state of the resources, because the processing hasn't completed yet.

For example, if Order updated its state, LiveView for Deliveries would re-render, however since the DeliveryProcess hasn't completed processing the creation of a new Delivery, the page would still show zero deliveries.

Solution to this potential issue is by creating a new PubSub event, which emits when the delivery is created. Then, we know exactly when to re-render the page.

### Discussing the benefits of Elixir based design

A couple benefits arise when we use Elixir to drive this system.

* Decoupled system
  * Erlang/OTP is fully decoupled in processes - the system makes the path with least amount of friction when you design yourself to make decoupled solutions. 
* Inherent message-passing communication
  * Each processes communicate by messages, and these messages arrive in each other's message queue chronologically, guaranteeing timely delivery. 
  * We don't have to explicitly design any custom delivery guarantee logic (e.g. backoff) because it is built in to the core of OTP! How cool is that?!
  * In addition, GenServer Processes also come with additional message features like "continue (when you want to guarantee something to be run before taking another message from the queue)", or "terminate (when you want to run something right before process quits/kills)". Again, no need to think about making theses because the framework is already provided by the core OTP layer.
* Failure is expected - let it fail
  * Erlang/OTP runs under the philosophy that failure should happen.
  * So, you can let it fail, and processes will get killed - as is the life that is chaos.
  * You can supervise the entire life cycle of a process - using Supervision trees. Let it start when app starts, or dynamically start based on business logic.
  * You can even specify how you want them to start up - for instance, let all of the processes restart when one of them fails, or just one.

### That's it for now!

Whew! You know those people who write long blog posts about what they are making and ALSO make them? I realized they are two separate things with different time commitments! How do they even do them?! I'm like I could've coded up what I explained this morning instead of writing here about them haha. New challenge and discipline I guess. Perhaps another "perk" of building in the open? :)