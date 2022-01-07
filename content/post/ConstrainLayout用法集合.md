---
title: "ConstrainLayout用法集合"
date: 2022-01-06T20:53:24+08:00
draft: false
---
## 一.布局的使用

#### 1.1 位置约束
>ConstraintLayout采用方向约束的方式对控件进行定位，至少要保证水平和垂直方向都至少有一个约束才能确定控件的位置
##### 1.1.1 基本方向约束

<!--more-->

```xml
<!-- 基本方向约束 -->
<!-- 我的什么位置在谁的什么位置 -->
app:layout_constraintTop_toTopOf=""           我的顶部和谁的顶部对齐
app:layout_constraintBottom_toBottomOf=""     我的底部和谁的底部对齐
app:layout_constraintLeft_toLeftOf=""         我的左边和谁的左边对齐
app:layout_constraintRight_toRightOf=""       我的右边和谁的右边对齐
app:layout_constraintStart_toStartOf=""       我的开始位置和谁的开始位置对齐
app:layout_constraintEnd_toEndOf=""           我的结束位置和谁的结束位置对齐
app:layout_constraintTop_toBottomOf=""        我的顶部位置在谁的底部位置
app:layout_constraintStart_toEndOf=""         我的开始位置在谁的结束为止
<!-- ...以此类推 -->

```
##### 1.1.2 基线对齐
```xml
app:layout_constraintBaseline_toBaselineOf=""
```
##### 1.1.3 角度约束
```xml
app:layout_constraintCircle=""         目标控件id
app:layout_constraintCircleAngle=""    对于目标的角度(0-360)
app:layout_constraintCircleRadius=""   到目标中心的距离
```
##### 1.1.4 百分比偏移
```xml
app:layout_constraintHorizontal_bias=""   水平偏移 取值范围是0-1的小数
app:layout_constraintVertical_bias=""     垂直偏移 取值范围是0-1的小数
```
#### 1.2 控件内边距、外边距、GONE Margin
```xml
<!--  外边距  -->
android:layout_margin="0dp"
android:layout_marginStart="0dp"
android:layout_marginLeft="0dp"
android:layout_marginTop="0dp"
android:layout_marginEnd="0dp"
android:layout_marginRight="0dp"
android:layout_marginBottom="0dp"

<!--  内边距  -->
android:padding="0dp"
android:paddingStart="0dp"
android:paddingLeft="0dp"
android:paddingTop="0dp"
android:paddingEnd="0dp"
android:paddingRight="0dp"
android:paddingBottom="0dp" 

<!--  GONE Margin  -->
app:layout_goneMarginBottom="0dp"
app:layout_goneMarginEnd="0dp"
app:layout_goneMarginLeft="0dp"
app:layout_goneMarginRight="0dp"
app:layout_goneMarginStart="0dp"
app:layout_goneMarginTop="0dp"
```
>当目标控件是显示的时候GONE Margin不会生效
#### 1.3 控件尺寸
##### 1.3.1 尺寸限制
```xml
android:minWidth=""   设置view的最小宽度
android:minHeight=""  设置view的最小高度
android:maxWidth=""   设置view的最大宽度
android:maxHeight=""  设置view的最大高度
```
##### 1.3.2 0dp(MATCH_CONSTRAINT)
>设置view的大小除了传统的wrap_content、指定尺寸、match_parent外，ConstraintLayout还可以设置为0dp（MATCH_CONSTRAINT），并且0dp的作用会根据设置的类型而产生不同的作用，进行设置类型的属性是layout_constraintWidth_default和layout_constraintHeight_default，取值可为spread、percent、wrap。
```xml
app:layout_constraintWidth_default="spread|percent|wrap"
app:layout_constraintHeight_default="spread|percent|wrap"
```

- spread（默认）：占用所有的符合约束的空间

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6fa75244dd3483eb32aac904431750b~tplv-k3u1fbpfcp-watermark.awebp)

- percent：按照父布局的百分比设置

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e88df6f6915b43d0a884bdedbec0136c~tplv-k3u1fbpfcp-watermark.awebp)
>percent模式的意思是自身view的尺寸是父布局尺寸的一定比例，上图所展示的是宽度是父布局宽度的0.5（50%，取值是0-1的小数），该模式需要配合layout_constraintWidth_percent使用，但是写了layout_constraintWidth_percent后，layout_constraintWidth_default="percent"其实就可以省略掉了。

- wrap：匹配内容大小但不超过约束限制

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fab93718e5bb4050b83b8b17f1d04707~tplv-k3u1fbpfcp-watermark.awebp)
##### 1.3.3 比例宽高（Ratio）
>ConstraintLayout中可以对宽高设置比例，前提是至少有一个约束维度设置为0dp，这样比例才会生效，该属性可使用两种设置：
1.浮点值，表示宽度和高度之间的比率
2.宽度:高度，表示宽度和高度之间形式的比率
```xml
app:layout_constraintDimensionRatio=""  宽高比例
```
#### 1.4 Chains(链)
```xml
app:layout_constraintHorizontal_chainStyle="spread|packed|spread_inside"
app:layout_constraintVertical_chainStyle="spread|packed|spread_inside"
```

- spread（默认）：均分剩余空间

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03484b53154d4c1ba42709484b5df335~tplv-k3u1fbpfcp-watermark.awebp)

- spread_inside：两侧的控件贴近两边，剩余的控件均分剩余空间

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f96459487bb34167949130979db787fe~tplv-k3u1fbpfcp-watermark.awebp)

- packed：所有控件贴紧居中

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28ee43c44a8e46a886c1ee0825395b76~tplv-k3u1fbpfcp-watermark.awebp)
>Chains(链)还支持weight（权重）的配置，使用layout_constraintHorizontal_weight和layout_constraintVertical_weight进行设置链元素的权重

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce061ce729524a848a9a11ca82a7d711~tplv-k3u1fbpfcp-watermark.awebp)
## 二. 辅助类
#### 2.1 Guideline（参考线）
```xml
android:orientation="horizontal|vertical"  辅助线的对齐方式
app:layout_constraintGuide_percent="0-1"   距离父级宽度或高度的百分比(小数形式)
app:layout_constraintGuide_begin=""        距离父级起始位置的距离(左侧或顶部)
app:layout_constraintGuide_end=""          距离父级结束位置的距离(右侧或底部)
```
#### 2.2 Barrier（屏障）
```xml
<!--用于控制Barrier相对于给定的View的位置-->
app:barrierDirection="top|bottom|left|right|start|end"  
<!--取值是要依赖的控件的id，Barrier将会使用ids中最大的一个的宽/高作为自己的位置-->
app:constraint_referenced_ids="id,id"
```
#### 2.3 Group（组）
```xml
app:constraint_referenced_ids="id,id"  加入组的控件id
```
#### 2.4 Placeholder（占位符）
>Placeholder的作用就是占位，它可以在布局中占好位置，通过app:content=""属性，或者动态调用setContent()设置内容，来让某个控件移动到此占位符中
#### 2.5 Flow（流式虚拟布局）
>Flow是用于构建链的新虚拟布局，当链用完时可以缠绕到下一行甚至屏幕的另一部分。当您在一个链中布置多个项目时，这很有用，但是您不确定容器在运行时的大小。您可以使用它来根据应用程序中的动态尺寸（例如旋转时的屏幕宽度）构建布局。Flow是一种虚拟布局。在ConstraintLayout中，虚拟布局(Virtual layouts)作为virtual view group的角色参与约束和布局中，但是它们并不会作为视图添加到视图层级结构中，而是仅仅引用其它视图来辅助它们在布局系统中完成各自的布局功能。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/53f5a0846fe04fdeae22b9fdb40b2299~tplv-k3u1fbpfcp-watermark.awebp)
##### 2.5.1 链约束
>Flow的constraint_referenced_ids关联的控件是没有设置约束的，这一点和普通的链是不一样的，这种排列方式是Flow的默认方式none，我们可以使用app:flow_wrapMode=""属性来设置排列方式，并且我们还可以使用flow_horizontalGap和flow_verticalGap分别设置两个view在水平和垂直方向的间隔，下面我们再添加几个控件来展示三种排列方式：

- none（默认值）：所有引用的view形成一条链，水平居中，超出屏幕两侧的view不可见

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb26ecdcad8c4155a1c8971802a2197b~tplv-k3u1fbpfcp-watermark.awebp)

- chian：所引用的view形成一条链，超出部分会自动换行，同行的view会平分宽度

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8664ec3c1b7e46b5ba8c79371f77cee8~tplv-k3u1fbpfcp-watermark.awebp)

- aligned：所引用的view形成一条链，但view会在同行同列

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff52a9fd5a7c4f478da5302d872961cb~tplv-k3u1fbpfcp-watermark.awebp)
>动画演示

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c8cfe785be524391a8e12d438c135c6e~tplv-k3u1fbpfcp-watermark.awebp)
##### 2.5.2 对齐约束
```xml
<!--  top:顶对齐、bottom:底对齐、center:中心对齐、baseline:基线对齐  -->
app:flow_verticalAlign="top｜bottom｜center｜baseline"
<!--  start:开始对齐、end:结尾对齐、center:中心对齐  -->
app:flow_horizontalAlign="start|end|center"
```
>使用flow_verticalAlign时，要求orientation的方向是horizontal，而使用flow_horizontalAlign时，要求orientation的方向是vertical

horizontal 水平排列

- top

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f28f70b5608483fb3642e7f381003d0~tplv-k3u1fbpfcp-watermark.awebp)

- bottom

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d816fd4fab8c4b9f9765c343e2e36643~tplv-k3u1fbpfcp-watermark.awebp)

- center

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a18664704e9a45f19b86bd8bab7f051e~tplv-k3u1fbpfcp-watermark.awebp)

- baseline

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8df33a9c674144abaab9462a843f92ad~tplv-k3u1fbpfcp-watermark.awebp)
##### 2.5.3 数量约束
>当flow_wrapMode属性为aligned和chian时，通过flow_maxElementsWrap属性控制每行最大的子View数量
#### 2.6 Layer（层布局）
>Layer继承自ConstraintHelper，是一个约束助手，相对于Flow来说，Layer的使用较为简单，常用来增加背景，或者共同动画，图层 (Layer) 在布局期间会调整大小，其大小会根据其引用的所有视图进行调整，代码的先后顺序也会决定着它的位置，如果代码在所有引用view的最后面，那么它就会在所有view的最上面，反之则是最下面，在最上面的时候如果添加背景，就会把引用的view覆盖掉。

#### 2.7 ImageFilterButton & ImageFilterView