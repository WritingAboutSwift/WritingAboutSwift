---
title: Floating Action Button in SwiftUI
tags: SwiftUI, NavigationLink, buttons
layout: article
author: david
show_author_profile: false
---

Floating action buttons are versatile components that implement a visually pleasing style borrowed from Android material design. They are most often used in conjunction with list or collection views to signal the capability of adding items in an intuitive way.

We'll create a floating action button in this example that can be used to navigate to another view in a navigation stack. This is a common pattern that can be useful when it's necessary to add items to a list view and the items require user input that can't be collected inline.

First, we'll create a button view `FloatingAddButtonView`. This view will encapsulate the look of the button without actually implementing standard button action functionality. This is intentional design decision as a button action is inherited by default from a [NavigationLink](https://developer.apple.com/documentation/swiftui/navigationlink]) a view is embedded.

```swift
    struct FloatingAddButtonView: View {
        var color:Color
        var plusColor:Color

        var body:some View {
            ZStack {
                Circle().fill(color).frame(width:54.0, height:54.0).shadow(color: Color.black.opacity(0.3),
                radius: 3,
                x: 3,
                y: 3)
                Image(systemName: "plus")
                    .font(.largeTitle)
                    .foregroundColor(plusColor)
            }
        }
    }
    
```

![image]({{ site.baseurl }}/assets/images/image-1.png){:width="1024"}


Now that we have the button view, we'll want to place it in the bottom trailing corner of the view body - as is customary for floating action buttons. To accomplish this, we'll add the `FloatingAddButtonView` to the bottom position of a [VStack](https://developer.apple.com/documentation/swiftui/vstack) and trailing position of an [HStack](https://developer.apple.com/documentation/swiftui/hstack) using [spacers](https://developer.apple.com/documentation/swiftui/spacer). We'll then pad the button from the view body edges using trailing and bottom padding.

```swift
    var body: some View {
        VStack {
            Spacer()
            HStack {
                Spacer()
                FloatingAddButtonView(color:Color.red,
                                        plusColor: Color.white).shadow(color: Color.black.opacity(0.3),
                                           radius: 3,
                                                x: 3,
                                                y: 3).padding(.trailing, 34).padding(.bottom, 24)
            }
        }
    }
```

![image]({{ site.baseurl }}/assets/images/image-2.png){:width="1024"}

The `FloatingAddButtonView` can now be embedded in a `NavigationLink`. As previously mentioned, when we embed the `FloatingAddButtonView` in the `NavigationLink` we will inherit button action functionality by default. To embed the view, we encapsulate the outer verical stack in a `NavigationView` and the `FloatingAddButtonView` in a `NavigationLink`. We also need to set up another view to navigate to - which in this example is just view with a single `Text` label.

```swift
    var body: some View {
        NavigationView {
            VStack {
                Spacer()
                HStack {
                    Spacer()
                    NavigationLink(destination: SecondView()) {
                        FloatingAddButtonView(color:Color.red,
                                              plusColor: Color.white).shadow(color: Color.black.opacity(0.3),
                                                                            radius: 3,
                                                                                 x: 3,
                                                                                 y: 3).padding(.trailing, 34).padding(.bottom, 24)
                        }
                    }
                }.navigationBarTitle("First View")
        }.accentColor( .black)
    }

    struct SecondView: View {
        var body:some View {
            Text("Second View")
        }
    }
```

![image]({{ site.baseurl }}/assets/images/image-3.gif){:width="1024"}


That's it!

---