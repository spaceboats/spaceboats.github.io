---
layout:     post
title:      busbus Is Now a Thing, May 2015
author:     Nick Gilliland, Alex Gustafson, Zane Ralston, Monica Shafii & Ian Weller
date:       2015-05-06 16:00
summary:    busbus is bussing bus data
---

We have put a lot of work into busbus since our last blog post and are proud to
finally say that it is a thing. Of course, we have had our fair share of issues
and obstacles, but we are starting to see busbus evolve into a real transit
platform with a well defined API. While we haven't been able to add support for
as many transit agencies as we would like, we have laid the groundwork in order
to make the platform easily extensible. Any transit agency that has a GTFS feed
can have static data available through busbus with just a few lines of code.
Realtime data has proven to be more difficult to implement than we expected. As
of right now we only support MBTA realtime data, but this serves as a proof of
concept and we are planning to add support for Lawrence and any agency that
uses Nextbus as soon as possible. This has been a great learning experience for
all of us due to the many obstacles that we have had to overcome.


## [busbus core](https://github.com/spaceboats/busbus)

TODO REWRITE THIS SECTION

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
TODO REWRITE OR REMOVE THIS SECTION
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
android app, a web application, and a LED board. In all honesty, we probably
spent too much time on these and not enough time on busbus-core, but the
applications were the fun part!

### [Android app][busbus_android]

[busbus_android]: https://github.com/spaceboats/busbus-android

In our last blog post, we talked about how we were able to make http requests
to the busbus web API for static data. Since then we have been able to use the
Android Location Services to get GPS location from the phone.  Using this
location, we allow users to get stops closest to the user. Using busbus, this
is easy with the following request:

http://fake-hosted-busbus.com/stops/find?latitude=xx.xx&longitude=xx.xx&distance=500

Which will get all stops withing 500 meters of the given location. 

Once busbus realtime for MBTA was added, we were able to get realtime data on
the Android app.  The great part about this is that we didn't have to change
ANY API requests. In order to get only static data, the only thing that needs to be
added to the request is "realtime=False".

Enough talking about it, here is the app in action:

TODO update with new video

<video autoplay loop controls style="display:block; margin-left:auto; margin-right:auto; width: 50%;">
    <source src="/assets/{{ page.id }}/androiddemo.mp4" type="video/mp4">
</video>

So what did we learn from writing the Android app? Well it helped us figure out
common use cases and issues with the busbus web API. We also learned that
Android is hard. There is a pretty steep learning curve so we are proud of what
we were able to accomplish with this app. We also gained some respect for
stable Android apps because we saw a lot of this...

<img src="/assets/{{page.id}}/androidcrash.png" alt="Android Crash" style="display:block; margin-left:auto; margin-right:auto; width: 50%;"/>

### [Web app][busbus_web_app]

[busbus_web_app]: https://github.com/spaceboats/busbus-web-sample

Nick and Monica have continued working on a web app to demonstrate how focus
can be placed on building the application rather than the process of retrieving
data from different transit agencies on the back end. The web application uses
a Flask framework, Python for the server side, JQuery for the client side, and
HTML/CSS formatting. It currently has a ‘home’ link linking to the busbus
repository on GitHub, a ‘datastream’ link, and a ‘search’ link.

<img src="/assets/{{page.id}}/web_homepage.jpg" alt="Web-sample Homepage" style="display:block; margin-left:auto; margin-right:auto; width: 100%;"/>

The ‘datastream’ link displays current arrivals in Lawrence, KS by bus route,
stop name, and headsign, and refreshes every five seconds. This was
accomplished by querying busbus for arrivals based on the Lawrence Transit
provider id and a start and end time as parameters. The data is then placed in
a table which appears on the screen, and the whole function is ran again after
a timeout on a continuous loop to ‘stream’ the data.

<img src="/assets/{{page.id}}/web_datastream.jpg" alt="Web-sample Datastream" style="display:block; margin-left:auto; margin-right:auto; width: 100%;"/>

Finally, the search functionality was added. The search tab allows the user to
select the transit agency upon which to conduct their search. Once this has
been done the appropriate data corresponding to routes and stops is loaded, and
the user can then select a time range, route and stop to display arrivals for.
The same sorts of queries are performed using busbus to retrieve the data.

<script src="https://gist.github.com/alexdgus/d5e6e835017da7291893.js"></script>

We aren't currently hosting this web app so here are a bunch of screenshots of
it in action.

<img src="/assets/{{page.id}}/web_searching.jpg" alt="Web-sample Waiting for Routes" style="display:block; margin-left:auto; margin-right:auto; width: 100%;"/>
<img src="/assets/{{page.id}}/web_addsearchparams.jpg" alt="Web-sample Adding query params" style="display:block; margin-left:auto; margin-right:auto; width: 100%;"/>
<img src="/assets/{{page.id}}/web_arrivals.jpg" alt="Web-sample Arrivals displayed" style="display:block; margin-left:auto; margin-right:auto; width: 100%;"/>

While the web app is quite simple in terms of functionality, both Nick and
Monica did not have much experience with web applications, and achieved the
goals they set out to accomplish with the project.

### [LED board][led_board]

[led_board]: https://github.com/spaceboats/3001-ledboard

Since our last blog post, we have completely rewritten the code for the LED
board using Node.js. Our [previous implementation][led_blog] was... interesting.
It had a Python web interface that ran a C++ board controller. We decided that
this was kind of a weird solution and it was difficult to prototype new
features because it took so long to compile.  We decided we could come up with
something better and found that a bunch of the libraries we had been using had
been ported to Node.js. Since we already had some experience with Node from the
labs at the beginning of the year, we figured that it would be a better option.
After rewriting everything in Node, we now have a much more sensible solution.
The main feature is what we call a state queue. Each state is a scene that is
displayed on the board. The state queue has add and remove features that can be
accessed by the web API, [documented here][led_github] (sorta). As of right now,
the only supported state is text, but we plan to add support for images as well
as more advanced text layouts by the end of the semester. We also have a web
app that allows users to add and remove text states. This web app is available
to any user inside the EECS network [here][led_webapp], assuming we actually
have the server running at the time.

We hope that the LED board will be used by future senior design classes. All of
the code is public on github [here][led_github], so it will be easy to add
features, or write something totally new that will utilize this resource.

Specific to our project, we are using the board to display upcoming bus
arrivals for the stops around the Engineering building. This serves as a good
example of how to make simple http calls to the busbus web API.

<video autoplay loop controls style="display:block; margin-left:auto; margin-right:auto; width: 50%;">
    <source src="/assets/{{ page.id }}/ledboardvideo.mp4" type="video/mp4">
</video>

[led_blog]: http://spaceboats.github.io/2014/10/31/building-brighter-lab-resources/
[led_webapp]: http://3001-ledboard.spaceboats.net/
[led_github]: http://github.com/spaceboats/3001-ledboard/

## Can busbus Continue Being a Thing?

The semester is nearing a close and we are about to be done working on busbus
for senior design. Our hope is that busbus continues to be a thing, and maybe
even grows into a somewhat widely used thing that supports transit agencies
around the world. All of the code is open source so anyone interested in
pitching in is welcome.  Find more infomormation on our [github group
page][spaceboats_github].

[spaceboats_github]: https://github.com/spaceboats/

