1. Functions can return other functions
2. How to add sounds
3. How to add Lottie Animation
4. How to add video
5. Click no ripple effect
6. States
7. Custom Shadow
8. Scaffold, Bottom Bar and Top Bar
9. Selectable Text
10. Swipeable Tab
11. Textfields
### ***Functions can return other functions***
```kotlin
fun main() {
    val trickFunction = trickOrTreat(true)
    val treatFunction = trickOrTreat(false)
    trickFunction()
    treatFunction()
}
val trick = {println("No treats!")}
val treat: () -> Unit = { println("Have a treat!") }
fun trickOrTreat(isTrick: Boolean): () -> Unit = if (isTrick) trick else treat
```
### ***How to add sounds***
```kotlin
val mediaPlayer = MediaPlayer()  
val audioUrl = "https://www.myinstants.com/media/sounds/deepbark.mp3"
mediaPlayer.setDataSource(audioUrl)
Modifier.clickable{
	mediaPlayer.prepare()  
	mediaPlayer.start()
} 
```
### ***How to add Lottie Animation***
```kotlin
// Gradle dependency: implementation("com.airbnb.android:lottie-compose:6.0.0")
val composition by rememberLottieComposition(LottieCompositionSpec.RawRes(R.raw.lottie))  
var isPlaying by remember { mutableStateOf(true) }  
val progress by animateLottieCompositionAsState(composition = composition, isPlaying = isPlaying)  
/** To iterate the above forever: , iterations = LottieConstants.IterateForever */  
LaunchedEffect(key1 = progress){  
    if (progress == 0f) isPlaying = true  
    if (progress == 1f) isPlaying = false  
}
Modifier.clickable { isPlaying = true }
LottieAnimation(composition = composition, progress = { progress })
```
### ***How to add video***
```xml
<?xml version="1.0" encoding="utf-8"?>  
<androidx.constraintlayout.widget.ConstraintLayout  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:app="http://schemas.android.com/apk/res-auto"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent">  
  
    <VideoView        
	    android:id="@+id/idVideoView"  
        android:layout_width="wrap_content"  
        android:layout_height="fill_parent"  
        app:layout_constraintBottom_toBottomOf="parent"  
        app:layout_constraintEnd_toEndOf="parent"  
        app:layout_constraintStart_toStartOf="parent"  
        app:layout_constraintTop_toTopOf="parent" />  
  
</androidx.constraintlayout.widget.ConstraintLayout>
```
```kotlin
@Composable  
fun VideoXML() {  
    AndroidView(  
        factory = { View.inflate(it, R.layout.linear_layout, null) },  
        modifier = Modifier.fillMaxHeight(),  
        update = {  
            it.findViewById<VideoView>(R.id.idVideoView).apply {  
                lateinit var videoView: VideoView  
                videoView = findViewById(R.id.idVideoView)  
                val uri = Uri.parse("https://joy1.videvo.net/videvo_files/video/free/video0461/large_watermarked/_import_60e3ed1b0a1143.59992304_preview.mp4")  
                videoView.setVideoURI(uri)  
                videoView.start()  
                videoView.setOnPreparedListener { it.isLooping = true } 
                /** If you want to add a media controller add this:
                val mediaController = MediaController(this)  
				mediaController.setAnchorView(videoView)  
				mediaController.setMediaPlayer(videoView)  
				videoView.setMediaController(mediaController); */ 
            }
        } )  
}
```
### ***Click no ripple effect***
```kotlin
fun Modifier.noRippleClickable(onClick: () -> Unit): Modifier = composed {  
    clickable(indication = null,  
        interactionSource = remember { MutableInteractionSource() }) {  
        onClick()  
    }  
}
```
### ***States***
Clicking on "ColorBox" will change the colour of the previous box:
```kotlin
val color = remember {  mutableStateOf(Color.Yellow)  }
Box(modifier = Modifier
   .background(color.value)) {}
ColorBox(updateColor = { color.value = it })
   
@Composable  
fun ColorBox(  
    modifier: Modifier = Modifier,  
    updateColor: (Color) -> Unit  
) {  
    Box(  
        modifier = modifier  
            .clip(CircleShape)  
            .background(Color.Red)  
            .height(50.dp)  
            .width(50.dp)  
            .clickable {  
                // Clicking on Colour Box will update the color on the previous box  
                updateColor(  
                    Color(  
                        Random.nextFloat(),  
                        Random.nextFloat(),  
                        Random.nextFloat(),  
                        1f  
                    )  
                )  
  
            }  
    )  
}
```
A more simple example of the above: Clicking on it changes colour of the same Box
```kotlin
ColorBox()
@Composable
fun ColorBox(modifier: Modifier = Modifier) {
	val color = remember {   mutableStateOf(Color.Yellow)   }
	Box(modifier = modifier
		.background(color.value)
		.clickable{
			color.value = Color(
				Random.nextFloat(),
				Random.nextFloat()
				Random.nextFloat()
				1f)
		})
}
```
**Understand States:**
```kotlin
@Composable  
internal fun ScaffoldEx() {  
    var presses by remember { mutableStateOf(0) }  
    Scaffold(  
        floatingActionButton = {  
            FloatingActionButton(onClick = { presses++ }) {  
                Icon(Icons.Default.Add, contentDescription = "Add")  
            }  
        }    ) { innerPadding ->  
        Column(modifier = Modifier.padding(innerPadding),  
            verticalArrangement = Arrangement.spacedBy(16.dp)) {  
            Text(text="You have pressed the Floating Action Button $presses times")  
        }  
    }
}
```
### ***Custom Shadow***
```kotlin
fun Modifier.shadow(  
    color: Color = Color.Black, offsetX: Dp = 0.dp,  
    offsetY: Dp = 0.dp, blurRadius: Dp = 9.dp  
) = then(  
    drawBehind {  
        drawIntoCanvas { canvas ->  
            val paint = Paint()  
            val frameworkPaint = paint.asFrameworkPaint()  
            if (blurRadius != 0.dp) {  
                frameworkPaint.maskFilter =  
                    (BlurMaskFilter(blurRadius.toPx(), BlurMaskFilter.Blur.NORMAL))  
            }  
            frameworkPaint.color = color.toArgb()  
            val leftPixel = offsetX.toPx()  
            val topPixel = offsetY.toPx()  
            val rightPixel = size.width + topPixel  
            val bottomPixel = size.height + leftPixel  
            canvas.drawRect(  
                left = leftPixel,  
                top = topPixel,  
                right = rightPixel,  
                bottom = bottomPixel,  
                paint = paint  
            )  
        }  
    }  
)
// Neumorphism button with the above +++++++++++++
CalcButtonComponent(  
    modifier = Modifier.size(100.dp),  
    color = ButtonPink, symbol = "1"  
)
@Composable  
internal fun CalcButtonComponent(  
    modifier: Modifier = Modifier,  
    color: Color,  
    symbol: String,  
    onClick: () -> Unit  
) {  
    Box(  
        modifier = modifier  
            .clip(MaterialTheme.shapes.medium)  
            .background(color)  
            .clickable { onClick() }  
            .then(modifier),  
        contentAlignment = Alignment.Center,  
    ) {  
        Box(  
            contentAlignment = Alignment.Center,  
            modifier = Modifier  
                .fillMaxSize(fraction = 0.8f)  
                .shadow(  
                    color = shadowWhite, offsetX = (-4).dp, offsetY = (-4).dp, blurRadius = 8.dp  
                )  
                .shadow(  
                    color = shadowWhite2, offsetX = (4).dp, offsetY = (4).dp, blurRadius = 8.dp  
                )  
                .clip(MaterialTheme.shapes.medium)  
                .background(color)  
        ) {  
            Text(text = symbol, color = Color.White, fontSize = 32.sp)  
        }  
}}
```
### ***Scaffold, Bottom Bar and Top Bar***
```kotlin
@Composable  
fun NewTrial () {  
    var press: Int = 0  
    Scaffold(  
        floatingActionButton = {  
            FloatingActionButton(onClick = { press++ }, containerColor = Pink40) {  
                Icon(Icons.Default.Add, contentDescription = "Add")  
            }  
        },  
        bottomBar = {  
            BottomAppBar(  
                modifier = Modifier.height(52.dp), containerColor = Pink40) {  
                Icon(imageVector = Icons.TwoTone.PlayArrow, contentDescription = "Play", tint = Color.White, modifier = Modifier.weight(1f))  
                Icon(imageVector = Icons.TwoTone.PlayArrow, contentDescription = "Play", tint = Color.White, modifier = Modifier.weight(1f) )  
                Icon(imageVector = Icons.TwoTone.PlayArrow, contentDescription = "Play", tint = Color.White, modifier = Modifier.weight(1f) )  
                Icon(imageVector = Icons.TwoTone.PlayArrow, contentDescription = "Play", tint = Color.White, modifier = Modifier.weight(1f) )  
            }  
        },  
        topBar = {  
            TopAppBar(colors = TopAppBarDefaults.smallTopAppBarColors(containerColor = Pink40,  
                    titleContentColor = Color.White), title = {Text(text="Scaffold", textAlign = TextAlign.End)  
                }  
            )  
        },  
    ) { innerPadding ->  
        Column(  
            modifier = Modifier.padding(innerPadding),  
            verticalArrangement = Arrangement.spacedBy(16.dp)  
        ) {  
            Text(text = "You have pressed the Floating Action Button $press times")  
        }  
    }}
```
### ***Selectable Text***
```kotlin
@Composable
fun SelectableText() {
	SelectionContainer {
		Column {
			Text("Select this!")
			DisableSelection {
				Text("Can't Select this!")
			}
		}
	}
}
```
Note: for superscript and subscript: Text(buildAnnotatedString { withStyle(style=SpanStyle(baselineShift = BaselineShift.Superscript)) }) { append(superText) }
### ***Swipeable Tabs***
```kotlin
data class TabItem(val title: String, val selectedIcon: ImageVector, val unselectedIcon: ImageVector)  
@Composable  
fun TabBabe() {  
    val tabItems = listOf(  
        TabItem(title = "Home", selectedIcon = Icons.Filled.Home, unselectedIcon = Icons.Outlined.Home),  
        TabItem(title = "Cart", selectedIcon = Icons.Filled.ShoppingBag, unselectedIcon = Icons.Outlined.ShoppingBag),  
        TabItem(title = "Profile", selectedIcon = Icons.Filled.AccountCircle, unselectedIcon = Icons.Outlined.AccountCircle))  
    var selectedTabIndex by remember { mutableIntStateOf(0) }  
    val pagerState = rememberPagerState {  tabItems.size  }  
    LaunchedEffect(selectedTabIndex) {  
        pagerState.animateScrollToPage(selectedTabIndex)  
    }  
    LaunchedEffect(pagerState.currentPage, pagerState.isScrollInProgress) {  
        if(!pagerState.isScrollInProgress) selectedTabIndex = pagerState.currentPage  
    }  
    Column(modifier = Modifier.fillMaxSize()) {  
        TabRow(selectedTabIndex = selectedTabIndex) {  
            tabItems.forEachIndexed { index, tabItem ->  
                Tab(selected = index == selectedTabIndex, onClick = { selectedTabIndex = index },  
                    text = { Text(text = tabItem.title) },  
                    icon = {  
                        Icon(  
                            imageVector = if (index == selectedTabIndex) tabItem.selectedIcon  
                            else tabItem.unselectedIcon,  
                            contentDescription = tabItem.title)  
                    })  
            }  
        }        
        HorizontalPager(state = pagerState, modifier = Modifier  
            .fillMaxWidth().weight(1f)) { index ->  
            Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {  
                Text(text = tabItems[index].title)  
            }  
        }
    }
}
```
### ***Textfields***
```kotlin
@Composable  
fun SimpleOutlinedTextFieldSample() {  
    var text by remember { mutableStateOf("") }  
    Column(modifier = Modifier.width(200.dp), verticalArrangement = Arrangement.Center,  
        horizontalAlignment = Alignment.CenterHorizontally) {  
        OutlinedTextField(  
            value = text,  
            onValueChange = { if (it.length <= 17) text = it },  /** Limit on number of characters */
            label = { Text("Email") },  
            singleLine = true,  
            leadingIcon = { Icon(imageVector = Icons.Filled.Email, contentDescription = "Email") },  
            trailingIcon = { IconButton(onClick = {}) {  
                Icon(imageVector = Icons.Filled.CheckCircle, contentDescription = null) }},  
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Email, imeAction = ImeAction.Done),  
            keyboardActions = KeyboardActions(onDone = { Log.d("imeAction", "onDone")}),   
            colors = TextFieldDefaults.outlinedTextFieldColors(focusedBorderColor = MaterialTheme.colorScheme.primary,)  
        )  
    }  
}

// And a PASSWORD FIELD: ++++++++++++
@Composable  
fun PasswordField() {  
    var password by remember { mutableStateOf("") }  
    var passwordVisibility by remember { mutableStateOf(false) }  
   // val passwordIcon = if (passwordVisibility) Icons.Filled.Face else Icons.Outlined.Face  
    val passwordIcon =if (passwordVisibility) rememberVisibility() else rememberVisibilityOff()  
    Column(modifier = Modifier.width(200.dp), verticalArrangement = Arrangement.Center,  
        horizontalAlignment = Alignment.CenterHorizontally) {  
        OutlinedTextField(  
            value = password,  
            onValueChange = { password = it },  
            label = { Text("password") },  
            singleLine = true,  
            placeholder = { Text("Password") },  
            leadingIcon = { Icon(imageVector = Icons.Filled.Lock, contentDescription = "Password") },  
            trailingIcon = { IconButton(onClick = { passwordVisibility = !passwordVisibility }) {  
                Icon(imageVector = passwordIcon, contentDescription = null) }},  
            keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Email, imeAction = ImeAction.Done),  
            keyboardActions = KeyboardActions(onDone = { Log.d("imeAction", "onDone")}),  
            visualTransformation = if (passwordVisibility) VisualTransformation.None else PasswordVisualTransformation(),  
            colors = TextFieldDefaults.outlinedTextFieldColors(focusedBorderColor = MaterialTheme.colorScheme.primary,)  
        )  
    }  
}
```
