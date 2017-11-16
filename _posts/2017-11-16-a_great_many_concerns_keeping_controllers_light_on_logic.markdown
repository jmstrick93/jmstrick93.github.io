---
layout: post
title:      "A Great Many Concerns: Keeping Controllers Light on Logic"
date:       2017-11-16 16:52:02 +0000
permalink:  a_great_many_concerns_keeping_controllers_light_on_logic
---


After a week of coding, wrestling with nested forms, and furious debugging, I had managed to throw together a well-functioning piecemeal set of controllers for my Clunkr app, full of actions that sometimes looked like this:

				```  
				def update
						@car = Car.find_by(id: params[:id])
						@car.assign_attributes(car_params)
						if !@car.brand.valid?
								if !!car_params[:brand_id] && !car_params[:brand_id].empty?
										@car.brand = Brand.find_by(id: car_params[:brand_id])
								end
						else
								@car.brand.save
								@car.brand_id = @car.brand.id
						end
						#weeds out any invalid car types
						@car.car_types = @car.car_types.reject {|type| type.id.blank?}
								if @car.save
										flash.clear
										flash[:notice] = "#{@car.full_title} successfully updated"
										redirect_to car_path(@car)
								else
										flash[:alert] = view_context.pluralize(@car.errors.count, 'error')+ " prevented this car from saving: "
										flash[:error] ||= []
										object.errors.full_messages.each do |error|
											flash[:error] << error
										end
										render 'cars/edit'
								end
						end
				end
				```

Oof.  A few adjectives come to mind here: hacky, ugly, convoluted, etc.  Clearly this and other controller actions were in dire need of refactoring and transfering of logic into other places.  Not knowing any better, I started moving the logic into some of my models:  for instance, I took the following lines from the update action above:      

```
flash.clear
flash[:notice] = "#{@car.full_title} successfully updated"
```


and moved it into my ApplicationRecord model, like so:

			```
			#models/car.rb
			
			...
			
			def success_message(action_name)
					flash.clear
					flash[:notice] =#{self.full_title} successfully #{action_name}ed"
			end
			```

This however, did not work at first, because ```flash``` is not available in models!  I found what I thought was a nifty trick to fix this by simply making flash passable to the method as an argument:

			```
			def success_message(action_name, flash)
					flash.clear
					flash[:notice] =#{self.full_title} successfully #{action_name}ed"
			end
			```

There were a number of problems with this: first, I realized that #full_title was present on most but not all of my models.  Realizing its usefulness, I went and made a standardized method on all of my models that served as a sort of "display name" for each of them.  

The more I looked at the method, the more I was bothered by the oddness of needing to pass "flash" into the method as an argument each time... it didnt seem very DRY. That's when I remembered that things like displaying flash messages are NOT the models' job, which generally handle all of the interactions between the object and the database, and a few other things like retrieving and displaying an object's attributes when prompted.  Therefore, this unavailability of flash in the models was likely an intentional design decision by the very opinionated [Rails Gods](https://en.wikipedia.org/wiki/David_Heinemeier_Hansson). 

I started looking around for a better place to put the method; ApplicationController, the controller superclass, was the most obvious candidate, but again my Rails senses were tingling because even though ApplicationController is a controller superclass, it is still a controller.  Besides, it already had over 100 lines of extra code in it, which was a problem in and of itself.  

So I took to the internets to see what information I could scrounge up.  A few very old questions (circa 2011) on StackOverflow seemed to suggest either putting this controller logic into the lib directory (outside of the app directory) or creating an "app/modules" directory to fill with controller helper methods, but this didn't quite seem right to me... surely the opinionated-but-omniscient Rails Gods would have forseen this issue and preempted it with a solution?

And the Rails Gods did deliver, because I quickly noticed the ```controllers/concerns```  directory, seemingly designed specifically to solve the issue I was experiencing.  (It also presented a nice opportunity to boost my StackOverflow score by adding a more updated answer for the issue).

So I was able to refactor my method to be DRYer and more versatile, like so:

			```
			def success_message(object, action_name)
					flash.clear
					flash[:notice] =#{object.full_title} successfully #{action_name}ed"
			end
			```

With the method now located in a module called "FlashHelpers" in the concerns directory, easily included in ApplicationController, I was able access flash without needing to pass it in as an argument.  Instead, I now needed to pass in the relevant object as an argument instead of using it as the method's receiver, but this seemed more appropriate given the task at hand.

Great!  Done, right?  Wrong.  Though the method in its current form was wonderfully abstract and flexible, there was still a key issue: most of the controller action names end in "e".  As the method currently stands, many of the flash messages will read like this:

"Car successfully createed"
"Resource successfully updateed"

This is a bit awkward.  Luckily I was able to use my vast knowledge of Ruby arrays and strings to my advantage, updating the method like so:

			```
			def success_message(object, action_name)
					flash.clear
					action_name[0...-1] if action_name[-1] == "e"
					flash[:notice] = "#{object.full_title} successfully #{action_name}ed"
			end
			```

Voila! Now we have a flexible success-message helper that can be used in most of the controllers that spells everything in its messages correctly.

Next, I took the copious amount of logic that I had improperly hidden away in my ApplicationController and put it where it belonged, in 'concerns'.  Some new methods I added, similar to those above, were:

			```
					def flash_errors_and_heading(object)
							flash[:alert] = view_context.pluralize(object.errors.count, 'error')+ " prevented this #{object.class} from saving: "
							prep_flash_errors(object)
					end

					def prep_flash_errors(object)
								flash[:error] ||= []
								object.errors.full_messages.each do |error|
										flash[:error] << error
								end
					end
			```


These flexible helper methods (not including the ones I have not listed here) on their own removed about 20-30 lines of code from my controllers in total.  Best of all, they are so flexible that I can use them in later projects with little-to-no tweaking.  Turns out my excessive use of concerns is not the least bit concerning.
