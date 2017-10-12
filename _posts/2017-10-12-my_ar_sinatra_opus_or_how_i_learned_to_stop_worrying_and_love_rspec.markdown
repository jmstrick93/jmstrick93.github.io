---
layout: post
title:      "My AR/Sinatra Opus (or: How I Learned to Stop Worrying and Love RSpec) "
date:       2017-10-12 18:07:02 +0000
permalink:  my_ar_sinatra_opus_or_how_i_learned_to_stop_worrying_and_love_rspec
---


The prompt for our Sinatra/ActiveRecord portfolio project said to make our program about something "important to you".  I like collecting books, so I decided to build a library cataloguing program.  I somewhat jokingly dubbed it "Opus" -- a great name considering what my software does, but sort of ironic considering the relative simplicity of the program.  Still, even as it stands right now, it could actually be really useful for managing my personal library, and be even better with a little bit of tweaking and a few added features.

I may have gone a little overboard with the number of models I created for this, especially since we were only really required to make two or three.  But I wanted to make this project into something that I could actually find useful.  Consequently, having six different interrelated models meant that there were a lot of "moving parts" to keep track of, but that ended up being a useful learning experience, for a number of reasons.  

When I was getting started, I started making RSpec tests as I went along.  In a previous project, I had attempted to do the same, but only had limited success with getting all of the tests working and actually making them useful.  As such, I did not plan to keep making them for long, especially since doing so made the initial process of writing code move forward relatively slowly.  But this time I didnt stop, and I kept making tests until I completed the basic functionalities of the program and had moved on to adding features like user accounts and input validation.  To my surprise, the fact that I had written these RSpec and Capybara tests made the final stretches of completing my program go by much more quickly than they would have otherwise.  

Laying down my RSpec tests early on made it much easier to keep track of all of my program's moving parts, and gave me peace of mind knowing that if a feature that I added caused something to break in my code, I would catch it immediately as long as I ran my rspec tests periodically after adding new code and features.  It really came in handy multiple times when my input validation or user accounts code caused a feature that I had worked on earlier to malfunction, which saved me a lot of time.

Since I stopped writing them while working on the last few features, I really got to see and feel the comparison between debugging with and without RSpec and Capybara.  While debugging the parts of my code that were not supported with tests, it took me at least as long, if not longer, to track down the location of problems that arose than it would have to just write tests for each of the features in which errors occurred.  In addition, when trying to do the walkthrough of my finished product, I kept running into issues that required me to stop recording and debug, and sometimes even start recording from the beginning.  If I had been demonstrating the software's functionality to clients or rolling out a version of the software for public use, those kinds of uncaught bugs would have been unacceptable.  I can only imagine how much more likely this is to occur when working with complex professional software, and how much more effort is needed to make sure that the programs are as bug-free as possible.

In sum, writing this program allowed me to put my knowledge of how Sinatra and ActiveRecord work together to good use, but my decision to use RSpec and Capybara tests allowed me to get even more out of the project by helping me to experience firsthand why it is useful to do so.  
