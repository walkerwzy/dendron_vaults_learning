---
id: h3QCYufccKaL1fMgBVXvV
title: Layers
desc: ''
updated: 1644510573650
created: 1637863479931
---

# Layers

* A UIView does not actually **draw itself** onto the screen; it draws itself **into its layer**, and it is the layer that is portrayed on the screen.
* a view is not **redrawn** frequently; 
* instead, its drawing is cached, and the cached version of the drawing (`the bitmap backing store`) is used where possible. 
* The cached version is, in fact, the `layer`. 
* the view’s graphics context is `actually` the layer’s graphics context.
* a layer is the `recipient` and `presenter` of a view’s drawing
* Layers are `made to be animated`
* View持有layer，是layer的代理（`CALayerDeletgate`）
    * 但layer不能找到View
* View的大部分属性都只是其`underlying layer`的便捷方法
* layer能操控和改变view的表现，而无需ask the view to redraw itself

自定义underlaying layer的方法
```swift
class CompassView : UIView {
    override class var layerClass : AnyClass {
        return CompassLayer.self
    }
}
```

## Layers and Sublayers

* layer的继承树跟view的继承树几乎一致
* layer的`maskToBounds`属性决定了能否显示sublayer超出了其bounds的部分，这也是view的`clipsToBounds`的平行属性
* `sublayers`是可写的，而`subviews`不是
    * 所以设为nil可以移除所有子层，但subview却需要一个个`removeFromSuperview`
* `zPostion`决定了层级（order），默认值都是0.0