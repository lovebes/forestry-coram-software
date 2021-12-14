+++
date = 2021-08-18T07:00:00Z
description = "How you fade out flashes for Phoenix LiveView 0.17 + TailwindCSS!"
image = "/images/screen-shot-2021-08-18-at-8-47-10-am.png"
title = "[Elixir Tip]: Fade-out flash in Phoenix LiveView 0.17 and TailwindCSS"

+++
This is a short snippet on how to do fade-out on @flash outputs, for Phoenix LiveView 0.17! Yes the latest one, utilizing [Phoenix.LiveView.JS](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.JS.html#content "Phoenix.LiveView.JS") commands.

The effect I was going for was when there are new flashes, the flash would automatically fade out, after waiting a couple seconds, so that the user will be nudged but message will still fade away.

This will do the animation, and then lastly fire a `JS.push()` to clear out the flash at the server.

Files that are needed are:

* `app.js` - to set up event handler to processing event message coming from server, and to run the JS command assigned to target attribute
* `component.ex` - your LiveComponent file
* `component.html.heex` - code for showing flash, and to have the target attribute, and `phx-value-key` attribute
* `tailwind.config.js` - optional, just showing how to add custom duration / delays if you need them

Check out the code in its entirety here:

{{< gist lovebes 8f18700ae874b85441f85b6dadbe3ba4 >}}

Oh, and this uses TailwindCSS. If you don't use TailwindCSS, you can look at [https://tailwindcss.com/docs](https://tailwindcss.com/docs "https://tailwindcss.com/docs") to get the one-to-one translation from Tailwind to CSS styling.

References:

* [Idea for sending event into DOM(ElixirForum)](https://elixirforum.com/t/how-to-show-a-modal-from-the-server-and-hide-with-phoenix-liveview-js-and-repeat-that/43299/4?u=mekusigjinn)
* [Phoenix.LiveView.JS.hide()](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.JS.html#hide/1)
* [Phoenix.LiveView.JS.push()](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.JS.html#push/1)
* [Chris McCord's KeyNote for JS Commands Feature](https://www.youtube.com/watch?v=Of1phFsC4ZI&ab_channel=ElixirConf)(Youtube)

This is so cool. Not gonna lie. Updating pre-0.15 Phoenix LiveView into 0.17.5 just to do this took me two days, and was just about to give up yesterday. 

Before this I relied on a hack to just fade out, but the flash would still be there. It was hard to time the various things going on during an animated fading-out of flashes, because it involved both server and DOM to act in unison:

1. Fade things out, and LiveView shouldn't trigger a rerender during that time
2. AFTER animation is done, remove that flash

What I was doing was very hacky. And if I did want to do it right, I'd have to have a whole pipeline of hook + alpineJS to get it up and running, which seems too much lift for small amount of feature footprint.

But now, it's so much more streamlined, with very small footprint being created. I love it.