+++
date = 2021-08-18T07:00:00Z
description = "How you fade out flashes for Phoenix LiveView"
image = "/images/screen-shot-2021-08-18-at-8-47-10-am.png"
title = "Tip: How to do fade-out flash hiding"

+++
This is a short snippet on how to do fade-out on @flash outputs.

Typically you'd do this in a component that has a `fixed` or some absolute positioned css.

{{< gist lovebes 8f18700ae874b85441f85b6dadbe3ba4 >}}

Oh, and this uses TailwindCSS. If you don't use TailwindCSS, you can look at [https://tailwindcss.com/docs](https://tailwindcss.com/docs "https://tailwindcss.com/docs") to get the one-to-one translation from Tailwind to CSS styling.