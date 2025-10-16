---
title: Jetpack Compose 入门：RadioButton、Checkbox、Switch
date: 2024-06-09 17:17:17
tags:
  - jetpack-compose
categories: JetpackCompose
---
`RadioButton`、`Checkbox`、`Switch` 是 Material3 组件：`androidx.compose.material3.Checkbox`、`androidx.compose.material3.RadioButton`、`androidx.compose.material3.Switch`。

示例代码：
	
```kotlin
@Composable
fun RadioButtonCheckBoxSwitchScreen(
    modifier: Modifier = Modifier
) {
    Column(
        modifier = modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        val radioList = listOf<String>("苹果","芒果","结果")
        var radioSelected: Int? by remember { mutableStateOf(null) }
        val radioSelectedText = if (radioSelected != null){
            "(${radioList[radioSelected!!]})"
        }else{
            "()"
        }

        Text(text = "那个不是水果？${radioSelectedText}")

        radioList.forEachIndexed { index,item ->
            Row(
                modifier = modifier
                    .fillMaxWidth()
                    .clickable { radioSelected = index },
                verticalAlignment = Alignment.CenterVertically
            ) {
                RadioButton(selected = index == radioSelected, onClick = { radioSelected = index })
                Text(text = item)
            }
        }

        var checkboxChecked by remember { mutableStateOf(false) }

        Row(
            modifier = modifier.fillMaxWidth(),
            verticalAlignment = Alignment.CenterVertically
        ){
            Checkbox(checked = checkboxChecked, onCheckedChange = { checkboxChecked = it})
            Text(text = "请阅读并同意《服务协议》")
        }

        var switchChecked by remember { mutableStateOf(false) }
        Row(
            modifier = modifier.fillMaxWidth(),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            Text(text = "开启消息通知")
            Switch(checked = switchChecked, onCheckedChange = { switchChecked = it })
        }
    }
}
```

要点就是状态处理：

`RadioButton`：selected， onClick;

`Checkbox`：checked、onCheckedChange；

`Switch`：checked、onCheckedChange；