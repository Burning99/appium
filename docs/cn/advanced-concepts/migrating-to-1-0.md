*This document is old, and only of interest for those migrating to Appium 1.x from an earlier version*
*这是一个旧文档，只有从早期版本迁移到Appium 1.x的用户才有兴趣。*

## Migrating your tests from Appium 0.18.x to Appium 1.x
## 将您的测试从Appium 0.18.x迁移到Appium 1.x

Appium 1.0 has removed a number of deprecated features from the previous versions. This guide will help you know what needs to change in your test suite to take advantage of Appium 1.0.

Appium 1.0已经从以前的版本中删除了许多不推荐使用的功能。 本指南将帮助您了解在测试套件中需要更改哪些变化才可以很好利用Appium 1.0。

### New client libraries

### 新的客户端库

The biggest thing you need to worry about is using the new Appium client libraries instead of the vanilla WebDriver clients you are currently using. Visit the [Appium client list](/docs/en/about-appium/appium-clients.md) to find the client for your language. Downloads and instructions for integrating into your code are available on the individual client websites.

您最需要担心的是使用新的Appium客户端库，而不是您正在使用的香草WebDriver客户端。 访问[Appium客户端列表](/docs/en/about-appium/appium-clients.md)以找到匹配您的语言的客户端。 用于集成到代码中的下载和说明可在各个客户端网站上找到。

Ultimately, you'll be doing something like (to use Python as an example):
最终，你会做以下一些事情（以Python为例）：

```center
from appium import webdriver
```

Instead of:
代替：

```center
from selenium import webdriver
```

### New desired capabilities
### 期望的新能力

The following capabilities are no longer used:
不再使用以下功能：

* `device`
* `version`


Instead, use these capabilities:
使用这些功能替换：

* `platformName` (either "iOS" or "Android")
* `platformVersion` (the mobile OS version you want)
* `deviceName` (the kind of device you want, like "iPhone Simulator")
* `automationName` ("Selendroid" if you want to use Selendroid, otherwise, this can be omitted)

* `platformName`（“iOS”或“Android”）
* `platformVersion`（你想要的手机操作系统版本）
* `deviceName`（你想要的那种设备，就像“iPhone模拟器”）
* `automationName`（“Selendroid”如果你想使用，可以使用Selendroid，除此之外，可以省略这一点）

The `app` capability remains the same, but now refers exclusively to non-browser apps. To use browsers like Safari or Chrome, use the standard `browserName` cap. This means that `app` and `browserName` are exclusive.

app功能保持不变，但现在专门针对非浏览器app。 要使用Safari或Chrome等浏览器，请使用标准的browserName上限。这意味着app和browserName是排他的。

We have also standardized on camelCase for Appium server caps. That means caps like `app-package` or `app-wait-activity` are now `appPackage` and `appWaitActivity` respectively. Of course, since Android app package and activity are now auto-detected, you should be able to omit them entirely in most cases.

我们还针对Appium服务器上限标准化了camelCase。 这意味着像app-package或app-wait-activity这样的上限现在分别是appPackage和appWaitActivity。当然，由于Android应用程序包和活动现在已被自动检测到，因此在大多数情况下，您应该可以完全省略它们。

### New locator strategies
### 新的定位策略

We've removed the following locator strategies:
我们删除了以下定位器策略：

* `name`
* `tag name`

We have now added the `accessibility_id` strategy to do what `name` used to do. The specifics will be relative to your Appium client.

我们现在用添加accessibility_id的策略来替代‘name’曾经的作用。具体细节将与您的Appium客户端相关。

`tag name` has been replaced by `class name`. So to find an element by its UI type, use the class name locator strategy for your client.

`tag name`已被`class name`替换。 所以要通过它的UI类型找到一个元素，请为你的客户端使用类名定位器策略。

Note about `class name` and `xpath` strategies: these now require the fully-qualified class name for your element. This means that if you had an xpath selector that looked like this:

关于`class name`和`xpath`策略的注意事项：这些策略现在需要您的元素完全限定类名。 这意味着如果你有一个如下所示的xpath选择器：

```center
//table/cell/button
```

It would now need to be:
现在需要：

```center
//UIATableView/UIATableCell/UIAButton
```

(And likewise for Android: `button` now needs to be `android.widget.Button`)
（同样对于Android：`button`现在需要是`android.widget.Button`）

We've also added the following locator strategies:
我们还添加了以下定位器策略：

* `-ios uiautomation`
* `-android uiautomator`

Refer to your client for ways to use these new locator strategies.
参考你的客户端了解如何使用这些新的定位策略。

### XML, not JSON

App source methods, which previously returned JSON, now return XML, so if you have code that relies on parsing the app source, it will need to be updated.

以前返回JSON的app源方法现在返回XML，因此如果您有代码依赖于解析app源，则需要更新。

### Hybrid support through context, not window

### Hybrid通过context支持，而不是Window

Hybrid apps were previously supported by switching between "windows" using
以前通过在“windows”之间切换支持hybrid app

* `window_handles`
* `window`
* `switch_to.window`

Now Appium supports the more conceptually consistent concept of "context". To get all of the available contexts, or the particular context the application is is, you use

现在Appium支持更概念上一致的“context”。要获得所有可用的context，或应用程序特定的context，您可以使用以下方法

```python
# python
driver.contexts
current = driver.context
```

```javascript
// javascript
driver.contexts().then(function (contexts) { /*...*/ })
```

```c#
// c#
driver.GetContexts ()
driver.GetContext ()
```

```java
// java
Set<String> contextNames = driver.getContextHandles();
String context = driver.getContext();
```

```php
// php
$contexts = $this->contexts();
$context = $this->context();
```

```ruby
# ruby
contexts = available_contexts
context = current_context
```

And to switch between them, you use
在他们之间切换，你使用以下方法

```python
# python
driver.switch_to.context("WEBVIEW")
```

```javascript
// javascript
driver.currentContext().then(function (context) { /*...*/ })
```

```c#
// c#
driver.SetContext ("WEBVIEW");
```

```java
java
driver.context(contextName);
```

```php
// php
$this->context('WEBVIEW');
```

```ruby
# ruby
set_context "WEBVIEW"
```

### No more `execute_script("mobile: xxx")`
### 不再有execute_script（“mobile：xxx”）

All the `mobile: ` methods have been removed, and have been replaced by native methods in the Appium client libraries. This means that a method call like `driver.execute("mobile: lock", [5])` will now look something more like `driver.lock(5)` (where `lock` has been turned into a native client method). Of course, the details on calling these methods will differ by client.

所有`mobile`：方法已被删除，并已被Appium客户端库中的本地方法所替代。这意味着像driver.execute（“mobile：lock”，[5]）的方法调用，现在看起来更像是driver.lock（5）（其中lock已经变成本机客户端方法）。 当然，调用这些方法的细节会因客户端而异。

Of particular note, the gesture methods have been replaced by the new TouchAction / MultiAction API which allows for a much more powerful and general way of putting gestural automation together. Refer to your Appium client for usage notes on TouchAction / MultiAction.

特别值得注意的是，手势方法已被新的TouchAction / MultiAction API所取代，该API允许一种更强大更普遍的方法将手势自动化放在一起。有关TouchAction / MultiAction的使用说明，请参考您的Appium客户端。

And that's it! Happy migrating!
就是这样！快乐的迁移！
