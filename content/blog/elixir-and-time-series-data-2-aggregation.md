+++
date = 2025-01-18T07:00:00Z
description = "Time Series Data #2 - Aggregation"
image = "/images/elixir_logo.jpeg"
title = "[Elixir and Time Series] #2 - Aggregation, or doing stuff with ingested data"

+++

Still in process of writing this blog post, but just throwing out what I want to write for now..

### Outline

* Aggregation of time series data, two ways to do this
  * The ~~naive~~ crude, simple minded way of just-in-time querying of the time series data
  * Actually thinking about architecture, problem space, requirements and doing something better
* How does one create such architecture? The pieces
* How would you do it in Elixir?
  * Simplify via good use of GenServer - split the logic from the time strategies
* Two well known implementations of Time Series Data I used
  * TimescaleDB
  * Kafka but using it like a queue (okay okay I know you shouldn't) - as in a log of events streaming through
  * MQTT (https://elixirforum.com/t/ingesting-stream-of-mqtt-events-with-flow-is-it-a-good-idea/51073)
* Broadway to the Rescue, or is it?


### Ideas from a smarter person, or excerpts from https://pragprog.com/titles/sgdpelixir/concurrent-data-processing-in-elixir/
