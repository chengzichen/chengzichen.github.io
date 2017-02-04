作者:邓浩宸
# **Android的热修复**

# 前言:
	
随着时代的发展,由于公司的项目需要去求变化平凡计划总赶不上变化,`react-native`的高灵活性,开发周期短,更新速度快`react-native`以及一些混合开发越来越被看好,然而主要原因之一:这种混合开发的方式容错率大,更新和修复BUG快.不用发布版本就可以让用户不觉的情况下就更新对应的内容或者BUG,我们不能否认混合开发的快捷,正在此前提下热修复和热更新技术也得到了非常大的发展,不管热修复还是热更新,都是对app的内容或者逻辑变化做出像web页面更新一样的体验.而本文只对热修复进行探索,不对`react-native`进行深入研究.而今天的主人公的话是[`微信Tinker.`](https://github.com/Tencent/tinker/wiki)

不久前微信开源了Tinker,github的star数量直飚5000+,我的天,还在等什么,学习学习.


## 什么是热修复
----------
热修复补丁（[hotfix](http://baike.baidu.com/subview/351151/351151.htm)），又称为patch，指能够修复软件漏洞的一些代码，是一种快速、低成本修复产品软件版本缺陷的方式。
前言中描述的"不用发布版本就可以让用户不觉的情况下就更新对应的内容或者BUG"可能不算准确,所以我自行百度了一下.
热修复说白了就是”打补丁”，比如你们公司上线一个app，用户反应有重大bug,需要紧急修复。如果按照通 
常做法,那就是程序猿加班搞定bug,然后测试,重新打包并发布。这样带来的问题就是成本高,效率低。于是,热 
修复就应运而生.一般通过事先设定的接口从网上下载无Bug的代码来替换有Bug的代码。这样就省事多了,用 
户体验也好.
### 原理

类似与插件开发,关于插件开发原理,看这篇 [Android插件原理剖析](http://www.alloyteam.com/2014/04/android-cha-jian-yuan-li-pou-xi/) ,其中介绍了一下java中的类加载器和android中的类加载器. 热修复就是利用android中的 DexClassLoader 类加载器,动态加载补丁dex,替换有bug的类

已有的热修复解决方案:

- [https://github.com/dodola/HotFix](https://github.com/dodola/HotFix)
- [https://github.com/jasonross/Nuwa](https://github.com/jasonross/Nuwa)
- [https://github.com/bunnyblue/DroidFix](https://github.com/bunnyblue/DroidFix)


## 微信Tinker

Tinker的github地址:[https://github.com/Tencent/tinke](https://github.com/Tencent/tinker)

Tinker原理:[微信Android热补丁实践演进之路](https://github.com/WeMobileDev/article/blob/master/%E5%BE%AE%E4%BF%A1Android%E7%83%AD%E8%A1%A5%E4%B8%81%E5%AE%9E%E8%B7%B5%E6%BC%94%E8%BF%9B%E4%B9%8B%E8%B7%AF.md)



官方给出的定义:
`Tinker is a hot-fix solution library for Android, it supports dex, library and resources update without reinstalling apk.`
Tinker是微信官方的Android热补丁解决方案，它支持动态下发代码、So库以及资源，让应用能够在不需要重新安装的情况下实现更新。当然，你也可以使用Tinker来更新你的插件。

这里原理以及好处.在这里就BB了,我们开发人员只需要关心怎么使用,实现就可以了.不过这里还是贴出来给大家学习..那么接下来直接实践作为一个资深的开发人员学习一个新的技术,第一想到就是去官网看看文档跑跑Demo,当然我也不例外(资深).

# 导入Sample工程 #
- [tinkerd地址](https://github.com/Tencent/tinker),下载下来解压打开导入Android Studio,我们只需要把`tinker-sample-android`这个目录导入即可.
- 导入之后,构建一下,想都不用想肯定出错,提示`“tinkerId is not set!!!”`,WTF????然后我们肯定会去看他的[接入指南](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97),前面一大堆BBBB...
看到了`Sample的使用方法`内心激动起来以为可以知道了什么原因了,再次WTF???没有直接就是运行的后的说明,不能忍,于是我又去网上找找,算是找到了解决的办法,但是后面才知道这些问题**微信维护开源人员**被问了烦了,直接列出了[常见问题](https://github.com/Tencent/tinker/wiki/Tinker-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98),我都不知道说什么了.......
问题解决:这是因为没有正确的配置IDE的git路径, 若不是通过clone方式下载tinker，需要本地手动commit一次。这里你也可以使用其他字符作为tinkerId;
![](http://i.imgur.com/hmcfntG.png)

我这里的话直接就把当前的版本号作为id..

**补充：关于获取git提交版本号?**

1git rev-parse --short HEAD
这段代码主要是用来显示最近一次提交到HEAD上的记录编号（类似于“b03b0c4”的字符串，每次提交，字符串都不一样。个人对git命令行了解不多，如果有知道的大神麻烦指教一下）。
所以前面说的，除了环境变量要配置git（可以在命令行输入 git --version ，显示出了版本号，便是配置成功），还要把你的项目与git关联起来，并且保证有一次提交记录，才能获取到该字符串。

具体使用可以看我的另一篇文章：关于git命令“git rev-parse --short HEAD”在android studio中使用与配置的个人探究

个人觉得，加入这段代码，显得更麻烦了，还不如直接写死，或者获取其他的版本号。


# 编译运行原版apk #
- 接下使用assembleDebug命令,再拿到下图中的app-debug-xxxxx.apk装在手机上运行
	
![](http://i.imgur.com/eGqj9FL.png)
![](http://i.imgur.com/HZRP6Y4.png)

或者直接运行(不过要先关闭Instant Run) ->file->setting->Build.E....->Instant Run 第一个去掉就可以运行了

# 配置原版apk路径 #
![](http://i.imgur.com/ngaL9AC.png)
这里的oldapkpath是上图`编译运行原版apk`中得到的apk路径和R.txt路径配置下就ok

	if (buildWithTinker()) {
    apply plugin: 'com.tencent.tinker.patch'

    tinkerPatch {
         * the old apk path, use to diff with the new apk to build
         * add apk from the build/bakApk
         */
        oldApk = "${bakPath}/app-debug-1108-13-43-27.apk"
        ignoreWarning = false
        useSign = true
        buildConfig {
          
            applyMapping = getApplyMappingPath()
        
            applyResourceMapping = getApplyResourceMappingPath()

            tinkerId = getTinkerIdValue()
        }
这里的oldapk也要修改成上面`编译运行原版apk`生成apk的路径

# 修改源码 生成新版apk 补丁 #

- 运行起来之后,打开代码MianActvity,修改代码,打开Log.e(TAG, "i am on onCreate string:" + getResources().getString(R.string.test_resource))的注释

- 再运行下面图中的tinkerPatchDebug,或者在Terminal使用gradlew tinkerPatchDebug ,Terminal->就是
Android studio 一般左下角的那个cmd控制台一样的东西
 ![](http://i.imgur.com/n9YNE7U.png)

这样在app/build/outputs/tinkerPatch/debug/patch_signed_7zip.apk路径下找到这个差异包,也就是我们俗称的补丁.
# 推送补丁
然后把patch_signed_7zip.apk放到手机SD卡中去使用命令
`adb push ./app/build/outputs/tinkerPatch/debug/patch_signed_7zip.apk /storage/sdcard0/`
这里放置的路径与apk中获取补丁位置一致
#运行应用,加载补丁
再次运行apk,点击LoadPatch时调用`TinkerInstaller.onReceiveUpgradePatch(getApplicationContext(), Environment.getExternalStorageDirectory().getAbsolutePath() + "/patch_signed_7zip.apk");` 方法,加载补丁.
查看控制台日志,打印出`i am on onCreate string:I am in the base apk `  就表示成功了


-------------------------------------------

**补充：返回键退出后进入，并没有执行修复**。

（当时以为是我手机的原因，就没太在意），现在有朋友评论说自己也加载成功但没法修复，是不是跟我一样按得返回键退出。

杀进程后再进入 ，应该就可以修复成功了，如果不成功，把补丁包逆向一下，看看自己修复的部分有没有在里面。

--------------------------

# 集成到自己的项目中

### 1. 添加gradle依赖

在项目的build.gradle中，添加tinker-patch-gradle-plugin的依赖

	buildscript {
	    dependencies {
	        classpath ('com.tencent.tinker:tinker-patch-gradle-plugin:1.7.3')
	    }
	}
	然后在app的gradle文件app/build.gradle，我们需要添加tinker的库依赖以及apply tinker的gradle插件.
	
	dependencies {
	    //可选，用于生成application类 
	    provided('com.tencent.tinker:tinker-android-anno:1.7.3')
	    //tinker的核心库
	    compile('com.tencent.tinker:tinker-android-lib:1.7.3') 
	}
	...
	...
	//apply tinker插件
	apply plugin: 'com.tencent.tinker.patch'


### 2. 添加生成补丁方法

	tinkerPatch {
    //有问题的apk的地址,就是要修复BUG的那个apk,这是在电脑上位置
    oldApk = "D://1//app-debug-old.apk"
    ignoreWarning = false
    useSign = true
    buildConfig {
        tinkerId = "1.0"
    }
    packageConfig {
        //写这个为了修复一个bug,详见github issue #22
        configField("TINKER_ID", "1.0")
    }
    dex {
        dexMode = "jar"
        pattern = ["classes*.dex", "assets/secondary-dex-?.jar"]
        loader = ["com.tencent.tinker.loader.*", "com.kairu.rxjava.app.MyApplicationLike"]
    }
    lib {
        pattern = ["lib/armeabi/*.so", "lib/arm64-v8a/*.so", "lib/armeabi-v7a/*.so", "lib/mips/*.so", "lib/mips64/*.so", "lib/x86/*.so", "lib/x86_64/*.so"]
    }
    res {
        pattern = ["res/*", "assets/*", "resources.arsc", "AndroidManifest.xml"]
        largeModSize = 100
    }
    sevenZip {
        zipArtifact = "com.tencent.mm:SevenZip:1.1.10"
    }
}


### 3. 配置Application

程序启动时会加载默认的Application类，这导致我们补丁包是无法对它做修改了。如何规避？在这里我们并没有使用类似InstantRun hook Application的方式，而是通过代码框架的方式来避免，这也是为了尽量少的去反射，提升框架的兼容性。

这里我们要实现的是完全将原来的Application类隔离起来，即其他任何类都不能再引用我们自己的Application。我们需要做的其实是以下几个工作：

将我们自己Application类以及它的继承类的所有代码拷贝到自己的ApplicationLike继承类中，例如SampleApplicationLike。你也可以直接将自己的Application改为继承ApplicationLike;
Application的attachBaseContext方法实现要单独移动到onBaseContextAttached中；
对ApplicationLike中，引用application的地方改成getApplication();
对其他引用Application或者它的静态对象与方法的地方，改成引用ApplicationLike的静态对象与方法；
更详细的事例，大家可以参考下面的一些例子以及[SampleApplicationLike](https://github.com/Tencent/tinker/blob/master/tinker-sample-android/app/src/main/java/tinker/sample/android/app/SampleApplicationLike.java)的做法。

**这是我的例子:**也可以参考[https://github.com/Tencent/tinker/wiki/Tinker-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95](https://github.com/Tencent/tinker/wiki/Tinker-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95)
	

	@DefaultLifeCycle(
	        application = "com.kairu.rxjava.app.MyApplication",//这的Application是以前项目中的MyApplication
	        flags = ShareConstants.TINKER_ENABLE_ALL
	)
	public class MyApplicationLike extends DefaultApplicationLike {
	
	    private static Application mApplication;
	    public static String currentGirl = "http://ww2.sinaimg.cn/large/610dc034jw1f5k1k4azguj20u00u0421.jpg";
	
	    public MyApplicationLike(Application application, int tinkerFlags, boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime, long applicationStartMillisTime, Intent tinkerResultIntent, Resources[] resources, ClassLoader[] classLoader, AssetManager[] assetManager) {
	        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent, resources, classLoader, assetManager);
	    }
	
	    @Override
	    public void onCreate() {
	        super.onCreate();
			//这里把所有的Application换成getApplication() 原因看https://github.com/Tencent/tinker/wiki/Tinker-%E8%87%AA%E5%AE%9A%E4%B9%89%E6%89%A9%E5%B1%95
	        mApplication = getApplication();
	
	        //配置是否显示log
	        LogUtil.isDebug = true;
	
	        //配置时候显示toast
	        ToastUtils.isShow = true;
	
	        //配置程序异常退出处理
	    }
	    @Override
	    public void onBaseContextAttached(Context base) {
	        super.onBaseContextAttached(base);
	        TinkerInstaller.install(this);
			//在初始化的时候调用加载补丁的方法,路径是实际补丁放的位置
	        TinkerInstaller.onReceiveUpgradePatch(this.getApplication(), Environment.getExternalStorageDirectory().getAbsolutePath()+"/patch_signed_7zip.apk");
			
	    }
	    public static Context getContext() {
	        return mApplication;
	    }
	
	
	    public static Application getIntstance() {
	        return mApplication;
	    }
	}

----------------------------
是不是简单暴力就完了?当然配置就搞定了,没有那么复杂.....

**最后我们都配置好了那怎么得到补丁包呢?**

也是一样
# 步骤1:**编译运行原版apk**
把生成的apk放在自己定义的路径下 	
	
	tinkerPatch {
		...
    //有问题的apk的地址,就是要修复BUG的那个apk,这是在电脑上位置
    oldApk = "D://1//app-debug-old.apk"
		...
		}
# 步骤2:修改源码 生成新版apk 补丁
这里修改源码指的是实际项目中修复BUG更改的代码...

后续的步骤都一样就搞定了............
-------------------

# Tinker的局限

	如果出现以下的情况，并且ignoreWarning为false，我们将中断编译。因为这些情况可能会导致编译出来的patch包带来风险：
1. minSdkVersion小于14，但是dexMode的值为"raw";
2. 新编译的安装包出现新增的四大组件(Activity, BroadcastReceiver...)；
3. 定义在dex.loader用于加载补丁的类不在main dex中;
4. 定义在dex.loader用于加载补丁的类出现修改；
5. resources.arsc改变，但没有使用applyResourceMapping编译。
	

	还有就是需要结束当前进程才能进行修复....


	[指南详情](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)


参考文献:

[http://www.tuicool.com/articles/2i67reV](http://www.tuicool.com/articles/2i67reV)   Android 热修复总结
	
[https://github.com/Tencent/tinker](https://github.com/Tencent/tinker) tinker项目

[https://github.com/Tencent/tinker/wiki](https://github.com/Tencent/tinker/wiki)  tinker wiki

[https://github.com/Tencent/tinker/wiki/Tinker-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98](https://github.com/Tencent/tinker/wiki/Tinker-%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)  常见问题