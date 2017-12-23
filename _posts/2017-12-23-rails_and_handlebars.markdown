---
layout: post
title:      "Rails and Handlebars"
date:       2017-12-23 19:00:02 +0000
permalink:  rails_and_handlebars
---


Now that I have finally completed my AJAX and JS project, I think I have earned the right to vent about it: it was hard!

Especially comparing to all the other material we have covered in this course, this was the most challenging for sure, particularly when it came to making sure multiple asyncronous requests and variables were all working together properly. 

On my first flyby of the project, I did something that we were sort of explicitly told not to do: I made my JavaScript render elements on the page by passing in interpolated strings in my JS stack.  This was not particularly good practice, but considering how clueless I was feeling at the beginning of the project, it amounted to a small, simple baby step that helped to boost my morale and get started.

After having done this to make some index and show actions of my Clunkr app load without the page needing to refresh, I went back to refactor my code so that I wouldn't be actually embarrassed by it -- enter JS Object Notation and Handlebars.

Though it took some time to review, the pattern of rendering elements by passing JS Objects into Handlebars templates made my life much easier.  Not only that, but it actually allowed me to access some of my Ruby/Rails variables in the Handlebars template sources to give my program even more functionality than before!  After refactoring these two parts of my app, I moved on to work on making POST requests via AJAX.

That's when things started to get really difficult.  Despite working with a somewhat more simple object (i.e. user-cars) the fact those objects functioned largely as a join table made things much more complicated; sometimes it was necessary to go three layers deep into the JSON response to gather info to display on the page. I was not actually able to do this due to some sort of unexplained bug that I was unable to solve, so I eventually settled for firing another AJAXT request to retrieve the information that I needed, and I had to force that AJAX request to be synchronous in order to make sure variables were assigned in the right order.  (This functionality has been deprecated for about five years, but it has yet to be removed, and rumor has it that it will not be removed until the jQuery wizards come up with a reasonable alternative).  This is an area that will require some later debuging on my part, but for now it works perfectly for my purposes.

Despite all of these difficulties, I was able to complete this project a day ahead of the schedule that I had set for myself.  When I started it I felt utterly clueless, but this project pushed me out of my comfort zone to make sure that I came out with a reasonably strong understanding of its covered topics in the end.
