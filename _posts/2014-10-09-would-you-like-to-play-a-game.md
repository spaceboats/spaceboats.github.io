---
layout:     post
title:      Would you like to play a game?
author:     Ian Weller
date:       2014-10-09 20:30
summary:    You are at the KANSAS UNION. Obvious exits are NORTH, SOUTH, and DENNIS.
---

For our first team project in EECS 581, we were given [a Node.js web
app](https://github.com/ku-eecs-capstone/lab2), which worked as an
adventure game set on KU's campus. The point of the game was simple:
find the basketball, take it to Allen Fieldhouse, you win.

![Do not attempt to drop your laptop at Eaton Hall](/assets{{ page.id }}/lab2_orig.png)

Three members of our group --- Alex, Nick, and I --- worked on this
particular version, which is available at [ianweller/lab2](https://github.com/ianweller/lab2).

The goal of this project was to make some improvements to the game.
Specifically, we needed to add:

* non-interference (kind of), so one user doesn't somehow ruin the game
  for everyone else;
* interactivity, so the user could interact with other players in the
  game; and
* persistence, so the user could refresh and remain in the same state.

## Real-time updates

Before we did any of the above, we wanted to make sure that there was as
little delay as possible between a user changing the world and another
user seeing those changes. So we used [WebSockets](http://en.wikipedia.org/wiki/WebSocket),
which is one of the more recent developments in the real-time web.

The original code was written in [Express](http://expressjs.com); I knew
off-hand that [Socket.IO](http://socket.io) was a quick and hacky way to
get started with WebSockets, and it turns out that
[Express.io](http://express-io.org/) is a drop-in replacement Express,
but with the added APIs of Socket.IO.

So instead of polling every two seconds or so for updates, we wait for
events to go across the socket --- a `world-changed` event calls the
same `refresh` function that was in the original code.

## Non-interference

In the original code, there was no concept of separate users playing the
same game at once. If another client connected, they could pick up items
into their inventory --- which was shared with every other client, so
*everybody* picked up the same item. Instead of keeping track of one
inventory, we kept track of separate users, who each had their own
inventories.

We made the decision that there should only be one of each object in the
world, mostly because we did not want to invent a replicator. So the
inventory of each "room" remains a global variable, not a user variable.

## Interactivity

Users in the same world ought to be able to interact with each other. We
couldn't think of anything actually fun to do, so we implemented buttons
that let you slap other players, which simply printed a message on their
screen saying they were slapped.

Be careful! You can slap yourself and there is no way to know which user
ID is yours.

!["Stong Hall" (sic)](/assets{{ page.id }}/slapping.png)

The goal was to make it so slapping dropped all your items, but that was
two weeks ago and I'm pretty sure our entire team forgot.

## Persistence

What happens when the server crashes? It sure would be nice to keep
track of what was going on in your extremely complicated adventure.

We adjusted the code so that when a world-changing event occurs, a
summary of that change is printed to a file, which is then read on
server startup. It's not as robust as a database, but it certainly gets
the job done.

## What we learned

* We learned about HTTP and creating RESTful APIs with the GET, POST,
  PUT, and DELETE methods in Express.
* We learned about implementing WebSockets with Socket.IO on both the
  server and client.
* We learned about how JSON documents are created (and how simple they
  really are).
* For those of us who haven't used JavaScript very much, we gained some
  experience programming with it.
* None of us had ever used Node so that was a learning experience.
* In general we learned a little about the process of writing web apps.
