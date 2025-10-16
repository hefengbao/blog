---
title: Char & String
date: 2025-06-13 17:17:17
tags:
  - kotlin
  - kotlin-char
  - kotlin-string
categories: Kotlin
---
## 定义字符、字符串

```kotlin
// 定义字符串，用双引号""
val hello = "你好 8ug.icu"

// 定义字符，可以用单引号''
val char = 'C'
```

## 字符串长度：`length` & `count()`

```kotlin
val length = hello.length
// 或者
val length2 = hello.count()

println("length = $length, length2 = $length2")
//输出结果： length = 10, length2 = 10
```

## 字符串拼接 & 模板

```kotlin
println("hello ".plus("www.8ug.icu")) //hello www.8ug.icu
println("hello " + "www.8ug.icu") //hello www.8ug.icu

val bug = "www.8ug.icu"

println("hello $bug") //hello www.8ug.icu


val strTemple = """
		众鸟高飞尽，
		孤云独去闲。
		相看两不厌，
		只有敬亭山。
""".trimIndent()

println(strTemple)
// 输出结果：
// 众鸟高飞尽，
// 孤云独去闲。
// 相看两不厌，
// 只有敬亭山。
```

## 字符查找：`first()`、`firstOrNull()`、`first{}`、`last()`、`lastOrNull()`、`find{}`、`indexOf()`

```kotlin
// 获取第一个元素
val firstChar = hello.first()
println(firstChar) // 结果：你

//println("".first()) //NoSuchElementException 这个会报错
println("".firstOrNull()) // 结果： null

val firstChar2 = hello.first {
		it == '你'
		// it == 'a' // 报 NoSuchElementException 错误
}
println(firstChar2) // 结果： 你

val lastChar = hello.last()
println(lastChar) //结果： u

hello.lastOrNull()
hello.lastOrNull { it == 'u' }

hello.find { it == '你' }

val index: Int = hello.indexOf('你')

println("字符第一次出现的下标 = $index")
//结果：字符第一次出现的下标 = 0
```

## 字符串截取 `substring()`、`subSequence()`
```kotlin
/**
 * 字符串截取：
 * substring(startIndex: Int, endIndex: Int)
 * substring(startIndex: Int)
 * substring(rang: IntRang)
 * startIndex参数：截取字符串的开始下标
 * endIndex参数：截取字符串的结束下标
 * rang参数，是指一个IntRang类型
 */

val substr = hello.substring(0,2)
println("截取的字符 = $substr")
//结果：截取的字符 = 你好

val substr2 = hello.substring(IntRange(0,2))
println("截取的字符2 = $substr2")
//结果：截取的字符2 = 你好

//println(hello.substring(0,hello.length + 1))
//StringIndexOutOfBoundsException 越界错误

/**
 * 字符串截取：
 * subSequence(startIndex: Int, endIndex: Int)
 * startIndex参数：截取字符串的开始下标
 * endIndex参数：截取字符串的结束下标
 */

val substr3 = hello.subSequence(0,2)
println("截取的字符3 = $substr3")
//结果：截取的字符3 = 你好
```

## 字符串替换：`replace()`、`replaceFirst()`、`replaceAfter()`、`replaceAfterLast()`、`replaceBefore()`、`replaceBeforeLast()`

```kotlin
/**
 * replace(oldChar: Char, newChar: Char, ignoreCase: Boolean = false)
 * oldChar: 要被替换的字符
 * newChar: 用来替换的字符
 * ignoreCase: 是否忽略大小写，默认否
 */

val replaceStr = hello.replace('u','U')
println("替换字符 = $replaceStr")
//结果：替换字符 = 你好 8Ug.icU

val replaceStr2 = hello.replace('U','哈', true)
println("替换字符 = $replaceStr2")
//结果：替换字符 = 你好 8哈g.ic哈

println(hello.replace("你好", "Hello"))
//结果：Hello 8ug.icu

/**
 * 把满足条件的第一个字符或字符串替换成新的字符或字符串
 * String.replaceFirst(oldChar: Char, newChar: Char, ignoreCase: Boolean = false)
 * String.replaceFirst(oldValue: String, newValue: String, ignoreCase: Boolean = false)
 */

println("ii".replaceFirst('i','I'))
//结果：Ii

/**
 * 把字符串中第一个匹配分隔符的之后的部分替换为替代字符串
 * String.replaceAfter(delimiter: Char, replacement: String, missingDelimiterValue: String = this)
 * String.replaceAfter(delimiter: String, replacement: String, missingDelimiterValue: String = this)
 * delimiter：分隔符
 * replacement：替换的字符串
 * missingDelimiterValue：没有查找到分隔符时返回的值，默认是原字符串
 */
println("uiuiui".replaceAfter('u',"X"))
//结果：uX
println("uiuiui".replaceAfter('a',"X"))
//结果：uiuiui
println("uiuiui".replaceAfter("a","X", "AA"))
//结果：AA

/**
 * 把字符串中最后一个匹配分隔符的之后的部分替换为替代字符串
 */
println("uui".replaceAfterLast('u',"X"))
//结果：uuX

/**
 * 把字符串中第一个匹配分隔符的之前的部分替换为替代字符串
 * String.replaceBefore(delimiter: Char, replacement: String, missingDelimiterValue: String = this)
 * String.replaceBefore(delimiter: String, replacement: String, missingDelimiterValue: String = this)
 */

println("hello 888ug.icu".replaceBefore('8',"B"))
//结果：B888ug.icu

/**
 * 把字符串中最后一个匹配分隔符的之前的部分替换为替代字符串
 * String.replaceBeforeLast(delimiter: Char, replacement: String, missingDelimiterValue: String = this)
 * String.replaceBeforeLast(delimiter: String, replacement: String, missingDelimiterValue: String = this)
 */
println("hello 888ug.icu".replaceBeforeLast('8',"B"))
//结果：B8ug.icu
```

## 字符串分割 `split()`

```kotlin
/**
 * 字符串分割，返回为 List<String>
 * CharSequence.split(vararg delimiters: String, ignoreCase: Boolean = false, limit: Int = 0)
 * CharSequence.split(vararg delimiters: Char, ignoreCase: Boolean = false, limit: Int = 0)
 * CharSequence.split(regex: Pattern, limit: Int = 0)
 */

println("hello 8ug.icu".split(" "))
//结果 [hello, 8ug.icu]
println("hello 8ug.icu".split(Pattern.compile("")))
```

## 字符串验证

```kotlin
/**
 * isBlank(): 判断字符串长度（length）是否为 0，不可用于可为 null 的字符串
 * isBlank(): 判断字符串长度（length）是否大于 0，不可用于可为 null 的字符串
 * isEmpty(): 判断字符串长度（length）是否为 0，不可用于可为 null 的字符串
 * isBlank(): 判断字符串长度（length）是否大于 0，不可用于可为 null 的字符串
 * isNullOrEmpty()：判断字符串是否为 null 或者长度为 0
 * isNullOrBlank()：判断字符串是否为 null 或者长度为 0
 */

println("".isBlank()) //结果：true
println("".isNotBlank()) //结果：false
println("".isEmpty()) //结果：true
println("".isNotEmpty()) //结果：false

val nullStr: String? = null
//nullStr.isEmpty() 错误
//nullStr.isBlank() 错误
println(nullStr?.isBlank()) //结果：null

println(nullStr.isNullOrEmpty()) //结果：true
println(nullStr.isNullOrBlank()) //结果：true
```

Demo: https://github.com/hefengbao/kotlin-demo.git