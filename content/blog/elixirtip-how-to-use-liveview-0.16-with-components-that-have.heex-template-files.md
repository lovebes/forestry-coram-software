+++
date = 2021-08-27T07:00:00Z
description = "Small finding I uncovered"
image = "/images/elixir_logo.jpeg"
title = "[Elixir Tip] How to use LiveView 0.16 with components that have .heex template files"

+++
![](https://i.imgur.com/pZLuYAa.png)

This is something that I banged by head against for the last two days.

My findings:

1. [Phoenix.Component](https://hexdocs.pm/phoenix_live_view/Phoenix.Component.html) is to be used like LiveComponent without states. Maybe it's even more basic to consider any of those.. perhaps it's to be used as a dumb UI atomic components.
2. So if originally the LiveComponent had `handle_event` callback functions, then that is a stateful live component - which means you gotta use it still, as `Phoenix.Component` is only to replace stateless components.
3. For any modules that `use Phoenix.Component`, you either can create a function that it expects for rendering - the 1-arity function taking in `assigns`, and returning a H-sigil based in-line template.
4. However, if your template is too big (and I don't think that's the most general use case for `Phoenix.Component`. The examples are for small UI elements), then you can just pass in the `render/1` function that gets added on via `use Phoenix.Component`, much like `use Phoenix.LiveComponent`. This is key. Above picture shows that.

Man I banged my head against this for so long.

And then I realized that the `update/2`, `handle_event/2` functions might not work.

Which led me to think... perhaps those functions are pertinent to the state-based live components that the documentation was referring to, which, in state-ful, you still need to use `LiveComponent`.

Something new! Overall really excited for [Phoenix 1.6](https://elixirforum.com/t/phoenix-1-6-0-rc-0-released/41939) release!! Much thanks to the team.