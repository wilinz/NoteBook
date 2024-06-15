# Android Compose Material3 ModalNavigationDrawer 抽屉的使用（处理了一些坑）

```kotlin
val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed)
val scope = rememberCoroutineScope()

ModalNavigationDrawer(
    drawerState = drawerState,
    drawerContent = {
        // 处理 Back 事件
        BackHandler(drawerState.isOpen) {
            scope.launch { drawerState.close() }
        }
        // requiredWidth 重置抽屉宽度为指定值，但是不能过大
        ModalDrawerSheet(modifier = Modifier.requiredWidth(320.dp)) {
            // 抽屉内容
        }
    },
    // 仅在抽屉打开时允许手势开关抽屉，防止手势冲突
    gesturesEnabled = drawerState.isOpen
) {
    Scaffold(
            modifier = Modifier.fillMaxSize(),
            topBar = {
                TopAppBar(
                    navigationIcon = {
                        // 打开抽屉的按钮
                        IconButton(onClick = {
                            scope.launch { drawerState.open() }
                        }) {
                            Icon(imageVector = Icons.Default.Menu, contentDescription = "打开抽屉")
                        }
                    }
                )
           }
      ){
        // 页面内容
    }  
}
```