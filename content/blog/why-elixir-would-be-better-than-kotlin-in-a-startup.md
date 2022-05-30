+++
date = 2022-05-30T07:00:00Z
description = "If I was a CTO, why I would choose Elixir over Kotlin"
image = "/images/pexels-ono-kosuki-5973926-1.jpg"
title = "Why Elixir would be better than Kotlin in a startup"

+++

**First of all - I'm not trying to start a war here, but to offer my opinions. Naturally they are biased towards Elixir, and limited by my industry experience.**

And if you wanted to talk about Brex, I wholeheartedly agree with assessments in [that ThinkingElixir podcast](https://thinkingelixir.com/podcast-episodes/061-elixirs-recent-brex-it/). Saying this because Brex switched from Elixir to Kotlin, and it was at one time all-in on Elixir, to the point of [supporting the core Elixir dev team](https://changelog.com/podcast/402) at one point. So I am grateful for Brex to have done so, and sad they moved on.

What I do want to discuss is a thought experiment from that idea. Assuming you are a CTO, with the power to choose how the development team will develop, would you choose Elixir versus Kotlin?

Why now? It's been around 10 months since Brex switched over, isn't it too late to throw some shade?

Oh you attention deficit reader. I SAID I'm not bitter about that choice Brex made.

What I AM wanting to point out is using that language as a model for an internal debate I was having recently, due to my increasing exposure to what Kotlin is.

There's a lot of assumptions:

* As a CTO, let's say I have equal knowledge/experience about Elixir and Kotlin
  * As in bringing both Elixir and Kotlin-based services to production, all through maintaining /debugging /monitoring setup.
  * Also let's assume the dev team count is zero, and I act following [servant leadership](https://www.investopedia.com/terms/s/servant-leadership.asp#:\~:text=Servant%20leadership%20is%20a%20leadership,achieve%20authority%20rather%20than%20power.) and want to have choices taken to be enjoyable by the developers getting hired.
  * No external forces other than the highest leadership (me) will give any reason to choose one or the other. The CEO completely relies on me to make the right choice, as any healthy company with separation of responsibility would.
  * Let's also assume that the industry doesn't have a biased amount of resources on one language or other. Both languages have a key playing company in the industry that has a 100% buy-in for said language.
  * **In reality the author** knows more about Elixir than Kotlin.
* The CTO job is in a startup, building a B2B SaaS.
  * Let's say I got hired after CEO and core team used consultants to build out the POC, and it has proven the business (shown product-market fit, yada yada).
  * It was in NodeJS, and it was already bursting at the seams as it was a POC, and CEO fully agrees NodeJS isn't going to be the way to go, given the bigger picture of where company wants to be.
  * **In reality NodeJS is... good enough...** ugh that makes me want to throw up but it's true. Successful companies are built up using NodeJS, and they might be okay until they hit a major scaling issue.
* Let's say the HR team is splendid in knowing how to pull equal about of talents into the hiring funnel for both Elixir and Kotlin so candidate pool is not the issue.
  * **In reality the** Kotlin pool is bigger because that includes the Java developers of the world, and HR typically has a harder time pulling in Elixir candidates.
    * Elixir pool is smaller
    * Bigger doesn't necessarily mean a good thing. I'll get into that in a minor point below.

And I hate long-running posts that can't get to the point quickly, so I'll get to my conclusion, like RIGHT NOW.

## Biggest difference between Elixir and Kotlin is the time to setup a company-wide robust framework for building.

I'll say more about other reasons, but the biggest pain point for Kotlin is the sheer choice of things to make that framework which developers in my team will enjoy.

### Time to discuss framework solutions for Kotlin is going to take a lot of time / emotion

Specific reason why Kotlin would be chosen is to tap into the JVM language pool, namely Java. Kotlin was designed so that it is fully backwards compatible with Java, so that Java libraries can be used seamlessly.

While that increases the pool of candidates, that means also there's going to be many hardcore afficionados on certain libraries/frameworks to build a web service.

Kotlin's most popular out-of-the-box framework is [ktor](https://ktor.io/), but [Spring Boot is also very popular.](https://www.reddit.com/r/Kotlin/comments/eja2ei/how_to_persuade_the_management_to_try_ktor/)

Therefore these are the decision topics to build a fitting framework:

* Tooling
  * Gradle, Maven, Ant, etc
* DataSource layer
  * ORM or not
    * If ORM, then which one?
  * Database - assumption is we'll use MySQL or PostgresSQL
    * Model representation strategy
      * JSON? YAML?
      * How to do validation checks, and how does it work with ORM (or not)?
    * Migration/ versioning strategy and tools
* API layer
  * Spring Boot or not?
    * Apparently there's a lot of emotion running on [both](https://stackoverflow.com/questions/56764255/what-are-the-differences-using-spring-boot-with-kotlin-and-with-java#:\~:text=0%20onward%20Kotlin%20has%20first,the%20same%20freedom%20of%20choice.) [sides](https://www.quora.com/What-is-the-worst-problem-in-Spring-Boot)
  * GraphQL or REST, or both?
    * Because some API protocol layers make it hard to do both
  * APM packages?
* Testing libraries
  * [There're options](http://www.douevencode.com/articles/2018-01/kotlin-the-best-testing-framework/) and reasons for/against
  * Integration level testing
    * How to build out so we can test via Docker locally?

This is a series of discussion that will take a whole lot of time amongst the key hires I'll be making as a CTO. The underlying premise is that it should be a team decision, so that people will have a buy-in.

You'd think this is necessary time invested so that we choose one and go forth from there, maybe throwing in a prototyping stage where we test out our decisions building out a microservice.

But I'd like to push against that notion. Is that really necessary? The debate to choose a solution for each question listed is a daunting task. Typically most companies are not documents-first, and let's assume we do this in actual meetings, where we bring engineers in a big room or a Zoom meeting.

This means before any development work, we have to spend hours on debates.

Also, **this doesn't end.** Because of the nature of object-oriented, state-sharing qualities of  Java or Kotlin (or broadly, any language that is OOP - like C#) - this naturally will mean having one decision of a package having a limiting affect on downstream choices.

10,000 ft view of this would result in slower time to develop features - because of the time to choose overall framework and after-effect of the limitation of said framework (looking at you mostly, Spring).

### Compare that to Elixir: we choose Phoenix + Absinthe (for GraphQL) + Ecto. Done. Let's get to work.

In a way this is a given benefit with any newer language - less choice to choose from, so everyone who's ever worked with the language has fewer disparaging opinions.

In Elixir, there's one web framework that is the most mature and most widely used, and that is [Phoenix](https://phoenixframework.org/), which out-of-the-box experience gives you:

* support for relatively high scalability
* wonderful tooling tool called `mix` that covers from project initialization to release
* drop-in terminal for essential on-call production maintenance/assessment
* scalable web socket server
* quick testing framework
* release solution that uses Docker
* Ecto as DB layer solution for schema/migration/["light" ORM](https://medium.com/@amandasposito/ecto-what-about-it-7c52b2dc3990)
* TailwindCSS support and cool DOM-on-websocket-for-SPA solution like LiveView

Of course, there are smaller fine-tuning decisions to make, such as:

* Should Absinthe be used?
  * Well.. it's practically the only seasoned library out there and it's doing well
* Underlying HTTP client for making API calls
  * Default is HTTPoison, but lately there are others like Mint, Finch, Tesla
  * For 80% of the cases, HTTPoison works.

However, the driving factor in tweaking any of the underlying libraries is due to the specific niche industry / company domain needs, such as the need to be performant in sending/receiving massively data-intensive calls, or having to [use Elixir in embedded systems](https://www.nerves-project.org/).

Core driver of this efficiency is of course the underlying BEAM architecture, where the language and VM is built to use ALL of the CPU core, while make it still relatively simple to program for said multi-core support. Elixir is the thin language on top of it to make the language more Ruby-esque and to also add metaprogramming support.

Therefore, the "standard" solution is more than efficient in supporting the business needs from POC -> MVC -> probably even during [Inflection Point](https://davidcummings.org/2014/09/18/inflection-points-in-a-startup/) of a startup company.

It is an effortless gain to start a company on top of all of THAT, where core decisions involves just saying the following to the team:

> "What do you think about using Phoenix + Ecto as the core library with Absinthe as the additional API layer for GraphQL, as the basis for each of our microservices?"

=> More than likely the candidates/ core dev team members will agree with it, because it works, it's simple, and it's a firm ground to stand up to build up.

No meetings upon meetings to say different historical anecdotes of why this or that library is good in adding to the company framework, because there's one framework that will cover MOST of the needs, while ALSO maintaining developer happiness by providing good tooling and having a language that is a joy to write in.

As a startup, that's what you need. Get things going fast, using tools that allow for fast building.

* Why not NodeJS? Well, it's definitely fast, but it's stressful for the developer AND it brings less confidence to withstand the inflection point (single threaded-ness)
* Same for Ruby, the single-threaded restriction and the Global Interpretation Lock that manages it
  * ...And you'd typically want to use the language and tools that seasoned Ruby on Rails developers have built after reaching the limits of the language.

### Addendum: pre-filtering effect of candidates by just choosing Elixir

Developers who thrive in the startup development team need to have the following traits:

* Willingness to own and solve problems
* Ownership over delegation of responsibility
* Persistent curiosity to look at things differently
* Ability to share knowledge by writing things down to share asynchronously
* Ability to enforce / share guidelines on code cleanliness

When you choose Kotlin, incoming job candidates are prone to include Java + Kotlin developers, and while knowing Kotlin shows signs of some of the traits I listed, following points are worth noting:

* Kotlin was designed to invite Java developers
* In the grand scheme of things, Kotlin is still a OOP language

The jump from Java to Kotlin is not too big, so you can technically hire Java developers to learn Kotlin.

But that brings us to the difficult trait to hire / filter for: the startup traits.

Just how would you interview to find the traits I listed above?

What you don't want are candidates with the "corporate engineer" mindset, who have a tendency to:

* Expect a certain level of tooling / automated CICD pipeline / code level is there
* Shy from making/referring final decisions that will affect / alert the business

I'd reckon it would take some pretty impressive HR hiring funnel to field out such candidates.

**On the other hand,** when you flat-out choose for Elixir positions, you're either getting beginner level candidates or well-seasoned developers - who've professionally worked with Elixir already.

Most of the well-seasoned, highly skilled developers will naturally have these characteristics:

* Proof of curiosity-driven passion: they've have successfully driven them to depart from a non-FP (functional programming) language into Elixir, which is quite a paradigm shift
* Follow the guiding principles of the Elixir ecosystem of enforcing documenting
* Not afraid of ownership, aka "I will make this work" attitude
* Understands developer happiness and will strive to persist it

You don't need intricate HR hiring funnels in this case, only to field out novice beginners which is relatively easy to do (until you have capacity to hire them too! :)). This would end up causing less money spend, more time saved on the hiring front. Only thing to solve is finding the internet groups where Elixir developers congregate, and [they](https://elixirforum.com/) [are](https://www.reddit.com/r/elixir/) [increasing](https://elixirjobs.net/) [in numbers](https://elixirforum.com/t/is-there-an-elixir-slack-or-chat/6862) by the day.