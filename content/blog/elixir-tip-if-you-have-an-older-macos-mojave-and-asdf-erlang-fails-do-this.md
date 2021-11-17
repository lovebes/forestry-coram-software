+++
date = 2021-11-16T08:00:00Z
description = "glu.h Error drove me crazy! Until I learned how to disable wx!!"
image = "/images/elixir_logo.jpeg"
title = "[Elixir Tip] If you have an older MacOS (Mojave) and asdf erlang fails, do this"

+++
Leaving this here in case anyone is like me,

using an old macOS and don't really care for updating to a new OS.

I have Mojave. I'm happy with it. I watched as people agonized over Catalina. I was happy until this error popped up when I tried to update erlang via asdf:

[https://github.com/asdf-vm/asdf-erlang/issues/101](https://github.com/asdf-vm/asdf-erlang/issues/101 "https://github.com/asdf-vm/asdf-erlang/issues/101")

And that was the start of a very agonizing weekend.

Every single recommended method wouldn't work.

The main reason is that XCode or Big Sur update changed location of `glu.h`, which is like an OpenGL library - that  wxWidgets needs.

What does erlang have to do with wxWidgets?

Good question! It is because to render the Observer and the debugging GUI:

[https://www.erlang.org/doc/man/observer.html#:\~:text=Observer%20is%20a%20graphical%20tool,Erlang%20tracing%20with%20module%20ttb.](https://www.erlang.org/doc/man/observer.html#:\~:text=Observer%20is%20a%20graphical%20tool,Erlang%20tracing%20with%20module%20ttb. "https://www.erlang.org/doc/man/observer.html#:~:text=Observer%20is%20a%20graphical%20tool,Erlang%20tracing%20with%20module%20ttb.")

Well, I'm not an Erlang guru (yet) and I think Phoenix LiveDashboard has enough output that I don't need the Observer.

I've never actually opened it up to do any debugging.

Sorry babbling on and on. Here's what I did to DISABLE wx, thus disabling Observer in erlang, when using asdf to install:

1. Install XCode for your MacOS. Mine was Xcode 11.3.1 ([https://developer.apple.com/download/all/?q=11.3](https://developer.apple.com/download/all/?q=11.3 "https://developer.apple.com/download/all/?q=11.3") has all the older XCode). Yeah! It's freagging 7 GIGS!!!
   1. No you can't just install Command Line Tools for Xcode. I tried. No bueno. :(
2. Follow what this post does: [https://github.com/kerl/kerl/issues/386#issue-1050365520](https://github.com/kerl/kerl/issues/386#issue-1050365520 "https://github.com/kerl/kerl/issues/386#issue-1050365520")
   1. Specifically:

   export KERL_CONFIGURE_OPTIONS="--with-ssl=`brew --prefix openssl@1.1`   
   \--without-wx   
   \--without-javac"
   
3. And then install asdf:
   1. asdf install erlang 24.1.5

I almost cried. It got installed!

Oh my gosh. I already bought a new SSD and was getting ready to upgrade to Big Sur.

Why a new SSD? Stupid Big Sur won't install, complaining I have disk issues. Sensitive little bastard. I'd remove Observer anyday. Small price to pay to not having to do a full backup of my Mac into new SSD, install Big Sur into new SSD, and then swap it out in my laptop by unscrewing + taking out the old one .... ew