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