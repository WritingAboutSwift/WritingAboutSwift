---
title: View Debugging Without the View Debugger
tags: debugging, uiview
layout: article
author: dylan
show_author_profile: true
---

You're not alone if you've spent countless hours trying to figure out why views aren't showing up as expected. Is the frame being set in the right place? Did I remember to add it as a subview? Are my layout constraints borked? After nearly 10 years of making iOS apps, I still get tricked by view layouts.

To make matters worse, sometimes it's not possible to use Xcode's built-in view debugger, like when you're working on an App Extension. Lately I've been working on [a custom keyboard for indiginous languages](), and several times have found myself wishing I could use the [view debugger](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/Xcode_Overview/ExaminingtheViewHierarchy.html) to see what was wrong.

Luckily it's easy to make a function that prints all views attached to a given view (and associated layout constraints, if you like).

To do so, we can make a static function that recursively prints the details of all views attached to a given view. Jump to the bottom if you want to go straight to the finished code.

1. Make a class and stub out the function. Don't forget to import UIKit since we're dealing with UI elements.

```swift
import UIKit

class ViewDebugger {

}
```



2. Write the method signature. Since it's recursive, we'll want to pass in a parameter indicating by how much to indent.

```swift
import UIKit

class ViewDebugger {
    public static func printViewHierarchy(view: UIView, indentLevel: Int = 0) {

    }
}
```

3. Fill in the method to print information about the given view and all of its subviews.

```swift
import UIKit

class ViewDebugger {
    public static func printViewHierarchy(view: UIView, indentLevel: Int = 0) {
        let indent = String(repeating: "    ", count: indentLevel)

        let viewDescription = String(describing: view.self)
        print(indent + viewDescription)

        for subview in view.subviews {
            printViewHierarchy(view: subview, indentLevel: indentLevel + 1)
        }
    }
}
```

And that's it. Now it can be used to help you debug your views like so:

```swift
ViewDebugger.printViewHierarchy(view: myView)
```

And you'll get an output such as:

```
<BaseKeyboard.BannerView: 0x7fdc844057e0; frame = (0 0; 375 48); layer = <CALayer: 0x600003e731e0>>
    <UICollectionView: 0x7fdc84814400; frame = (0 0; 375 48); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x600003071b90>; layer = <CALayer: 0x600003e73320>; contentOffset: {0, 0}; contentSize: {377, 48}; adjustedContentInset: {0, 0, 0, 0}; layout: <_TtCC12BaseKeyboard10BannerView30BannerCollectionViewFlowLayout: 0x7fdc86e3bb70>; dataSource: <BaseKeyboard.BannerView: 0x7fdc844057e0; frame = (0 0; 375 48); layer = <CALayer: 0x600003e731e0>>>
        <_TtCC12BaseKeyboard10BannerView10BannerCell: 0x7fdc9392b120; baseClass = UICollectionViewCell; frame = (0 0; 125 48); layer = <CALayer: 0x600003e7b340>>
            <UIView: 0x7fdc9392b5a0; frame = (0 0; 125 48); gestureRecognizers = <NSArray: 0x6000030159b0>; layer = <CALayer: 0x600003e7b360>>
                <UILabel: 0x7fdc9392b330; frame = (50 8; 25 32); text = '"Ž"'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x600001d01220>>
        <_TtCC12BaseKeyboard10BannerView10BannerCell: 0x7fdc93926dd0; baseClass = UICollectionViewCell; frame = (126 0; 125 48); layer = <CALayer: 0x600003e79960>>
            <UIView: 0x7fdc93927710; frame = (0 0; 125 48); gestureRecognizers = <NSArray: 0x600003015200>; layer = <CALayer: 0x600003e79980>>
                <UILabel: 0x7fdc93927290; frame = (57 8; 11 32); text = 'B'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x600001d00500>>
        <_TtCC12BaseKeyboard10BannerView10BannerCell: 0x7fdc93929300; baseClass = UICollectionViewCell; frame = (252 0; 125 48); layer = <CALayer: 0x600003e7a660>>
            <UIView: 0x7fdc93929780; frame = (0 0; 125 48); gestureRecognizers = <NSArray: 0x6000030155c0>; layer = <CALayer: 0x600003e7a680>>
                <UILabel: 0x7fdc93929510; frame = (55.3333 8; 14.6667 32); text = 'M'; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x600001d00b90>>
        <_TtCC12BaseKeyboard10BannerView13SeparatorView: 0x7fdc9392f780; baseClass = UICollectionReusableView; frame = (251 0; 1 48); layer = <CALayer: 0x600003e73ee0>>
            <UIView: 0x7fdc9392f9a0; frame = (0 12; 1 24); layer = <CALayer: 0x600003e73220>>
        <_TtCC12BaseKeyboard10BannerView13SeparatorView: 0x7fdc9392e250; baseClass = UICollectionReusableView; frame = (125 0; 1 48); layer = <CALayer: 0x600003e77600>>
            <UIView: 0x7fdc9392e470; frame = (0 12; 1 24); layer = <CALayer: 0x600003e773a0>>
        <_TtCC12BaseKeyboard10BannerView13SeparatorView: 0x7fdc9392cf40; baseClass = UICollectionReusableView; frame = (377 0; 1 48); layer = <CALayer: 0x600003e7bfc0>>
            <UIView: 0x7fdc9392d160; frame = (0 12; 1 24); layer = <CALayer: 0x600003e7bfa0>>
        <_UIScrollViewScrollIndicator: 0x7fdc86e07a70; frame = (369 -39; 3 36); alpha = 0; autoresize = LM; layer = <CALayer: 0x600003e48e40>>
            <UIView: 0x7fdc86e07f20; frame = (0 0; 3 36); layer = <CALayer: 0x600003e48e20>>
```

From here, we could take it a step further and also print autolayout constraints.

```swift 
... 
print(indent + viewDescription)

  for constraint in view.constraints {
    let constraintDescription = String(describing: constraint)
      let constraintPrefix = "  - " // So we can tell views and constraints apart
      print(indent + constraintPrefix + constraintDescription)
  }

for subview in view.subviews {
...
```

Or you can customize it to print more or less, as you see fit.
