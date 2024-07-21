---
layout: post
title: Setting Swift compiler flags in CocoaPods
tags:
- cocoapods
- xcode
- xcodeproj
- pbxproj
- compiler flags
- swift flags
- other swift flags
- flags
- swift macros

---

Lately I've been working on a Swift framework that I'm integrating into an existing app with CocoaPods. The framework relies on an `#if DEBUG` macro to run one of two code paths, depending on whether we're building with the `Debug` configuration or something else.

~~~swift
public var baseURL: NSURL {
    #if DEBUG
        return NSURL(string:"https://coolapp-staging.herokuapp.com")!
    #else
        return NSURL(string:"https://coolapp.com")!
    #endif
}
~~~

In order for our code to trigger an `#if <something>` macro, we need to set a `-D<something>` flag in the `other Swift flags` section of our target's build settings. There's nothing special about "debug" as it's used here, we're just creating a global variable and naming it `DEBUG`.

![Target build settings]({{"/assets/target-build-settings.jpg" | relative_url}})

So far so good -- we can see that the `#if DEBUG` branch runs when we build with our `Debug` configuration and the `#else` branch runs otherwise. But this changes when we consume our framework in another project. Firstly, *CocoaPods doesn't look at the build settings in our library's Xcode project at all.* The xcconfig files that CocoaPods generates for our framework are entirely independent of the project file in our framework's own Xcode project. (Thanks to Caleb Davenport at North for pointing this out.) This means that even if we specify a `-DDEBUG` flag for the `Debug` build configuration in our framework's build settings, they won't be there when CocoaPods installs the framework into our app's workspace.

So let's set the flag higher up, say in our app target's build settings. Well it turns out that those flags don't trickle down to our framework targets at compile time. Any flags you set on the app target only apply to the app target.

OK, different idea -- why don't we make the changes in our podspec instead, using [`pod_target_xcconfig`](https://guides.cocoapods.org/syntax/podspec.html#tab_pod_target_xcconfig)? Unfortunately, it doesn't seem possible to set flags for only our `Debug` configuration, which is the whole point. And besides, we don't want to be beholden to the consumer of our API --- what if they're using a different naming convention for their build configurations?

Fortunately, we can use CocoaPods's [`post_install_hooks`](https://guides.cocoapods.org/syntax/podfile.html#tab_post_install) to get what we want. As you can see in the docs, each framework target holds an array of `build_configurations` representing the `xcconfig` files generated for each of our project's build configurations. Each of these `build_configuration` objects then holds a hash of `build_settings` representing the structured data inside the `xcconfig` file. Using `post_install_hooks` we can just write out the relevant flags for the configurations we care about.

~~~ruby
post_install do |installer|
    installer.pods_project.targets.each do |target|
        if target.name == 'CoolFramework'
            target.build_configurations.each do |config|
                if config.name == 'Debug'
                    config.build_settings['OTHER_SWIFT_FLAGS'] = '-DDEBUG'
                    else
                    config.build_settings['OTHER_SWIFT_FLAGS'] = ''
                end
            end
        end
    end
end
~~~

Bong bong.

