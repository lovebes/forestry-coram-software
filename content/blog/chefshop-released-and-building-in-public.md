+++
date = 2021-04-13T07:00:00Z
description = "I learned how to release!"
draft = true
image = "/images/pexels-clem-onojeghuo-375889.jpg"
title = "[Chefshop] Released a build to public url!"

+++
[https://chefshop.coramsoftware.com](https://chefshop.coramsoftware.com "https://chefshop.coramsoftware.com") 

NOTE: if you are going to play around, please do NOT enter real email / name / addresses!

NOTE2: Stripe is in test-mode so it will only take in the test card number, during checkout step.

Website is for my future cottage business - we're gonna sell maple-glazed nuts, home-baked bread, seaweed snacks! And sure I could've used Shopify but I thought might as well build it myself - I mean how hard can it be! I was too arrogant and naive. Building out a purchase flow is... quite involving haha.

Really work-in-progress now, I don't even have good authentication in place yet :) I just got so excited my deployment works so I wanted to share haha.

Idea-wise, one thing I do differently from Shopify or any other main order platform is setting max cap on delivery slots, which after looking into [r/restaurateur](https://www.reddit.com/r/restaurateur) it seems that's what people wish the most from other solutions. But then the idea isn't new - other niche solutions are doing this already.

I started learning Elixir last September-ish (last year was a blur), and finally got it released to a free-tier GCP VPC using this article: [https://damonvjanis.medium.com/optimizing-for-free-hosting-elixir-deployments-6bfc119a1f44](https://damonvjanis.medium.com/optimizing-for-free-hosting-elixir-deployments-6bfc119a1f44 "https://damonvjanis.medium.com/optimizing-for-free-hosting-elixir-deployments-6bfc119a1f44") (which was recommended from my question on this subreddit - thank you so much!! One of the best article ever).

The key aspect was doing it in Phoenix, especially trying to do LiveView + LiveComponents for the purchase flow. I've been doing React professionally for about 4 years now so I wanted to recreate the SPA feeling.

Asset-wise, it uses TailwindCSS mostly, with some remnant CSS from the default Phoenix package, together with AlpineJS. So yeah PETAL stack :D

Not only that, since I'm learning I thought I'd go all in on GenServer too. So there're some GenServer / DynamicSupervisor / Hydration logic in there. Some stuff like Delivery actually hydrates from the DB, loads it into a new process, and only writes to DB if updated. You know stuff I probably don't need to do but did for learning haha.

The owner's side (admin side) is expected to run in a kitchen so it is going to be all "soft real time", via Phoenix's PubSub messaging updating the LiveView for Orders and Delivery update without page refreshing. The layer handles situations like when order status changes, new Delivery gets created, or when users see the delivery slots, the numbers will update based on if orders got delivered / deleted / completed etc.

Drivers would have their React Native apps (not worked on now) that will update the status of delivery, and I guess that will be when I actually create REST endpoints for non-Phoenix clients. But that's in the future.

It's still very rough on the edges, but I'm shocked, shocked I tell you - of the responsiveness of it. I'm hosting it on top of F1-micro, which only has 0.6GB RAM + 1GB swap memory on 30GB HDD.

I haven't load tested it but having to work with React a lot I have a gut expectation of 1\~2 second wait time before anything loads, and man this is a lot quicker than that.

And to see zero REST XHR calls on the Network, with most of the communication going through Websocket... it's like magic.

Obviously I can experience all of this locally but this is my very first project I hosted / released for public successfully so I'm super excited.

Also, wow I didn't realize Linux has so many good tools for help with deploying / logging! Systemctl and Journalctl are amazing! I've used K8S tools before and realized they probably piggy back on these good tools too.

Anyways I'm super happy that I get to control the release too. One deploy bash script to do no-downtime releases based on OTP release! Crazy cool.

Now back to cutting out a lot of the jankiness in the project :D

Man I love working on my own project haha

Thanks for reading!