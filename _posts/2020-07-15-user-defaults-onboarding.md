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

