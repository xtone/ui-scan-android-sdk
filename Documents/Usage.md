## Usage

### For XML
Basically, it is written at the end of `onCreate()` of Activity or `onViewCreated()` of Fragment.

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

### For Compose

It is possible to generate screenshots of the UI created with Jetpack Compose. However, there are many limitations, and in most cases it can only be used with simple screens.

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

### Notes
- Make sure to tap the button on the dialog after all screen drawing is complete.
- Do not run the library at the same time.
- Specify appropriate argument values for each method.
- Write in the appropriate file location to run the library in the state of the UI of the screen you want to take a screenshot of.
- Call the method of the library corresponding to the corresponding View or Composable.