How you can speed up compile times in XCode with Swift Package Manager<!--more--> 

## Workflow features:
✅  Speeds up builds  
✅  Simple to setup    
✅  Version Controlled  
✅  Encourages re-usability  
✅  Future proof  
✅  Supports macOS  
🚫  Supports iOS  

## Sync your modules to github:
1: ``cd /dev/MyApp/Packages/My-Awesome-Module-1.0.0/`` 👈 navigate to your module  
2: ``git checkout master`` 👈 switch to the master branch for your module  
3: ``git commit -a -m "Updated Feature-x"``  
4: ``git push origin master``  

## Blazing fast compile times:
When parts of your apps get complete and is not actively worked on move it to a Module. This will make your app faster to build because your only re-compile the code that is not in your modules. If you change code inside a module it will re-build When you run the app, it will take some time. If you want other parts of your app to be able to use new method calls, just hit ``cmd + b`` and it will become available to your entire app.

## Module oriented programing:
By keeping your app in modules you are also able to reuse these in XCode playground. Because modules produce .framework files which works in XCode playground read [this](http://stylekit.org/blog/2017/01/16/playground-and-framework/)  for a .frame work + playground workflow

## Further reading: 
- Use .framework by splitting up in different frameworks helps with compile time artsy.com, see comments) [here](http://artsy.github.io/blog/2014/11/13/eidolon-retrospective/)   