---
toc: true
layout: post
description: using user defaults for onboarding
categories: [iOS, Swift, onboarding]
title: iOS Onboarding: Part One
---
# FirstTimeUser or Nah?

## User Defaults
We can determine if a user is a first time user by setting boolean flags via **UserDefaults**, which Apple defines [UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults) as: 
>An interface to the user’s defaults database, where you store key-value pairs persistently across launches of your app.

I find **UserDefaults** very useful for determine things like: 
  - is a customer a first time user?
  - is a customer a beta tester? 

## Setup
We all have to deal with onboarding at some point so below are general steps to get one started.

1. create a boolean flag in the coordinator responsible for handling the OnBoarding flow: 
    ```swift
    class AppCoordinator: Coordinator {
    
        func start() {
            let isFirstTimeUser = UserDefaults.standard.bool(forKey: "isFirstTimeUser")
            
            if (isFirstTimeUser) {
                presentOnboarding()
            } else {
                presentDefaultFlow()
            }
        }  
    }
    ``` 

    Substitue coordinator for AppDelegate/SceneDelegate if you don't follow the coordinator pattern:
    
    ```swift 
    class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    
      //MARK: Instance functions
      func functionThatDeterminesWhatVCToPresent() {
        let isFirstTimeUser = UserDefaults.standard.bool(forKey: "isFirstTimeUser")
        if (isFirstTimeUser) {
          presentOnboarding()
        } else {
          presentDefaultFlow()
        }
      }  
    }
    ```

2. you need a mechanism to indicate that the onboarding flow is complete. Typically this is accomplished via a button click. Thus in the viewcontroller responsible for handing onboarding user interactions, create an action that toggles the `isFirstTimeUser` boolean flag to false. 

    If you use a storyboard, the code for that would look something like this:
    
    ```swift
    class OnboardingViewControllerOrPageController {
    
      //MARK: - Properties
      @IBOutlet weak var onboardingCompletedButton: UIButton!
      ...
      //MARK:- Actions
      @IBOutlet func onboardingCompletedButtonTapped(_ sender: UIButton) {
        let isFirstTimeUser = false
        let defaults = UserDefaults.standard
        defaults.set(isFirstTimeUser, forKey: "isFirstTimeUser")
        //add code for what happens after boolean is switched
      }
    }
    ```
    
    If you use programatic constraints, the code looks something like this: 
    
    ```swift 
    class OnboardingViewControllerOrPageController {
        private func whenOnBoardingCompletedButtonIsTapped() {
            onboardingView.onboardingCompletedButton.addTarget(self, action: #selector(ActionButtonProtocol.buttonTapped(_:)), for: .touchUpInside)
        }   
        
        private buttonTapped(_ sender: UIButton) {
            let isfirstTimeUser = false
            let defaults = UserDefaults.standard
            defaults.set(isfirstTimeUser, forKey: "isfirstTimeUser")
            coordinator?.onboardingCompleted()
        }
    }
    ```

Note that we have the coordinator handle wnat to present next after onboarding is completed (via a call to `coordinator?.onboardingCompleted()` function). This is because we typicaly want to present a new ViewController (thus returning to applications default flow) at this point -  presenting any ViewController is the responsibility of a coordinator because coordinators deal with navigation flow. 

## Resetting UserDefaults 
Discovered that **UserDefaults** does not have a `reset` function while testing. 2 options that work are: 
- use `removeObject` function to remove the key of a set value thus setting resetting the entire key-value to nil. For example: `UserDefaults.standard.removeObject(forKey: "isFirstTimeUser")`

- writing an extension. [Bart Jacobs](https://twitter.com/_bartjacobs) has a really good post on this [here](https://cocoacasts.com/ud-8-how-to-clear-or-reset-user-defaults-in-swift)

### Summary
The logic followed/implemented is simple. 
- Is this a first time user? 
- Yes => present the onboarding flow
- No => present the application's usual flow
