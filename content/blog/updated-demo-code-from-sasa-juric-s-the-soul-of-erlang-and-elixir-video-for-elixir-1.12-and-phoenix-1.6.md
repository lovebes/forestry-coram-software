+++
date = 2021-09-06T07:00:00Z
description = "Updated code for latest versions of Elixir, Erlang, and Phoenix"
image = "/images/elixir_logo.jpeg"
title = "Updated demo code from Saša Jurić’s “The Soul of Erlang and Elixir” video, for Elixir 1.12 and Phoenix 1.6"

+++
This is the original repo: [GitHub - sasa1977/demo_system](https://github.com/sasa1977/demo_system)

And this is my fork which has updated code to run under most up-to-date Erlang/Elixir/Phoenix. The original git repo has `.tool-versions`, so you can use `asdf` to install the correct version. Disclaimer is I don’t have `asdf` in my machine, so I just updated that file to the versions I have running.

And yes Phoenix 1.6 - `esbuild` and everything.

[GitHub - lovebes/demo_system](https://github.com/lovebes/demo_system)

Contribute to lovebes/demo_system development by creating an account on GitHub.

The video: [The Soul of Erlang and Elixir • Saša Jurić • GOTO 2019 - YouTube](https://www.youtube.com/watch?v=JvBT4XBdoUE&t=498s)

Now you can follow along with the video using the latest versions!

Oh and by the way, how he did the graphs? Pure genius. He drew it using SVG! So LiveView updates the DOM, and SVG draws the graph, data populated by assigns… it’s so lean. No D3.js or whatever, just harnessing the power of HTML5 to the max. Super lean.

And the way he used the processes to monitor, tally up stats, and bringing it into LiveView was quite a learning experience. I mean I’m not super experienced in “process flow design” so this was a really an eye opening experience. (Yes I know this was a demo so code might not be super clean). Even then, wow.