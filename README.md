# FiniteStateMachine
## Overview
This project is an easy to implement state machine that allows for either manual or automatic state tranisitions. Automatic state transitions can happen when the configurable maximum tick count has been reached. This can be done inside of timer interrupts, for set intervals, or something like button presses.

At a minimum, each State in this state machine must have a function that fires when the state is updated. Optionally, there can be callback functions that are fired when a state is entered or exited, as well as assigning each state an ID number. While the FSM library has operator overrides to directly compare states with == or !=, State ID is useful for comparing states inside switch statements that require a numeric comparison.

## Table of Contents 
- [Requirements](#Requirements)
- [Installation](#installation)
- [Quick Start](#Quick-Start)
- [Function Reference](#Function-Reference)
- [Credits](#Credits)

## Requirements
- Arduino.h 

## Installation
Using command line git, you can install this as a submodule with:

    git submodule add https://github.com/kgmossey/FiniteStateMachine.git <project root>/lib/FiniteStateMachine
    
## Quick Start
This is a quick example for how to implement this library in your own code:
```
#include "FiniteStateMachine.h"

enum StateList {
  aState,
  anotherState
};

State FirstState (aState, someUpdate);
State SecondState (anotherState, anotherUpdate);
FSM StateMachine (FirstState);  // Set the current state to "FirstState"

void main() {
  StateMachine.transitionTo(SecondState);
  StateMachine.update();
}

void AnotherUpdate() {
  // do something
}
```
## Function reference
### State machine functions
Create a new state machine, the active starting state is *current*. There is a built in alias "FSM"

    FSM NewStateMachine(CurrentState);
  
Transition from the current state to a new state: (replace \<FSM> with the declared state machine variable name)

    <FSM>.transitionTo(NewState);

Currently any code in the update() callback function must be called manually:

    <FSM>.update();

The internal tick keeps track of the automatic trigger condition. To set the trigger value and use this feature use the following functions:

    <FSM>.setMaxTick(value);           // a non-zero value to set the maximum tick counter value
    <FSM>.getMaxTick();                // returns the MaxTick value
    <FSM>.setTrigger(TriggerFunction); // sets the callback function when the trigger condition is met
    <FSM>.wasTriggered();              // A boolean that returns true only immediately after triggering. Any other time this will return false
    <FSM>.tick();                      // increment the internal tick counter
    <FSM>.getTick();                   // return the current tick setting
    <FSM>.resetTick();                 // manually reset the tick count to 0

To check either the current state, or peek at the previous state, use the following:

    <FSM>.getCurrentState()            // returns the State object of the current state
                                       //   ex: if(MySM.getCurrentState() == Somestate) { // do something }
                                       //   note: both == and != are valid comparison operators
    <FSM>.getCurrentStateId()          // returns the current State objects ID if it was set
                                       //   ex: switch(MySM.getCurrentStateID()) { case enum1: //do stuff }
    <FSM>.getPreviousState()           // returns the State object of the previous state
    <FSM>.getPreviousStateID()         // returns the previous State objects ID if it was set
    <FSM>.isInState(State);            // returns true if the State object parameter matches the current state
                                       // example use: if(MySM.isInState(Somestate)) { // do something }

States can be optionally configured with an execution order. If it's configured use the following to transition the next state in the order:

    <FSM>.transitionNext();            // transition to the next state as configured by <State>.setNext(<NextState>);

The following function exists from the legacy code but doesn't do anything at the moment:

    <FSM>.timeInCurrentState();

### State functions
To create a state object, you have several constructors. At a minimum you must either include a unique numeric ID value or an update callback function. The other callback functions available are enter and exit. Valid constructor syntax is as follows:

    State( void (*updateFunction)() );
    State( void (*enterFunction)(), void (*updateFunction)(), void (*exitFunction)() );
    State( byte stateID );
    State( byte stateID, void (*updateFunction)() );
    State( byte stateID, void (*enterFunction)(), void (*updateFunction)(), void (*exitFunction)() );

The enter and exit functions are executed automatically upon entering and exiting the state. The update function must be manually called. To manually execute any of the callback functions at any time use the following:

    <FSM>.enter();
    <FSM>.update();
    <FSM>.exit();

To set a transition order, you can use the following functions: (note this is set at the state object level, not the state machine level)

    <State>.setNext(nextState);

To view a particular states next state in the transition order:

    <State>.getNext()

## Credits
Original library by Alexander Brevig (alexanderbrevig@gmail.com). Updates by Kevin Mossey.
