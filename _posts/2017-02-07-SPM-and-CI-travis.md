My notes on Swift PM + CI Travis<!--more--> 

<img width="600" alt="img" src="https://rawgit.com/stylekit/img/master/TravisCI-Full-Color.eps.svg">

## Workflow:

1. Create a project named HelloTravis on github. Then download it via Github desktop app
2. Terminal: ``cd dev/HelloTravis`` 👈 Navigate to the folder you just downloaded
3. Terminal: ``swift package init`` 👈 Creates a Package.swift file etc
4. Terminal: ``swift test`` 👈 Runs the first test
5. add an .travis.yml see [YML paragraph](#yml) 
6. Uploaded all the changes to github again. 
7. Create an account at: [travis-ci.org](https://travis-ci.org) (it takes zero time if you are logged into github)
8. Find your HelloTravis project in your account list. Then switch the on/off but to on. 
9. Copy the MarkDown badge and add it to your README.md file in the HelloTravis project on github. 
10. Wait a little while and your Badge will become Green if all tests pass like this: [![Build Status](https://travis-ci.org/stylekit/swift-utils-tests.svg?branch=master)](https://travis-ci.org/stylekit/swift-utils-tests)
11. Add More complex tests under the HelloTravis/Tests/HelloTravisTests.swift file. 
12. Add nested dependencies to your Tests by following this [Tutorial on SPM + Nested frameworks](http://stylekit.org/blog/2017/02/06/SPM-and-nested-frameworks/) 

## YML

A typical ``.travis.yml`` file looks like this: (This is the config file for Travis)

```
os:
  - osx
language: generic
sudo: required
dist: trusty
osx_image: xcode8.2
script:
  - swift build #Builds the dependencies
  - swift test #Tests whatever is inside the Test folder
notifications:
  email:
    on_success: never
    on_failure: change
```

## Final word: 

Some people like to keep a /Tests folder in the github repo. I prefer not to because they are not always relevant to whom ever uses the repo. As such I prefer to keep tests in a "test" repo. Which pulls in the dependencies from other repos. Travis-ci has a simple button which can test your project daily/weekly/monthly. So you don't have to push changes to test things. 