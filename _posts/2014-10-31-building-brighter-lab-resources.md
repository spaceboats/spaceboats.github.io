---
layout:     post
title:      Building brighter lab resources
author:     Ian Weller
date:       2014-10-31 15:30
summary:    Let's spend some money and build something neat.
---

Our group's main project, busbus --- a common interface for live transit data
from any agency (more details and our full project proposal here next week) ---
seems like it needs some interesting applications of the software.

From what I can tell, the standard CS capstone project here at KU is usually
something with a end-user or consumer focus. We're taking a bit of a different
route by focusing on developers... but how do you present that to a boss,
investor, or more importantly, someone who's going to give you a grade?

The end goal --- which, once again, we'll post our full proposal and some notes
about it here as soon as that's written --- is to build this API, and then build
cool things *with* it. Something tangible, and more importantly, something most
people can start using. In our mind from the beginning of the project was an LED
board that told you when the next bus was going to arrive at the stops near
Eaton Hall.

So we built that LED board.

![Hello, world](/assets{{ page.id }}/hello-world.jpg)

Inspired by [Pavel Shved's Muni LED sign][muni-led], the end goal is to rotate
through the various routes that stop near Eaton and Murphy Halls, and show their
next two arrivals (or so).

[muni-led]: http://coldattic.info/shvedsky/pro/blogs/a-foo-walks-into-a-bar/posts/98

We were initially going to be quite lazy and just buy the LED board Pavel used,
but it was out of stock at the time, and [Prof. Gill][andy] thought it might be
a good idea to have something to show in the lab before the event held last
night, honoring [Dr. Min Kao's gift to the School of Engineering][endowment-kao].

[andy]: https://ku-fpg.github.io/people/andygill/
[endowment-kao]: http://www.kuendowment.org/s/1312/endowment/news.aspx?sid=1312&gid=1&pgid=1713&cid=3361&ecid=3361&crid=0&calpgid=15&calcid=3341

## Components

![Humble beginnings](/assets{{ page.id }}/sloppy-cables.jpg)

The board is made up of three [16&times;32 RGB LED matrix panels][ada-420] from
Adafruit --- usually used to make large video walls --- and the [(new!)
Raspberry Pi Model B+][ada-1914], along with a laundry list of components to
hook the two up to each other and to power.

[ada-420]: https://www.adafruit.com/products/420
[ada-1914]: https://www.adafruit.com/products/1914

![Alex wiring up the board to be installed on the plexiglass](/assets{{ page.id }}/alex-wiring-board.jpg)

The hardware itself is uninteresting --- we're not electrical engineers, so we
followed [Adafruit's tutorial on daisy-chaining the LED displays][ada-tut]. The
interesting part is software: we wanted to make a web API for anybody in the lab
to use.

[ada-tut]: https://learn.adafruit.com/16x32-rgb-display-with-raspberry-pi-part-2

## Controller code

[The code is, of course, available online][gh-3001-ledboard] --- it's a board
controller written in C++ using [Henner Zeller's library for controlling RGB LED
boards with the Raspberry Pi GPIOs][gh-ledmatrix] and a smallish CherryPy web
server to feed requests through to the controller.

[gh-3001-ledboard]: https://github.com/spaceboats/3001-ledboard
[gh-ledmatrix]: https://github.com/hzeller/rpi-rgb-led-matrix

![Basic web interface](/assets{{ page.id }}/web-interface.png)

## LaaS (LEDs as a Service)

The goal here is to make this LED board a service that our lab --- not just this
year's, but future years' --- can use for their projects. Our interface isn't
quite there yet; text rendering can use some work, documentation needs to be
written, and a lot of features need to be added to the web interface before
we'll call this done.

But damn, did it make our lab a lot less boring as people toured it last night.

<video autoplay loop controls style="width: 100%;">
    <source src="/assets{{ page.id }}/rock-chalk.webm" type="video/webm">
    <source src="/assets{{ page.id }}/rock-chalk.mp4" type="video/mp4">
</video>
