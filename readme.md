# 软件项目实践实验2：构建Kotlin应用并使用Compose布局

## **实验1：创建首个Kotlin应用**

### 1. 新建项目

- 打开 Android Studio，选择 `File > New > New Project`
- 选择 `Empty Activity`
- 填写项目信息：
  - Name: `kotlin1`
  - Language: `Kotlin`
  - Minimum SDK: `API 34: Android 14.0 `

![image-20250423094327835](readmeImg/pic1.png)

### 2.运行app

- 点击工具栏中的绿色运行按钮（▶），选择模拟器或真实设备进行部署

- 等待构建并启动应用，屏幕应显示 “Hello, World!”

  ![image-20250423094833582](pic2.png)

## **实验2：**实践Compose布局

### 1.微调界面

利用surface对Greeting背景颜色进行修改

```kotlin
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(
            text = "Hello $name!",
            modifier = modifier
        )
    }
}

```

![image-20250423095530673](readmeImg/pic3.png)

### 2.修饰符

利用padding修饰符为默认修饰符添加内边距修饰符：modifier.padding(24.dp)。

```kotlin
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.unit.dp
// ...

@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(
            text = "Hello $name!",
            modifier = modifier.padding(24.dp)
        )
    }
}

```

![image-20250423100050044](readmeImg/pic4.png)

### 3.重复使用可组合项

创建一个MyApp 的可组合项，该组合项中包含Greeting。

```kotlin
@Composable
fun MyApp(modifier: Modifier = Modifier) {
    Surface(
        modifier = modifier,
        color = MaterialTheme.colorScheme.background
    ) {
        Greeting("Android")
    }
}

```

现在可以重复使用 MyApp 可组合项，可以省去 onCreate 回调和预览，从而避免重复编写代码。

在预览中，调用 MyApp 并移除预览的名称。MainActivity.kt 的代码应如下：

```kotlin
package com.example.kotlin1

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Surface
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.example.kotlin1.ui.theme.Kotlin1Theme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            Kotlin1Theme {
                MyApp(modifier = Modifier.fillMaxSize())
            }
        }
    }
}
@Composable
fun MyApp(modifier: Modifier = Modifier) {
    Surface(
        modifier = modifier,
        color = MaterialTheme.colorScheme.background
    ) {
        Greeting("Android")
    }
}
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(
            text = "Hello $name!",
            modifier = modifier.padding(24.dp)
        )
    }
}


@Preview(showBackground = true)
@Composable
fun GreetingPreview() {
    Kotlin1Theme {
        MyApp()
    }
}

```

### 4.创建Compose中的列（Column）和行（Row）

现在尝试更改Greeting

```kotlin
import androidx.compose.foundation.layout.Column
// ...

@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Column(modifier = modifier.padding(24.dp)) {
            Text(text = "Hello ")
            Text(text = name)
        }
    }
}

```

修改之前定义的MyApp，使用循环向Column中添加元素。

```kotlin
@Composable
fun MyApp(
    modifier: Modifier = Modifier,
    names: List<String> = listOf("World", "Compose")
) {
    Column(modifier) {
        for (name in names) {
            Greeting(name = name)
        }
    }
}

```

效果如下：

![image-20250423102709344](readmeImg/pic5.png)

当前尚未设置可组合项的尺寸，也未对可组合项的大小添加任何限制，因此每一行仅占用可能的最小空间，预览时的效果也是如此。更改预览效果，以模拟小屏幕手机的常见宽度 320dp。按如下所示向 @Preview 注解添加 widthDp 参数：

```kotlin
@Preview(showBackground = true, widthDp = 320)
@Composable
fun GreetingPreview() {
    Kotlin1Theme {
        MyApp()
    }
}

```

![image-20250423102908025](readmeImg/pic6.png)

接下来为修饰符（modifier）添加更多的属性，来查看显示效果。使用 fillMaxWidth 和 padding 修饰符复制以下布局。注意更改工程中的代码，使用以下代码修改Compose函数：

```kotlin
import androidx.compose.foundation.layout.fillMaxWidth

@Composable
fun MyApp(
    modifier: Modifier = Modifier,
    names: List<String> = listOf("World", "Compose")
) {
    Column(modifier = modifier.padding(vertical = 4.dp)) {
        for (name in names) {
            Greeting(name = name)
        }
    }
}

@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Column(modifier = Modifier.fillMaxWidth().padding(24.dp)) {
            Text(text = "Hello ")
            Text(text = name)
        }
    }
}

```

![image-20250423103028513](readmeImg/pic7.png)

### 5.添加按钮

向 Greeting 添加可点击元素，因此需要先添加对应的按钮。由于没有 alignEnd 修饰符，因此需要在开始时为该可组合项赋予一定的 weight。

```kotlin
import androidx.compose.foundation.layout.Row
import androidx.compose.material3.ElevatedButton
// ...

@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { /* TODO */ }
            ) {
                Text("Show more")
            }
        }
    }
}

```

![image-20250423103254121](readmeImg/pic8.png)

### 6.Compose中的状态（State）

在本部分中，将向屏幕中添加一些互动。到目前为止，已经创建了一些静态布局，但现在要让它们响应用户更改。

具体代码：

```kotlin
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    val expanded = remember { mutableStateOf(false) }
    val extraPadding = if (expanded.value) 48.dp else 0.dp
    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(
                modifier = Modifier
                    .weight(1f)
                    .padding(bottom = extraPadding)
            ) {
                Text(text = "Hello ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }
    }
}

```

![image-20250423103503475](readmeImg/pic9.png)

这段代码实现了一个可展开的问候组件，点击“Show more”按钮后，卡片底部会增加额外间距以模拟展开效果，按钮文字变为“Show less”；再次点击可收起。这通过 `remember` 和 `mutableStateOf` 实现状态管理，结合条件 padding 动态调整布局，展现了 Jetpack Compose 中响应式 UI 和交互逻辑的简洁实现方式。