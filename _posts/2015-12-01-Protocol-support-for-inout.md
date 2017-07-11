When you cast a class to a protocol you end up with an immutable reference, which cant be used in inout function parameters. So you can<!--more--> :  

**Update 1:** As long as you extend the protocol with class it can mutate variables, I'm unsure if it also works on `inout` variables example bellow:  

```swift
protocol IA:class{
	var text:String {get set}
}
class A:IA{
	var something:String = "" 
}
class B:A{
}
let b = B()
(a as! IA).text = "abc"//<- this line will throw an error if the protocol didnt extend class. Why? because protocols can also extend struct etc

```
 
1. Use Method overloading (which can duplicate your code and make it cognitively harder to read, even if you refactor)  
2. Not use inout (this is what you should do)  
3. Or you can do this: (which is code smell deluxe and not recommended)  

==Note to future self==: I think you can get away with casting variables as long as you attach them to a variable. THeir sub values should remain changeable when using modifier methods
```swift
protocol IPositional{
    func setPosition(position:CGPoint)
}
extension IPositional{
    var positional:IPositional {get{return self as IPositional}set{}}
}
class A:IPositional{
    var position:CGPoint = CGPoint()
    var someString:String = "default"
    func setPosition(position:CGPoint){
        self.position = position
    }
}
func test(inout positional:IPositional){
    positional.setPosition(CGPointMake(10,10))
}
var a = A()
a.someString = "new value"
test(&a.positional)
a.position//(10.0, 10.0)
a.someString//new value /*<--this proves that the a instance is the original*/
```
**Conclusion:**   
The benefit of doing it this way: Is that you can now have one "inout method" for all classes that implements IPositional 
But I recommend going with option 2. (Not using inout) 

**NOTE:**  
If you go with option 2 and you still need to change a variable in an instance, what you need to do is not use extension methods, if you use regular implicit setter methods you avoid needing to use inout: Quick example:

```swift
protocol IPositional{
    func setPosition(position:CGPoint)
}
class A:IPositional{
    var position:CGPoint = CGPoint()
    func setPosition(position:CGPoint){
        self.position = position
    }
}
func test(positional:IPositional){
    positional.setPosition(CGPointMake(10,10))
}
var a:Any = A()/*we define a as Any to test the theory*/
test(a as! IPositional)/*the argument now becomes a reference to the orgiginal instance, but it also becomes immutable unfortunatly*/
a.position//(10.0, 10.0)/*not to worry, the implicit settermethod isnt defined by swift as mutable, even though it is. I guess indirectly, so the values are mutated on the orginal instance and all is well*/
```

**NOTE:** 
If you get this warning:  
cannot pass immutable value of type 'IPositional' as inout argument

Then consider skipping Extension methods and stick to the implicit explicit getter and setter scheme described above. The reason you cant use extension method is because then you have to make the extension mutable etc. (This may be mitigated with the protocol :class suffix, research needed)

**NOTE:**  
I could be missing some key concept about protocol here, maybe there is some suffix that makes an protocol mutable after casting, but i havnt been able to find anything related to this on stackoverflow etc. (1-2 hours of research)

**NOTE:**    
Why is this concept so important? Because method overloading generates duplicate code, i like to reduce complexity and amount of code. Therefor i prefere Polymorphism, Yes one could do this with pure struct -> Extension -> extension subclassing. But Classes has benefits over the struct extension scheme, like: (cant recall atm, fill in this when you recall) Struct seems to be immutable and class seems to be mutable. Functional programming -> immutable. But IMO some data should be altered and not always copied.