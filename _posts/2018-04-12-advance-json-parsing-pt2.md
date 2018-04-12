My notes on "Throw Oriented Code", great for parsing spaghetti JSON <!--more--> ⚠️️ This article is in beta at the moment ⚠️️


### Example showing how you can create Nested Error types

And handle these accordingly from the first try call in the "try-hierarchy". In theory, you could structure entire Code frameworks with nested Try / Error code.

```swift
/*
 * TODO: Add description switch in the error types 
 * TODO: Add relevant JSON case
 */
struct A{
   let b:B?
   struct B{
       let c:C?
       struct C{
           let value:String?
       }
       func getC() throws -> C{
           guard let c = self.c else {
               throw OuterError.InnerError(error:.RegularInnerError)//👈 this is 2 steps deep in the call hierarchy
           }
           return c
       }
   }
}

func test(_ a:A) throws -> String {
   guard let b = a.b else {
       throw OuterError.RegularError
   }
   let c = try b.getC()
    _ = c
   ///Handle all the other,
   return "success"
}



enum OuterError: Swift.Error { //, CustomStringConvertible
   enum InnerErrorType:Swift.Error{
      case RegularInnerError
      case OtherInnerError
   }
   case RegularError
   case UnexpectedError(message: String)
   case InnerError(error: InnerErrorType)
}

func login() {
   Swift.print("login")
   do {
       let b = A.B(c:nil)
       let a = A(b:b)
       let result = try test(a)
       print("result \(result)")
   } catch OuterError.RegularError {
       print("regular err")
   } catch OuterError.InnerError(let innerError) {
       print("inner err")
       switch innerError {
       case .RegularInnerError:
           print("regular inner err 🎉 i'm deep in the hierarchy 💪")
       case .OtherInnerError:
           print("other err")
       }
   } catch {//all other cases
       print(error)
   }
}

login()

//Output:
//login
//inner err
//regular inner err 🎉 i'm deep in the hierarchy 💪
```