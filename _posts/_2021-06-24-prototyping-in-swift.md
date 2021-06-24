My notes on prototyping applications in swift<!--more-->

### Introduction
- Creating a modern app is hard. You have to implement darkmode, and support many different sizes and many different versions of iOS. It wuickly gets out of hand.
- It's even harder to modernize legacy apps with a lot of spagethi code all over the place
- So regardless if you making or upgrading an app its smart to begin from the start, and build a prototype in code, that you later can add more complex code on top. You also get the benefit of having everything fresh in mind so you can use this intimate but ephemeral knowledge and move quickly when implementing the more complex functionality on top.

### Problem:
- App projects seems daunting
- So many requirements
- So high ecxpectations
- So little time and resources to use

### Solution:
- Build a mock app. With fake data and fake graphics.
- Focus on how the app works, not understanding and implementing complex apis
- Focus on getting the spacing and interactions right
- Since mock-data is always simpler to code, the codebase remain easy to experiment with
- Once your happy with how the app feels, Start adding more complex logic and apis and cover side effects
