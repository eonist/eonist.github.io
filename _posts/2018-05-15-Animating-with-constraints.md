My notes on animating with constraints in swift<!--more-->. This example constitutes my "starter kit" when making animation scenarios that works with UIKit. 

### Example app:

<img width="559.5" alt="img" src="https://rawgit.com/stylekit/img/master/weatherapp_rev1.mov.gif">

### App source code:

[https://github.com/eonist/WeatherApp](https://github.com/eonist/WeatherApp) 

### Demo gif:
<img width="516" alt="img" src="https://rawgit.com/stylekit/img/master/constraint_animation.mov.gif">

### Swift code:

```swift
class AnimVC:UIViewController{
    lazy var square:Square = createSquare()
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .green
        _ = square
    }
}
extension AnimVC{
    func createSquare() -> Square{
        let square = Square()
        square.backgroundColor = .orange
        self.view.addSubview(square)
        _ = {//Define constraints for Square
            square.translatesAutoresizingMaskIntoConstraints = false
            let anchor = Constraint.anchor(square, to: view, align: .centerCenter, alignTo: .centerCenter)
            let size = Constraint.size(square, size: CGSize(width:100,height:100))
            square.anchor = anchor
            square.size = size
            NSLayoutConstraint.activate([anchor.x,anchor.y,size.w,size.h])
        }()
        
        let tap = UITapGestureRecognizer(target: self, action:  #selector(handleTap))
        square.addGestureRecognizer(tap)
        return square
    }
    @objc func handleTap(_ sender:UITapGestureRecognizer){
        let newConstraint = {
            guard let oldAnchorConstraint = self.square.anchor else {fatalError("err posConstraint not available")}
            NSLayoutConstraint.deactivate([oldAnchorConstraint.x])
            let newAnchorConstraint = Constraint.anchor(self.square, to: self.view, align: .topLeft, alignTo: .topLeft, offset: CGPoint(x:0,y:0))
            NSLayoutConstraint.activate([newAnchorConstraint.x])
            self.square.anchor?.x = newAnchorConstraint.x
        }
        let anim = UIViewPropertyAnimator(duration: 0.3, curve: .easeOut, animations: {
            newConstraint()// ⚠️️ Set the new constraint goal
            self.view.layoutIfNeeded()//⚠️️  Ask the parent view to update its layout
        })
        anim.startAnimation()
    }
}
class Square:UIView{
    var anchor:(x:NSLayoutConstraint,y:NSLayoutConstraint)?
    var size:(w:NSLayoutConstraint,h:NSLayoutConstraint)?
}

```

### Supporting files:

After the jump, hit the keyboard char `T` and search for Constraint.swift and Alignment.swift. These are 2 very simple extensions that makes constraints easier to work with. 
All this can be achieved with regular constraints as well. 

[https://github.com/eonist/swift-utils](https://github.com/eonist/swift-utils) 
