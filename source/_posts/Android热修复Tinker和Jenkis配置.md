---
title: Android热修复Tinker和Jenkis配置
date: 2016-10-20 02:02:41
tags: 热修复
category: Android
---

简单来说，在编译时通过新旧两个Dex生成差异patch.dex。

在运行时，将差异patch.dex重新跟原始安装包的旧Dex替换成新的Dex,过程可能比较耗费时间与内存，所以单独放在一个后台进程中。

为了补丁包尽量的小，微信自研了DexDiff算法，它深度利用Dex的格式来减少差异的大小。它的粒度是Dex格式的每一项，可以充分利用原本Dex的信息，而BsDiff的粒度是文件，AndFix/Qzone的粒度为class

<!-- more -->
## 官方教程
1. 如何快速接入请参考[Tinker 接入指南](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)。
2. 如何自定义类请参考[Tinker 自定义扩展](https://github.com/Tencent/tinker/wiki/Tinker-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95)。
3. Tinker的API预览请参考[Tinker API预览](https://github.com/Tencent/tinker/wiki/Tinker-API%E6%A6%82%E8%A7%88)。
4. 还要其他问题，请参考[常见问题](https://github.com/Tencent/tinker/wiki/Tinker-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)。

官方教程很详细,　写这篇文章主要是为了测试，以及集合Jenkins使用

## Tinker的已知问题
由于原理与系统限制，Tinker有以下已知问题：

1. Tinker不支持修改AndroidManifest.xml，Tinker不支持新增四大组件；
2. 由于Google Play的开发者条款限制，不建议在GP渠道动态更新代码；
3. 在Android N上，补丁对应用启动时间有轻微的影响；
4. 不支持部分三星android-21机型，加载补丁时会主动抛出`"TinkerRuntimeException:checkDexInstall failed"`；
5. tinker的一般模式并不支持加固，需要使用usePreGeneratedPatchDex模式，即提前生成补丁模式。

## Tinker 比较
|                  | Tinker     | QZone   | AndFix    | Dexposed   | 
| ---------------- | -------------- | ----------| ----------  |  ---------- |
| 类替换            | yes            | yes       | no          |  no         |
| So替换            | yes            | no        | no          | no          |
| 资源替换           | yes            | yes       | no          | no          |
| 全平台支持         | yes            | yes       | yes         | no         |
| 即时生效           | no             | no        | yes         | yes        |
| 性能损耗           | 较小              | 较大       | 较小         |  较小    |
| 补丁包大小          | 较小              | 较大         | 一般         |  一般    |
| 开发透明           | yes            | yes       | no         | no         |
| 复杂度             | 较低              | 较低       | 复杂          | 复杂     |
| gradle支持         | yes            | no        | no         | no         |
| 接口文档           | 丰富              | 较少        | 一般         | 较少      |
| Rom体积           | Dalvik较大             | 较小        | 较小         | 较小      |
| 成功率           | 较高             | 最高        | 一般         | 一般      |

--- 

## 使用到的项目

 - [Jenkins - 持续集成](http://www.jincanshen.com/2016/08/22/%E5%8F%AF%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90/)

 - [SkyHot - DEMO项目](https://github.com/skyJinc/SkyHot)

 - [Tinker - 热修复开源项目](https://github.com/Tencent/tinker)


## 接入Tinker

### 添加Tinker插件依赖

[build.gradle](https://github.com/skyJinc/SkyHot/blob/master/build.gradle)

```gradle
buildscript {
    dependencies {
        classpath ('com.tencent.tinker:tinker-patch-gradle-plugin:1.7.0')
    }
}
```
### 添加Tinker库依赖

[app/build.gradle](https://github.com/skyJinc/SkyHot/blob/master/app/build.gradle)

```gradle
dependencies {
    // tinker 依赖
    compile('com.tencent.tinker:tinker-android-anno:1.7.0')
    compile('com.tencent.tinker:tinker-android-lib:1.7.0')
}
```

### 创建 ApplicationLike

 - 创建 SkyHotApplicationLike 继承 DefaultApplicationLike

```class
public class SkyHotApplicationLike extends DefaultApplicationLike {
    
    public SkyHotApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent, Resources[] resources, ClassLoader[] classLoader, AssetManager[] assetManager) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent, resources, classLoader, assetManager);
    }
    
}
```

 - 创建后 tinker 有一定的注解规则 `@DefaultLifeCycle`

```class
@DefaultLifeCycle(application = "sky.skyhot.AppApplication", flags = ShareConstants.TINKER_ENABLE_ALL, loadVerifyFlag = false)
public class SkyHotApplicationLike extends DefaultApplicationLike {

    public SkyHotApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent, Resources[] resources, ClassLoader[] classLoader, AssetManager[] assetManager) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent, resources, classLoader, assetManager);
    }

}
```
`application = "sky.skyhot.AppApplication"`: 会自动创建AppApplication

`flags = ShareConstants.TINKER_ENABLE_ALL`: tinker支持的类型，dex,library，还是全部都支持！

`loadVerifyFlag = false`: 运行加载时是否校验dex与,ib与res的Md5

 - 配置 [AndroidManifest.xml](https://github.com/skyJinc/SkyHot/blob/master/app/src/main/AndroidManifest.xml)

编译后会生成 `sky.skyhot.AppApplication`
![tu](https://skyJinc.github.io/images/hotfix/2.png)

### 创建 [TinkerHot.gradle](https://github.com/skyJinc/SkyHot/blob/master/app/tinker_hot.gradle)

 - 这里是为了配合`Jenkins` 返回路径这样写的

```class
//补丁APK路径
def getPatchApk() {
    return "${rootDir}/path.apk"
}

//补丁Mapping路径
def getPatchMapping() {
    return "${rootDir}/path_mapping.txt"
}

//布丁R文件路径
def getPatchR() {
    return "${rootDir}/path_R.txt"
}
....
```

 - 如果自行测试 修改成 `${buildDir}/path` 找到build

```class
//补丁APK路径
def getPatchApk() {
    return "${buildDir}/app/app-release-1.0.0.apk"
}

//补丁Mapping路径
def getPatchMapping() {
    return "${buildDir}/app/app-release-1.0.0-mapping.txt"
}

//布丁R文件路径
def getPatchR() {
    return "${buildDir}/app/app-release-1.0.0-R.txt"
}
....
```

 - 对Apk,Mapping,R文件 进行改名后 放到 `${buildDir}/app/` 目录下


```gradle
//apk
from "${buildDir}/outputs/apk/${project.getName()}-${taskName}.apk"
into "${buildDir}/app/"
rename { String fileName ->
	fileName.replace("${project.getName()}-${taskName}.apk", "${project.getName()}-${taskName}-${versionName}.apk")
}
//mapping
from "${buildDir}/outputs/mapping/${taskName}/mapping.txt"
into "${buildDir}/app/"
rename { String fileName ->
	fileName.replace("mapping.txt", "${project.getName()}-${taskName}-${versionName}-mapping.txt")
}
//R文件
from "${buildDir}/intermediates/symbols/${taskName}/R.txt"
into "${buildDir}/app/"
rename { String fileName ->
	fileName.replace("R.txt", "${project.getName()}-${taskName}-${versionName}-R.txt")
}
```

 - 其他内容参考 [Tinker 接入指南](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)


###  配置 [app/build.gradle](https://github.com/skyJinc/SkyHot/blob/master/app/build.gradle)

 - 添加 TinkerHot.gradle 

```gradle
apply from: 'tinker_hot.gradle'

```

 - 添加 multiDex 配置

DEX混淆文件 [proguard/keep_in_main_dex.pro](https://github.com/skyJinc/SkyHot/blob/master/app/proguard/keep_in_main_dex.pro)


```gradle
//打开
dexOptions {
	jumboMode = true
}
```

```gradle
defaultConfig {

	multiDexEnabled true
	//混淆文件
	multiDexKeepProguard file("proguard/keep_in_main_dex.pro")
}
```

```gradle
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile "com.android.support:multidex:1.0.1"
}
```

```class
SkyHotApplicationLike 里配置

@TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH) 
@Override public void onBaseContextAttached(Context base) {
	super.onBaseContextAttached(base);
	// you must install multiDex whatever tinker is installed!
	MultiDex.install(base);
}
```

### 初始化 Tinker 

 -  设置代理类

```class
@DefaultLifeCycle(application = "sky.skyhot.AppApplication", flags = ShareConstants.TINKER_ENABLE_ALL, loadVerifyFlag = false)
public class SkyHotApplicationLike extends DefaultApplicationLike {

    public SkyHotApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent, Resources[] resources, ClassLoader[] classLoader, AssetManager[] assetManager) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent, resources, classLoader, assetManager);
    }
    //代理类
    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH) public void registerActivityLifecycleCallbacks(Application.ActivityLifecycleCallbacks callback) {
        getApplication().registerActivityLifecycleCallbacks(callback);
    }
}
```

 - 初始化,这里我采用了自定义初始化，

```class
@DefaultLifeCycle(application = "sky.skyhot.AppApplication", flags = ShareConstants.TINKER_ENABLE_ALL, loadVerifyFlag = false)
public class SkyHotApplicationLike extends DefaultApplicationLike {

    public SkyHotApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent, Resources[] resources, ClassLoader[] classLoader, AssetManager[] assetManager) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent, resources, classLoader, assetManager);
    }

    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH) @Override public void onBaseContextAttached(Context base) {
        super.onBaseContextAttached(base);

        //RepairPatch类是用来修复当前补丁包的处理类, 无需修改
        AbstractPatch upgradePatchProcessor = new UpgradePatch();//默认方法
        //UpgradePatch类是用来升级当前补丁包的处理类，无需修改
        AbstractPatch repairPatchProcessor = new RepairPatch();//默认方法

        TinkerInstaller.install(this,new DefaultLoadReporter(getApplication()),new DefaultPatchReporter(getApplication()),new DefaultPatchListener(getApplication()), SampleResultService.class,upgradePatchProcessor,repairPatchProcessor);
    }
}
```
 - 说明 `TinkerInstaller.install` 参数说明

1. [LoadReporter类](https://github.com/Tencent/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/reporter/LoadReporter.java)定义了Tinker在加载补丁时的一些回调，我们为你提供了默认实现[DefaultLoadReporter.java](http://git.code.oa.com/wechat-android-dev/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/reporter/DefaultLoadReporter.java)
2. [PatchReporter类](https://github.com/Tencent/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/reporter/PatchReporter.java)定义了Tinker在修复或者升级补丁时的一些回调，我们为你提供了默认实现[DefaultPatchReporter.java](http://git.code.oa.com/wechat-android-dev/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/reporter/DefaultPatchReporter.java)
3. [PatchListener类](https://github.com/Tencent/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/listener/PatchListener.java)是用来过滤Tinker收到的补丁包的修复、升级请求，也就是决定我们是不是真的要唤起:patch进程去尝试补丁合成。我们为你提供了默认实现[DefaultPatchListener.java](https://github.com/Tencent/tinker/blob/master/tinker-android/tinker-android-lib/src/main/java/com/tencent/tinker/lib/listener/DefaultPatchListener.java)

## 打包

 - 生成新包

`Terminal->gradle clean assembleRelease`

![tu](https://skyJinc.github.io/images/hotfix/3.png)

 - 打补丁包

记得修改 `tinker_hot.gradle` 路径看看对不对

`Terminal->gradle tinkerPatchRelease`

![tu](https://skyJinc.github.io/images/hotfix/4.png)

其中 `patch_signed_7zip.apk` 是布丁 其他的看官方介绍吧

## 测试

加载布丁: `TinkerInstaller.onReceiveUpgradePatch(centext, path);`

```class
项目里添加测试代码
//小米4 路径是 /storage/emulated/0/patch_signed_7zip.apk
File file = new File(Environment.getExternalStorageDirectory().getAbsolutePath() + "/patch_signed_7zip.apk");
if (file.exists()) {
	SKYHelper.toast().show("修复BUG");
	TinkerInstaller.onReceiveUpgradePatch(SKYHelper.getInstance(), file.getAbsolutePath());
} else {
	SKYHelper.toast().show("没有文件");
}
```

 - 原始包

![tu](https://skyJinc.github.io/images/hotfix/1.gif)

 - 往手机里导入补丁包

```adb
adb push /Users/sky/Downloads/patch_signed_7zip.apk /storage/emulated/0/
```
![tu](https://skyJinc.github.io/images/hotfix/2.gif)
## 错误码
| 错误码              | 数值       | 描述       | 
| ----------------- | ---------  | ---------  | 
| ERROR_PATCH_DISABLE    |-1|当前tinkerFlag为不可用状态。     | 
| ERROR_PATCH_NOTEXIST| -2|输入的临时补丁包文件不存在。  | 
| ERROR_PATCH_RUNNING |-3    | 当前:patch补丁合成进程正在运行。|
| ERROR_PATCH_INSERVICE | -4|不能在:patch补丁合成进程，发起补丁的合成请求。|
| 其他     | |在SamplePatchListener里面，我们还检查了当前Rom剩余空间，最大内存，是否是GooglePlay渠道等条件。| 
## Jenkins配置
### 参数化构建 - 配置命令

![tu](https://skyJinc.github.io/images/hotfix/8.png)

### 参数化构建 - 添加文件选择

![tu](https://skyJinc.github.io/images/hotfix/6.png)

 - 名字要一致

![tu](https://skyJinc.github.io/images/hotfix/7.png)

### 构建后输出 
![tu](https://skyJinc.github.io/images/hotfix/9.png)

```path
app/build/app/**,app/build/outputs/tinkerPatch/debug/patch_signed_7zip.apk,app/build/outputs/tinkerPatch/release/patch_signed_7zip.apk
```
### 测试

 - 打原始包

![tu](https://skyJinc.github.io/images/hotfix/10.png)
![tu](https://skyJinc.github.io/images/hotfix/11.png)

 - 打补丁包

![tu](https://skyJinc.github.io/images/hotfix/12.png)
![tu](https://skyJinc.github.io/images/hotfix/13.png)

## 结尾

最近才有时间继续写文章, 以后更新尽量快一点～～～

热修复，一直在关注，只是总有问题, 直到微信开源了tinker后，我才写了这篇文章～～

如果大家对其他热修复开源项目感兴趣,提供一个QQ群: 561394234

看图片里的名字大家应该不陌生～

![tu](https://skyJinc.github.io/images/hotfix/1.png)


