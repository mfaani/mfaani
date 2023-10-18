---
title: "State Machine"
date: 2023-10-16T20:16:42-04:00
draft: true
---

## What's the difference between the 'New State' and 'Command' in State Machine Architecture?

In the code smaple, Andy is first setting the state machine to the new state. Then gives a command. 
The command is optional. The purpose of the command is to help _apply_ the state change.

Example, if user went from UnAuthorized state to an Authorized state, then from the perspective of the state machine, everything is complete and checked. Yet to the user who's using the app, what will change? To explain: 

oldState: Unauthorized + event: correct user/pass given -> newState: Authorized
Does simply setting the new state to 'Authorized' cause the app to transition? No! Does it replace the login screen with the user's account? No!

Your state machine needs to emit a command. And something needs to process that command. Example of command is: `.navigateScreen(to: .authorized)`

Your State Machine can be either at the business level or at the programtic level. Examples:

**Business Level:**
- Login Screen
- Start Screen
- Transaction Screen

**Engineering Level:**
- Login Screen
- Login Loader
- Login Success
- Start Screen
- Transaction Screen
- TransAction Loader
- Transaction History 
- Transaction Failure
- etc. 

Piecing it all together: 

`LoginScreen.loginTapped` passes user/pass and calls `loginController.login`. The screen no longer affects the state machine. 

`loginController` calls `loginManager.loginAPI(user: user, pass:pass)` returns success message. 

`LoginScreen.login`


```
/// First up, the StateType. Note that this type is isolated, pure, trivial to fully test with zero mocks/stubs, etc.
enum HouseState: StateType {
	case dirty
	case clean
	case cleaning

	enum Event {
		case momWillArriveInOneHour
        case momArrived
		case kidsPlayed
		case cleaning
        case cleaningFinished
	}

	enum Command {
		case clean
		case checkHouse
        case stopCleaning
	}

	static let initialState = HouseState.clean
	
	mutating func handleEvent(event: Event) -> Command? {
		switch (self, event) {
		case (.clean, .momArrived):
			return nil
		case (.clean, .kidsPlayed):
			self = .dirty
            return .clean
		case (.dirty, .cleaning):
			return nil // so it continues cleaning
        
        case (.dirty, .cleaningFinished):
            self = .clean
            return .stopCleaning
        case (.)

		
		default: break
			// Or should we throw?
			// In a more ideal world, it should be impossible to write code which reaches this line (I discuss
			// how this might be achieved at the very end).
		}

		return nil
	}
}
```

