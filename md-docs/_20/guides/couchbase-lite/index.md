---
---

<block class="java" />

⚠ Support in the current Developer Build is for Android only. The SDK cannot be used in Java applications.

<block class="all" />

## Getting Started

<block class="swift objc" />

### Frameworks

<block class="swift" />

- Download the latest [developer build](../../whatsnew.html).
- Drag **CouchbaseLiteSwift.framework** from your Finder to the Xcode navigator.
- Click on Project > General > Embedded Binary and add **CouchbaseLiteSwift.framework** to this section.
- Import the framework and start using it in your project.

	```swift
	import CouchbaseLiteSwift
	...
	```

<block class="objc" />

- Download the latest [developer build](../../whatsnew.html).
- Drag **CouchbaseLite.framework** from your Finder to the Xcode navigator.
- Click on Project > General > Embedded Binary and add **CouchbaseLite.framework** to this section.
- Import the framework and start using it in your project.

	```objectivec
	#include <CouchbaseLite/CouchbaseLite.h>
	...
	```

<block class="swift objc" />

### Carthage

<block class="swift" />

1. [Install Carthage](https://github.com/Carthage/Carthage#installing-carthage)
2. Add `github "couchbase/couchbase-lite-ios" "2.0DB021"` to your **Cartfile**.
3. Run `carthage update --platform ios`.
4. Drag **CouchbaseLiteSwift.framework** from **Carthage/Build/** to the Xcode navigator.
5. Click on Project > General > Embedded Binary and add **CouchbaseLiteSwift.framework** to this section.

<block class="objc" />

1. [Install Carthage](https://github.com/Carthage/Carthage#installing-carthage)
2. Add `github "couchbase/couchbase-lite-ios" "2.0DB021"` to your Cartfile.
3. Run `carthage update --platform ios`.
4. Drag **CouchbaseLite.framework** from **Carthage/Build/** to the Xcode navigator.
5. Click on Project > General > Embedded Binary and add **CouchbaseLite.framework** to this section.

<block class="swift objc" />

### CocoaPods

<block class="swift" />

1. [Install Cocoapods](https://guides.cocoapods.org/using/getting-started.html)
2. In your `Podfile`, add the following.

    ```ruby
    target '<your target name>' do
      use_frameworks!
      pod 'CouchbaseLiteSwift', :git => 'https://github.com/couchbase/couchbase-lite-ios.git', :tag => '2.0DB021', :submodules => true
    end
    ```

3. Install the pods and open the .xcworkspace file generated by Cocoapods.

    ```ruby
    pod install
    ```

<block class="objc" />

1. [Install Cocoapods](https://guides.cocoapods.org/using/getting-started.html)
2. In your `Podfile`, add the following.

    ```ruby
    target '<your target name>' do
      use_frameworks!
      pod 'CouchbaseLite', :git => 'https://github.com/couchbase/couchbase-lite-ios.git', :tag => '2.0DB021', :submodules => true
    end
    ```

3. Install the pods and open the .xcworkspace file generated by Cocoapods.

    ```ruby
    pod install
    ```

<block class="csharp" />

- Add `http://mobile.nuget.couchbase.com/nuget/Developer/` to your Nuget package sources and expect a new build approximately every 2 weeks!

Your app must call the relevant `Activate()` function inside of the class that is included in the support assembly (there is only one public class in each support assembly, and the support assembly itself is a nuget dependency).  For example, UWP looks like `Couchbase.Lite.Support.UWP.Activate()`.  Currently the support assemblies provide dependency injected mechanisms for default directory logic, and platform specific logging (i.e. Android will log to logcat with correct log levels and tags.  No more "mono-stdout" at always info level).

<block class="java" />

- In the top-level **build.gradle** file, add the following Maven repository in the `allprojects` section.

	```groovy
	allprojects {
		repositories {
			jcenter()
			maven {
				url "http://mobile.maven.couchbase.com/maven2/dev/"
			}
		}
	}
	```

- Next, add the following in the `dependencies` section of the application's **build.gradle** (the one in the **app** folder).

	```groovy
	dependencies {
		compile 'com.couchbase.lite:couchbase-lite-android:2.0.0-DB021'
	}
	```

<block class="all" />

### Resources

<block class="swift" />

The API references for the Swift SDK are available [here](http://docs.couchbase.com/mobile/2.0/couchbase-lite-swift/db021).

#### Sample App

The following tutorial is using the latest Developer Build to demonstrate some of the new features in the 2.0 API.

<div class="dp">
	<div class="tiles">
		<div class="column size-1of2">
			<div class="box">
				<div class="container">
					<a href="http://docs.couchbase.com/tutorials/travel-sample-mobile.html" taget="_blank">
						<p style="text-align: center;">Travel Sample Mobile</p>
					</a>
				</div>
			</div>
		</div>
	</div>
</div>
<br/>
<br/>

<block class="objc" />

The API references for the Objective-C SDK are available [here](http://docs.couchbase.com/mobile/2.0/couchbase-lite-objc/db021).

<block class="csharp" />

The API references for the .NET SDK are available [here](http://docs.couchbase.com/mobile/2.0/couchbase-lite-net/db021).

<block class="java" />
	
The API references for the Java SDK are available [here](http://docs.couchbase.com/mobile/2.0/couchbase-lite-java/db021).

<block class="objc java" />

The following sections cover the features that are implemented in the latest developer build. Additionally, the [tutorial app](https://github.com/couchbaselabs/mobile-training-todo/tree/feature/2.0) is incrementally updated to use the 2.0 API.
