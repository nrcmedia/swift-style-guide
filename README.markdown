# NRC's Swift style guide and coding conventions

The conventions in this document should be used for all projects of NRC Media. As you can see it is forked from [The Official raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide), but is now a seperate document with its own additions and changes. See the git log for specifics.

The focus of this guide is similar to its origin: to have readable code that is consistent over all our projects — even though we have many different authors working on them. The overarching goals are conciseness, readability, and simplicity.

## Table of Contents

* [Naming](#naming)
  * [Class Prefixes](#class-prefixes)
  * [Protocol Naming](#protocol-naming)
* [Spacing](#spacing)
* [Comments](#comments)
* [Classes and Structures](#classes-and-structures)
  * [Use of Self](#use-of-self)
* [Function Declarations](#function-declarations)
* [Closures](#closures)
* [Types](#types)
  * [Constants](#constants)
  * [Optionals](#optionals)
    * [Nil coalescing operator](#nil-coalescing-operator)
  * [Struct Initializers](#struct-initializers)
  * [Type Inference](#type-inference)
  * [Syntactic Sugar](#syntactic-sugar)
  * [Generics](#generics)
* [Control Flow](#control-flow)
* [Semicolons](#semicolons)
* [Language](#language)
* [Smiley Face](#smiley-face)
* [Credits](#credits)


## Naming

Use descriptive names with camel case for classes, methods, variables, etc. Class names and constants in module scope should be capitalized, while method names and variables should start with a lower case letter.

**Preferred:**

```swift
let MaximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Not Preferred:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

For functions and init methods, prefer named parameters for all arguments unless the context is very clear. Include external parameter names if it makes function calls more readable.

```swift
func dateFromString(dateString: NSString) -> NSDate
func convertPointAt(#column: Int, #row: Int) -> CGPoint
func timedAction(#delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// would be called like this:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(delay: 1.0, perform: someOtherAction)
```

For methods, follow the standard Apple convention of referring to the first parameter in the method name:
```swift
class Guideline {
  func combineWithString(incoming: String, options: Dictionary?) { ... }
  func upvoteBy(amount: Int) { ... }
}
```

When referring to functions in prose (tutorials, books, comments) include the required parameter names from the caller's perspective. If the context is clear and the exact signature is not important, you can use just the method name.

> Call `convertPointAt(column:row:)` from your own `init` implementation.
>
> If you implement `didSelectRowAtIndexPath`, remember to deselect the row when you're done.
>
> You shouldn't call the data source method `tableView(_:cellForRowAtIndexPath:)` directly.


### Class Prefixes

Swift types are all automatically namespaced by the module that contains them. As a result, prefixes are not required in order to minimize naming collisions. If two names from different modules collide you can disambiguate by prefixing the type name with the module name:

```swift
import MyModule

var myClass = MyModule.MyClass()
```

You **should not** add prefixes to your Swift types.

If you need to expose a Swift type for use within Objective-C you can provide a suitable prefix (following our [Objective-C style guide](https://github.com/raywenderlich/objective-c-style-guide)) as follows:

```swift
@objc (RWTChicken) class Chicken {
   ...
}
```

### Protocol Naming

Use **one** of the following postfixes:

- type
- ing
- able
- ible

Example of -ing versus -able

-Containing implicates it is always there hence no optional
```swift
protocol MediaContaining {
  var media: Media { get }
}
```
-Containable implicates an optional because it can contain something but it is not said it currently does
```swift
protocol MediaContainable {
  var media: Media? { get }
}
```

## Spacing

* Indent using 2 spaces rather than tabs to conserve space and help prevent line wrapping. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**Preferred:**
```swift
if user.isHappy {
  //Do something
} else {
  //Do something else
}
```

**Not Preferred:**
```swift
if user.isHappy
{
    //Do something
}
else {
    //Do something else
}
```

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods.

When using closures that fit on one line, always add a space between the opening and closing bracket and the content of the closure:

**Preferred:**
```swift
attendeeList.filter { $0.attending }
```

**Not Preferred:**
```swift
attendeeList.filter {$0.attending}
```

## Comments

When they are needed, use comments to explain **why** a particular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self-documenting as possible. *Exception: This does not apply to those comments used to generate documentation.*


## Classes and Structures

Here's an example of a well-styled class definition:

```swift
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }
  var center : (x: Int, y: Int) {
      return (x,y)
  }

  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }

  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }

  func describe() -> String {
    return "I am a circle at \(centerString()) with an area of \(computeArea())"
  }

  override func computeArea() -> Double {
    return M_PI * radius * radius
  }

  private func centerString() -> String {
    return "(\(x),\(y))"
  }
}
```

The example above demonstrates the following style guidelines:

 + Specify types for properties, variables, constants, argument declarations and other statements with a space after the colon but not before, e.g. `x: Int`, and `Circle: Shape`.
 + Define multiple variables and structures on a single line if they share a common purpose / context.
 + Indent getter and setter definitions and property observers.
 + When possible, omit the `get` keyword on read-only computed properties and read-only subscripts.
 + Don't add modifiers such as `internal` when they're already the default. Similarly, don't repeat the access modifier when overriding a method.


### Prefer structs over classes

Unless you require functionality that can only be provided by a class (like identity or deinitializers), implement a struct instead.

Note that inheritance is (by itself) usually _not_ a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.

**Preferred:**
```swift
protocol Vehicle {
    var numberOfWheels: Int { get }
}

func maximumTotalTirePressure(vehicle: Vehicle, pressurePerWheel: Float) -> Float {
    return pressurePerWheel * vehicle.numberOfWheels
}

struct Bicycle: Vehicle {
    let numberOfWheels = 2
}

struct Car: Vehicle {
    let numberOfWheels = 4
}
```

**Not Preferred:**
```swift
class Vehicle {
    let numberOfWheels: Int

    init(numberOfWheels: Int) {
        self.numberOfWheels = numberOfWheels;
    }

    func maximumTotalTirePressure(pressurePerWheel: Float) -> Float {
        return pressurePerWheel * numberOfWheels
    }
}

class Bicycle: Vehicle {
    init() {
        super.init(numberOfWheels: 2)
    }
}

class Car: Vehicle {
    init() {
        super.init(numberOfWheels: 4)
    }
}
```

The rationale behind this is that value types are simpler, easier to reason about, and behave as expected with the `let` keyword.

### Use of Self

For conciseness, avoid using `self` since Swift does not require it to access an object's properties or invoke its methods.

Use `self` when required to differentiate between property names and arguments in initializers. Otherwise, Only include the explicit keyword when required by the language — for example, in a closure to make capture semantics explicit, or when parameter names conflict:

```swift
class BoardLocation {
  let row: Int, column: Int

  init(row: Int,column: Int) {
    self.row = row
    self.column = column

    let closure = { () -> () in
      println(self.row)
    }
  }
}

class CustomView: UIView {
  func frameAfterTransform(transform: CGAffineTransform, andOffset offset: (CGFloat,CGFloat)) -> CGRect {
    //This can be done easier, but just to make point of using duplicate variable names
    var frame = CGRectApplyAffineTransform(self.frame, transform)
    let (dx,dy) = offset
    frame = CGRectOffset(frame, dx, dy)
    return frame
  }
}
```

## Function Declarations

Keep short function declarations on one line including the opening brace:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

For functions with long signatures, add line breaks at appropriate points and add an extra indent on subsequent lines:

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```


## Closures

Use trailing closure syntax wherever possible. In all cases, give the closure parameters descriptive names:

```swift
return SKAction.customActionWithDuration(effect.duration) { node, elapsedTime in
  // more code goes here
}
```

For single-expression closures where the context is clear, use implicit returns:

```swift
attendeeList.sort { a, b in
  a > b
}
```

Only use anonymous closure arguments when the closure is really short and has only _one_ argument:

**Preferred:**
```swift
attendeeList.filter { $0.attending }
```

**Not Preferred:**
```swift
attendeeList.filter { attendee in attendee.attending }
```
When you use multiple anonymous closure arguments, especially when they have different types, it can get ugly pretty quickly:

**Preferred:**
```swift
func sumFirstElements(numberOfElements: Int, ofArray array: NSArray) -> Int {
    var sum = 0
    array.enumerateObjectsUsingBlock { value, index, stop in
        if index >= numberOfElements-1 {
            stop.memory = true
        }
        sum += value.integerValue
    }
    return sum
}

let list = [1,2,3,4,5]
sumFirstElements(3, ofArray: list) // returns 6
```

**Not Preferred:**
```swift
func sumFirstElements(numberOfElements: Int, ofArray array: NSArray) -> Int {
    var sum = 0
    array.enumerateObjectsUsingBlock {
        if $1 >= numberOfElements-1 {
            $2.memory = true
        }
        sum += $0.integerValue
    }
    return sum
}
```

This example could of course be written much concise:

**More Preferred:**
```swift
func sumFirstElements(numberOfElements: Int, ofArray array: [Int]) -> Int {
    return array[0..<numberOfElements].reduce(0, combine: +)
}
```

## Types

Always use Swift's native types when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

**Preferred:**
```swift
let width = 120.0                                    //Double
let widthString = (width as NSNumber).stringValue    //String
```

**Not Preferred:**
```swift
let width: NSNumber = 120.0                                 //NSNumber
let widthString: NSString = width.stringValue               //NSString
```

In Sprite Kit code, use `CGFloat` if it makes the code more succinct by avoiding too many conversions.

### Constants

Constants are defined using the `let` keyword, and variables with the `var` keyword. Any value that **is** a constant **must** be defined appropriately, using the `let` keyword. As a result, you will likely find yourself using `let` far more than `var`.

**Tip:** One technique that might help meet this standard is to define everything as a constant and only change it to a variable when the compiler complains!

### Optionals

Declare variables and function return types as optional with `?` where a nil value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad`.

One valid use for implicitly unwrapped types is early returns like this one:

```swift
func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
    let item : Item! = datasource.itemAtIndexPath(indexPath)
    if item == nil {
        return collectionView.dequeueReusableCellWithReuseIdentifier("identifier", forIndexPath: indexPath) as UICollectionViewCell
    }
    // use item
}
```

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
myOptional?.anotherOne?.optionalView?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let view = self.optionalView {
  // do many things with view
}
```
#### Nil coalescing operator

When it's possible to use a [nil coalescing operator](https://developer.apple.com/library/mac/documentation/swift/conceptual/swift_programming_language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-XID_123) to provide a default value, use it.

**Preferred:**
```swift
struct Party {
    var guestList: [Person]?
    func isAllowedAccess(person: Person) -> Bool {
        let guestList = self.guestList ?? []
        return contains(guestList, person)
    }
}
```

**Not Preferred:**
```swift
struct Party {
    var guestList: [Person]?
    func isAllowedAccess(person: Person) -> Bool {
        var allowedAccess = false
        if let list = guestList {
            allowedAccess = contains(list, person)
        }
        return allowedAccess
    }
}
```

Be carefull not to conjure some non-sensible value though, so never do something like this:

```swift
protocol Schrodinger {
    func numberOfAliveCats() -> Int?
}

struct Experiment {
    func boxHasAliveCats(box: Schrodinger) -> Bool {
        //When you don't know the number of alive cats, don't just assume it's zero!
        let aliveCats = box.numberOfAliveCats() ?? 0
        return aliveCats > 0
    }
}
```

The best thing to do here is make the return type of the function `Bool?`


### Struct Initializers

Use the native Swift struct initializers rather than the legacy CGGeometry constructors.

**Preferred:**
```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
var centerPoint = CGPoint(x: 96, y: 42)
```

**Not Preferred:**
```swift
let bounds = CGRectMake(40, 20, 120, 80)
var centerPoint = CGPointMake(96, 42)
```

### Type Inference

The Swift compiler is able to infer the type of variables and constants. You can provide an explicit type via a type alias (which is indicated by the type after the colon), but in the majority of cases this is not necessary.

Prefer compact code and let the compiler infer the type for a constant or variable.

**Preferred:**
```swift
let message = "Click the button"
var currentBounds = computeViewBounds()
```

**Not Preferred:**
```swift
let message: String = "Click the button"
var currentBounds: CGRect = computeViewBounds()
```

**NOTE**: Following this guideline means picking descriptive names is even more important than before.


### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred:**
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred:**
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

### Generics

Methods of parameterized types can omit type parameters on the receiving type when they’re identical to the receiver’s. Omitting redundant type parameters clarifies the intent, and makes it obvious by contrast when the returned type takes different type parameters.

**Preferred:**
```swift
struct Composite<T> {
  …
  func compose(other: Composite) -> Composite {
    return Composite(self, other)
  }
}
```

**Not Preferred:**
```swift
struct Composite<T> {
  …
  func compose(other: Composite<T>) -> Composite<T> {
    return Composite<T>(self, other)
  }
}
```

## Control Flow

If possible, parentheses must not be used in control flow statements:

**Preferred:**
```swift
if value == nil {
  // do something
}

for person in attendeeList {
  // do something
}
```

**Not Preferred:**
```swift
if (value == nil) {
  // do something
}

for (person in attendeeList) {
  // do something
}
```

Prefer an early return over `if-else` when checking for edge cases or errors to reduce indentation

**Preferred:**
```swift
protocol JSONParsable {
    class func fromJSON(AnyObject) -> (Self?,NSError?)
}

func JSONAtPath<T:JSONParsable>(path: String) -> (T?, NSError?) {
    var error: NSError? = nil
    let data: NSData! = NSData(contentsOfFile: path, options: nil, error: &error)
    if data == nil {
        return (nil, error)
    }
    
    let JSON: AnyObject? = NSJSONSerialization.JSONObjectWithData(data, options: nil, error: &error)
    if JSON == nil {
        return (nil, error)
    }
    return T.fromJSON(JSON!)
}
```

***Not Preferred:**
```swift
func JSONAtPath<T:JSONParsable>(path: String) -> (T?, NSError?) {
    var error: NSError? = nil
    let data: NSData! = NSData(contentsOfFile: path, options: nil, error: &error)
    if data != nil {
        let JSON: AnyObject? = NSJSONSerialization.JSONObjectWithData(data, options: nil, error: &error)
        if JSON != nil {
            return T.fromJSON(JSON!)
        }
    }
    return (nil, error)
}
```

**Preferred:**
```swift
struct Engine  {
    private var running = false
    
    mutating func start() {
        if running {
            return
        }

        // method for starting the engine
        running = true
    }
}
```

**Not Preferred:**
```swift
struct Engine  {
    private var running = false
    
    mutating func start() {
        if !running {
          // method for starting the engine
          running = true
        }
    }
}
```

Prefer the `for-in` style of `for` loop over the `for-condition-increment` style.

**Preferred:**
```swift
for _ in 0..<3 {
  println("Hello three times")
}

for person in attendeeList {
  // do something
}
```

**Not Preferred:**
```swift
for var i = 0; i < 3; i++ {
  println("Hello three times")
}

for var i = 0; i < attendeeList.count; i++ {
  let person = attendeeList[i]
  // do something
}
```

## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

The only exception to this rule is the `for-conditional-increment` construct, which requires semicolons. However, alternative `for-in` constructs should be used where possible.

**Preferred:**
```swift
var swift = "not a scripting language"
```

**Not Preferred:**
```swift
var swift = "not a scripting language";
```

**NOTE**: Swift is very different to JavaScript, where omitting semicolons is [generally considered unsafe](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)

## Language

Use US English spelling to match Apple's API.

**Preferred:**
```swift
var color = "red"
```

**Not Preferred:**
```swift
var colour = "red"
```

## Smiley Face

Smiley faces are a very prominent style feature of the raywenderlich.com site! It is very important to have the correct smile signifying the immense amount of happiness and excitement for the coding topic. The closing square bracket `]` is used because it represents the largest smile able to be captured using ASCII art. A closing parenthesis `)` creates a half-hearted smile, and thus is not preferred.

**Preferred:**
```
:]
```

**Not Preferred:**
```
:)
```


## Credits

* [Niels van Hoorn](https://github.com/nvh)

And of course all the people mentioned in the credits of [the originating document](https://github.com/raywenderlich/swift-style-guide). The [Github swift style guide](https://github.com/github/swift-style-guide) also served as inspiration for some sections
