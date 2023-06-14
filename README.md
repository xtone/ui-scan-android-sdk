# AndroidAutoScreenShot

"AndroidAutoScreenShot" is a library for semi-automatically creating and simultaneously sending screenshots of your app to the XXX service

## Features
- Semi Automatically take screenshots of your app
- Full Screen screenshots contains scroll of your app
- Automatically Upload screenshot to XXX

## Requirements
- Android 8+
- Java 8+
- Kotlin Project

## Installation
### Gradle
　　
1. Add the following line to your `project/build.gradle` file:

```Groovy
allprojects {
    repositories {
        google()
        jcenter()
        maven { url 'https://raw.githubusercontent.com/xtone/AndroidAutoScreenShot/main/repository/' } //New
    }
}
```

2. Add the following line to your `app/build.gradle` file:

```Groovy
dependencies {
	implementation 'com.xtone.android:AndroidAutoScreenShot:0.2.0' //New
}　
```

## Usage

Below is a simple sample code for taking a screenshot. [Please check the Usage for more details.](https://github.com/xtone/AndroidAutoScreenShot/blob/main/Documents/Usage.md)

```kotlin
import com.xtone.android.autoscreenshot.AutoCapture

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //create and initialize an instance
        val auto = AutoCapture.Builder(activity)
            .apiKey("your api key")
            .projectId("your project id")
            .shouldSaveImageLocally(true)
            .build()

        //execute screenshot
        auto.captureStart()
    }
}
```

Detailed references on methods, arguments, etc.  can be found [here](https://github.com/xtone/AndroidAutoScreenShot/blob/main/Documents/Reference.md).

## Unsupported Screen Patterns
The following patterns are not consistent with the basic design of the library and are not planned to be supported.

- Map
- Screens containing dialogs
- Entire screen scrolled horizontally
- WebView(Compose version)

## known issue
Screenshots cannot be obtained in the following cases:

### Both 
- Screen where the fixed UI section at the bottom is hidden from view or reduced in size when scrolling(https://github.com/xtone/AndroidAutoScreenShot/issues/1)
- Upward scrolling vertical screen like a chat application(https://github.com/xtone/AndroidAutoScreenShot/issues/9)
- Screen with a collapsible toolbar created in Compose and wrapped in an `AppBarLayout` element(https://github.com/xtone/AndroidAutoScreenShot/issues/10)

### XML
- Entire screen with `GridView` scrolled vertically(https://github.com/xtone/AndroidAutoScreenShot/issues/2)
- Entire screen scrolled vertically by combining RecyclerView and `GridLayoutManager` or `StaggeredGridLayoutManager`(https://github.com/xtone/AndroidAutoScreenShot/issues/3)
- Entire screen with multiple RecyclerViews and ListViews in the screen, each scrolling vertically(https://github.com/xtone/AndroidAutoScreenShot/issues/4)
- Screen where ads exist at the bottom of WebView(https://github.com/xtone/AndroidAutoScreenShot/issues/5)
- Screen with RecyclerView set up under certain conditions(https://github.com/xtone/AndroidAutoScreenShot/issues/11)

### Compose
- Screens that specify paddingTop or paddingBottom in the layout itself(https://github.com/xtone/AndroidAutoScreenShot/issues/6)
- Screens where the layout is directly below in the content property of the Scaffold function(https://github.com/xtone/AndroidAutoScreenShot/issues/7)
- Screen with Arrangement.spacedBy() specified for layout(https://github.com/xtone/AndroidAutoScreenShot/issues/8)
- Screen where contentPadding is specified in the relevant layout that allows vertical scrolling(https://github.com/xtone/AndroidAutoScreenShot/issues/12)
