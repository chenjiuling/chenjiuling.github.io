---
title: "Android12快速适配"
date: 2022-01-01T20:53:24+08:00
draft: false
---

## 一、android:exported

**它主要是设置 `Activity` 是否可由其他应用的组件启动**， “`true`” 则表示可以，而“`false`”表示不可以。

若为“`false`”，则 `Activity` 只能由同一应用的组件或使用同一用户 ID 的不同应用启动。

当然不止是 `Activity`， `Service` 和 `Receiver` 也会有 `exported` 的场景。

<!--more-->

**一般情况下如果使用了 `intent-filter`，则不能将  `exported` 设置为“`false`”**，不然在 `Activity` 被调用时系统会抛出 `ActivityNotFoundException` 异常。

> 相反如果没有 `intent-filter`，那就不应该把 `Activity` 的 `exported` 设置为`true` ，**这可能会在安全扫描时被定义为安全漏洞**。

而在 Android 12 的平台上，也就是使用 `targetSdkVersion 31` 时，那么你就需要注意：

**如果 `Activity` 、 `Service` 或  `Receiver` 使用 `intent-filter` ，并且未显式声明 `android:exported` 的值，App 将会无法安装。**



## 二、SplashScreen

Android 12 新增加了 [`SplashScreen`](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Fwindow%2FSplashScreen) 的 API，它包括启动时的进入应用的动作、显示应用的图标画面，以及展示应用本身的过渡效果。

它大概由如下 4 个部分组成，这里需要注意：

- 1 最好是矢量的可绘制对象，当然它可以是静态或动画形式。
- 2 是可选的，也就是图标的背景。
- 与自适应图标一样，前景的三分之一被遮盖 (3)。
- 4 就是窗口背景。

启动画面动画机制由进入动画和退出动画组成。

- 进入动画由系统视图到启动画面组成，这由系统控制且不可自定义。
- 退出动画由隐藏启动画面的动画运行组成。如果要[对其进行自定义](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fguide%2Ftopics%2Fui%2Fsplash-screen%23customize-animation)，可以通过 [`SplashScreenView`](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Fwindow%2FSplashScreenView) 自定义
