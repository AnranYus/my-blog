---
  title: 日志
  pubDate: 2024-06-29
  categories: [Android,Work]
  description: '对于Android 多屏幕任务的一些猜测'
---
## Android 多屏幕显示的问题
今天工作时遇到了多屏幕显示的问题，因为车机与手机不同，车机通常拥有不止一个屏幕，因此可能存在一个App被两个屏幕同时打开的情况。

## 默认情况
默认情况下，当在一个屏幕(CID)上打开应用后，会建立一个TaskStack并在该屏幕上启动，而当在另一个屏幕(PID)上启动App时，会将CID上的应用杀死，而后在PID上再次启动。这对于用户来说体验很差，毕竟车机作为娱乐中枢，却不能做到同屏互动，有违背最初的理念。

## 一些猜测
默认情况下应用会被杀死，我猜测是因为Android的资源分配机制无法为两个task分配同一个taskAffinity（纯猜测，待验证），事实上，即使我以NEW_TASK启动MainActivity也无法做到同时启动两个相同App。也或许是因为系统无法为同一个ApplicationID构建两个相同的Application实例？当然，这也是猜测。

## 折中的方式
既然同时启动两个Application不可行，那么就只能启动两个Acitivty。
建造一个PIDMainActivity，该类继承MainActivity，没有任何额外的属性，纯粹是为了能够同时展示两个MainActivity而做出的替身。
同时取消以MainAcitivty作为入口，增加RouterActivity作为导航，在onCreate方法中通过Display.displayId来获取当前的屏幕ID,若为0则为CID,若不为0则为PID。
这样就能在两块屏幕上同时运行一个App了。当然，为了保证两个Activity的操作逻辑不相互影响，必须为两个Activity制定不同的Task来响应返回，跳转的逻辑。

## 官方方案？
折中的方法虽然能用，但是也只能适用于简单的App,对于十几甚至几十个Activity的大型应用来说，这样为每个Activity建立一个附加Activity的方法显然并不方便。
实际上android系统提供了ActivityOptions.setLaunchDisplayId()来支持在不通屏幕上启动activity。

## 存在的问题
无论哪种方式，都存在一种问题：数据同步问题。当前的android项目都采用mvvm架构，通常在Activity中绑定ViewModel,并在Activity中监听ViewModel中的数据变化。
而通过上面的方式建立的两个Activity，实际上绑定的是同一个ViewModel，这就造成了，当一个屏幕更新数据后，另一个屏幕也会收到数据更新事件并响应，这也降低了用户的使用体验。

## 解决方案
目前我能想到的解决方案无非两种，拆分ViewModel和检查发起请求的DisplayId。
对于现有的项目，为两个的Activity拆分成两个完全相同ViewModel工程量太大，且没有必要，以及如同Activity一样为每个ViewModel都建立一个附属的ViewModel显然比较蠢，因此我选择在需要两个屏幕对数据更新进行差异化响应的时候判断发起操作的DisplayId。

## 未来
以我的角度观察，以上的处理方式都不够完美，要达到真正完善的多屏协同需要从AMS开始，剖析应用的启动流程，在根源上将两边的逻辑区分开来才是最优解。
