+++
date = 2021-12-17T08:00:00Z
description = "I struggled so hard to get this working, and I still have questions!"
image = "/images/screen-shot-2021-12-17-at-7-02-16-am.png"
title = "[Advent of Code 2021] Day 15: Implementing Djikstra's Shortest Path in Elixir"

+++
**Question: below is(what I think) the slowest part of the algorithm in my implementation. How can I make it faster, and would Nx (using CPU) help sorting go faster (is this a nice simple usage to learn Nx)?**

I'm late to the party, and have not done CS related practices in a while. So it took me three days to figure out implementing Djikstra's shortest path for AoC Day 15 challenge.

While doing so - I wanted to get into learning how to optimize `Enum.reduce`, and `Enum.sort_by` in HUGE maps, and to see if Nx is something I can use here to speed things up.

Here's my full answer to Day 15 challenge of Advent of Code: [gist](https://gist.github.com/lovebes/de57c109217ff87745f9153e25ef65a6).
It works and I got those coveted two stars. Timing results:

* Part I: 700 ms \~ 1000 ms
* Part II: HOURS (I don't know exactly. I left it running two hours and just slept).

[Following is what I see as the slowest (link: same thing but in gist)](https://gist.github.com/lovebes/de57c109217ff87745f9153e25ef65a6#file-part_where_it_is_slowest-ex):

```elixir
defp get_next_coord(visited_nodes, dist, inverted_dist) do
  inverted_dist
  |> Enum.reduce([], fn {val, coord_list}, acc ->
    filtered_coords =
      coord_list
      |> Stream.filter(fn coord -> !get_value_at(visited_nodes, coord) end)
      |> Enum.sort_by(fn {x, y} -> {x, y} end)

    case filtered_coords do
      [] ->
        acc

      rest ->
        updated_pair = {val, rest}
        [updated_pair | acc]
    end
  end)
  |> get_smallest_inv_dist_coord()
end

defp get_smallest_inv_dist_coord(inverted_dist_in_list) do
  smallest =
    inverted_dist_in_list
    |> Stream.map(fn {val, _coord} -> val end)
    |> Enum.sort()
    |> hd

  Enum.find(inverted_dist_in_list, fn {val, _coord} -> val == smallest end)
  |> elem(1)
  |> hd
end

defp get_value_at(mapped, {x, y}) do
  Map.get(mapped, {x, y})
end
```

Structure of `inverted_dist` is a map with integer as key, and a list of tuples, for example:
`%{4 => [{4, 5}]}`. This is an inverted representation of the distance list, in a [Dijkstra shortest path implementation](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/).

Size of `inverted_dist` map, for solving Advent of Code Day 15 challenge Part II, is: 250,000 keys, and values are lists with potentially around < 10 items per list. The map grows its keys as algorithm progresses.

I chose maps as the main data structure because it enables most efficient and good API to work with (reference: [https://www.theerlangelist.com/article/sequences](https://www.theerlangelist.com/article/sequences)).

## EDIT: OH. MY. GOSH!!!!!!! I GOT IT TO WORK FASTER!!! ([gist](https://gist.github.com/lovebes/de57c109217ff87745f9153e25ef65a6) is updated with the new solution described below)

I was at a point of giving up. Today was going to be day 3 of dealing with this. Since I spend time after work on stuff like this - yeah, it took total of 9\~10 hours total figuring it out.

But I don't know what it is, but I don't really give up on code that easily. At a certain point I wantd to go into [Nx](https://dashbit.co/blog/nx-numerical-elixir-is-now-publicly-available) to see if dropping into bare-bone CPU layer can help me sort faster. Yes, I was desperate!

So I asked around. Thanks to few good answers, I went into a Rabbit Hole of learning about Priority Queue, and also the underlying data structure that powers it (I guess one of many) - [Pairing Heaps](https://en.wikipedia.org/wiki/Pairing_heap). I didn't want to "blindly" use a ready solution so I looked around for a more barebones-yet-simple implementations of key parts I needed to know.

Luckily there was a good Elixir implementation of [PairingHeap](https://github.com/ewildgoose/elixir_priority_queue/blob/master/lib/pairing_heap.ex), and aftering implementing parts of it to understand it, I was still a bit skeptical.

Mainly because `Enum.sort`, if it is for a list, would fall into using Erlang's `:list.sort`, which would be doing merge sort, which would be O(N*logN).

Pairing Heap was going to be O(logN) for `delete-min` operation (for when removing min value in the heap if it is visited).

And I've forgotten much of Log N notation so I didn't quite sense how it would turn out.

# Lo and behold, compare the results:

1. With Enum.sort of distance map
   * Part I: 700ms \~ 1000ms
   * Part II: hours (I left it running and slept)
2. With using Pairing Heap (current solution)
   * Part I: 38.558 ms
   * Part II: 1797 ms !!!!!!

This is the power of algorithms and data structures! It took me three days of learning Dijkstra, implementing said algorithm into Elixir, and then learning about Priority Queues and Pairing Heaps.. but I think I am a bit wiser than three days ago. I feel like I went through a mini-CS course. woah.