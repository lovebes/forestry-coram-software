+++
date = 2021-03-29T07:00:00Z
description = "How to make them call exactly the same way so subscriber catches them"
image = "/images/elixir_logo.jpeg"
title = "[Elixir Tips] Endpoint.broadcast versus PubSub.broadcast"

+++
This is a snippet post since I couldn't find this documented clearly.

MyAppWeb.Endpoint.broadcast/3 is a wrapper around PubSub.broadcast.

Why would you need to use the Phoenix.PubSub.broadcast?

Well, because you want to isolate your context layer from your Web side of the code - I'm talking about in Phoenix. This allows for good divide between what is web related and what is business related. And last weekend was when I realized that I was using MyAppWeb.Endpoint.broadcast() everywhere in my context layer! Aghast!

For more information on how to divide / organize Elixir projects, please take a look at [https://medium.com/very-big-things/towards-maintainable-elixir-the-core-and-the-interface-c267f0da43](https://medium.com/very-big-things/towards-maintainable-elixir-the-core-and-the-interface-c267f0da43 "https://medium.com/very-big-things/towards-maintainable-elixir-the-core-and-the-interface-c267f0da43"), post by Saša Jurić.

In order to make them call the same signature so it gets captured by following subscription function signature:

      @impl true
      def handle_info(
            %Phoenix.Socket.Broadcast{
              event: "testing",
              payload: payload,
              topic: _topic
            } = msg,
            socket
          ) do
        IO.inspect(msg, label: "TESTING BROADCST ON EVEN TTESTING")
        {:noreply, socket}
      end

You have to do the following.

1. First, subscribe.

             ChefshopWeb.Endpoint.subscribe("delivery_slots:1")
2. If this is how it's broadcasted in MyAppWeb.Endpoint.Broadcast:

       MyAppWeb.Endpoint.broadcast("delivery_slots:1", "testing", %{test: "data"})
3. You have to call it this way when using PubSub.broadcast:

             Phoenix.PubSub.broadcast(MyApp.PubSub, "delivery_slots:1", %Phoenix.Socket.Broadcast{
               topic: "delivery_slots:1",
               event: "testing",
               payload: %{test: "data"}
             })

Of course before all of this - you have to setup your PubSub in the applications.ex:

      def start(_type, _args) do
        children = [
          # Start the Ecto repository
          MyApp.Repo,
          # Start the Telemetry supervisor
          MyAppWeb.Telemetry,
          ...
          # Start the PubSub system
          {Phoenix.PubSub, [name: MyApp.PubSub, adapter: Phoenix.PubSub.PG2]},
          ...