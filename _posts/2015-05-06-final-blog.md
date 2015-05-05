---
layout:     post
title:      busbus Is Now a Thing, May 2015
author:     Nick Gilliland, Alex Gustafson, Zane Ralston, Monica Shafii & Ian Weller
date:       2015-05-06 16:00
summary:    Work is getting done.
---

We have put a lot of work into busbus since our last blog post and are proud to
finally say that it is a thing. Of course, we have had our fair share of issues and
obstacles, but we are starting to see busbus evolve into a real transit platform
with a well defined API. While we haven't been able to add support for as many
transit agencies as we would like, we have laid the groundwork in order to make
the platform easily extensible. Any transit agency that has a GTFS feed can have
static data available through busbus with just a few lines of code. Realtime data
has proven to be more difficult to implement than we expected. As of right now
we only support MBTA realtime data, but this serves as a proof of concept and
we are planning to add support for Lawrence and any agency that uses Nextbus as
soon as possible. This has been a great learning experience for all of us due to
the many obstacles that we have had to overcome.

<video autoplay loop controls style="width: 50%;">
    <source src="/assets/{{ page.id }}/androiddemo.mp4" type="video/mp4">
</video>

## [busbus core](https://github.com/spaceboats/busbus)
 
In September, we envisioned busbus as an interface for accessing transit data
without rewriting code for new agencies. That's our main story driving what
we're calling "busbus core", the Python interface we're creating, and we think
we can complete that before the end of the semester at KU (about six weeks out
from now).

We're currently quite a bit behind. Three months ago [we stated][midterm_plans]
we'd have 10 transit agencies, with the hope that at least one of them had live
data.

[midterm_plans]: https://github.com/ku-eecs-capstone/blackboard/wiki/busbus#midterm-plans

Right now, the only code we have actually working is importing the GTFS feed
for Lawrence, KS. Current code on the master branch has an infinite loop trying
to read the MBTA feed (Boston, MA) and crashes trying to read the KCATA feed
(Kansas City).

But hey, Lawrence's GTFS feed reads fine and has minimal memory usage. I do not
imagine this will be the case for larger agencies (or agencies that don't use
``frequencies.txt``).

So here's the plan for the next six weeks:

- Start storing GTFS data in a SQLite database cache, instead of just caching
  the GTFS ZIP files. If done right this should improve busbus's memory
  footprint as well as make certain queries faster.
- Debug what's wrong with reading our current focus GTFS feeds.
- Start getting realtime data for services that provide open data feeds.
- Get a setuptools entry point working so that users can ``pip install``
  providers not available in busbus core (providers that aren't open data, for
  instance).

Our initial plans were to have gevent (or the like) available to subscribe to
events, for instance, estimated arrival time updates for arrivals. We may not
get the chance to do this before the end of the semester, but it's something I
intend to write this summer.

As I (Ian) tend to do, I'm writing this post at the last possible minute. I
could put samples of using the API in Python (as well as via HTTP in the next
section) but that will work much better as [fixing the README][issue_5].

[issue_5]: https://github.com/spaceboats/busbus/issues/5

## busbus web

On the other hand, the web interface for busbus is pretty much done (not merged
into master yet because of a lack of tests, but once that's done we're ready to
go).

The remaining challenge is hooking up a WebSocket to the gevent system, as
mentioned above, but because I've likely cut that for end-of-semester release,
it's not a challenge for the end of the semester.

## Applications

In addition to busbus core, we wanted to show real world uses of the busbus 
platform. Our end users are developers, so we needed some kind of real world 
application to showcase how to use busbus. This lead us to choosing the three 
applications that are most likely going to be made with this platform. An 
android app, a web application, and a LED board.

### [Android app][busbus_android]

[busbus_android]: https://github.com/spaceboats/busbus-android

No one in the group had any experience with android development, so there was 
a learning curve. After a few weeks, we started to gain the appropriate 
knowledge of the android app lifecycle, and how to pass data back and forth 
between different activities and fragments. Due to time contraints and lack of 
android knowledge, we elected to only support android 5 for our app. Android 5 
introduced many new libraries, including one that we used called 
[RecyclerView][rc_view]. 
Google introduced the [material design specifications][mat_design] with 
Android 5 as well. This specification is designed to help developers make 
good design decisions when creating animation and color schemes. We wanted 
to mock what a real android developer would be doing, so we made sure to 
follow these specifications.

[rc_view]: https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html
[mat_design]: http://www.google.com/design/spec/material-design/introduction.html#

So far we have been able to make Http requests to the server in order to 
retrieve routes, stops, and arrivals. These items are then displayed in the 
RecyclerView list that was mentioned above. 

![RecyclerView items](/assets{{ page.id }}/android_routes.png)

While trying to figure out the best ways to retrieve and display these items, 
we were able to learn a lot about what works best for developers who want to 
use our API.

At this point we have created a solid framework for getting data from the server 
and are shifting focus to creating an intuitive user interface. Many current 
transit apps require multiple clicks to get to things that should be available 
when the app opens. For example, if a user takes the same route every day from 
the same stop, it should not take 5 clicks in order to get the next stop time for 
their route at their stop. In order to make this more easily accessible, we are 
making use of favorites, by using a SQLite database stored on the device. 
Whenever a user navigates to the favorites page, it will take the route ids and
stop ids from the database and query the busbus web server for when the next time 
this route will be at this stop. This happens for all favorites and is displayed 
like below.

![Favorites](/assets{{ page.id }}/android_favorites.png)

After we receive a google api key, we will be using GPS location to display the 
closest stops on a map in the near future.

### [Web app][busbus_web_app]

[busbus_web_app]: https://github.com/spaceboats/busbus-web-sample

To demonstrate the ability to focus on applications versus APIs when using 
busbus, we have been working on a web app that aggregates transit data and 
streams it to the screen. The web application uses a Flask framework, Python for 
the server side, JQuery for the client side, and HTML/CSS formatting. It 
currently has a `home` link to the busbus repository on GitHub, and a 
`datastream` link which displays current arrivals in Lawrence by bus route, 
stop name, and headsign. 

![Website datastream](/assets{{ page.id }}/web_datastream.png)

The datastream refreshes every five seconds. Future plans are to implement 
a way for the user to control the data displayed by choosing specific parameters 
to base their search on. For example, only showing route 29 data.

### [LED board][led_board]

[led_board]: https://github.com/spaceboats/3001-ledboard

Since our last blog post, we have completely rewritten the LED board using
Node.js. Our [previous implementation][led_blog] was... interesting. It had a
Python web interface that ran a c++ board controller. We decided that this was
kind of a weird solution and it was difficult to prototype new features because
it took so long to compile. We found that a bunch of the libraries we had been
using had been ported to Node.js. Since we already had some experience with
Node from the labs at the beginning of the year, we decided that it would be
better option. We now have a solution that actually makes some sense. The
main feature is what we call a state queue. Each state is a scene that is displayed
on the board. The state queue has add and remove features that can be accessed by
the web API. As of right now, the
only supported state is text, but we plan to add support for images as well as more
advanced text layouts by the end of the semester. We also have a web app that allows
users to add and remove text states. This web app is available to any user inside the
EECS network [here][3001-leboard.spaceboats.net], assuming we actually have the server
running at the time.

[led_blog]: http://spaceboats.github.io/2014/10/31/building-brighter-lab-resources/
