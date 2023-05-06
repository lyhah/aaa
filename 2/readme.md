### 1. 创建第一个Kotlin应用程序并在模拟器上运行应用程序
- #### 实验结果如下图：
- <img src="https://github.com/lyhah/aaa/blob/main/2/1.png" />
### 2. 查看布局编辑器
查看布局的代码（Code），修改Textview的Text属性
修改字符串属性值为“Hello Kotlin!”。更进一步，修改字体显示属性，在Design视图中选择textview_first文本组件，在Common Attributes属性下的textAppearance域，设置相关的文字显示属性
重新运行应用程序，查看显示效果
- <img src="https://github.com/lyhah/aaa/blob/main/2/2.png" />
### 3. 向页面添加更多的布局
添加button按钮，调整Next按钮，删除两个按钮间的约束，添加Next的右边和底部约束至父类屏幕（如果不存在的话），Next的Top约束至TextView的底部。最后，TextView的底部约束至屏幕的底部。效果看起来如下图所示：
- <img src="https://github.com/lyhah/aaa/blob/main/2/3.png" />
### 4. 更改组件的文本
fragment_first.xml布局文件代码中，找到toast_button按钮的text属性部分
 ```xml
 <Button
   android:id="@+id/toast_button"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:text="Button"
```
弹出对话框，令资源名为toast_button_text，资源值为Toast，并点击OK。
### 5. 更新Next按钮
在string.xml文件，右键next字符串资源，选择 Refactor > Rename，修改资源名称为random_button_text，点击Refactor 。随后，修改Next值为Random。
### 6. 添加第三个按钮
向fragment_first.xml文件中添加第三个按钮，位于Toast和Random按钮之间，TextView的下方。新Button的左右约束分别约束至Toast和Random，Top约束至TextView的底部，Buttom约束至屏幕的底部，看起来的效果：
- <img src="https://github.com/lyhah/aaa/blob/main/2/4.png" />
### 7. 完善UI组件的属性设置
更改新增按钮id为count_button，显示字符串为Count，对应字符串资源值为count_button_text。同时，更改TextView的文本为0。修改后的fragment_first.xml的代码
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".FirstFragment">

    <TextView
        android:id="@+id/textview_first"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:fontFamily="sans-serif-condensed"
        android:text="@string/hello_first_fragment"
        android:textColor="@android:color/darker_gray"
        android:textSize="30sp"
        android:textStyle="bold"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/random_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/random_button_text"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textview_first" />

    <Button
        android:id="@+id/toast_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toast_button_text"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textview_first" />

    <Button
        android:id="@+id/count_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/count_button_text"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/random_button"
        app:layout_constraintStart_toEndOf="@+id/toast_button"
        app:layout_constraintTop_toBottomOf="@+id/textview_first" />
</androidx.constraintlayout.widget.ConstraintLayout>
```
### 8. 更新按钮和文本框的外观
values>colors.xml定义了一些应用程序可以使用的颜色，添加新颜色screenBackground 值为 #2196F3，这是蓝色阴影色；添加新颜色buttonBackground 值为 #BBDEFB
```xml
<color name="screenBackground">#2196F3</color>
<color name="buttonBackground">#BBDEFB</color>
```
fragment_first.xml的属性面板中设置屏幕背景色为
```xml
android:background="@color/screenBackground"
```
设置每个按钮的背景色为buttonBackground
```xml
android:background="@color/buttonBackground"
```
注意：在实验的API level中（31），这种设置并不生效，需修改res/values/themes.xml的style值，添加**.Bridge**。
```xml
<style name="Theme.MyFirstApp" parent="Theme.MaterialComponents.DayNight.DarkActionBar.Bridge">
```
移除TextView的背景颜色，设置TextView的文本颜色为color/white，并增大字体大小至72sp。
Toast与屏幕的左边距设置为24dp，Random与屏幕的右边距设置为24dp，利用属性面板的Constraint Widget完成设置。
设置TextView的垂直偏移为0.3，
```xml
app:layout_constraintVertical_bias="0.3"
```
最终效果如下图：
- <img src="https://github.com/lyhah/aaa/blob/main/2/5.png" />
### 9. 设置代码自动补全
Android Studio中，依次点击File>New Projects Settings>Settings for New Projects…，查找Auto Import选项，在Java和Kotlin部分，勾选Add Unambiguous Imports on the fly。
- <img src="https://github.com/lyhah/aaa/blob/main/2/6.png" />
### 10. TOAST按钮添加一个toast消息
打开FirstFragment.kt文件，有三个方法：onCreateView，onViewCreated和onDestroyView，在onViewCreated方法中使用绑定机制设置按钮的响应事件（创建应用程序时自带的按钮）。
```xml
binding.randomButton.setOnClickListener {
    findNavController().navigate(R.id.action_FirstFragment_to_SecondFragment)
}
```
接下来，为TOAST按钮添加事件，使用**findViewById()**查找按钮id，代码如下：
```xml
// find the toast_button by its ID and set a click listener
view.findViewById<Button>(R.id.toast_button).setOnClickListener {
   // create a Toast with some text, to appear for a short time
   val myToast = Toast.makeText(context, "Hello Toast!", Toast.LENGTH_LONG)
   // show the Toast
   myToast.show()
}
```
### 11. 使Count按钮更新屏幕的数字
此步骤向Count按钮添加事件响应，更新Textview的文本显示。
在FirstFragment.kt文件，为count_buttion按钮添加事件：
```xml
view.findViewById<Button>(R.id.count_button).setOnClickListener {
   countMe(view)
}
```
countMe()为自定义方法，以View为参数，每次点击增加数字1，具体代码为：
```xml
private fun countMe(view: View) {
   // Get the text view
   val showCountTextView = view.findViewById<TextView>(R.id.textview_first)

   // Get the value of the text view.
   val countString = showCountTextView.text.toString()

   // Convert value to a number and increment it
   var count = countString.toInt()
   count++

   // Display the new value in the text view.
   showCountTextView.text = count.toString()
}
```
### 12. 向界面添加TextView显示随机数
打开fragment_second.xml的设计视图中，当前界面有两个组件，一个Button和一个TextView（textview_second）。
去掉TextView和Button之间的约束
拖动新的TextView至屏幕的中间位置，用来显示随机数
设置新的TextView的id为**@+id/textview_random**
设置新的TextView的左右约束至屏幕的左右侧，Top约束至textview_second的Bottom，Bottom约束至Button的Top
设置TextView的字体颜色textColor属性为**@android:color/white**，textSize为72sp，textStyle为bold
设置TextView的显示文字为“R”
设置垂直偏移量layout_constraintVertical_bias为0.45
### 13. 更新显示界面文本的TextView(textview_second)
在fragment_second.xml文件中，选择textview_second文本框，查看text属性
更改该文本框id为textview_header

设置layout_width为match_parent，layout_height为wrap_content。

设置top，left和right的margin为24dp，左边距和右边距也就是start和end边距。

若还存在与Button的约束，则删除。

向colors.xml添加颜色colorPrimaryDark，并将TextView颜色设置为@color/colorPrimaryDark，字体大小为24sp
```xml
<color name="colorPrimaryDark">#3700B3</color>
```
strings.xml文件中，修改hello_second_fragment的值为"Here is a random number between 0 and %d."

使用Refactor>Rename将hello_second_fragment 重构为random_heading
### 14. 更改界面的背景色和按钮布局
向colors.xml文件添加第二个Fragment背景色的值，修改fragment_second.xml背景色的属性为screenBackground2
```xml
<color name="screenBackground2">#26C6DA</color>
```
将按钮移动至界面的底部，完成所有布局之后，如下图所示：
- <img src="https://github.com/lyhah/aaa/blob/main/2/7.png" />
### 15. 检查导航图
打开nav_graph.xml文件（res>navigation>nav_graph.xml），形如：
- <img src="https://github.com/lyhah/aaa/blob/main/2/8.png" />
### 16. 启用SafeArgs
首先打开 Gradle Scripts > build.gradle (Project: My First App)
找到buildscript脚本中的dependencies章节，添加如下代码
```xml
def nav_version = "2.3.0-alpha02"
classpath "androidx.navigation:navigation-safe-args-gradle-plugin:$nav_version"
```
接着打开 Gradle Scripts > build.gradle (Module: app)
apply plugin开头的代码下添加一行
```xml
apply plugin: 'androidx.navigation.safeargs.kotlin'
```
Android Studio开始同步依赖库
重新生成工程Build > Make Project
### 17. 创建导航动作的参数
打开导航视图，点击FirstFragment，查看其属性。
在Actions栏中可以看到导航至SecondFragment
同理，查看SecondFragment的属性栏
点击Arguments **+**符号
弹出的对话框中，添加参数myArg，类型为整型Integer
### 18. FirstFragment添加代码，向SecondFragment发数据
打开FirstFragment.kt源代码文件
找到onViewCreated()方法，该方法在onCreateView方法之后被调用，可以实现组件的初始化。找到Random按钮的响应代码，注释掉原先的事件处理代码
实例化TextView，获取TextView中文本并转换为整数值
```xml
val showCountTextView = view.findViewById<TextView>(R.id.textview_first)
val currentCount = showCountTextView.text.toString().toInt()
```
将currentCount作为参数传递给actionFirstFragmentToSecondFragment()
```xml
val action = FirstFragmentDirections.actionFirstFragmentToSecondFragment(currentCount)
```
添加导航事件代码
```xml
findNavController().navigate(action)
```
### 19. 添加SecondFragment的代码
导入navArgs包
```xml
import androidx.navigation.fragment.navArgs
```
onViewCreated()代码之前添加一行
```xml
val args: SecondFragmentArgs by navArgs()
```
onViewCreated()中获取传递过来的参数列表，提取count数值，并在textview_header中显示
```xml
val count = args.myArg
val countText = getString(R.string.random_heading, count)
view.findViewById<TextView>(R.id.textview_header).text = countText
```
根据count值生成随机数
```xml
val random = java.util.Random()
var randomNumber = 0
if (count > 0) {
   randomNumber = random.nextInt(count + 1)
}
```
textview_random中显示count值
```xml
view.findViewById<TextView>(R.id.textview_random).text = randomNumber.toString()
```
运行应用程序，查看运行结果。
- <img src="https://github.com/lyhah/aaa/blob/main/2/9.png" />
- <img src="https://github.com/lyhah/aaa/blob/main/2/10.png" />





