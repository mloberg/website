+++
title = 'Playr - The Ruby Music Manager'
categories = ['ruby']
proof = false
+++
A while back I started to learn Ruby. When I learn something I have to be actively working with it. That's why I started Playr. The goal was to create a webapp that could manage music. After a month I got a basic webapp that you could upload music to and queue up music, and after a little more work, I actually got it to play music.

I deployed it on the music computer my company at the time was using, and it was received pretty well. After a couple of weeks in production, I realized some bugs in it, and went to work on version 2.

At first, version 2 was just suppose to fix the process issues Playr dealt with in v1. After diving into the code again, I decided to throw most of it out and start again. To force myself to learn more tools, I used haml, SASS, and CoffeeScript in v2. After some slowing issues (from rendering SASS and CoffeeScript every request), I went to Unicorn and some Rack plugins to render SASS and CoffeeScript assets. But the biggest issue from v1 was still there, processes crashing. In v1 all processes were in a single file in different forks. This was really hard to manage, and often times when one crashed, the rest would crash. I looked into process management tools, and found [god](http://godrb.com/). God is a process monitoring framework. Once I got the webapp (Sinatra), music, and WebSocket processes over to god (and some configuring) the processes became stable and easy to stop and restart. Because adding another process was almost nothing, I added a simple task management process. There are a couple more additions/fixes from v1, but I won't go into them.

I am so happy with Playr, that I am open-sourcing it today. You can find the code on [GitHub](https://github.com/ivorisoutdoors/Playr), and a [wiki](https://github.com/ivorisoutdoors/Playr/wiki) to get you going with Playr.
