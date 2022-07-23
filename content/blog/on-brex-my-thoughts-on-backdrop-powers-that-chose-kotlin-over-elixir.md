+++
date = 2022-07-23T07:00:00Z
description = "I couldn't agree on the tech reasons, and thought I'd look deeper into the Brex decision."
draft = true
image = "/images/blog_money_bag_yvyqg7cd3.jpg"
title = "On Brex: My thoughts on backdrop powers that chose Kotlin over Elixir"

+++
I'd like to share my thoughts on what probably most people suspected on why Brex dropped Elixir in favor of Kotlin, as in there was probably more than meets the eye. Below is strictly my thoughts, and they are probably not correct, but worth pondering ;)

This hypothesis is probably why the decision to drop Elixir was made. Nevermind the technical reasons - at this time and age, of Discord and WhatsApp showing the limits of scalability, choosing Kotlin over Elixir for technical reasons, especially after existing code, was something I just couldn't fully understand. There seemed to be a deeper reason, the backdrop pressure which allowed such decisions to take place.

And that is probably the "toxic" mix of increased pressure from VCs that invested $1.5B, tech leadership not having personal experience scaling an Elixir platform, and the ultimate root of it all - not having product market fit.

Background/Timeline (funding record here: https://www.crunchbase.com/organization/brex/school_financials):

04/01/2017 - Series A investment: $7M
06/19/2018 - Series B investment: $50M
10/05/2018 - Series C investment: $125M
10/05/2018 - [TechCrunch: How the 22-year-old founders of Brex built a billion-dollar business in less than 2 years](https://techcrunch.com/2018/10/05/how-the-22-year-old-founders-of-brex-built-a-billion-dollar-business-in-less-than-2-years/)
11/15/2019 - [The Start-Up Offering Credit to Other Start-Ups](https://www.nytimes.com/2018/11/15/business/smallbusiness/start-up-brex-offers-credit-to-other-start-ups.html)
12/04/2018 - [Why Brex Chose Elixir - Medium](https://medium.com/brexeng/why-brex-chose-elixir-fe1a4f313195)
04/16/2019 - Debt Financing: $100M
06/11/2019 - Series C investment: $100M
10/01/2019 - Secondary Market
12/11/2019 - Debt Financing: $200M
05/19/2020 - Series C investment: $150M
01-02/2021 - during my interview I heard Elixir is sunsetting, and will use Kotlin for new services
04/26/2021 - Series D investment: $425M
06/23/2021 - [Brex announcement they are moving to Kotlin first](https://medium.com/brexeng/building-backend-services-with-kotlin-7c8410795e4b)
08/24/2021 - [Thinking Elixir podcast episode about Brex decision](https://thinkingelixir.com/podcast-episodes/061-elixirs-recent-brex-it/)
10/22/2021 - Series D investment: $300M
...
06/21/2022 - [Brex fires small-business customers: The fallout](https://www.protocol.com/newsletters/protocol-fintech/brex-small-business)

Result of all this investment is that Brex has been funded for total of $1.5B(billion) dollars from a total of 11 funding rounds.
Brex valuation is at $12.3B.

That's a lot of money, and where is Brex now business-wise?
[Courtesy of Growjo(free signup)](https://growjo.com/company/Brex#:~:text=Brex%20Revenue%20and%20Competitors&text=Brex's%20current%20valuation%20is%20%2412.3B.), Brex's estimated annual revenue is at $203.2M/yr.

I'm not a business person, so let's do rough math.
Assuming profit is at 15% of revenue, and define profit as pure profit, after paying COGS, worker salary, etc.
That's $30M/yr.

At that rate, with zero interest (not realistic) on the VC funds, it will take 50 years (1500/30) to repay the investors.
I know, this is a very rough, and probably incorrect way to assess a company.

At this point I ask people with a lot more business / corporate / VC company experiences to chime in, but with my 6 years of professional code-monkey life,
with short stint in a startup as number 4 hire, this is a point where VCs will start to breathe down the company's neck.

Yeah I know, VC's invest with a mindset that some will fail. So each VC's probably invested on 10 other different Brex's. 

But in Brex's point of view, dare I say there is a soul-crushing need to grow and increase revenue.
Such was probably the reason from 2019 - 2020 of taking in more investment - probably to onboard more startups.

So to recap, the backdrop of the decision to move to Kotlin had these events happening:

* Two Series C and one Series D investment
* Onboarding new startups, picking up steam
* Increased hiring for software developers

During 04/16/2019 ~ 04/26/2021, span of two years of building out their platform, 
Brex got a total investment of $795M, and went into a hiring spree with that VC money.

Somewhere along that time, tech leaders decided that Kotlin would be a better way forward.

And then, in about a year - they decide to fire their small-scale business customers to focus on being the financial platform for VC - backed startups.

What's the delta in that decision? Well, for one thing the target market is more targeted instead of a haphazard collection of any and all startups who need a business account.

What comes with a business decision like that it generally makes the domain design a lot simpler to scope out, code-wise.
Therefore, whatever scale-based reasoning that Brex had that made the change to Kotlin no longer is the correct deciding factor.

As you see, it is therefore the fueling of investment capital that might have allowed over-engineering of the platform which might have led to the need to over-hiring and relative scarcity of Elixir developers adding pressure to go to Kotlin.

When VCs invest around $800M over two years, they are gonna be breathing down the CEO's neck, and yes probably that same pressure for the CTO. New avenues of trying to earn more profit might have led to building more web apps, more things that their platform should be able to do - pretty soon leading to a shot gun approach - of business, and of code products.

This actually fits Kotlin better at that point. You use prepared library of stuff to build your own version of that stuff. What better way than to tap into the Java libraries to do that fast?

Plus perhaps a new VP of Engineering or CTO that doesn't know Elixir might have joined - potentially from the financial sector, a veteran with a track record in growing fintech companies fast and sellable. Quite possibly the only foray into programming languages known to the CTO was based solely on the world of OOP.

Now that CTO probably had no problem with Elixir, if business was chugging along. But no, Brex was gifted with a $800M sledge hammer with pressure to scale up as quick as possible - in business-speak, throw people at the problem level urgency.

That would give a jolt of need to rely on things you know the most. And typically with such settings, new tech is not what you rely on. Something you know inside-and-out, one which you can trust the developers to build. One which you can say you are able to hire off of a fat candidate pipeline.

But there's more to this.

Having the VC money and scaling a product where product-market fit is not there, is probably the business step-sibling of pre-optimization. What could have been apparent early on if one was not VC-backed is the product-market fit. But with VC money, it's hard to see it, as the overshadowing premise becomes "let's throw money on it (and see where we end up)". 

From 2019 to 2021, I'm guessing they tried hard to scale the business.

Alas, but their recent decision to drop the small-scale entrepreneurs means the product has not found the fit yet.
They have pivoted.

There's a reason Javascript reigns supreme - its malleability. It's very cheap to build something, and then change it if things don't work out. The energy is minimal to test things out.

Same goes for Elixir. Functional languages shine in the world of startups, where product-market fit is not yet there.

And yet - for Brex, they pre-optimized for Kotlin, while they had __NO__ product market fit. I emphasize NO because "somewhat of a product market fit" multiplied by $1.5B to repay means "somewhat" equals "NO".

Changing production codebase to do something else is not fun. Brex did it for Elixir -> Kotlin, and now they are doing it because of a business pivot.

And who's to say the business pivot is the last pivot they'll make?
A startup that takes on VC money to the tune of $1.5B to be a fintech platform for other VC-backed startups.. doesn't sound fishy at all.

In situations like this, where there are a lot of unknowns, you actually need a more nimble codebase. 
You need to use a functional language - a stateless language that lower mental overhead across the board. 
Having an OOP slows down implementing business pivots.

It is therefore my assertion that, now - more than ever - Brex needs Elixir.

