---
id: LNZjLBhN8Uak67H0G9cEH
title: Ios_skill
desc: ''
updated: 1643039551468
created: 1643039037793
---
# cocoa pods

[https://www.jianshu.com/p/dfe970588f95](https://www.jianshu.com/p/dfe970588f95)

* `inhibit_all_warnings!`

屏蔽cocoapods库里面的所有警告
这个特性也能在子target里面定义,如果你想屏蔽某pod里面的警告也是可以的:
```ruby
pod 'JYCarousel', :inhibit_warnings => true
```

* 指定了私有的podspec，官方的也需要手动指定 
source 'ssh://git@gitlab.9ijx.com:9830/iOS/Specs.git'
source 'https://github.com/CocoaPods/Specs.git'

```ruby
pod 'JYCarousel', //不显式指定依赖库版本，表示每次都获取最新版本
pod 'JYCarousel', '0.01'//只使用0.0.1版本
pod 'JYCarousel', '>0.0.1' //使用高于0.0.1的版本
pod 'JYCarousel', '>=0.0.1' //使用大于或等于0.0.1的版本
pod 'JYCarousel', '<0.0.2' //使用小于0.0.2的版本
pod 'JYCarousel', '<=0.0.2' //使用小于或等于0.0.2的版本
pod 'JYCarousel', '~>0.0.1' //使用大于等于0.0.1但小于0.1的版本，相当于>=0.0.1&&<0.1
pod 'JYCarousel', '~>0.1' //使用大于等于0.1但小于1.0的版本
pod 'JYCarousel', '~>0' //高于0的版本，写这个限制和什么都不写是一个效果，都表示使用最新版本

pod 'JYCarousel', :path => '/Users/Dely/Desktop/JYCarousel'

# 使用仓库中的master分支:
pod 'JYCarousel', :git => 'https://github.com/Delyer/JYCarousel.git'
# 使用仓库的其他分支:
pod 'JYCarousel', :git => 'https://github.com/Delyer/JYCarousel.git' :branch => 'release'
# 使用仓库的某个tag:
pod 'JYCarousel', :git => 'https://github.com/Delyer/JYCarousel.git', :tag => '0.0.1'
# 或者指定一个提交记录:
pod 'JYCarousel', :git => 'https://github.com/Delyer/JYCarousel.git', :commit => '5e473f1e0530bb3799f2f0d70554b292570bd8f0'


pod 'JSONKit', :podspec => 'https://example.com/JSONKit.podspec'
```

