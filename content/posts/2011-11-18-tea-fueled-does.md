+++
title = 'Tea-Fueled Does'
categories = ['php']
proof = false
+++
_Author's Note: Since posting this, Tea-Fueled Does has been deprecated. We recommend to check out [Symfony](https://symfony.com/doc/current/book/index.html) or [Laravel](http://laravel.com/). These are awesome and modern PHP frameworks._

Earlier this week I publicly released [Tea-Fueled Does version 2](https://github.com/ivorisoutdoors/Tea-Fueled-Does). An open-source PHP 5.3 framework written by myself.

#### A Little History

Tea-Fueled Does (or TFD) started 11 months ago when I was trying to build a web application. I tried out a couple of different frameworks, but didn't really like how any of them worked. After this disappointment, I set out to write my own framework. At this point I had been writing PHP for just over a year and really didn't know a whole lot (but I thought I did), so trying to write a framework was a challenge. I looked at a lot of source code to figure out how other frameworks functioned. After a month or so, I had a (barely) functioning framework. After trying to build something with it, I realized where it fell short and went back and tried to fix it. I would get into a pattern of rewrite, building, read source code, rewrite, etc. Finally after 5 months, TFD had become stable enough to give it a version number of 1.0.

While my initial goal with TFD was build a framework that functioned how I wanted it, it become a lot more then that. It had become a learning platform. I learned a lot about PHP and I also learned I don't know that much. It also became a place to experiment. I was using Git and branches are cheap, so I would often branch off, experiment, and if it worked, merged it back into, or if it didn't delete the branch. By version 1.5 I had some pretty neat features.

After version 1, I started using it at my work to build new websites and I was pleased with how it worked. This fall I decided to start writing that web app that inspired this in the first place. After getting ready to release it, I found a flaw in it.

When I added new features, it often was hacked in and not really thought about all that well. This lead to messy code and inter-tangled dependencies. When I branched and tried to fix the issue, it wreaked havoc on the framework. At this point, another rewrite was needed. After doing some initial rewrites, I realized this was going to have to be a complete rewrite/refactor.

2 months later and 277 commits version 2 was completed. Version 2 followed some best practices, was leaner, and included a lot more features then before. Version 2 is also the first version to be publicly released.

#### Why You Should Use TFD

There are tons of frameworks out there. Why should you check out TFD?

##### MVC

I know all frameworks are MVC, but TFD takes a little different approach to MVC. There aren't multiple controllers, models are optional, and views are also awesome (see below). Oh, and following an MVC pattern is completely optional. TFD will try to "auto-route" your request, so if you want, all you need is the view.

##### It's DRY

I hate having to write code over and over again. TFD aims to be as DRY (don't repeat yourself) as possible. We make use of a layout-view render engine, partials, and take care of user authentication without you having to write any code. MVC is also optional because of this reason.

##### It Fits To You

I originally designed TFD to fit the way I did things. But one thing I've learned is people have a different way of doing some things. That's why I've tried to make TFD fit to you and extendible. While it's not going to be perfect (you have to follow some rules for it to work), a lot of effort has been making it open.

##### It's Free

I've licensed TFD under the unlicense, so you can do whatever you want with it. Use for personal, non-profit, or commercial projects. Modify to source code, take bits of the code and use it in your code. You could even copy the framework and rewrite it if you want. I just ask you don't copy my code exactly and sell it as your own (that would be uncool).

#### Getting TFD

TFD must have you at least a little interested, you're still reading. The next step is to get a copy of TFD and start playing with it. Getting TFD is really easy. It's hosted on [GitHub](https://github.com/ivorisoutdoors/Tea-Fueled-Does), so you can download it there.

#### Getting Involved

If you like this project, you can get involved. You can fork this project, submit pull requests, ask for features, or whatever. If you have questions you can send me a message on [Github](https://github.com/ivorisoutdoors), or mention me on [Twitter](http://twitter.com/mloberg), and I'll try to respond to you.

Thanks and happy building.
