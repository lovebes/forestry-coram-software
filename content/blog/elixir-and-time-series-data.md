+++
date = 2022-08-08T07:00:00Z
description = ""
draft = true
image = "/images/elixir_logo.jpeg"
title = "[Elixir and Time Series] #1 - Background and Ingestion considerations"

+++
Hi! Sit down. Grab a drink, and let me stoke the fire for you. I was in the middle of talking about my trial by fire.

![](https://i.kym-cdn.com/entries/icons/original/000/039/993/resthere.jpg)

## Storytime

But let me catch you up to speed. It was around 2018. I was busy leveling up at a startup. They did video security as a SaaS.

This meant they were, effectively an enterprise level IoT company.

They had a gateway that was on-premise, with hundreds of cameras hooked into each gateway, pumping video and metadata out to the cloud.

That metadata was then pumped into a Time Series-tuned PostgresDB called TimescaleDB.

So all in all, standard mode of operations.

As I leveled up, I went from frontend of the business all the way to the backend side of things, and the backend was in Golang. Done in a web of microservices.

The original system dreamt up by an architect, and two engineers implementing it - the ingestion and aggregation - started crapping out as we added more videos. At one point the frontend stopped loading as the call to the aggregation service kept crashing.

To the customers? Product just fails. I mean, a Video Security Platform that doesn't have their video player playing security feeds playing. Like what the hell, right? Thankfully our clients understood that we're currently scaling it out, and product is in beta. They liked how responsive we were, so that's what we did. Be responsive, no matter the cost.

Our team that was formulated to solve this, was like your standard heist movie, where we had:

* the frontend genius who singlehandedly upgraded our crappy video player React component
* Finnish contractor who brought Finnish humor and an expansive NodeJS skillset that helped rewrite the ingestion microservice, which received the GCP PubSub messages and pushed them into TimescaleDB
* someone pushed to quarterback all of this (yours truly), who tried multiple designs for the aggregator (Golang) and attempted to function as an architect drawing lots of boxes and arrows
* seasoned SRE with penchant for rapid iterations and knowledge overlapping with development that happily spun up whatever PostgresDB configuration we asked in GCP

We had hours of discussions trying to understand the complexity of the problem. Then we diagrammed out how to make it better. At one point it was civilized; but then all hell broke loose. At one time in our morning diagramming sessions, a senior role came in and just said all of our planning is shit, just threw a wrench in it - partly I'm assuming he felt personally attacked, as he was one of the original team that created the initial architecture. It was a shock to everyone who was in that room. Then another from the infrastructure side blabbered on and on about a solution which had no bearing, and me trying to explain why it won't work; then one time another senior role said to stop throwing shit at the wall and see if anything sticks. Everyone was stressed to their limits, understandably. Maybe we deserved that much shit. Who knows. No one likes their ideas being pulled apart like that, but granted I do have my insecurity issues, and being an introvert doesn't help. I mean, in hindsight, we were all somewhat juniors than us now. Good thing is we - everyone involved, even the tech senior roles, and the company - all turned out great in the end. Standard startup growing pains.

Hindsight is 20/20, but then, knowing that's the level of pushback I'll get, I had to confirm my ideas work. Days on end - until midnight I'd read and learn about how to aggregate time series, various ways that GCP can help out, and then implement ideas we confirmed the past day, load up the DB, test it out, and then report back to the team.

In the end though, after all of that - my ideas weren't selected for the aggregation side, as it was deemed too complex. Granted, yes, doing it in Golang + Kubernetes would've made it a delicate dance which I'd be wincing too. 

..And, HAD it worked, I probably wouldn't have learned Elixir.

This is how I got into the problem of Time Series data.

It laid dormant in my brain until a friend told me about a similar problem she had in her company, which dealt with pharmaceutical data. 

Same problem with aggregation - taking way too long.

Now let's generalize things a bit here. Since we have two parts, let me discuss the Ingestion first, and then the Aggregation.

## Ingestion

Let's say you want to track the movement of your mouse, but just the X-axis.

We are going to chunk that information by 1 second, and push metadata event out to the cloud as messages.

Things you have to worry about are the following:

* Difference between event time and message time
  * WHEN the event happened is different from when the message containing the event has hit the cloud.
* The messaging architecture time dynamics
  * Does it guarantee delivery?
  * Does it guarantee chronological order?
  * Can it do both?
  * Can we replay events? How long do these events live?

The messaging system handles the action of the mouse data going into the cloud.

Once that is safely covered by the cloud platform's SLA, then what we have to do is create an ingestion service that will ingest the messages into some database.

Typically, what I'm describing is that the messaging system is some sort of a pub/sub system, and the ingestion service writing into the database will subscribe to the topic to which the messages are being populated.

Factors to worry about here are:

* How much concurrent receiving of messages can the ingestion service handle?
* If it can't handle the messages, would the messages be retrying later, or would they be forever lost?
* If the ingestion service senses a bottleneck at the database side when it writes the messages, how will the ingestion service retry the writing into the database?

As for the database, things to think about are:

* This definitely needs to write as fast as possible, but how much would that impact the read?
* Can it safely connect read replicas?
  * For instance, PostgresDB has easy ways to connect a read replica, and ensure the data timely reflects the main DB, with various concurrent guarantee settings.
* Cost
  * I mean sure, if I had a blank check then I'd just dump everything into a data lake
  * Or, maybe things are cheaper now.
* How do they scale?
  * If they want us to set up some strategy for sharding of data, so that data can be evenly spread across, then that's added complexity
* Suggested Database systems
  * [snowflake](https://www.snowflake.com/creating-azure-data-warehouse-snowflake/)/[bigquery](https://cloud.google.com/bigquery)/[redshift](https://aws.amazon.com/redshift/) - data warehouse offerings from major cloud platforms
  * [TimescaleDB](https://www.timescale.com/) - PostgresDB "add-on" that makes PostgresDB become a write-friendly DB
  * Also, there is also time series specific database systems like [https://www.influxdata.com/time-series-database/](https://www.influxdata.com/time-series-database/ "https://www.influxdata.com/time-series-database/")

Not only this, but if you were to choose a language to implement this ingestion service, then there are these considerations:

* Complexity on dealing with concurrency
* Friendliness with scaling via Kubernetes replicas
  * Would this work with all of the services maintaining connection with the DB? Would DB pooling put a cap on scalability?
* General performance - how shitty are the languages in general?

For example, single threaded languages like NodeJS or Rails are not going to be that performant, or put it other way - in order to be performant, you have to either add ugly hacks or do some crazy replica scaling as size of messages grows. This adds risk downstream of writing into the DB, where PostgresDB connections are typically memory heavy, so you can't endlessly have many connections.

Golang is performant, but due to its crude syntax and goroutines being still a bit confusing and hard to maintain good concurrency architectures - I would rather use NodeJS, but that's a personal thing. In the end, both don't make it enjoyable to write code for highly performant ingestion services.

What? Python? Pfff. GTFO. Talk to me when Asyncio and SQLAlchemy aren't complex beasts.

Any of these languages end up having to scale via increasing replicas of the Kubernetes pod hosting the ingestion service.

However that leads to increased DB connections, and typically this becomes a bottleneck, as DB will end up crashing or limiting connections, so that becomes the ceiling for scalability.

Naturally then you'd also have to have a discussion on how to do DB connection pooling, and that by itself becomes a place where you'd need yet another service to add to manage pooling.

### So how can we make ingestion simpler and still performant? 

Yep, let's do that as it's the simplest. 

What if we had a highly performant language that can handle massive connections?

Then the ONLY bottleneck becomes the DB connection, which limits the problem drastically.

This is where Elixir shines.

Elixir with GenStage based libraries like Flow or [Broadway](https://elixir-broadway.org/) was built for something like this. It will handle ingestion, back off pressure from DB, and make it easier for the developer to handle the business side, instead of having to manually code the message handling, AND to do it efficiently via investing time to do concurrency right.

With Broadway, we can let the library handle the hard concurrency pieces, and that's because Elixir does it well already - and THAT is because we [use BEAM virtual machine under the hood, which is in erlang](https://medium.com/flatiron-labs/elixir-and-the-beam-how-concurrency-really-works-3cc151cddd61).

If we were to use TimescaleDB, or straight up use vanilla PostgresDB, then Elixir's Phoenix web framework already has Ecto library set up, which will handle the pooling.

That's it for now. Next post will be on Aggregation considerations!

More information/reference here:

[https://elixirforum.com/t/creating-persistent-real-time-analytics-of-time-series-data/10442](https://elixirforum.com/t/creating-persistent-real-time-analytics-of-time-series-data/10442 "https://elixirforum.com/t/creating-persistent-real-time-analytics-of-time-series-data/10442")

[https://www.reddit.com/r/Database/comments/weehox/time_series_data_architecture_question_to/](https://www.reddit.com/r/Database/comments/weehox/time_series_data_architecture_question_to/ "https://www.reddit.com/r/Database/comments/weehox/time_series_data_architecture_question_to/")

[https://elixirforum.com/t/implementing-a-distributed-users-counter/39609](https://elixirforum.com/t/implementing-a-distributed-users-counter/39609 "https://elixirforum.com/t/implementing-a-distributed-users-counter/39609")

[https://www.youtube.com/watch?v=cVQUPvmmaxQ&ab_channel=CodeSync](https://www.youtube.com/watch?v=cVQUPvmmaxQ&ab_channel=CodeSync "https://www.youtube.com/watch?v=cVQUPvmmaxQ&ab_channel=CodeSync")

Visualization: [https://hexdocs.pm/kino/Kino.Process.html](https://hexdocs.pm/kino/Kino.Process.html "https://hexdocs.pm/kino/Kino.Process.html")