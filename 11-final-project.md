# Final Project

It's time to put some of those newfound ES6 skills to the test. For a final project we'll build a timer that emits events at certain points in its cycle. We'll define the public interface for each class. The implementation details are up to you.

## EventEmitter

Create an EventEmitter that registers handlers and runs those handlers when events are emitted.

* emit - should emit an event, accepting any number of args, calling all event handlers
* on - register an event listner with a handler
* once - register an event listener that runs exactly once
* many - register an event listener that runs n times
* remove - remove a handler for an event
* removeAll - remove all handlers for an event

## Timer

Create a Timer that runs every n milliseconds until stopped. Timer actions (jobs) may be synchronous or asynchronous. For long running jobs the timer should not start another while the first is in progress.  

* initialization - interval, an action to perform, if timer should auto start (consider the implications of this for event listeners), if it should ignore any errors that might occur in the passed action.
* start - method that begins the timer
* stop - method that stops the timer
* interval - a property for retrieving and setting the timer interval, ensure it only accepts positive numeric values

### Events

Using your EventEmitter emit the following Timer events.

* timerStart - emitted when the timer starts
* timerPerform - emitted before the timer performs its designated action
* timerDone - emitted after a timer cycle completes
* timerError - emitted when a timer action has an error
* timerStop - emitted when a timer is stopped

## Bonus

* track timer action executions
* optionally allow multiple running jobs
* optionally allow the timer to run *n* times
* create static functionality to hold all created timers
* waitTime - returns time in milliseconds until next execution
