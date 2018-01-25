---
layout: post
title:      "Posting to your Rails API backend with React/Redux"
date:       2018-01-16 14:09:52 -0500
permalink:  redux_is_life
---


When I was seemingly finished with the hard parts of my ReactDice project (i.e. the parts involving React) I turned to the seemingly innocuous task of making my app interact with the backend API I had set up.  But soon after starting, I came upon a major stumbling block: while it seemed that processing GET requests was relatively straightforward, POSTing to the API proved to be a major headache.

Here's why:  I needed to be able to POST the contents of a roll of the dice to my database to save roll history (a feature which I plan to expand in later versions.)  This involved setting up a POST request as a side effect of the rollDice() action that updates the Redux state of my app.  Theres just a few small problems with this: 

1.  React actions are supposed to be pure functions with no side effects.  Adding a POST request as a side effect of an action violates a core tenet of the functional programming ideas at the core of React and Redux.  Redux's `thunk` middleware provides a workaround for this, allowing the user have the action return a function that produces multiple effects.

2. Even when using the aforementioned `thunk` middleware, I needed the API post to happen *after* the object's state and/or props had changed in response to the action, not before.  

The latter of these two points made it seem as though React lifcycle methods would be the best approach.  The most promising contender among these seemed to be `ComponentDidUpdate()` which, as the name suggests, is triggered *after* the component has finished updating its state and/or responding to new props it has received.  

Just one problem: the component that I needed to watch -- DiceListContainer -- updates quite frequently, and not only after a roll of the dice.  It also updates whenever a new die is added or removed or when the amount of sides on one of the dice is changed.  Simply placing the action that would trigger the POST request within `ComponentDidUpdate()` caused data to be posted to the API with *every* update, which was less than ideal.

The fix, however, turned out to be quite simple. 

First, I wrote some code designed to determine what kind of state and/or props change had taken place:

```
componentDidUpdate(prevProps, prevState){
    let lengthChange
    let sidesChange
    prevProps.dice.length === this.props.dice.length ? lengthChange = false : lengthChange = true;
    if (lengthChange === false && this.props.dice.length > 0){
      let index = 0
      for (const die of this.props.dice){
        if (die.sides !== prevProps.dice[index].sides) {
          sidesChange = true;
          break;
        } else {
          sidesChange = false;
          index++
        };
      };
    }
...
```

This basically checked to see whether, the update was triggered due to a change in the number of dice or the change in the number of sides on one or more of the dice.  If neither of these two things had occurred, then the render must have been triggered by a roll of the dice, since this is the only other kind of action that could trigger an update.

Then, after checking making sure that there was no change in length or size (i.e. `lengthChange === false && sidesChange===false`) only then would the app be allowed to post to the API.  The complete code of componentDidUpdate() is below:

``` 
componentDidUpdate(prevProps, prevState){
    let lengthChange
    let sidesChange
		
    prevProps.dice.length === this.props.dice.length ? lengthChange = false : lengthChange = true
		
    if (lengthChange === false && this.props.dice.length > 0){
      let index = 0
      for (const die of this.props.dice){
        if (die.sides !== prevProps.dice[index].sides) {
          sidesChange = true;
          break;
        } else {
          sidesChange = false;
          index++
        };
      };
    }
    sidesChange === false && lengthChange === false ? postRollHistory(this.props.dice) : null
  }	
	```
	
I had spent an entire day trying to figure this out, so, upon discovering that this approach worked (!!!!) I was elated. Since I would expect that this sort of problem is a common stumbling block for React/Redux beginners, I hope that this post gives some of my readers ideas on how to approach it.  



