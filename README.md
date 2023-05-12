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
	implementation 'com.xtone.android:AndroidAutoScreenShot:0.0.6' //New
}　
```

## Usage

### XML
Basically, it is written at the end of `onCreate()` of Activity or `onViewCreated()` of Fragment.

The following is a description of each method at the time of instance creation.

- `apiKey(String)`: set your API key obtained from the web page.
- `projectId(String)`: Set your project ID.
- `shouldSaveImageLocally(Boolean)`: set whether images should be saved locally (default is false)

#### no scroll

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

#### with scroll

Select the corresponding method from the following five methods according to the View element that will do the scrolling.

- `captureStartScrollView()`
- `captureStartNestedScrollView()`
- `captureStartRecyclerView()`
- `captureStartListView()`
- `captureStartWebView()`

For example, the following is a case of a screen with a scrolling ScrollView.

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
        auto.captureStartScrollView(
            scrollViewId =  R.id.specified_scroll_view,
            appBarLayoutId = R.id.specified_appbar_layout,
            outerTopOffset = 100,
            outerBottomOffset = 100,
            innerTopOffset = 10,
            innerBottomOffset = 50
        )
    }
}
```

The following is a description of the method arguments corresponding to the View to be scrolled.Customize the arguments accordingly.

argument name     | description |
-----------| ------------ |
| ○○○Id | Specify the id of the View element to be scrolled. |
| appBarLayoutId | Specify the id of the AppBarLayout element only if an interlocking toolbar exists. If not specified, 0 is used. |
| outerTopOffset | specifies the height in `dp` of the fixed UI portion that exists above the scrolling View element. If not specified, the sum of your terminal's status bar and default ToolBar (ActionBar) height will be used. |
| outerBottomOffset | The height, in `dp`, of the fixed UI portion below the scrolling View element. If not specified, 0 is used. |
| innerTopOffset | If a fixed element exists inside the scrolling View element, specify the height in `dp` of the fixed UI portion that extends beyond the upper fixed UI portion. If not specified, the default shade (4dp) specified by default for the default ToolBar (ActionBar) will be used. |
| innerBottomOffset | If there is a fixed element inside the View element to be scrolled, specify the height in `dp` that extends beyond the fixed UI portion below. If not specified, 0 is used. |

### Compose
#### no scroll

This is described in the route's Composable function.

```kotlin
import com.xtone.android.autoscreenshot.AutoCaptureCompose

@Composable
fun YourRootComposable() {

    //create and initialize an instance
    val activity = LocalContext.current as Activity
    val auto = remember{
        AutoCaptureCompose.Builder(activity)
            .apiKey("your api key")
            .projectId("your project id")
            .shouldSaveImageLocally(true)
            .build()
    }

    //execute screenshot
    auto.captureStart()


    Box(contentAlignment = Alignment.Center) {
        Button(onClick = { }) {
            Text(text = "BUTTON")
        }
    }
}
```
#### with scroll

The description for LazyColumn is placed in the Composable function.

```kotlin
import com.xtone.android.autoscreenshot.AutoCaptureCompose

@Composable
fun YourLazyColumnComposable() {

    val activity = LocalContext.current as Activity
    val scrollState = rememberLazyListState()

    //create and initialize an instance
    val auto = remember{
        AutoCaptureCompose.Builder(activity)
            .apiKey("your api key")
            .projectId("your project id")
            .shouldSaveImageLocally(true)
            .build()
    }

    //execute screenshot
    auto.captureStartLazyColumn(
        scrollState =  scrollState,
        outerTopOffset = 100,
        outerBottomOffset = 100,
        innerTopOffset = 10,
        innerBottomOffset = 50
    )

    LazyColumn(
        state = scrollState,
        modifier = Modifier.fillMaxSize()
    ) {
        item {
            ...
        }
    }
}
```

### XML & Compose Mix
#### no scroll
you can use `captureStart()` in both XML and Compose versions.

#### with scroll
select one of the two libraries depending on whether the element performing the scrolling itself is a View or a Compose.

## Unsupported Screen Patterns
- Map
- Dialog alone
- Entire screen scrolled horizontally
- Screen where the fixed UI section at the bottom is hidden from view or reduced in size when scrolling

### XML
- Entire screen with `GridView` scrolled vertically
- Entire screen scrolled vertically by combining RecyclerView and `GridLayoutManager` or `StaggeredGridLayoutManager`
- Entire screen with multiple RecyclerViews and ListViews in the screen, each scrolling vertically
- Screen where ads exist at the bottom of WebView

## Notes
- Make sure to tap the button on the dialog after all screen drawing is complete.
- Do not run the library at the same time.
- Specify appropriate argument values for each method.

### XML
- Run the library on the appropriate Activity or Fragment.
- Call the method of the library corresponding to the corresponding View(For example, if you have a RecyclerView or ListView within a NestedScrollView, call the method for the NestedScrollView, which is the main source of scrolling).
- captureStartWebView() is a
`webViewClient.onPageFinished()`.
- Call captureStartRecyclerView() for a configuration screen using the `Preferences` framework.