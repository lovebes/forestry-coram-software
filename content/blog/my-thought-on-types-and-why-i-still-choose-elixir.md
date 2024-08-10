+++
date = 2024-08-10T08:00:00Z
description = "Reasons why without static typing, Elixir is still my language of choice and happiness"
image = "/images/elixir_logo.jpeg"
title = "Why Elixir is still my choice for web products even with dynamic typing"

+++
I want to talk about Elixir and types. It's coming, but at the same time if the research doesn't pan out we might end up not getting it. Here's [José Valim explaining the ins and outs](https://www.youtube.com/watch?v=giYbq4HmfGA)

I totally understand the concern of typing, and granted I only have about 8 years of professional software developer experience(okay it might be long for some, short for some, for me I still don't feel like I'm a seasoned developer) in only the web development field, with no deep OOP experience. To those who know type systems better than me, my experience is just a grain of salt.

But in that short time I jumped around companies - startup and SMB - using tech stacks that were:

* Python + Angular : lab informatics
* Node (mix of JS + TS)/Golang + React : video surveillance in cloud
* C# + React : health insurance
* Rails/Elixir + React : healthcare records
* Elixir + React : logistics

Again, anectdotal experience data points but for my experience so far, biggest headache prior to working in Elixir was mostly around the data intensive business setting and the complicate hoops to go through to squeeze performance in concurrent data processing.

Type systems of each language didn't really help in that aspect. I guess it was fortunate they at least had the type system because without it, dealing with a sub-par performant language +/- system would be that much soul-tormenting. One language of my past tech combinations stand out. Guess which one? ;)

In Elixir / erlang / BEAM world, what was super complex became generic support coming out the gate. Buliding blocks already prepared for us to use, and it's just using the building blocks to build concurrent systems. This provided me with great happiness, and together with pseudo type system currently available by way of pattern matching + dialyzer (compile-time static code analysis) - most of on-call situations I have gone through didn't have typing being the main culprit. And if it were, there's really good tooling to inspect the running system (way better than my past tech stacks, including Rails).

There's also Gleam, which is closely reaching 1.0, which is a typed language on top of BEAM architecture. There's a way to introduce [Gleam inside an Elixir project](https://blog.appsignal.com/2024/07/23/enhancing-your-elixir-codebase-with-gleam) as well!

I would suggest at least keeping tabs on Elixir and how it turns out - after all it is the No 2 most admired language, and its web framework - Phoenix - is the [No 1 most admired web framework](https://survey.stackoverflow.co/2024/technology#admired-and-desired) this year(2024) :)