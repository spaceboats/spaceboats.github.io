---
layout:     post
title:      Blag post title
author:     Zane Ralston
date:       2015-04-03 16:00
summary:    Work is getting done.
---
 
## busbus core
 
TODO: Fill this section

## busbus web ?

TODO: Fill this section

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

TODO: Fill this section
