+++
date = 2021-03-19T07:00:00Z
description = "Origin Story: Cottage food business as excuse to learn Elixir"
image = "/images/pexels-pixabay-301703.jpg"
title = "[Chefshop] My Elixir Project/Business Idea"

+++
# How I decided to create my first Elixir project

## Or, more like how I decided to do a food delivery business out of my home kitchen

.. legally. You see, in California there is what is called a [Cottage Food Operations](https://www.ocfoodinfo.com/cottage).

But I'm getting ahead of myself. Here, see the alpha version in action. It's broken so the entire flow won't be seen.

{{< youtube u9fE9NO8bRI >}}

### The beginning

This was in July\~September of 2020. COVID was rampant. Massive protests and BLM movement was near the end of the peak. Lockdowns. Masks.

I was in my fourth month of moving to a new job. And we moved out.

A lot of movements were happening in my life. Also a lot of time alone.

....

And then I started learning Elixir. Honestly it's a blur. I don't know what it was, or when it was, but I found myself looking at the main Elixir language website, doing tutorials every day.

And then [https://exercism.io/](https://exercism.io/ "https://exercism.io/"). Then reading books - "Programming Elixir 1.6" and "Elixir in Action". Then countless Youtube videos expounding the virtue of Elixir. I was sold.

And then Phoenix tutorials. Completing that made me dangerous enough.

I was at the cusp of what I should be learning after all of that.

Then, another need for myself to start any side gig came into play. Since our family has a pretty banging recipe to make glazed roasted nuts, at the back of my head I always thought perhaps something food related might be possible.

Add to that, I found out about the Cottage Food Operations regulation - where I can legally cook approved range of food and sell it. Only caveat is you gotta be delivering them. There's also another set of regulations called ["Microenterprise Home Kitchen Operations"](https://rivcoeh.org/OurServices/Foods/MicroenterpriseHomeKitchenOperations), where you can legally create small restaurants in your resident place, under set of rules. Actually reading through the rules was the first epiphany that they might use something like what I am building.

### Arrogance or Petty pride, I opted to not use Shopify and just make it

How hard can it be? You list food, and then some sort of form to submit payment info, and some order/delivery management system.

### I was humbled

Oh my gosh it is complicated. Perhaps I just over-engineered because software engineer.

Here are the some of the things I had to design/overcome:

1. Do I want this to be a SaaS later on? Or should I keep the scope to just host my shop?
2. So I have a center of operations, then delivering people ("deliverers"), and then team members, and users ("eaters"). How should I design the DB? And domain?
3. What should I use for payment processing? Oh right, Stripe
   1. How does Elixir work with Stripe?
4. How do people deliver?
5. Can I somehow track things ordered, delivered real time?

Not only that, I'm creating one of the core sins of startup-building, not checking if there's demand, and only then build. No more "build first and then they will come". But in this case my argument is I am going to dogfood my creation. And also it's not like I didn't do the [research](https://coramsoftware.com/blog/creating-a-product-competitor-research/).

### And here's what I decided so far

First of all, it was amazingly refreshing to design something by myself. No leads or CTO's to consult with. I can choose EVERYTHING by myself. I can over-engineer to my heart's content. Oh yeah no designer either so I can bring my crappy design A-game. And I can choose whatever platform I want, and I chose Elixir + Phoenix.

OK, let me just calm down. I get it now. This is why people create side projects!!

I decided for this to be a SaaS-ready architecture. Meaning, I wanted to be able to host multiple cottage operations and be able to charge people some monthly payment.

That meant I had to have a concept of something.. like a hub, that could model the center of operations. With that I can add in other entities that reflect the real life. Like an "owner", some "team member", and "deliverer".

The model I wanted to support was intentionally anti-Doordash, where "deliverer"s won't be operating independent of hubs. They gotta be associated with a hub. But I kept it flexible for now. Who knows the future? I aim to just build for my immediate need, but to keep it flexible enough.

Then, the issue of food menu items. For now I just kept things very simple, where a menu item consists of name, description, some url for image, and associated with a hub.

I won't shower you with other domain designs I did, but let me  just say I had to design the shopping cart, how it transitions into an "order", and checkout/confirmation, and how it gets flipped to a "delivery".

Then, I had to figure out how to have the kitchen get notified in real time when orders are made, and also have deliverers get notified when orders are ready and assigned to them.

### Elixir throwing it down

Let me give you my background just to set the baseline for what I've been through when it comes to DB related web service development.

1. Python(Pyramid) + SQLAlchemy layer, with Angular 1.0(yuck) /2.0
   * SQLAlchemy is pretty complex. It has ORM AND a non-ORM layer. Reading it just to do something I feel wasn't too hard was just insanely brutal. I was a junior dev at the time so I just have a bad memory of realizing it took so long to do something. Oh man and the alembic migrations which were meh.
   * Angular 1.0 was a practice in hacking Javascript. Two-way communication was brittle and often so quirky. Angular 2.0 was a pleasant return to sanity. However RxJS being the default form management and general state manager was just.. meh
2. Golang (base DB layer, custom DB library, gorm) + React
   * Nevermind the reason why a custom DB library had to be built. gorm was not that robust, and it did what a standard ORM did - obfuscate and frustrate, creating a sub-par language abstract layer to hide a battle-tested DB language, the SQL. At then end we resulted to using bare-metal SQL whenever we could, and it turned out to be fairly good.
   * The serialization/deserialization of null values into zeros was one example of the rigidity, brittleness of the golang DB layer. We had to build DB libraries at the end to do basic plumbing of getting data from DB to Golang Structs and back.
   * Oh and the syntax. The stupid verbose syntax. That basic stupid baremetal syntax. I know that's not popular opinion, but to a startup who needed to go as fast as possible, being exposed to the inner workings of low-level code layer was just friction all around. But at the inception of the startup, Golang was the best language to get up and going. This was 2017. Not a lot of people heard of Elixir then. Golang is massively effective, training junior developers insanely fast enough to be productive in matter of weeks. Buuuuut BOOM your team grew to 40, and all of a sudden there's brittle verbose code all around you, small scattered microservices powering business and everyday you cross your fingers.. until you have those fresh moments of calmness where you can refactor.
   * React was a blast, and still is. Although doing it for 4 years now, it seems like there's a peak. But doing it has taught me, nay opened my closed eyes - to doing things functional. That led me to believe perhaps ReasonML (React based on OCaml) is the future, and no more dealing with Javascript. And then Flutter (who used much of React concept into Dart-based mobile app development framework), which I again became a fan-boy, even giving a presentation about it.
3. .Net core + React
   * I didn't have any C# experience so I was nervous and cautiously brought OOP knowledge from ye-olde cave of archives in my brain, from the yester-years of learning computer science.
   * OOP. Haha. After being in JS / React land, where they tried to enforce functional programming, and being a student of that school of thinking, OOP seemed like too much for me. Too much freedom to do anything and everything. I could see why SOLID principle came to be. With the hindsight of having to learn Elixir, and listening to Alan Kay (who coined the term "object-oriented") and Joe Armstrong (creator of Erlang) talk about [the real meaning behind being "object-oriented"](https://youtu.be/fhOHn9TClXY?t=600), I became lesser of a fan.
   * Seeing classically trained OOP developers write React was also interesting. I can see how they focused on class-based approach more, and tried to create Service classes, to instantiate them in order to use them for API calls. It really does show the power of paradigms and exposure.

Looking into ReasonML and Flutter, realizing that there might be a day where I won't need to use React / Javascript to build single page applications (SPA)... I think was what prepared my mind for Elixir.

### So here I am, with a pretty huge scoped out project and using it to learn Elixir / Phoenix / Ecto

Ecto is Elixir's solution for doing database layer work. It's ORM, but tries not to be in the way of the developer. In that sense it was bare-metal SQL. However it borrowed a lot from Lynx (C#'s way of trying to just plainly use SQL syntax into C#), so you can literally use Elixir to write SQL-esque datastore layer code.

Key awesomeness of Elixir is that C# had to change to make Lynx work. Elixir, with its immensely versatile macro system, did not need to. It really shows the power of Elixir, which in turn shows the power of Erlang.

Also, Elixir is a functional language. It doesn't maintain state. In OOP, and also Golang in some form, you instantiate a class  or a struct. And then that instantiated .. thing.. has the data as the first-class citizen. AND, it has functions with it so you can run functions on the data. This creates state. State is hard because when in distributed computing, the fact that you have states mean you are going to have to share memory references to those states. That creates concurrency / dependency issues - the discussion of which is by itself a huge topic so I won't touch here.

Elixir on the other hand, is a functional language. Functional languages does not maintain state. Nor are data the first class citizen. Since there is no state, logic is simpler. You get data, run operations on data, and then push out updated data. No storing into an instantiated class or struct.

### Benefits of Elixir I come across

While developing this project, here's some benefits of Elixir I came to experience.

1. It is super efficient!
   * I built, by myself, in a matter of 5\~6 months of squeezing in hours here and there:
     * DB layer code that does simple CRUD per tables, multi-transactional operations, Many-to-many, multi-table insertions
     * Kept migrations of DB
     * Real-time operations based on PubSub technique that are fairly involved
     * Single Page App without using Javascript/React
2. Unit tests are a breeze and sooooo fast
   * Since there're no states, you can literally take cut sections of the business code, and then create tests around it so easily.
   * The tests are run so fast
   * In Golang/.net core - trying to create DB-using unit tests are really involved. In Elixir, no problem at all!
     * So much plumbing is done for you with the help of "mix", the tool which helps maintain / test / release Elixir projects.
3. I'm generally more happier
   * Elixir syntax is new, and I like things new.
   * It's also designed to trigger thoughts like the language has my back. Effort to care for other developers shows.
4. It is super efficient, resource-wise
   * I've yet to test this because I'm not ready to deploy to the cloud, but I've read how you can host an Elixir project using a very small resource and have no problem scaling your business up until you need to beef it up or create another node!
   * For large scale businesses that probably would make a pretty big delta in the operations finances.
5. It's growing in reach
   1. Nerves is a project that specializes in deploying Elixir in IoT devices, like Raspberry Pi.
   2. Membrane is trying to bring Elixir into the video streaming pipeline management.
   3. Nx - the most recent outcome from the creator/maintainers of Elixir - aims to explore the Machine Learning space, by allowing matrix calculations be done by native machine layer (not in pure Elixir) on CPU or GPU.

### In Closing

So yeah, just wanted to share this post of what I've been working on, and why I feel Elixir is continuing to be a game changer for years to come. This would be like the first product of CoramSoftware! Hopefully many more would follow :)