---
layout:     post
title:      The state of the busbus, April 2015
author:     Nick Gilliland, Alex Gustafson, Zane Ralston, Monica Shafii & Ian Weller
date:       2015-04-03 16:00
summary:    Work is getting done.
---
 
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

After setting up the led board with controller code, we have not 
touched it much since. We figured that the other two applications were more 
important to the developers that would be using the API, so we focused on 
integrating busbus into those first. We do have the led board working 
for text and images. In the near future we will be able to easily incorporate 
the same http request used in the other two apps to retrieve bus data for the 
led board.

We have already written a blog post on what we have done on the LED board. 
It can be found [here][led_blog].

[led_blog]: http://spaceboats.github.io/2014/10/31/building-brighter-lab-resources/
