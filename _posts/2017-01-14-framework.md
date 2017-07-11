Some notes on swift .framework <!--more--> 

### Export:

1. Xcode -> New project -> Cocoa framework   
2. Add .swift files to your framework  
3. cmd + b (aka build)   

**If you want to build your framework along side your normal XCode project:**  

1. Save as target -> pick Cocoa framework  
2. Select .swift files and set their target membership to the .framework  
3. cmd + b (aka build)  

### Import:

1. Right click on the ‘.framework’ and select ‘Show in Finder’  
2. copy the .framework file in the same folder where your .swift files are. Then drag it into Xcode.  
3. In your new project: Project settings -> General -> Embedded binaries add the  .framework via the + button🔑   


### Access level:

Swift has three levels of access control. Use the following rules of thumb when creating your own frameworks:  
- **Public**: for code called by the app or other frameworks, e.g., a custom view.  
- **Internal**: for code used between functions and classes within the framework, e.g., custom layers in that view.  
- **Fileprivate**: for code used within a single file, e.g., a helper function that computes layout heights.  
- **Private**: for code used within an enclosing declaration, such as a single class block. Private code will not be visible to other blocks, such as extensions of that class, even in the same file, e.g., private variables, setters, or helper sub-functions.  
- **@testable** if you prefix your import with ``@testable`` then you can work with framework classes even if they are not marked public 🔑  


### Resources:
- Make a framework of your swift files in a xcode project. And also add **playground** that can import the framework: [here](https://medium.com/@LogMaestro/adding-playgrounds-to-your-xcode-project-79d5ea0c7087#.q27u3w639)   
- Use the a .framework file in other projects by copying it: [here](https://www.youtube.com/watch?v=vChxJ_Nk6kI)  
- Export and Drag and drop .framework to different projects: [here](http://stackoverflow.com/a/40991398/5389500)  
- Nice video [here](https://realm.io/news/tryswift-jeff-hui-creating-a-swift-library/)  

### NOTES:

- Apple claims that its best to use less than 6 .frameworks in your app. Or else compile time may suffer  
- Dynamic frameworks supported after ios 8 on iOS. Dynamic frameworks are linked form your app, static frameworks are included in the app.  