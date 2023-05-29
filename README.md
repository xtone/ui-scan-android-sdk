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
	implementation 'com.xtone.android:AndroidAutoScreenShot:0.1.0' //New
}　
```

## Usage

### For XML
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
- `captureStartRecyclerView()` ※1
- `captureStartListView()`
- `captureStartWebView()` ※2

※1 Call captureStartRecyclerView() for a configuration screen using the `Preferences` framework.

※2 Call captureStartWebView() at `webViewClient.onPageFinished()`.

For example, the following is a case of a screen with a scrolling ScrollView.

```kotlin
import com.xtone.android.autoscreenshot.AutoCapture

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //create an instance and initialize
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
| outerTopOffset | Specify the height in `dp` of the fixed UI portion that exists above the scrolling View element. If not specified, the sum of your terminal's status bar and default ToolBar (ActionBar) height will be used. |
| outerBottomOffset | The height, in `dp`, of the fixed UI portion below the scrolling View element. If not specified, 0 is used. |
| innerTopOffset | If a fixed element exists inside the scrolling View element, specify the height in `dp` of the fixed UI portion that extends beyond the upper fixed UI portion. If not specified, the default shade (4dp) specified by default for the default ToolBar (ActionBar) will be used. |
| innerBottomOffset | If there is a fixed element inside the View element to be scrolled, specify the height in `dp` that extends beyond the fixed UI portion below. If not specified, 0 is used.If BottomNavigationView is used for the fixed UI at the bottom of the screen, innerBottomOffset should be 4dp of the default shade value specified for BottomNavigationView. |

### For Compose
#### no scroll

```kotlin
import com.xtone.android.autoscreenshot.AutoCaptureCompose

@Composable
fun YourRootComposable() {

    //create an instance and initialize
    val activity = LocalContext.current as Activity
    val auto = remember {
        AutoCaptureCompose.Builder(activity)
            .apiKey("your api key")
            .projectId("your project id")
            .shouldSaveImageLocally(true)
            .build()
    }

    LaunchedEffect(Unit) {
        //execute screenshot
        auto.captureStart()
    }

    Box(contentAlignment = Alignment.Center) {
        Button {
            Text(text = "BUTTON")
        }
    }
}
```
#### with scroll

Depending on the Composable element to be scrolled, select the corresponding Modifier function from the following three.

`TakeScreenShotWithVerticalScroll()` is used when vertical scrolling is caused by the Modifier function `verticalScroll()`.

- `takeScreenShotWithLazyColumn()`
- `takeScreenShotWithLazyVerticalGrid()`
- `takeScreenShotWithVerticalScroll()`

For example, the following is a case of a screen with a scrolling LazyColumn.

```kotlin
import com.xtone.android.autoscreenshot.AutoCaptureCompose

@Composable
fun YourLazyColumnComposable() {

    val activity = LocalContext.current as Activity
    val scrollState = rememberLazyListState()

    //create an instance and initialize
    val auto = remember {
        AutoCaptureCompose.Builder(activity)
            .apiKey("your api key")
            .projectId("your project id")
            .shouldSaveImageLocally(true)
            .build()
    }

    Scaffold(
        topBar = {
            TopAppBar()
        },
        content = { innerPadding ->
            Box(modifier = Modifier.padding(innerPadding)) {
                LazyColumn(
                    state = scrollState,
                    modifier = Modifier
                        .fillMaxSize()
                        .takeScreenShotWithLazyColumn( //execute screenshot
                            auto = auto,
                            lazyListState = scrollState,
                            outerTopOffset = 100,
                            outerBottomOffset = 100,
                            innerTopOffset = 10,
                            innerBottomOffset = 50
                        )
                ) {
                    item {
                        ...
                    }
                }
            }
        })
}
```

### XML & Compose Mix
#### no scroll
you can use `captureStart()` in both XML and Compose versions.

#### with scroll
select one of the two libraries depending on whether the element performing the scrolling itself is a View or a Compose.

(1) If the scrolling UI element is in a Compose in an existing View

```kotlin
val composeView = ComposeView(this).apply {
    setContent {
        //create an instance and initialize
        ...

        LazyColumn(
            state = scrollState,
            modifier = Modifier
                .fillMaxSize()
                .takeScreenShotWithLazyColumn( //execute screenshot
                    auto = auto,
                    lazyListState = scrollState
                )
        ) {
            item {
               ...
            }
        }

    }
}
```

(2) When a scrolling UI element is placed in an existing View in a Composable function

```kotlin
val activity = LocalContext.current as Activity
AndroidView(
    factory = { context ->
        val scrollView = ScrollView(context)
        scrollView.id = View.generateViewId() //If View id is missing, please generate it

        //create an instance and initialize
        ...

        //execute screenshot
        auto.captureStartScrollView(scrollViewId = scrollView.id)

        scrollView
    }
)
```

## Unsupported Screen Patterns
The following patterns are not consistent with the basic design of the library and are not planned to be supported.

- Map
- Dialog alone
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

### Compose
- Screens that specify paddingTop or paddingBottom in the layout itself(https://github.com/xtone/AndroidAutoScreenShot/issues/6)
- Screens where the layout is directly below in the content property of the Scaffold function(https://github.com/xtone/AndroidAutoScreenShot/issues/7)
- Screen with Arrangement.spacedBy() specified for layout(https://github.com/xtone/AndroidAutoScreenShot/issues/8)

## Notes
- Make sure to tap the button on the dialog after all screen drawing is complete.
- Do not run the library at the same time.
- Specify appropriate argument values for each method.
- Write in the appropriate file location to run the library in the state of the UI of the screen you want to take a screenshot of.
<<<<<<< HEAD
- Call the method of the library corresponding to the corresponding View or Composable.
=======
- Call the method of the library corresponding to the corresponding View or Composable.
>>>>>>> f37653f53074ca1b2aef872ba589ac154b7bb71a
