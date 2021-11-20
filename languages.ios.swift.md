---
id: f78aa886-676c-485e-82ad-e0d590e59bfc
title: Swift
desc: ''
updated: 1606930709432
created: 1606930709432
---

# struct and class

拥有差不多的结构
- stored vars
- computed vars
- constant lets
- functions
- initializers

differents:
struct | class
-------|------
Value type | Reference type
Copied when passed or assigned | Passed around via pointers 
Copy on write | Automatically reference counted 
Functional programming | Object-oriented programming 
No inheritance | Inheritance (single) 
“Free”（缺省） init initializes ALL vars | “Free” init initializes NO vars 
Mutability must be explicitly stated | Always mutable (即使用let, 只表示不会改变指针)
Your “go to” data structure | Used in specific circumstances
Everything you’ve seen so far is a struct (except View which is a protocol) | The ViewModel in MVVM is always a class (also, UIKit (old style iOS) is class-based)

# 泛型，函数类型,闭包

* 允许未知类型，但swift是强类型，所以用类型占位符，用作参数时参考.net的泛型
* 函数也是一种类型，可以当作变量，参数，出现在变量，参数的位置
* in-line风格的函数叫`closure`(闭包)

# enum

* 枚举是值类型
* 枚举的项对应的值是`rawValue`，它是可以被打印，也可以被反射回枚举的`myEnum(rawValue: value)`
* 枚举的每个state都可以有`associated data`（等于是把每个state看成一个class/struct，associated data就可以理解为**属性**)

```swift
enum FastFoodMenuItem {
    case hamburger(numberOfPatties: Int)
    case fries(size: FryOrderSize)
    case drink(String, ounces: Int) // the unnamed String is the brand, e.g. “Coke”
    case cookie }

enum FryOrderSize {
    case large
    case small }

let menuItem: FastFoodMenuItem = FastFoodMenuItem.hamburger(patties: 2)
var otherItem: FastFoodMenuItem = FastFoodMenuItem.cookie
var yetAnotherItem = .cookie // Swift can’t figure this out
```
1. FryOrderSize同时又是一个枚举
2. 状态drink拥有两个“属性”，而且其中一个还**未命名**

## break and fall through/defaults

```swift
var menuItem = FastFoodMenuItem.cookie
switch menuItem {
    case .hamburger: break  // break
    case .fries: print(“fries”)
    default: print(“other”) // default
}
```

1. 如果把drink写上，但没有方法体，则叫`fall through`，只会往后面一个state fall through
2. 如果漏写了drink，则会匹配到default项（cookie同理）

## with associated data

```swift
var menuItem = FastFoodMenuItem.drink(“Coke”, ounces: 32)
  switch menuItem {
      case .hamburger(let pattyCount): print(“a burger with \(pattyCount) patties!”)
      case .fries(let size): print(“a \(size) order of fries!”)
      case .drink(let brand, let ounces): print(“a \(ounces)oz \(brand)”)
      case .cookie: print(“a cookie!”)
 }
 ```

 ## 可以拥有方法

 这就可以扩展出computed vars

 ```swift
 enum FastFoodMenuItem { ...
      func isIncludedInSpecialOrder(number: Int) -> Bool {
          switch self {
            case .hamburger(let pattyCount): return pattyCount == number
            case .fries, .cookie: return true // a drink and cookie in every special order 
            case .drink(_, let ounces): return ounces == 16 // & 16oz drink of any kind
 } }
}
```

## Iterable

conform `CaseIterable`协议就能被遍历，因为增加了一个`allCases`的静态变量：
```swift
enum TeslaModel: CaseIterable {
      case X
      case S
      case Three
      case Y
}
for model in TeslaModel.allCases {
    reportSalesNumbers(for: model)
}
func reportSalesNumbers(for model: TeslaModel) {
    switch model { ... }
}
```

SwiftUI实例， `LazyVGrid`中：
```swift
struct GridItem {
    ...
    enum Size {
        case adaptive(minimum: CGFloat, maximum: CGFloat = .infinity)
        case fixed(CGFloat)
        case flexible(minimum: CGFloat = 10, maximum: CGFloat = .infinity)
    }
}
```
1. `associated data`还能带默认值
2. 核心作用是告诉系统griditem的size是采用哪种方案（枚举），顺便设置了这种方案下的参数。所以这种场景在swift下完全可以用枚举做到

# Optionals

可靠类型其实就是一个`Enum`

```swift
enum Optional<T> { // a generic type, like Array<Element> or MemoryGame<CardContent> 
    case none
    case some(T) // the some case has associated value of type T }
```

它只有两个状态，要么是none，要么就是is set的状态，具体的值其实是绑定到了`associate data`里去了

所以你现在知道了有一种取法其实就是从`some`里面来取了。

## 语法糖

```swift
var hello: String?
var hello: String? = “hello”
var hello: String? = nil
// 其实是：
var hello: Optional<String> = .none
var hello: Optional<String> = .some(“hello”)
var hello: Optional<String> = .none
```

使用：
```swift
let hello: String? = ...
print(hello!) 
// 其实是：
switch hello {
    case .none: // raise an exception (crash) 
    case .some(let data): print(data)
}

if let safehello = hello {
    print(safehello)
} else {
    // do something else
}
// 其实是：
switch hello {
    case .none: { // do something else } 
    case .some(let data): print(data)
}

// 还有一种：

let x: String? = ...
let y = x ?? “foo”
// 其实是：
switch x {
    case .none: y = “foo”
    case .some(let data): y = data
}
```
1. 所以用`!`来解包是会报错的原理在此
2. `guard`的原理同样是`switch`
3. 默认值的原理你应该也能猜到了
4. 三个语法糖，对应的底层就是一句switch，其实就是`.none`时的三种处理方案

当然，还可以`chain`起来
let x: String? = ...
let y = x?foo()?bar?.z

// 尝试还原一下：
```swift
switch x {
    case .none: y = nil
    case .some(let xval)::
        switch xval.foo() {
            case .none: y = nil
            case .some(let xfooval):
                switch xfooval.bar {
                    case .none: y = nil
                    case .some(let xfbarval):
                        y = xfbarval.z
                }
        }
}
```
记住每一个句号对应一个switch，然后在`.none`的状态下安全退出就是`?`的用法了。

# @ViewBuilder

1. 任意`func`或`只读的计算属性`都可以标识为`@ViewBuilder`，一旦标识，它里面的内容将会被解析为`a list of Views`（也仅仅是这个，最多再加上if-else来选择是“哪些view”，不能再定义变量和写其它代码了）
    * 一个典型例子就是View里面扣出来的代码(比如子view)做成方法，这个方法是需要加上@ViewBuilder的
    * 或者改语法
    * 或者只有一个View，就不会产生语法歧义，也是可以不加@ViewBuilder的
2. 所以不需要return，而如果你不打标，也是可以通过return来构建view的
    * 但是就不支持默认返回list或通过if-else返view list的语法了
3. `@ViewBuilder`也可以标识为方法的参数，表示需要接受一个返回views的函数

```swift
init(items: [Item], @ViewBuilder content: @escaping (Item) -> ItemView) {...}
```
同时也注意一下`@escaping`，凡是函数返回后才可能被调用的闭包（逃逸闭包）就需要，而我们的view是在需要的时候才创建，或反复移除并重建（重绘）的，显然符合逃逸闭包的特征。

> viewbuilder支持的控制流程代码指的是`if-else`和`ForEach`, 所以`for...in...`是不行的。

# Protocol

接口，协议，约束... 使用场景：

* 用作类型(Type):
    * func travelAround(using moveable: Moveable)
    * let foo = [Moveable]
* 用作接口:
    * struct cardView: View
    * class myGame: ObservableObject
    * behaviors: Identifiable, Hashable, ... Animatable
* 用作约束：
    struct Game<Content> `where` Content: Equtable   // 类
    extension Array `where` Element: Hashable {...}  // 扩展
    init(data: Data) `where` Data: Collection, Data.Element: Identifiable // 方法
* OC里的delegate
* code sharing (by `extension`)
    * `extension` to a protocol
    * this is how Views get forecolor, font and all their other modifiers
    * also `firstIndex(where:) get implemented
    * an `extension` can add *default implementation* for a func or a var
        * that's how `objectWillChange` comes from
    * `extension`可以作用到所有服从同一协议的对象
        * func filter(_ isIncluded: (Element) -> Bool) -> Array<Element>
        * 只为`Sequence` protocol写了一份filter的扩展代码，但能作用于Array, Range, String, Dictionary
        * 等一切conform to the `Sequence` protocol的类


SwiftUI的`View` protocol非常简单，conform 一个返回`some view`的`body`方法就行了，但是又为它写了无数`extension`，比如`foregroundColor`, `padding`, etc. 示意图：

![](/assets/images/2021-10-28-03-38-30.png)

## Generics(泛型)

举例：
```swift
protocol Identifiable {
    associatedtype ID: Hashable
    var id: ID { get }
}
```
1. 不像struct，protocol并不是用`Identifiable<ID>`来表示泛型，而是在作用域内定义
2. 上例中，ID既定义了类别别名，还规范了约束

* 所以你Identifiable的类, 是需要有一个Hashable的ID的
* 而Hashable的对象，又是需要Equatable的(因为hash会碰撞出相同的结果，需要提供检查相等的方法)
* -> `protocol inheritancee`

# Shape

* Shape is a `protocol` that inherits from `View`.
* In other words, all Shapes are also Views.
* Examples of Shapes already in SwiftUI: RoundedRectangle, Circle, Capsule, etc.
* by default, Shapes draw themselfs by `filling` with the current foreground color.

```swift
func fill<S>(_ whatToFillWith: S) -> some view where S: ShapeStyle
```

`ShapeStyle` protocol turns a `Shape` into a `View`: Color, ImagePaint, AngularGradinet, LinearGradient

自定义shape最好用path(系统的已经通过extension实现好了view的body)：
```swift
func path(in rect: CGRect) -> Path {
    return a Path 
}
```

# ViewModifier

`.aspectRatio(2/3)` is likely something like `.modifier(AspectModifier(2/3))` AspectModifier can be `anything` that conforms to the `ViewModifier` protocol ...

它只有一个body方法：
```swift
protocol ViewModifier {
    associatedtype Content // this is a protocol’s version of a“don’t care” 
    func body(content: Content) -> some View {
        return some View that represents a modification of content }
}
```

* 对一个view调用`.modifier`就是把这个view传成了上述body方法的content
* 而从`.modifer`变成`.cardify`，不过是用了`extension`：
```swift
extension View {
    func cardify(isFaceUp: Bool) -> some View {
        return self.modifier(Cardify(isFaceUp: isFaceUp))
    }
}
```

# Property Observers

* 语法长得像`computed var`, 但完全不是一回事 （get, set之于willSet, didSet）
* willSet, didSet，对应newValue, oldValue

## @State

your view is **Read Only**, 

为什么？
> 因为view的生命周期足够短，基本上是不断地生成和销毁，根本不需要”被改变“

* 所以永远用`let`
* 所以是`stateles`的

这样的结构很简单，任何view的变化其实就是重绘。

仍然有些时候需要状态：
- 编辑表单
- 模态窗口或通知窗口等临时窗口
- 动画需要追踪动画进度

声明：
```swift
@State private var somethingTemporary: SomeType // this can be of any type
```
* private 表示别人访问不到
* @State的的变化会在**必要时**引起重绘 （相当于一个`@ObservedObject`）
* view会不断销毁和重建 -> 指针会永远指向新的内存地址
* 而state是在堆上分配的空间
* 所以销毁和重建view并不会丢失state
* 后文`property wrapper`详述

# Layout

1. `Container`提供空间
2. `Views`确定自身的大小
3. `Container`提供`View`的位置
4. `Container`确定自身大小（等同于#2)

## HStack and VStack

横/纵向排列元素(View)，并提供“尽可能小”的空间，根据元素性质，有三种场景：
1. `inflexble` view: `Image`，fixed size
2. slightly more flexible view: `Text`，适应文字的合适大小
3. very flexible view: `RoundedRectangle`: 占满空间 -> 基本上`Shape`都会有多少空间占多少

* 一旦元素确定了size，多余的空间就会给下一个元素，最后`very flexible view`平均分配剩下的空间
* 所有元素大小确定，容器大小也就确定了，如果有`very flexible`的，那么容易本身也是`very flexible`的

remark：
* `Spacer(minLength: CGFloat)` 空格, draw nothing, 占尽可能多的空间
* `Divider()` 画条分隔线，占尽可能小的空间
* `.layoutPriority(100)` 用优先级来表示分配空间的顺序，默认值为0。后分配者如果没有空间了会用省略号表示
* `HStack(alignment: .leading)`用来控制元素的对齐

> List, Form, OutlineGroup 其实就是 `really smart VStacks`，即本质上就是一个纵向排列的布局。

## LazyHStack and LazyVStack

* *Lazy*的意思是如果元素对应的位置没有出现在屏幕上，就不会构建View.
* they also size themselves to fit their views
* 前两条加一起，得出这个容器不会尽可能多的占用空间，即使含有very flexible的view -> 尽可能小的空间
* 显然，它最多出现在`ScrollView`里（只有在有限窗口里滚动，才有可见不可见的差别）

## Scrollview

* 给多少空间占多少空间

## LazyHGrid and LazyVGrid

* 一个方向view数量固定，另一个方向动态增减（scroll）的H/V stack，以竖向的`LazyVGrid`为例：
* 确定每行元素个数，多少行由元素总数决定
* 或者确定元素大小，在行方向铺满后，再往下一行铺
* HGrid方向则是先纵向铺满，再水平铺

## ZStack

* sizes itself to fit its children
* can be very flexible (if one children is)

两个modifier其实也是用的ZStack:
* `.background`，插入一个view在底层，stack起来: `Text("hello").background(Rectangle().foregroundColor(.red))`
* `.overlay`，覆盖到表层的zstack: `Circle().overlay(Text("hello"), alignment:.center)`

More：
* 一个view是可以选择任意size的，哪怕比给它的空间更大(产生裁剪)
* `.aspectRatio(2/3, contentMode: .fit)`如果是在HStack里，
    * 则是把元素横向排列后得到宽度，根据宽度计算出高度，得到元素大小
    * `.fit`表示完整显示图片（就长边），短边部分补成黑色，`.fill`应该是就短边，长边部分就裁剪了

```swift
HStack {
    ForEach(cards) { card in
        CardView(card).aspectRatio(2/3, contentMode: .fit)
    }
}
    .foregroundColor(.orange)
    .padding(10)
```
1. 在能够分配的空间里，四边各减10 -> padding(10)
2. 减10后的空间里，根据aspectRation确定一个size
3. 这个size应用给CardView
4. 组合成HStack的size

总大小就是HStack的size四边各加10

而View们如何知道能占多少空间？-> `GeometryReader`

## GeometryReader

```swift
var body: View {
    GeometryReader { geometry in
        ...
    }
}
```
参数`geometry`是一个`GeometryProxy`:

```swift
struct GeometryProxy {
    var size: CGSize
    var safeAreaInsets： EdgeInsets
    func frame(in: CoordinateSpace) -> CGRect
}
```
* `size`表示被提供了多少的空间（by its container)
* 并且不包含safe area（如刘海）
* 如果需要绘制到safe area里去: `ZStack{...}.edgesIgnoringSafeArea([.top])`

![](/assets/images/2021-10-28-02-12-42.png)

图中演示的是设置卡片字体的大小，希望尽可能地填充卡片，`geometry.size`能给出运行时数据，而无需硬编码。

# Animation

* One way to do animation is by animating a Shape.
* The other way to do animation is to animate Views via their `ViewModifiers`.
* Only `changes` can be animated
    * ViewModifier arguments (not all, i.e. fonts)
    * Shapes
    * the *existance* of a View in the UI
        * 比如if-else和ForEach
* You can only animate changes to Views in *containers that are already on screen* (`CTAAOS`).

两个golden rule:
1. 要有view modifier的属性变化
2. 要在屏幕上
才会触发动画（其实就是上面的最后两条）

* 课程的动画例子里，用了if-else来生成view，这样导致了新生成的view不会触发动画
* 比如点开两张牌，新点开的那张牌由于之前牌的内容并没有出现在屏幕上，导致动画没有触发
* 所以把view的结构由if-else的生成和销毁机制，变成了透明度切换机制
    * 即正面和反面都在屏幕上，只不过透明度相反，以在视觉上要么是正面要么是反面
    * 本以为透明度为0就会销毁视图(UIKit？)，看样子并不是这样的，大胆用opacity就好了

## 隐式调用

```swift
Text(“👻 ”)
    .opacity(scary ? 1 : 0)                             // 普通modifier, 即如果没有动画，也需要的状态（即代码也不会删）
    .rotationEffect(Angle.degrees(upsideDown ? 180 : 0))    // 动画modifier，即定制的动画效果，不需要动画的时候，就不需要这一行
    .animation(Animation.easeInOut)                         // 触发
```
- 上述所有`ViewModifier`都会被动画
    * `scary, upsideDown`等值改变时也会触发动画
- 隐式调用会冒泡（所以不要对一个container view做`.animation`，还有定位的问题)
- animation的参数就是一个struct： duration, delay, repeat, curve...

对于不能动画的modifier，看一下这个实例（上为修改前，下为修改后）
![](/assets/images/2021-10-28-17-54-47.png)

1. 把font设为常量，把缩放变成一个geometric effect
2. 同时也说明`.animation()`不止作用于它前面的

## 显式调用

```swift
withAnimation(.linear(duration: 2)) {
    // do something that will cause ViewModifier/Shape arguments to 
change somewhere }
```
- It will appear in closures like `.onTapGesture`.
- 显式动画不会覆盖掉隐式动画
- 很少有处理用户手势而不包`.withAnimation`的

# Transition

- 转场，主要用于view的出现和消失
- 一对`ViewModifier`，一个`before`, 一个`after`

```swift
ZStack {
    if isFaceUp {
        RoundedRectangle() // default .transition is .opacity 
        Text(“👻 ”).transition(.scale)
    } else {
        RoundedRectangle(cornerRadius: 10).transition(.identity)
    }
}
```

Unlike .animation(), .transition() does not get redistributed to a container’s content Views. So putting .transition() on the ZStack above only works if the entire ZStack came/went. 

(Group and ForEach do distribute .transition() to their content Views, however.)

意思是`.transition`并不会向下传递，如果对`ZStack`做转场，只会把整个容器进行转场而不是里面的view（见实例二）

- 转场只是一个声明，并没有触发动画（其实就是设置了`ViewModifier`）
- 所以转场没有隐式调用
- 只对CTAAOS有用

`.onAppear`或`.onDisappear`时，container必然是在屏幕上的，所以这是一个写`.transition`的好地方（记得要`withAnimation`)

built-in transitions:

- AnyTransition.opacity: 通过`.opacity` modifier来实现淡入淡出
- AnyTransition.scale: 通过`.frame` modifier来实现缩放
- AnyTransition.offset(CGSize): 通过`.offset`来实现移动
- AnyTransition.modifier(active:identity:): 你提供两个`ViewModifier`

通过`AnyTransition.animation`(Animation`)来定制动画细节：

```swift
.transition(.opacity.animation(.linear(duration: 20))) 
```

# 动画机制

其实就是给出一系列的数据点，系统会根据这些数据点把时间切分，你给的数据点越多，切的时间块也就越多，而且系统会根据你的线性函数来决定是平均还是怎样去切分这些时间块：
- the animation system divides the animation duration up into little pieces.
- The animation system then tells the Shape/ViewModifier the current piece it should show. 
- And the Shape/ViewModifier makes sure that its code always reflects that.

系统通知变量当前的值，UI根据这个值实时绘制当前的View，不断销毁重建，就是动画的过程。

系统是用一个变量来通知这个进度的：`Animatable` protocol的唯一成员变量：`animatableData`:

```swift
var animatableData: Type
```

* Type只需要满足`VectorArithmetic`协议，其实就是一个可以被细分的值，基本上是Float, Double, CGFloat，以及`AnimatablePair`(其实就是两个`VectorArithmetic`)
* 想要支持动画的`Shape`, `ViewModifier`，只需要实现`Animatable`协议即可（即提供一个`animatableData`属性）

Because it’s communicating both ways, this animatableData is a `read-write` var.
* The `setting` of this var is the animation system telling the Shape/VM which piece to draw.
* The `getting` of this var is the animation system getting the `start/end` points of an animation.

**实例一**

![](/assets/images/2021-10-29-00-41-47.png)

* view modifier里面有一个变量`rotation`（ZStack, content, rotation3DEffect)
* 那么外层在`withAnimation{}`的时候，我们是期望rotation的值能动起来的
    * 内置的viewmodifier当然会自己动，如`opacity`等
* 那么我们首先就要让`Cardify` conform to `Animatable`（例子中的AnimatableModifer = Animatable + ViewModifer)
* 然后我们就要实现`animatableData`, 因为系统事实上就是不断去更新这个data值
* 教材里把它进行了封装（当然你也可以直接用它），这只是思维方式上的区别
* `animatedData`会随时间变化，自然会不断invalidate view，然后rebuild view，动画就产生了。

**实例二**

课程里有这么个需求：卡片由`LazyVGrid`提供布局，且卡片出现和消失的时候都要有动画。

出现和消失？那当然就是`Transition`的事了:
```swift
Card()
  .transition(AnyTransition.asymmetric(insertion: .scale, 
                                         removal: .opacity)))
```
运行时发现消失的时候有动画，出现的动画却没有。原因是`transition`只会在*出现和消失*时触发，而我们的卡片是包在grid容器里的，所以grid出现在屏幕上的时候，就带着卡片一起出现了，transition并不会向下传递（前文也已经说过了，这里刚好印证）。

1. 所以解决方法当然可以“延迟”呈现这些卡片
2. 课程里用了另一种方法，机制当然也是延迟，但不是那么地直白：

![](/assets/images/2021-10-29-01-58-23.png)

* 就是利用了`.onAppear`来阻断容器和卡片的连续生成，而改用容器呈现后，再逐个“添加”的方式，让每一张卡片都有一个单独出现的机会
* 同时也必须利用`@State`, 让每添加一张卡片都会invalidate view一次
* 也能看出，animate能animate的就是属性和transition

> 当然，课程最后改成了“发牌”的机制，手动添加卡片，彻底阻断了卡片和容器一起出现的场景。

这就带我们来到了实例三，同一个view在不同容器间的动画，怎么计算各自尺度下同一个view的位置：`matchedGeometryEffect`

**实例三**

![](/assets/images/2021-10-29-02-38-33.png)

* 想要有牌一张张发出去的效果，自然会想到添加延时
* 实现成了同时做动画，只不过越到后面的牌，延时越长（动作越慢），而不是我们想象的先后触发

为了让不同的牌发出去时有立体效果，还以index为依据设置了`zIndex`，最终效果：

![card_deck](/assets/images/card_deck.gif)

# Color, UIColor & CGColor

Color:
* Is a color-specifier, e.g., `.foregroundColor(Color.green)`.
* Can also act like a `ShapeStyle`, e.g., `.fill(Color.blue)`.
* Can also act like a `View`, e.g., Color.white can appear `wherever` a View can appear.（可以当作view）

UIColor:
* Is used to `manipulate` colors.（主打操控）
* Also has many `more` built-in `colors` than `Color`, including “system-related” colors.(颜色更多)
* Can be interrogated and can convert between color spaces.

For example, you can get the RGBA values from a UIColor.
Once you have desired UIColor, employ `Color(uiColor:)` to use it in one of the roles above.

CGColor:
* The fundamental color representation in the Core Graphics drawing system
* `color.cgColor`

# Image V.S. UIImage

Image:
* Primarily serves as a View.(主要功能是View)
* Is `not` a type for vars that `hold an image` (i.e. a jpeg or gif or some such). That’s UIImage. 
* Access images in your Assets.xcassets (in Xcode) by name using `Image(_ name: String)`. 
* Also, many, many system images available via `Image(systemName:)`.
* You can control the size of system images with `.imageScale()` View modifier.
* System images also are affected by the .font modifier. 
* System images are also very useful `as masks` (for gradients, for example).

UIImage
* Is the type for actually `creating/manipulating` images and `storing` in vars. 
* Very powerful representation of an image.
* Multiple file formats, transformation primitives, animated images, etc. 
* Once you have the UIImage you want, use Image(uiImage:) to display it.

# Multithreading

* 多线程其实并不是同时运行，而是前后台非常快速地切换
* `Queue`只是有顺序执行的代码，封装了`threading`的应用
* 这些“代码”用`closure`来传递
* **main queue**唯一能操作UI的线程
    * 主线程是单线程，所以不能执行异步代码
* **background queues**执行任意：*long-lived, non-UI* tasks
    * 可以并行运行(running in parallel) -> even with main UI queue
    * 可以手动设置优先级，服务质量(`QoS`)等
    * 优先级永远不可能超过main queue
* base API: GCD (`Grand Central Dispatch`)
    1. getting access to a queue
    2. plopping a block of code on a queue

A: Creating a Queue

There are numerous ways to create a queue, but we’re only going to look at two ...
```swift
DispatchQueue.main // the queue where all UI code must be posted
DispatchQueue.global(qos: QoS) // a non-UI queue with a certain quality of service qos (quality of service) is one of the following ...
    .userInteractive    // do this fast, the UI depends on it!
    .userInitiated  // the user just asked to do this, so do it now
    .utility    // this needs to happen, but the user didn’t just ask for it
    .background // maintenance tasks (cleanups, etc.)
```

B: Plopping a Closure onto a Queue

There are two basic ways to add a closure to a queue ...
```swift
let queue = DispatchQueue.main //or
let queue = DispatchQueue.global(qos:) 
queue.async { /* code to execute on queue */ }
queue.sync { /* code to execute on queue */ }
```

主线程里永远不要`.sync`, 那样会阻塞UI

```swift
DispatchQueue(global: .userInitiated).async {
    // 耗时代码
    // 不阻塞UI，也不能更新UI
    // 到主线程去更新UI
    DispatchQueue.main.async {
        // UI code can go here! we’re on the main queue! 
    }
}
```

# Gestures

手势是iOS里的一等公民
```swift
// recognize
myView.gesture(theGesture) // theGesture must implement the Gesture protocol

// create
var theGesture: some Gesture {
    return TapGesture(count: 2)  // double tap
}

// discrete gestures
var theGesture: some Gesture {
      return TapGesture(count: 2)
        .onEnded { /* do something */ }
}

// 其实就是：
func theGesture() -> some Gesture {
    tapGesture(count: 2)
}

// “convenience versions”
myView.onTapGesture(count: Int) { /* do something */ } 
myView.onLongPressGesture(...) { /* do something */ }

// non-discrete gestures

var theGesture: some Gesture {
      DragGesture(...)
.onEnded { value in /* do something */ } 

```

non-discrete手势里传递的`value`是一个state:

* For a `DragGesture`, it’s a struct with things like the `start and end location` of the fingers.
* For a `MagnificationGesture` it’s the `scale` of the magnification (how far the fingers spread out). 
* For a `RotationGesture` it’s the `Angle` of the rotation (like the fingers were turning a dial).
* 还可以跟踪一个state: `@GestureState var myGestureState: MyGestureStateType = <starting value>`

唯一可以更新这个`myGestureState`的机会：
```swift
var theGesture: some Gesture {
     DragGesture(...)
        .updating($myGestureState) { value, myGestureState, transaction in 
            myGestureState = /* usually something related to value */
        }
        .onEnded { value in /* do something */ }
 }
 ```
 注意`$`的用法
 
 如果不需要去计算一个`gestureState`传出去的话，有个`updating`用简版：
 ```swift
 .onChanged { value in
/* do something with value (which is the state of the fingers) */
}
```
事实上，目前来看`gestureState`只做了两件事：
1. 把实时手势对应的值保存起来
2. 在手势结束时复原（对于缩放，变为1，对于移动，变为0）
3. 同时，它是只读的，只在`.updating`方法里有更新的机会

所以，如果你的UI和动画逻辑，用到了手势结束时的值（即需要它复原），那么你也可以直接在`.onEnded`方法里手动把它设回去，等同于你也实现了你的`gestureState`，并且没有它那些限制。

## Drag and Drop

### Item Provider

* The heart of drag nad drop is the `NSItemProvider` class.
* It facilitates the transfer of data between processes (via drag and drop, for example)
* It facilitates the transfer of a number of data types in iOS, for example:
    * NSAttributedString and NSString
    * NSURL
    * UIImage and UIColor
* pre-Swift，所以需要bridging，比如：`String as NSString`

结合几个要点，一句话就能让你的元素能被拖动(drag)：
```swift
Text(emoji).onDrag{ NSItemProvider(object: emoji as NSString)}
```

而接收(drop)则要复杂很多：
```swift
otherView.onDrop(of: [.plainText], isTarget: nil) {providers, location in return false }
```
* 参接收的类型由`of`参数指定，这里假定是文本
* 方法里最终要返回一个bool值，表示成功接收与否，我返了个false，意思是你能让物体拖动，但是一松开手指就复原了

从`itemprovider`里加载对象有模板代码：

```swift
extension Array where Element == NSItemProvider {
  func loadObjects<T>(ofType theType: T.Type, firstOnly: Bool = false, using load: @escaping (T) -> Void) -> Bool where T: NSItemProviderReading {
    if let provider = first(where: { $0.canLoadObject(ofClass: theType)}) {
      provider.loadObject(ofClass: theType) { object, error in
        if let value = object as? T {
          DispatchQueue.main.async {
              load(value)
          }
        }
      }
      return true
    }
    return false
  }

// and
// where T: _ObjectiveCBridgeable, T._ObjectiveCType: NSItemProviderReading
```

1. 提供了两段代码，可以看到其实就是对要加载的对象的约束不同，提供了对OC的兼容
2. 模板代码演示了
稳健地从拖拽对象加载内容（canload -> load)
3. 真正的业务逻辑其实就是为拖进来的这个view选择一个位置存放（或读取它携带的数据）
4. `T.Type`传的是类别的`.self`，比如`String.self`

# Property Wrappers

C#中的`Attributes`，python中的`Decorators`, Java的`Annonations`，类似的设计模式。

* A property wrapper is actually a `struct`.
* 这个特殊的`struct`封装了一些模板行为应用到它们wrap的vars上：
    1. Making a var live in the heap (`@State`)
    2. Making a var publish its changes (`@Published`)
    3. Causing a View to redraw when a published change is detected (`@ObservedObject`)

即能够分配到堆上，能够通知状态变化和能重绘等，可以理解为`语法糖`。

```swift
@Published var emojiArt: EmojiArt = EmojiArt()

// ... is really just this struct ...
struct Published {
    var wrappedValue: EmojiArt
    var projectedValue: Publisher<EmojiArt, Never>  // i.e. $
}

// `projected value`的类型取决于wrapper自己，比如本例就是一个`Publisher`

// 我理解为一个属性和一个广播器

// ... and Swift (approximately) makes these vars available to you ...
var _emojiArt: Published = Published(wrappedValue: EmojiArt()) 
var emojiArt: EmojiArt {
     get { _emojiArt.wrappedValue }
     set { _emojiArt.wrappedValue = newValue }
 }
```

把get,set直接通过`$emojiArt`(即projectedValue)来使用

当一个`Published`值发生变化：
* It publishes the change through its *projectedValue* (`$emojiArt`) which is a `Publisher`. 
* It also invokes `objectWillChange.send()` in its enclosing `ObservableObject`.

下面列的几种`Property wrapper`，我们主要关心最核心的两个概念，`wrappedValue`和`projectedValue`是什么就行了:

## @State

这是第二次提到了，在`Property Observers`一节里预告过，基本上点`@`的，大都为`Property Wrapper`的内容。

* The wrappedValue is: `anything` (but almost certainly a value type).
* What it does: 
    * stores the wrappedValue in the heap; 
    * when it changes, `invalidates` the `View`. 
* Projected value (i.e. $): a `Binding` (to that *value in the heap*).

```swift
@State private var foo: Int
init() {
    _foo = .init(initiaValue: 5)
}
```

注意`_`和`$`的区别。 

## @StateObject & @ObservedObject

* The wrappedValue is: `anything` that implements the `ObservableObject` protocol (ViewModels). 
* What it does: 
    * `invalidates` the `View` when wrappedValue does *objectWillChange.send()*. 
* Projected value (i.e. $): a `Binding` (to the vars of the wrappedValue (a *ViewModel*)).

> **@StateObject V.S. @State**

* 一个类型是`ObservableObject`s， 一个是value type

> **@StateObject V.S. @ObservedObject**
 
* @StateObject is a "source of truth"，也就是说可以直接赋值：`@StateObject var foo = SomeObservableObject()`
* 能用在*View, APP, Scene*等场景
* 如果用在View里，生命周期与View一致 

```swift
@main
struct EmojiArtApp: App {
    // stateObject, source of truth
    // defined in the app
    @StateObject var paletteStore = PaletteStore(named: "default")

    var body: some Scene {
    DocumentGroup(newDocument: { EmojiArtDocument() }) { config in
        EmojiArtDocumentView(document: config.document)
            .environmentObject(paletteStore)  // passed by environment
        }
    }
}
```

## @Binding

* The wrappedValue is: `a value` that is bound to something else.
* What it does: 
    * gets/sets the value of the wrappedValue from `some other source`. 
    * when the bound-to value changes, it `invalidates` the `View`. 
    * Form表单典型应用场景，有UI变化的控件
    * 手势过程中的State, 或drag时是否targted
    * 模态窗口的状态
    * 分割view后共享状态
    * 总之，数据源只有一个(source of the truth)的场景，就不需要用两个@State而用@Binding, 
* Projected value (i.e. $): a Binding (self; i.e. the Binding itself)

```swift
struct MyView: View {
      @State var myString = “Hello”               // 1
      var body: View {
          OtherView(sharedText: $myString)        // 2
      }
  }
  struct OtherView: View {
      @Binding var sharedText: string             // 3
      var body: View {
          Text(sharedText)                        // 4
          TextField("shared", text: $sharedText)  // 5 _myString.projectValue.projectValue
      }
}
```
1. `_myString`是实际变量，包含一个`wrappedValue`，一个`projectedValue`
2. `myString`就是`_myString.wrappedValue`
3. `$myString`是`_myString.projectedValue`，
    * 是一个`Binding<String>`，传值和接值用的就是它
    * 所以传`$myString`的地方也可以用`_myString.projectedValue`代替，学习阶段的话
4. 要把`projectedValue`层层传递下去，并不是用同一个`projectedValue`，而是设计成了`Binding<T>`
    * 参考上面代码块的第5条

其它
* 也可以绑定一个常量：`OtherView(sharedText: .constant(“Howdy”))`
* computed binding: `Binding(get:, set:).`

比如你的view是一个小组件，里面有一个`Binding var user: User`，那么在preview里面怎么传入这个User呢？用常量：
```swift
static var preview: some View {
    myView(user: .constant(User(...)))
}
```

## @EnvironmenetObject

* The wrappedValue is: `ObservableObject` obtained via .environmentObject() sent to the View. 
* What it does: `invalidates` the View when wrappedValue does objectWillChange.send(). 
* Projected value (i.e. $): a `Binding` (to the vars of the wrappedValue (a ViewModel)).

与`@ObservedObject`用法稍有点不同，有单独的赋值接口：

```swift
let myView = MyView().environmentObject(theViewModel)
// 而@ObservedObject是一个普通的属性
let myView = MyView(viewModel: theViewModel)

// Inside the View ...
@EnvironmentObject var viewModel: ViewModelClass 
// ... vs ...
@ObservedObject var viewModel: ViewModelClass
```
* visible to all views in your body (except modallay presented ones)
* 多用于多个view共享ViewModel的时候

## @Environment

* 与`@EnvironmentObject`完全不是同一个东西
* 这是`Property Wrapper`不只有两个变量（warped..., projected...）的的一个应用
* 通过`keyPath`来使用：`@Environment(\.colorScheme) var colorScheme`
* wrappedValue的类型是通过`keyPath`声明时设置的

```swift
view.environment(\.colorScheme, .dark)
```

so:

* The wrappedValue is: the value of some var in `EnvironmentValues`. 
* What it does: gets/sets a value of some var in `EnvironmentValues`. 
* Projected value (i.e. $): none.

```swift
// someView pop 一个 modal 的 myView,传递 environment
someView.sheet(isPresented: myCondition){
    myView(...init...)
    .enviroment(\.colorScheme, colorScheme) 
}
```

除了深色模式，还有一个典型的应用场景就是编辑模式`\.editMode`，比如点了编辑按钮后。

> `EditButton`是一个封装了UI和行为的控件，它只做一件事，就是更改`\.editmode`这个环境变量(的`isEditing`)

## @Publisher

It is an object that `emits values` and possibly a `failure object` if it fails while doing so.
```swift
Publisher<Output, Failure>
```
* Failure需要实现`Error`，如果没有，可以传`Never`

### 订阅

一种简单用法，`sink`:
```swift
cancellable = myPublisher.sink(
    receiveCompletion:{resultin...}, //result is a Completion<Failure> enum
        receiveValue: { thingThePublisherPublishes in . . . }
  )
```
返回一个`Cancellable`，可以随时`.cancel()`，只要你持有这个`cancellable`，就能随时用这个sink

View有自己的订阅方式：
```swift
.onReceive(publisher) { thingThePublisherPublishes in
    // do whatever you want with thingThePublisherPublishes 
}
```
1. `.onReceive` will automatically `invalidate` your View (causing a redraw).
2. 既然参数是publisher，所以是一个binding的变量，即带`$`使用：
```swift
.onReceive($aBindData) { bind_data in 
    // my code
}
```

publisher来源：
1. `$` in front of vars marked `@Published`
    * 还记得$就是取的projectedValue吗？
    * 一般的projectedValue是一个*Binding*，Published的是是个*Publisher*
2. URLSession’s `dataTaskPublisher` (publishes the Data obtained from a URL)
3. `Timer`’s publish(every:) (periodically publishes the current date and time as a Date) 
4. `NotificationCenter`’s publisher(for:) (publishes notifications when system events happen)

> 如果你有一个`ObservedObject`(Document)，它里面有一个`@Publisher`(background)，那么注意以下两者的区别：

* document.`$`background: 是一个publisher
* `$`document.background: 是一个binding

> `.onReceive`只能接收`Publisher`的推送，而事实上，`onChange`（一般用于接收ObservedObject或State)同样也能接收Publisher。

# Persistence

持久化数据的方式有
* File system（FileManager）
* Sqlite/CoreData
* iCloud: 根据上面两种格式存储
* CloutKit: a database in the cloud (network)
* UserDefaults
* Codable/JSON
* UIDocument (UIKit feature)(与Files App集成)
* 3rd-party

## UserDefaults

* 只能存储`Property List`
* `Property List`支持String, Int, Bool, floating point, Date, Data, Array or Dictionary
    * 任何其它类型需要转成`Property List`
    * `Codable` converts structs into `Data` objects (and `Data` is a `Property List`).

```swift
let defaults = UserDefaults.standard
defaults.set(object, forKey: “SomeKey”) // object must be a Property List
defaults.setDouble(37.5, forKey: “MyDouble”)

// retrive

let i: Int = defaults.integer(forKey: “MyInteger”)
let b: Data = defaults.data(forKey: “MyData”)
let u: URL = defaults.url(forKey: “MyURL”)
let strings: [String] = defaults.stringArray(forKey: “MyString”) 
// etc.
// Retrieving Arrays of anything but String is more complicated ...
let a = defaults.array(forKey: “MyArray”) // will return Array<Any>
// 最好用Codable的data(forKey:)替代
```

## Core Data

SwiftUI进行的集成:
* 创建的对象是`ObservableObjects`
* 一个property wrapper `@FetchRequest`
* 管理对象(context)是`NSManagedObjectContext`
* context通过`@Environment`传入

demo:
```swift
@Environnment(\.managedObjectContext) var context
let flight = Flight(context: context)
flight.aircraft = “B737” // etc.

let ksjc = Airport(context: context)
ksjc.icao = “KSJC” // etc.

flight.origin = ksjc // this would add flight to ksjc.flightsFrom too try? context.save()

let request = NSFetchRequest<Flight>(entityName: “Flight”) request.predicate =
NSPredicate(format: “arrival < %@ and origin = %@“, Date(), ksjc) 
request.sortDescriptors = [NSSortDescriptor(key: “ident”, ascending: true)] 

let flights = try? context.fetch(request) // past KSJC flights sorted by ident
// flights is nil if fetch failed, [] if no such flights, otherwise [Flight]
```

以上是core data部分，还是浓浓的OC的痕迹，看看Swift UI的版本。

首先，上述的`Flights, Airports`都是ViewModel。它自然拥有它的`Property Wrapper`:

```swift
@FetchRequest(entity:sortDescriptors:predicate:) var flights: FetchedResults<Flight>
@FetchRequest(fetchRequest:) var airports: FetchedResults<Airport>

// flights and airports will continuously update as the database changes. 
ForEach(flights) { flight in
    // UI for a flight built using flight 
}

// bi-binding
_flights = FetchRequest(...)
```

## Cloud Kit

上个demo吧
```swift
let db = CKContainer.default.public/shared/privateCloudDatabase 
// Record理解为Table
let tweet = CKRecord(“Tweet”)
// 索引理解为Field
tweet[“text”] = “140 characters of pure joy”
let tweeter = CKRecord(“TwitterUser”)
tweet[“tweeter”] = CKReference(record: tweeter, action: .deleteSelf)
db.save(tweet) { (savedRecord: CKRecord?, error: NSError?) -> Void in
    if error == nil {
    // hooray!
    } else if error?.errorCode == CKErrorCode. NotAuthenticated.rawValue {
        // tell user he or she has to be logged in to iCloud for this to work!
    } else {
        // report other errors (there are 29 different CKErrorCodes!) 
    }
}

// Query
// 类似core data, 构造predict, request(就是query)即可

let predicate = NSPredicate(format: “text contains %@“, searchString)
let query = CKQuery(recordType: “Tweet”, predicate: predicate)
db.perform(query) { (records: [CKRecord]?, error: NSError?) in
    if error == nil {
        // records will be an array of matching CKRecords
    } else if error?.errorCode == CKErrorCode.NotAuthenticated.rawValue {
        // tell user he or she has to be logged in to iCloud for this to work!
    } else {
        // report other errors (there are 29 different CKErrorCodes!) 
    }
}
```

One of the coolest features of Cloud Kit is its ability to `send push notifications` on changes. All you do is register an `NSPredicate` and whenever the database changes to match it,

## File System

Sandbox包含：
* Application directory — Your executable, .jpgs, etc.; not writeable.
* Documents directory — Permanent storage created by and always visible to the user. 
* Application Support directory — Permanent storage not seen directly by the user. 
* Caches directory — Store temporary files here (this is not backed up).
* Other directories (see documentation) 
* ...

```swift
let url: URL = FileManager.default.url(
    for directory: FileManager.SearchPathDirectory.documentDirectory, // for example 
    in domainMask: .userDomainMask // always .userDomainMask on iOS
    appropriateFor: nil, // only meaningful for “replace” file operations
    create: true // whether to create the system directory if it doesn’t already exist
 )
```

Examples of SearchPathDirectory values :
```swift
.documentDirectory, 
.applicationSupportDirectory, 
.cachesDirectory, 
etc.
```

再列些常用api：
```swift
// URL

func appendingPathComponent(String) -> URL
func appendingPathExtension(String) -> URL // e.g. “jpg”
var isFileURL: Bool // is this a file URL (whether file exists or not) or something else? 
func resourceValues(for keys: [URLResourceKey]) throws -> [URLResourceKey:Any]? 
// Example keys: .creationDateKey, .isDirectoryKey, .fileSizeKey

// Data

// retrive binary data
// option almost always []
init(contentsOf: URL, options: Data.ReadingOptions) throws 
// write
// The options can be things like .atomic (write to tmp file, then swap) or .withoutOverwriting.
func write(to url: URL, options: Data.WritingOptions) throws -> Bool

// FileManager
fileExists(atPath: String) -> Bool
// Can also create and enumerate directories; move, copy, delete files; etc.
```

## Codable

* 保留一个对象所有的var（变量）的机制
* 如果一个Struct它的成员变是Codable的，那么Swift会帮你把这个Struct实现Codable，比如没有associated data的Enum。
* 帮你实现不代表不要显式声明
* 基础类型基本上都实现了Codable

```swift
let object: MyType = ...
// encode
let jsonData: Data? = try? JSONEncoder().encode(object)

// write file
try jsonData.write(to: url)

// deocde as string
let jsonString = String(data: jsonData!, encoding: .utf8)

// decode as object
let myObject: MyType = try? JSONDecoder().decode(MyType.self, from: jsonData!)
// 从字符串到对象没有一步到位的办法，只能先string->Data
let data = jsstring.data(using: .utf8) // 再把data传到上术方法里
```

encode, decode是会throw的，注意try_catch相应的Error，比如`.keyNotFound, .dataCorrupted...`

### CodingKeys

json与对象相互进行转化有一个通用的需求，就是键的映射，这更常用在外部API与本地类的映射中，比如userId，别人叫guestId，等等，Swift中，用一个叫`CodingKeys`的枚举来实现这个映射：

```swift
private enum CodingKeys: String, CodingKey {
    case uid = "user_id"
    case someDate = "some_date"
    case pname = "panme" // 表示在JSON中也叫这个名字 
    case sku // 如果名字一样的话，可以这么简写 
    // 但是不写的话，序列化的时候就不会序列这个字段了
    // 解码时会有 KeyNotFound 类的错误
}

// 结合起来，用在init中
init(from decoder: Decoder) throws {
    // container是切入点，要弄清楚
    // 如果没有手写键的映射表，那么keydBy就是自己
    let container = try decoder.container(keyedBy: CodingKeys.self)
    someDate = try container.decode(Date.self, forKey: .someDate) 
    // 从json中加载.someDate对应的键的值，尝试解码成Date
    // other vars (每种case必须全部都有)
}
```

### Enum

序列化枚举有点复杂：
1. 简单枚举应该怎么序列化？ 其实是序列化成case对应的名字和表示空JSON的`{}`组成的键值对，比如`{"math":{}}`
2. 有关联数据的枚举呢？ 那就得自己提供`encoder`:
    * `case url: try container.encode(url, forKey: .url)` 即对相应的枚举值进行相应的encode
3. 并且自行decode，但是与struct（为每一个key填值）不同，因为枚举变量只是一个值，所以是依次尝试，解码成功就认定是那一个枚举值
```swift
if let url = try? container.decode(URL.self, forKey: .url) {
    self = .url(url)
} else if ... // 别的尝试

// 此句的作用是根据.url对应的键名，取出值，反射成URL对象，如果成功，那么这个枚举值是.url无疑
// 而且关联数据就是反射的结果
// 如果失败，继续换一个键名，将对应的值转成对应的类型，依次类推
```
4. 那么如何手动decode一个原始的枚举呢？
    * 我们知道上述实践是为了反射出关联数据，并且根据能够成功反射关联数据来判断枚举类型
    * 原始枚举需要encode哪个值呢？-> 目前我只能做一个空`struct`来实现序列化成`{}`的目的 -> 为了跟默认形态保持一致
        * 事实上你是可以encode成任意值的（比如100，"hello"，因为我们只关心有没有这个键，有的话，就是这个枚举类型，只是`{}`拥有可读性
        * 你encode成什么值，decode的时候对对应的键尝试去反射回这个值就行了


最后，思考题：
> 上面说了，原生枚举序列化成： `{"math":{}}`，也说了，如果，键对应的值对原生枚举序列化是没意义的，可以是任何值，那么对于`{"math":100}`，能否顺序序列化回其枚举形态`.math`呢？

答案：
1. 值为100报错了
2. 于是我改为""或"other“等字符串或空字符串，解码的结果是`nil`

也就是说，默认的decode只认`{}`

![](/assets/images/2021-11-02-00-09-31.png)

而前面我们知道了，如果是自己手写，它可以是任何值，它的意义仅仅是个标识，并不会取它的值。验证：
```swift
enum NormEnum: Codable {
    case history, math, geometry
    
    private enum keyMap: String, CodingKey{
        case history  = "HIST"
        case math     = "MATH"
        case geometry = "GEOM"
    }

    func encode(to encoder: Encoder) throws {
        var container = try encoder.container(keyedBy: keyMap.self)
        switch self{
        case .history: try container.encode("", forKey: .history)
        case .math: try container.encode("", forKey: .math)
        case .geometry: try container.encode("", forKey: .geometry)
        }
    }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: keyMap.self)
        if let s = try? container.decode(String.self, forKey: .history) {
            self = .history
        } else if let s = try? container.decode(String.self, forKey: .math) {
            self = .math
        } else {
            self = .geometry
        }
    }
}
```
上面的代码中，我将三个字段全部用空字符串编码，并且给了三个不同的键名，现在，我真入任意值，比如`"HAHA"`，解码看看：
```swift
let js2 = "{\"MATH\":\"HAHA\"}"
let js2d = js2.data(using: .utf8)
let myobj2 = try? JSONDecoder().decode(NormEnum.self, from: js2d!)
```
结果成功认出myobj2是一个`.math`。原理当然是我的代码里在尝试转成一个字符串，而没有限定是什么字符串。

# Document Architecture

所谓的Dopcument Architecture，其实就是支持把用app产生的作品保存起来，比如你创作的一幅图片，可以保存为`.jpg`，你用photoshop做的文件是`.psd`，下次用自己的app加载这个文件，能认出所有组件和模型，比如我们想为document取个名字叫`.emojiart`。

## App Architecture

### App protocol

* 一个app里只能有一个struct服从`App Protocol`
* mark it with `@main`
* it's `var body` is `some Scene`

### Scene protocol

* A `Scene` is a container fo a `top-lever` View that you want to show in your UI
* `@Environment(\.scenePhase)`
* three main types of Scenes:
```swift
WindowGroup {return aTopLevelView}
DocumentGroup(newDocument:) { config in ... return aTopLevelView}
DocumentGroup(viewing: viewer:) { config in ... return aTopLevelView}  // 只读
```
* 后两个类似view里面的`ForEach`但不完全相同：
    * 而是："**new window**" on Mac, "**splitting the screen**" on iPad -> for create new Scene
* `content`参数是一个返回some View的方法
    * 返回的是top-level view
    * 每当新建一个窗口或窗口被分割时都会被调用

当你在iPad上分屏，且两个打开同一应用，就是`WindowGroup`在管理，为每一个windows生成一个Scene(share the same parameter e.g. view model, 因为代码是同一份，除非额外为每个scene设置自己的viewmodel之类的).

`config`里保存了document(即viewModel)，也保存了文件位置。

### SceneStorage

* 能持久化数据
* 以窗口/分屏为单位 -> per-Scene basis
* 也会invalidate view
* 数据类型有严格限制，最通用的是`RawRepresentable`

![](/assets/images/2021-11-04-16-44-48.png)

一个View里的`@State`改为`@SceneStorage(uniq_id)`后，app退出或crash了，仍然能找回原来的值。

这个时候每个Scene里的值就已经不一样了。

### AppStorage

* application-wide basis
* 存在UserDefaults里
* 服从`@SceneStorage`的数据才能被存储
* invalidate view

## DocumentGroup

`DocumentGroup` is the document-oriented Scene-building Scene.

```swift
@main
struct MyDemoApp: App {
    @StateObject var paletteStore = PaletteStore(named: "Default")
    var body: some Scene {
        WindowGroup {
            MyDemoView()
            .environmentObject(paletteStore)
        }
    }
}

// V.S.

struct MyDemoApp: App {
    var body: some Scene {
        DocumentGroup(newDocument: {myDocument()}) { config in
            MyDemoView(document: config.document)
        }
    }
}
```

* 不再用`@StateObject`传递ViewModel，每新建一个Document都会有一个独立的ViewModel
    * 必须要服从`ReferenceFileDocument`(这样能存到文件系统以及从文件系统读取了)
    * `config`参数包含了这个ViewModel（就是document)，以及document的url
    * 很好理解，每一个document肯定有自己的数据（想象一个“最近打开”的功能，每一个文档都是独立的）
* `newDocument`里自行提供一个新建document的方法
* 封装了关联的（选择document的）UI和行为
* you **MUST** implement `Undo` in your application

如果不去实现`Undo`，也可以直接把model存到document文件里：
1. 你的ViewModel要能init itself from a `Binding<Type>`
    * 如`config.$document`
2. ViewModel由一个`ObservedObject`变成一个`StateObject`
    * 这次必须服从`FileDocument`

```swift
struct MyDemoApp: App {
    var body: some Scene {
        DocumentGroup(newDocument: {myDocument()}) { config in
            // MyDemoView(document: config.document) // 之前的
            MyDemoView(document: viewModel(model: config.$document))
        }
    }
}
```

把`newDocument: {myDocument()}`改为`viewer: myDocument.self`，就成了一个只读的model，（你甚至不需要传入实例），如果你要开发的是一个查看别人文档的应用，这个特性就比较有用了。

### FileDocument protocol

This protocol gets/puts the contents of a document from/to a file. 即提供你的document读到文件系统的能力。

```swift
// create from a file
init(configuration: ReadConfiguration) throws {
    if let data = configuration.file.regularFileContents {
        // init yourself from data
    } else {
        throw CocoaError(.fileReadCorruptFile)
    }
}

// write
func fileWrapper(configuration: WriteConfiguration) throws -> FileWrapper {
    FileWrapper(regularFileWithContents: /*my data*/)
}
```

### ReferenceFileDocument

* 几乎和`FileDocument`一致
* 继承自`ObservableObject` -> ViewModel only
* 唯一的区别是通过后台线程的一个`snapshot`来写入
```swift
// 先snapshot
func snapshot(contentType: UTType) throws -> Snapshot {
    return // my data or something
}
// then write
func fileWrapper(snapshot: Snapshot, configuration: WriteConfiguration) throws -> FileWrapper {
    FileWrapper(regularFileWithContents: /* snapshpt converted to a Data */)
}
```

流程大概是，你的model有变化之后，会先找`snapshot`方法创建一份镜像，然后再要求你给出一个`fileWrapper`来写文件。

### 自定义文件类型

声明能打开什么类型的文件，通过：UTType(`Uniform Type Identifier`)

可以理解为怎么定义并注册（关联）自己的扩展名，就像photoshop关联.psd一样。

1. 声明(Info tab)，设置`Exported/Imported Type Identifier`，所以表面上的扩展名，内里还对应了一个唯一的标识符，一般用反域名的格式
![](/assets/images/2021-11-04-17-27-55.png)
2. 声明拥有权，用的就是上一步标识符，而不是扩展名
![](/assets/images/2021-11-04-17-28-25.png)
3. 告知系统能在`Files` app里打开这种文档
    * info.plist > Supports Document Browser > YES
4. 代码里添加枚举：
```swift
extension UTType {
    static let emojiart = UTType(exportedAs: "edu.bla.bla.emojimart")
}

static let readableContentTypes = [UTType.emojiart]
```

## Undo

* use `ReferenceFileDocument` must implement Undo
* 这也是SwiftUI能自动保存的时间节点
* by `UndoManager` -> `@Environment(\.undoManager) var undoManager`
* and by register an `Undo` for it: `func registerUndo(withTarget: self, howToUndo: (target) -> Void)`

```swift
func undoablePerform(operation: String, with undoManager: UndoManager?, doit: () -> Void){
    let oldModel = model
    doit()
    undoManager?.registerUndo(withTarget: self) { myself in
        myself.model = model
    }
    undoManager?.setActionName(operation) // 给操作一个名字，如"undo paste"， 非必需
}
```

用`undoablyPerform(with:){} 包住的任何改变model的操作就都支持了undo

## Review

回顾一下，我们把应用改造为`Document Architechture`的步骤：
1. 应用入口，将`WindowGroup`改为了`DocumentGroup`，并修改了相应的传递document的方式
2. 实现document(即view model) comform to `ReferenceFileDocument`
    * 实现snapshot, write to file (`FileWrapper`), and read from file
3. 自定义一个文件类别（扩展名，标识符，声明拥有者等）
4. 此时启动应用，入口UI已经是文档选择界面了，所以我说它封装了UI和行为
    * 但此时不具备保存的功能，需要进一步实现`Undo`'
5. 通过`undoManager`把改动model的行为都包进去实现undo/redo
    * 此时document已能自动保存
6. 增加toolbar, 实现手动undo/redo
7. 顺便注册文档类型，以便在Files应用内能用本app打开
    * `Info.plist` > `Supports Document Browser` > YES

# MVVM

![](/assets/images/2021-10-27-12-24-18.png)

* viewmodel要起到gete keeper的作用，它就要把model给private起来
    * 或者private (set), 这样保护了写，但是能读
    * 或者用一个计算属性把需要的model 暴露出去
* 一个viewmodel通常要conform `ObservableObject`
    * 就隐含了一个`var objectWillChange: ObservableObjectPublisher`
    * model要改动前：`objectWillChange.send()`
    * 或者，把model改为`@Publisher var model`，会自动广播
* 订阅者（通常就是View）就要把这个viewmodel打个可订阅的标识：
    * `@ObservedObject var viewModel: MyViewModel`
    * 只能是`var`，因为很明显是会变的
    * View监听到是会自动invalicate view的，就会重绘

# UIKit Integration

UIKit并不是纯View的世界，大多数时候是跟ViewController一起出现的，还严重依赖`Delegate`这种机制进行跨View的事件传递（回调）。

## Representbles

`UIViewRepresentable`，`UIViewContorllerRepresentable`都是SwiftUI的View了，包含几个组件：

1. `makeUIView{Controller}(context: Context) -> view/controller`
2. `updateUIView{Controller}(view/controller, context: Context) ->`
3. `makeCoordiinator() -> Coordinator` // handle delegate
    * 调用该方法后, `context.coordinator`就有值了
4. a `Context` containn the coordinator, swiftui's env, animation transaction
5. `dismantleUIView{Controller}(view/controller, coordinator: Coordinator)` // clean up when disappears

# skills

## basic

* [0...6]是0到6，[0..<6]是0到5
* var s = struct_a; s["a"] = 3, 不会改变struct_a, 因为struct永远是复制
* `arr.firstIndex(where: { item in item.id == myID})`，因为where需要的函数传递的是本身（类似map, filter)，所以可以简化为：
    * `arr.firstIndex(where: { $0.id == myID})`
* `typealias Card = MemoryGame<String>.Card` 别名
* `var a_int_array = [Int]()` 一种初始化方式
* extension中的属性可以直接用，（当然也可以用`self.`）
* `arr.filter { isGood($0)}` 因为参数就是自己，还可以继续简化： `arr.filter(isGood)``
    * 同理：`[1...100].reduce(0, +)`，因为默认参数是两个，所以会自动填到+号两边，展开就是`{ $0 + $1 }`
* `Collection` protocol is for *immutable* collections
    * mutalbe Collection protocol is `RangeReplaceableCollection`
    * 所以要写一个扩展，在改变集合的元素，先选对正确的protocol
* 用`try`还是`try?`调用一个声明了`throw`的函数，取决于你是要忽略它还是处理它
    * `try`就是不处理，结果就是包含了这段代码的函数也要标上`throw`
    * `try?`就是忽略掉，承认`nil`
* `String(describing: obj)`: 对象的字符串表示，或字符串描述
* `#function` 程序名
* `@ScaleMetric var fontSize: CGFloat = 40.0` 固定大小的字体，用`@ScaleMetric`也能按比例缩放
* 剪贴板：`UIPasteboard.general.image?.jpegData(...)`
* safe area: `UIAplication.shared.windows.first?.safeAreaInsets`
* `views.map{ UIHostingController(rootView: $0)}` 把一组View转为ViewController
* `timer = Timer.publish(erery: 3, on: .current, in: .common).autoconnect()`
    * view`.onReceive(timer, perform: {}) `
* `Texxt(Image(systemName: "video.circle")) + Text("视频")`: 两个知识点
    * Text view重载了`+`操作符，省去了用`HStack`
    * Image也可以作为Text的内容
* `Circle + trim + stroke + rotation` 可以组合出一段任意角度的弧形
* 一个`PreferenceKey`用来广播属性变化的例子：
![](/assets/images/2021-11-08-00-57-05.png)
    * see more [https://swiftwithmajid.com/2020/01/15/the-magic-of-view-preferences-in-swiftui/](https://swiftwithmajid.com/2020/01/15/the-magic-of-view-preferences-in-swiftui/)

看一个简化的实例：
![](/assets/images/2021-10-28-00-41-01.png)

可以看到，其实化简化可读性更强，用for循环，再在里面做逻辑，会把直白的初衷绕进去：
* 返回唯一一个面朝上的卡片
* 设置选定索引的卡片面朝上

* 同样， `Button`的声明是：`(_ title: StringProtocol, action: () -> Void)`, 
    * 简化后也更加直观了：`Button("text"){ actions }`

* 给class/struct添加和使用默认的`description`有点绕，等于原生并不支持，还理解成了`String`的方法
```swift
struct abc: CustomStringConvertible {
    var a:Int
    var b:Int
    func de() -> String{
        // #function, file, filePaht, fileID, line, column
        "\(String(describing: self))\n\(#function)\n\(#filePath)"

        // String(describing: obj)
        // 理解为用obj对象的description属性来构造字符串
        // 而一般人的设计思路会是：给obj对象增加一个description属性，这个属性是个string
        // 并且这个对象要服务 CustomStringConvertible 协议
    }
    var description: String {
        "{\"a\":\(a), \"b\":\(b)}"
    }
}

abc(a: 77, b: 88).de() // 输出： {"a": 77, "b": 88} \n de() \n myfile_path
```

## view

* `var body : some View {...}` 意思是你自己不需要实现View，但你要返回some实现了View的（别的）对象
    * 它是一个computed var，所以跟的{}就是一个function
    * 所以{}里隐含了一个return
* `Text("hello").padding()`返回的不再是Text
* `ZStack(alignment: .center, content: {...})`
    * 简化为：`ZStack(alignment: .center) {...}`，提取了方法体
    * 如果`alignment`为空： `ZStack {...}`
    * 所以它里面也可以有局部变量
* 多个函数参数也可以简化：
    * `Button(action: {...}, label: {...})`
    * `Button {...} label: {...}`省掉了第一个参数名，省掉了逗号
* `Button.contextMenu{ some View}` 上下文菜单，内容就是some View
* `Menu{ some View} label: { Label }` 呈现为一个button，点击后会自动呈现some View组成的菜单
    * 也就是说它自己帮你封装了UI和行为（点击弹出菜单），不需要写什么`onTap`事件
* `myView.sheet(isPresented: $flag) { some View}` 通过`$flag`就能根据`myView`的位置在合适的位置打开sheet，内容由@viewBuilder的closure提供
* `popover`也同理，还有一种popover时把对象传进去的用法：
    * `popover`与`sheet`的区别是`popover`在计算自身大小的时候是“尽可能小”，所以在包的对象里对好自己size一下
* alert有点不同：`.alert(item: $flag) { alertToShow in return Alert}`， 就是要返回一个`Alert`对象
* `myView.popover(item: $obj) {obj in ...}` 这一类传item做flag的用法也有广泛的使用场景
* 弹出的页面查看自己的状态，用`presentationMode`环境变量
    * `presentationMode.wrappedValue.isPresented`
* `NavigationView`里的`NavigationLink`也是一样封装了UI和行为（点击跳转）
*  toolbaritem的placement除了leading, trailing等直观表示，还有一些语义对应的(类似alert中有红色的销毁按钮），如`destructiveAction, cancellationAction, confirmationAction`等，甚至`automaic`
* 工具条放到底部：ToolbarItemGroup(placement: .bottmbar){}`
* `.StackNavigationViewStyle`, 让大屏幕iPhone横屏时不去尝试左右分屏，直接铺满
* `UIDevice.current.userInterfaceIdiom == .pad`
* 环境变量：`horizontalSizeClass`, `verticalSizeClass`等，根据是否compact来判断布局，而不是写死的大小，以实现跨机型适配


## layout

* `lazyVGrid(columns: [GridItem(.fixed(200)), GridItem(.flexable()), GridItem())])` 
    * 其实就是一个flex的排版
    * 横向利用所有空间，竖向尽可能小
    * 竖排，没定义，看效果是top
    * 横排，由每一个GridItem来定义
    * `Lazy`的意思是只有出现在屏幕上时，才会渲染`body`
    * 如果横向元素也自由排列呢？比如横屏15个，竖屏6个
        * `lazyVGrid(columns: GridItem(.adaptive(minimum: 80)))` 只要一个item, 然后指定一个最小宽度即可
    * 同理应该有lazyHGrid
* `.position()`返回的是一个新view，包含了所有的可用的空间（才好去定位它的子元素到任何位置），所以：
    * `Text("hello").background(Color.red).position(x:10, y:10)` 文字底色是红色
    * `Text("hello").position(x:10, y:10).background(Color.red)` 红色则会会铺满整个空间
* 而`.offset()`则不同，不会返回整个view，但也造成了先offset再backgournd的话，文字确实移动了，但background还是高亮在原来文字的位置
    * 可以这么理解，`offset`只是把文字渲染到了其它位置，并没有改变坐标系，也不会改变background的位置。
    * 应用：一个VStack, 你把某一项offset下移15像素，结果只会是重叠到下一项，而不是后面的通通下移。
        * 所以你接一个.padding(.bottom, 15)，就实现了增加15间距的目的
        * 如果你去background，你将会知道虽然显示得很好看，其实发生了什么
* 以上两个的区别，可以在preview里面，看选中元素表示的蓝色框的位置。设了`offset`后，UI位移了，但是蓝框不变；设了`position`后，蓝框则立刻充满了整个safearea（这也意味着蓝框范围内你是都吧可以摆放这个元素的）
   * position其实充满的是整个父空间

## static

* .largeTitle, .white, 其实就是静态变量: `Font.largeTitle`, `Color.white`，所以不要觉得代码里用`static let xxx = xxx`很low
    * 静态方法同理，只要不需要是实例变量的，都可以staic起来，跳出初始化流程


## XCode

* 设置 > Behaviors > Generates output 可以设置模拟器有output时的行为，比如拉出控制台看输出
```swift
    // 设置预览的设备
    .previewDevice(PreviewDevice(rawValue: "iPhone 12"))
    .previewDisplayName("iPhone 12")
```
* preview里面你做两个`.preferredColorScheme(.dark/.light)`就可以同时预览两种颜色模式下的效果了 