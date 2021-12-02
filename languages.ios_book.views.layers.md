---
id: h3QCYufccKaL1fMgBVXvV
title: Layers
desc: ''
updated: 1637864437380
created: 1637863479931
---

# Layers

* a view is not redrawn frequently; 
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