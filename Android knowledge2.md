### 零、其他

Gradle: https://developer.android.google.cn/studio/build/dependencies.html#dependency_configurations

0.As下gradle中的aar

cd .gradle

modules-2/files-2.1/

1.mac设置adb环境

https://www.jianshu.com/p/f38ec3f4cd4f

但是每次进来都需要重新配置，然后：

open .zshrc

source ~/.bash_profile

source .zshrc

2.多来A梦(DoraemonKit)

https://github.com/didi/DoraemonKit

3.依赖

debugCompile（debugImplementation）
debugCompile 只在debug模式的编译和最终的debug apk打包时有效。

releaseCompile（releaseImplementation）
releaseCompile 仅仅针对Release模式的编译和最终的Release apk打包。

```
在Windows上：
gradlew cleanBuildCache

在Mac或Linux上：
./gradlew cleanBuildCache
```

4.android:exported属性的坑

这个属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。如果设置为true，则能够被调用或交互，否则不能。设置为false时，只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。

5.区块链技术

a.防止篡改：每隔10分钟对账本进行hash运算，形成一个区块，只需验证最后一个hash值

b.账户所有权问题：私钥去签名/验证

   1.签名过程：对交易信息hash,然后sign(hash值,私钥),再相邻结点广播

   2.验证过程：verify(签名信息,付款地址)-->交易摘要

6.mac 如何使用公司和个人git账号

https://www.jianshu.com/p/698f82e72415

a.配置公司git环境

b.配置个人git环境

```
1.将ip配置到host环境
sudo vi /etc/hosts
i    //进行编辑
//复制ip地址
:qw!  //退出

2.Permission denied (publickey) 没有权限的publickey
//输入自定义的rsa名字到自己的邮箱上去
ssh-keygent -t rsa -f ~/.ssh/id_rsa_personal -C "760539154@qq.com"//id_rsa_personal与公司区分（id_rsa）

3.给他们分别添加到ssh-agent信任列表
ssh-add ~/.ssh/id_rsa   //公司
ssh-add ~/.ssh/id_rsa_personal   //个人

4.复制公钥，然后粘贴到git网站的公钥中去
pbcopy < ~/.ssh/id_rsa.pub  //公司
pbcopy < ~/.ssh/id_rsa_personal.pub  //个人

```

7.gradle

https://www.jianshu.com/p/584684c08948

\# 编译并安装debug包

 ./gradlew app:installBinanceDebug    //./gradlew installDebug

\# 编译并打印日志

./gradlew build --info

8.Charles报错

针对API Level 24及更高版本的应用程序不再信任用户或管理员添加的CA用于安全连接。意思就是就算你在手机上安装了受信任的证书也是没卵用的

| Failure | SSL handshake with client failed: An unknown issue occurred processing the certificate (certificate_unknown) |
| ------- | ------------------------------------------------------------ |
| Notes   | You may need to configure your browser or application to trust the Charles Root Certificate. See SSL Proxying in the Help menu. |

```
menifest文件配置如下：

<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <debug-overrides>
        <trust-anchors>
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </debug-overrides>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

9.无法打开app，因为Apple无法检查其是否包含恶意软件

```undefined
sudo spctl --master-disable

按回车键，随后提醒你输入电脑密码，这个时候输入密码然后按回车键即可解决。
```

10.chrome://inspect

查看webview 请求的url

11.dos命令

ciw修改word,

i:修改

vim  

### 一、View

#### 1.转场动画

a.自定义ColorTransition

```

public class ColorTransition extends Transition {

    private static final String COLOR_BACKGROUND = "ColorTransition:change_color:background";
    private int mStartColor;
    private int mEndColor;

    public ColorTransition(int mStartColor, int mEndColor) {
        this.mStartColor = mStartColor;
        this.mEndColor = mEndColor;
    }

    @Override
    public void captureStartValues(TransitionValues transitionValues) {
        transitionValues.values.put(COLOR_BACKGROUND,mStartColor);
    }

    @Override
    public void captureEndValues(TransitionValues transitionValues) {
        transitionValues.values.put(COLOR_BACKGROUND,mEndColor);
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    @Override
    public Animator createAnimator(ViewGroup sceneRoot, TransitionValues startValues, TransitionValues endValues) {
        if (null == startValues || null == endValues) {
            return null;
        }

        final View view = endValues.view;

        int startColor = (int) startValues.values.get(COLOR_BACKGROUND);
        int endColor = (int) endValues.values.get(COLOR_BACKGROUND);

        if (startColor != endColor) {
            ValueAnimator animator = ValueAnimator.ofArgb(startColor, endColor);
            animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                @Override
                public void onAnimationUpdate(ValueAnimator animation) {
                    Object value = animation.getAnimatedValue();
                    if (null != value) {
                        view.setBackgroundColor((Integer) value);
                    }
                }
            });
            return animator;
        }
        return null;
    }
}

```

b.自定义textSizeTransition

```
class ChangeTextTransition1 constructor(textView: TextView) : Transition() {

    val mTextView = textView

    companion object {
        val PROPNAME_TEXTSIZE = "ChangeTextTransition1::textSize"
        val PROPNAME_TEXTCOLOR = "ChangeTextTransition1::textColor"
    }

    init {
        addTarget(TextView::class.java)
        TransitionHelper.isTrans = true
    }

    override fun captureStartValues(transitionValues: TransitionValues) {
        if(TransitionHelper.isTrans){
            transitionValues.values[PROPNAME_TEXTSIZE] = mTextView.textSize/2
        }else{
            transitionValues.values[PROPNAME_TEXTSIZE] = mTextView.textSize
        }
    }

    override fun captureEndValues(transitionValues: TransitionValues) {
        if(TransitionHelper.isTrans){
            transitionValues.values[PROPNAME_TEXTSIZE] = mTextView.textSize
        }else{
            transitionValues.values[PROPNAME_TEXTSIZE] = mTextView.textSize/2
        }
    }

    override fun createAnimator(
        sceneRoot: ViewGroup?,
        startValues: TransitionValues?,
        endValues: TransitionValues?
    ): Animator {
        TransitionHelper.isTrans = false
        val textView = endValues?.view as TextView
        textView.pivotX = 0f
        textView.pivotY = 0f
        val animator: ObjectAnimator = ObjectAnimator.ofFloat(
            endValues.view as TextView, TextSizeProperty(),
            startValues?.values?.get(PROPNAME_TEXTSIZE) as Float,
            endValues.values[PROPNAME_TEXTSIZE] as Float
        )
        return animator
    }


    class TextSizeProperty : Property<TextView, Float>(Float::class.java, "textsize") {

        override fun get(view: TextView): Float {
            return view.textSize
        }

        override fun set(view: TextView, value: Float) {
         view.setTextSize(TypedValue.COMPLEX_UNIT_PX,value)     
        }

    }

}
```

2.dailog.show 出现crash

```
diaog的show()方向在onSaveInstance()之后调用，solve:
override fun show(manager: FragmentManager, tag: String?) {
        println("this a dialog showing")

        val ft = manager.beginTransaction()
        ft.add(this,tag).addToBackStack(null)
        ft.commitAllowingStateLoss()
    }
添加了addToBackStack(null)后，每次替换新的fragment,旧的fragment都被添加到回退栈中，当按返回键时，将之前的fragment恢复。
```

#### 2.scrollview

fillVeewport=“true” //屏幕适配,底部button可以使用marginbottom属性

#### 3.ConstraintLayout 

按比例

```
<TextView
        android:id="@+id/view0"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:background="@color/colorPrimary"
        android:gravity="center"
        android:text="这是一个Banner"
        android:textColor="@color/colorWhite"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHeight_percent="0.3"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0" />
```

### 二、Activity

#### 1.Lifecycle

**Lifecycle**是一个生命周期感知组件，一般用来响应Activity、Fragment等组件的生命周期变化，并将变化通知到已注册的观察者。有助于更好地组织代码，让代码逻辑符合生命周期规范，减少内存泄漏，增强稳定性。

```
dependencies {
    ......
    implementation "android.arch.lifecycle:runtime:1.1.1"
    implementation "android.arch.lifecycle:extensions:1.1.1"
    // 如果你使用java8开发，可以添加这个依赖，里面只有一个类
    implementation "android.arch.lifecycle:common-java8:1.1.1"
}
```

```
public class Java8Observer implements DefaultLifecycleObserver {
    private static final String TAG = Java8Observer.class.getSimpleName();

    @Override
    public void onCreate(@NonNull LifecycleOwner owner) { Log.d(TAG, "onCreate"); }

    @Override
    public void onStart(@NonNull LifecycleOwner owner) { Log.d(TAG, "onStart"); }

    @Override
    public void onResume(@NonNull LifecycleOwner owner) { Log.d(TAG, "onResume"); }

    @Override
    public void onPause(@NonNull LifecycleOwner owner) { Log.d(TAG, "onPause"); }

    @Override
    public void onStop(@NonNull LifecycleOwner owner) { Log.d(TAG, "onStop"); }

    @Override
    public void onDestroy(@NonNull LifecycleOwner owner) { Log.d(TAG, "onDestroy"); }
}
```

```
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // 直接调用getLifecycle()，添加Observer
        getLifecycle().addObserver(new Java7Observer());
        getLifecycle().addObserver(new Java8Observer());
    }
}
```

AppCompatActivity中添加了一个ReportFragment，其生命周期变化时，调用**LifecycleRegistry.handleLifecycleEvent()**方法通知LifecycleRegistry改变状态，LifecycleRegistry内部调用**moveToState()**改变状态，并调用每个**LifecycleObserver.onStateChange()**方法通知生命周期变化。

LifecycleDispatcher中：

```
/**
     * 给所有子Fragment设置State
     */
    private static void markState(FragmentManager manager, State state) {
        Collection<Fragment> fragments = manager.getFragments();
        if (fragments == null) {
            return;
        }
        for (Fragment fragment : fragments) {
            if (fragment == null) {
                continue;
            }
            markStateIn(fragment, state);
            if (fragment.isAdded()) {
                markState(fragment.getChildFragmentManager(), state);
            }
        }
    }

    private static void markStateIn(Object object, State state) {
        if (object instanceof LifecycleRegistryOwner) {
            LifecycleRegistry registry = ((LifecycleRegistryOwner) object).getLifecycle();
            registry.markState(state);
        }
    }
```

问：State是如何与Activity/Fragment的生命周期绑定的？

在Activity中添加一个**ReportFragment**(如果你的Activity继承AppCompatActivity，会在父类的onCreate()中添加**ReportFragment**，否则由**LifecycleDispatcher**添加)，在**ReportFragment**生命周期函数中调用**LifecycleRegistry.handleLifecycleEvent**()方法改变**State**。

问：Event事件是如何分发到LifecycleObserver的？

**LifecycleRegistry**在收到**handleLifecycleEvent**()后，内部调用**moveToState**()方法，改变State值，每一次State值改变，都会调用**LifecycleObserver.onStateChanged**()方法将Event分发到**LifecycleObserver**

#### 2.MVVM

1.提供View,ViewModel以及Model三层

2.将布局改为DataBinding布局--->DataBindingUtil.setContent(layout)

3.View与ViewModel之间通过DataBinding进行通信

4.获取数据并展示在界面上

#### 3.监听home键

```
onUserLeaveHint() //按home键此方法会触发
```



### 三、Broadcast



### 四、Service



### 五、Other

#### 1.日志上报

(1)Logan解决的问题：

- 卡顿，影响性能

  对Android来说，对日志加密压缩等操作全部在Java堆里面。由于日志写入是一个高频的动作，频繁地堆内存操作，容易引发Java的GC，导致应用卡顿；

  集中压缩会导致CPU短时间飙高，出现峰值；

  由于日志是内存缓存，在杀进程、Crash的时候，容易丢失内存数据，从而导致日志丢失

- 日志丢失

  MMAP是一种内存映射文件的方法

- 安全性

- 日志分散

(2)特点

Logan自研的日志协议解决了日志本地聚合存储的问题，采用“先压缩再加密”的顺序，使用流式的加密和压缩，避免了CPU峰值，同时减少了CPU使用。跨平台C库提供了日志协议数据的格式化处理，针对大日志的分片处理，引入了MMAP机制解决了日志丢失问题，使用AES进行日志加密确保日志安全性。Logan核心逻辑都在C层完成，提供了跨平台支持的能力，在解决痛点问题的同时，也大大提升了性能。

日志文件只保留最近7天的日志，过期会自动删除。在Android设备上Logan将日志保存在沙盒中，保证了日志文件的安全性。

参考文档：https://github.com/Meituan-Dianping/Logan/blob/master/README-zh.md

2.存储路径

一般我们可以通过 Context 和 Environment 相关的方法获取文件存取的路径。

(1)内部存储

1.1 根目录($rootDir)：/data，通过 Environment.getDataDirectory() 获取。
1.2 应用程序目录($applicationDir)：$rootDir/data/包名（不一定，不同设备可能不同）
应用缓存目录：$applicationDir/cache，通过Context.getCacheDir()获取。
应用文件目录：$applicationDir/files，通过Context.getFilesDir()获取。Context.getFileStreamPath(String name)返回以name为文件名的子文件目录，若name为空，则返回文件根目录。假设我们的应用程序包名为com.learn.test，路径如下：

```
Environment.getDataDirectory():      /data
Context.getCacheDir():               /data/data/com.learn.test/cache
Context.getFilesDir():               /data/data/com.learn.test/files
Context.getFileStreamPath(""):       /data/data/com.learn.test/files
Context.getFileStreamPath("test"):   /data/data/com.learn.test/files/test
```

(2)外部存储

2.1 根目录($rootDir)：/storage/emulated/0（不一定，不同设备可能不同），通过 Environment.getExternalStorageDirectory() 获取。
2.2 应用程序目录($applicationDir)：$rootDir/Andorid/data/包名
应用缓存目录：$applicationDir/cache，通过Context.getExternalCacheDir()获取。
应用文件目录：$applicationDir/files，通过Context.getExternalFilesDir(String type)，type为空字符串时获取。type系统给我们提供了很多常用的类型，比如图片和下载等等:

```
public static String DIRECTORY_MUSIC = "Music";
public static String DIRECTORY_ALARMS = "Alarms";
public static String DIRECTORY_NOTIFICATIONS = "Notifications";
public static String DIRECTORY_PICTURES = "Pictures";
public static String DIRECTORY_MOVIES = "Movies";
public static String DIRECTORY_DOWNLOADS = "Download";
public static String DIRECTORY_DCIM = "DCIM";
public static String DIRECTORY_DOCUMENTS = "Documents";
```

假设我们的应用程序包名为com.learn.test，路径如下：

```
Environment.getExternalStorageDirectory():               /storage/emulated/0
Context.getExternalCacheDir():                           /storage/emulated/0/Android/data/com.learn.test/cache
Context.getExternalFilesDir(""):                         /storage/emulated/0/Android/data/com.learn.test/files
Context.getExternalFilesDir("test"):                     /storage/emulated/0/Android/data/com.learn.test/files/test
Context.getExternalFilesDir(Environment.DIRECTORY_PICTURES):    /storage/emulated/0/Android/data/com.learn.test/files/Pictures
```

#### 2.Fresco

假如有这样的一个需求：当图片正在加载时应该呈现正在加载时的图像，当图片加载失败时应该呈现图片加载时的图像，当重新加载图片时，应该呈现重试时图像，直到这张图片加载完成。这时建议推荐用Fresco

```
<com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv3"
        android:layout_marginTop="20dp"
        android:layout_below="@+id/main_sdv2"
        android:layout_width="100dp"
        android:layout_height="100dp"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/default_error"
        fresco:placeholderImageScaleType="focusCrop"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="focusCrop"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="focusCrop"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="focusCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:roundAsCircle="true"
        fresco:roundedCornerRadius="30dp"
        fresco:roundTopLeft="true"
        fresco:roundTopRight="true"
        fresco:roundBottomLeft="true"
        fresco:roundBottomRight="true"
        fresco:roundingBorderWidth="10dp"
        fresco:roundingBorderColor="#008dd7"
        ></com.facebook.drawee.view.SimpleDraweeView>
```

```
private void initView() {
        //创建SimpleDraweeView对象
        simpleDraweeView = (SimpleDraweeView) findViewById(R.id.main_sdv);
        //创建将要下载的图片的URI
        Uri imageUri = Uri.parse("http://avatar.csdn.net/5/D/9/1_vatty748895431.jpg");
        //开始下载
        simpleDraweeView.setImageURI(imageUri);
        //创建DraweeController
        DraweeController controller = Fresco.newDraweeControllerBuilder()
                //重试之后要加载的图片URI地址
                .setUri(imageUri)
                        //设置点击重试是否开启
                .setTapToRetryEnabled(true)
                        //设置旧的Controller
                .setOldController(simpleDraweeView.getController())
                        //构建
                .build();
        //设置DraweeController
        simpleDraweeView.setController(controller);
    }
```

3.正则表达式

```
 //“\\D”去除[0,9]之外的数字 “\\d”数字[0,9]
 //必须这样写
 Sting entry = "123as12"
 entry = entry.replaceAll("\\D", "");
 
 12312
```

#### 3.okhttp

1.优点

支持http2，对一台机器的所有请求共享同一个socket,内置连接池，

支持连接复用，减少延迟。

通过缓存避免重复请求，请求失败时自动重试主机的其他ip，自动重定向，

okhttp请求网络切换回来是在线程里面的，不是在主线程，不能直接刷新UI

2.缓存

a .配置缓存

当 app 无法访问网络时，也可以使用缓存的接口数据，避免缺省页,节省流量、提高响应速度、增强用户体验

指定缓存目录和缓存大小,使用OkHttpClient.Builder()的cache()方法来配置缓存对象

```
public class OkHttpManager {
    private OkHttpClient client;

    private OkHttpManager() {
        // 缓存目录
        File file = new File(Environment.getExternalStorageDirectory(), "a_cache");
        // 缓存大小
        int cacheSize = 10 * 1024 * 1024;
        client = new OkHttpClient.Builder()
                .cache(new Cache(file, cacheSize)) // 配置缓存
                .build();
    }

    public static OkHttpManager getInstance() {
        return OkHttpHolder.instance;
    }

    private static class OkHttpHolder {
        private static final OkHttpManager instance = new OkHttpManager();
    }
    ......
}
```

b.拦截器

```
public class NetCacheInterceptor implements Interceptor {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();
        Response originResponse = chain.proceed(request);

        //设置响应的缓存时间为60秒，即设置Cache-Control头，并移除pragma消息头，因为pragma也是控制缓存的一个消息头属性
        originResponse = originResponse.newBuilder()
                .removeHeader("pragma")
                .header("Cache-Control", "max-age=60")
                .build();

        return originResponse;
    }
```

把定义好的拦截器添加到OkHttpClient中:

```
client = new OkHttpClient.Builder()
                .cache(new Cache(file, cacheSize))
                .addNetworkInterceptor(new NetCacheInterceptor())
                .build();
```

c.测试

封装一个asyncGet()方法实现异步get请求,http://publicobject.com/helloworld.txt 是官方实例

```
public class OkHttpManager {
    private OkHttpClient client;
    ......
    public void asyncGet(Callback callback) {
        Request request = new Request.Builder()
                .url("http://publicobject.com/helloworld.txt")
                .build();
        client.newCall(request).enqueue(callback);
    }
}
```

发起请求、以及回调:

```
OkHttpManager.getInstance().asyncGet(new Callback() {
                @Override
                public void onFailure(Call call, IOException e) {
                    Log.e("failure", e.toString());
                }

                @Override
                public void onResponse(Call call, Response response) throws IOException {
                    if (response.isSuccessful()) {
                        if (response.networkResponse() != null) {
                            Log.e("network", response.body().string().length() + "");
                        } else if (response.cacheResponse() != null) {
                            if (Utils.isNetworkAvailable(context)) {
                                Log.e("cache", response.body().string().length() + "");
                            } else {
                                Log.e("cache(no network)", response.body().string().length() + "");
                            }
                        }
                    }
                }
            });
```



#### 4.adb命令

1.adb shell pm list package //所有应用包名

2.adb shell pm path 包名  //找到应用备份包的位置

3.adb pull /data/app/com.game.play.gogogo-1/base.apk //提取安装包

#### 5.kotlin中json转换器插件

JsonToKotlinClass

#### 6.git创建项目 .gitignore配置

```
*.iml
.gradle
/.idea
/local.properties
/.idea/workspace.xml
/.idea/libraries
.DS_Store
.idea/gradle.xml
.idea/modules.xml
/build
/captures
.externalNativeBuild
/livenessLib/build
/IDCardLib/build
/language

/*/build
/*/*/build
/*/*/*/build

/*/*.iml
```

#### 7.ZoomableDraweeView

Zoomable是Fresco Demo工程里自带的图片缩放库,但是它并不在依赖包里.所以你依赖了Fresco也无法找到这个View.所以我们需要手动找到这个包并且导入它.

https://blog.csdn.net/weixin_30642561/article/details/98935399

#### 8.git pull error: cannot lock ref

解决方法：git remote prune origin

### 六、RXJava

#### 1.Disposable

订阅了事件后没有及时取阅，容易遭层内存泄漏

- dispose():主动解除订阅
- isDisposed():查询是否解除订阅 true 代表 已经解除订阅

使用CompositeDisposable：

a、可以快速解除所有添加的Disposable类.
b、每当我们得到一个Disposable时就调用CompositeDisposable.add()将它添加到容器中, 在退出的时候, 调用CompositeDisposable.clear() 即可快速解除.

b、onError和onComplete不可以同时调用的原因：每次掉用过onError或onComplete其中一个方法后，就会掉用dispose()方法，此时订阅取消，自然也就不能掉用另一个方法了

### 七、kotlin

#### 1.Kotlin 的空安全设计

```
class User{
    var name: String? = null //可空类型
    //新的问题
    var view: View = null
    view?.setBackgroundColor(Color.RED)  //?.做一次非空判断后再调用
    
    view!!.setBackgroundColor(Color.RED)  //!!.肯定不会为空
}
```

#### 2.延迟初始化

```
lateinit var view: View
override fun onCreate(...){
   ...
   view = findViewById(R.id.tvContent)
}
```

#### 3.函数的声明

```
fun cook(name: String): Food {
    ...
}
```

```
// 👇可空变量传给不可空参数，报错
var myName : String? = "rengwuxian"
fun cook(name: String) : Food {}
cook(myName)
  
// 👇可空变量传给可空参数，正常运行
var myName : String? = "rengwuxian"
fun cook(name: String?) : Food {}
cook(myName)

// 👇不可空变量传给不可空参数，正常运行
var myName : String = "rengwuxian"
fun cook(name: String) : Food {}
cook(myName)
```

#### 4.属性的 getter/setter 函数

```
class User {
    var name = "Mike"
    fun run() {
        name = "Mary"
        // 👆的写法实际上是👇这么调用的
        // setName("Mary")
        // 建议自己试试，IDE 的代码补全功能会在你打出 setn 的时候直接提示 name 而不是 setName
        
        println(name)
        // 👆的写法实际上是👇这么调用的
        // print(getName())
        // IDE 的代码补全功能会在你打出 getn 的时候直接提示 name 而不是 getName
    }
}
```

如何来操作「钩子」呢？

```
class User {
    var name = "Mike"
     
        get() {
            return field + " nb"
        }
 
        set(value) {
            field = "Cute " + value
        }
}
```

上面的那个 `String name` 就是 Kotlin 帮我们自动创建的一个 Java field。这个 field 对编码的人不可见，但会自动应用于 getter 和 setter，因此它被命名为「Backing Field」（backing 的意思是在背后进行支持，例如你闯了大祸，我动用能量来保住你的人头，我就是在 back you）。

#### 5.基本类型

```
var number: Int = 1 // 👈还有 Double Float Long Short Byte 都类似
var c: Char = 'c'
var b: Boolean = true
var array: IntArray = intArrayOf(1, 2) // 👈类似的还有 FloatArray DoubleArray CharArray 等，intArrayOf 是 Kotlin 的 built-in 函数
var str: String = "string"
```

#### 6.装箱

```
var a: Int = 1 // unbox
var b: Int? = 2 // box
var list: List<Int> = listOf(1, 2) // box
var array: IntArray = intArrayOf(1, 2) // 这种也是 unbox 的

```

Java 里的基本类型，类比到 Kotlin 里面，不装箱的条件：

- 不可空类型。
- 使用 IntArray、FloatArray 等。

#### 7.可见性

a.类的可见性，Java 中的 public 在 Kotlin 中可以省略，Kotlin 的类默认是 public 的

b.override 函数的可见性是继承自父类的

c.Kotlin 里的类默认是 final 的，而 Java 里只有加了 `final` 关键字的类才是 final 的

使用open

```
open class MainActivity : AppCompatActivity() {}
```

这样一来，我们就可以继承了。

```
class NewActivity: MainActivity() {}
```

NewActivity 仍然是 final 的

#### 8.关闭 `override` 的遗传性

```
open class MainActivity : AppCompatActivity() {
    // 👇加了 final 关键字，作用和 Java 里面一样，关闭了 override 的遗传性
    final override fun onCreate(savedInstanceState: Bundle?) {
        ...
    }
}
```

#### 9.`abstract` 关键字

`abstract` 关键字修饰的类无法直接实例化，并且通常来说会和 `abstract` 修饰的函数一起出现，当然，也可以没有这个 `abstract` 函数。

```
abstract class MainActivity : AppCompatActivity() {
    abstract fun test()
}
```

但是子类如果要实例化，还是需要实现这个 abstract 函数的：

```
class NewActivity : MainActivity() {
    override fun test() {}
}
```

#### 10.没有new

```
fun main() {
    var activity: Activity = NewActivity()
}
```

 Java 和 Kotlin 中关于类的声明主要关注以下几个方面：

- 类的可见性和开放性
- 构造方法
- 继承
- override 函数

#### 11.类型的判断和强转

java:

```
void main() {
    Activity activity = new NewActivity();
    if (activity instanceof NewActivity) {
        ((NewActivity) activity).action();
    }
}
```

kotlin:

使用 is :

```
fun main() {
    var activity: Activity = NewActivity()
    if (activity is NewActivity) {
        // 👇的强转由于类型推断被省略了
        activity.action()
    }
}
```

使用 as :

```
fun main() {
    var activity: Activity = NewActivity()
    (activity as NewActivity).action()
}
```

如果强转成一个错误的类型，程序就会抛出一个异常:

```
fun main() {
    var activity: Activity = NewActivity()
    // '(activity as? NewActivity)' 之后是一个可空类型的对象，所以，需要使用 '?.' 来调用
    (activity as? NewActivity)?.action()
}
```

#### 12.子类重写父类的 `override` 函数，能否修改它的可见性？

可以将子类的方法修饰法权限放开，权限只能修改为大于等于当前权限。
比如父类方法是protected权限，则子类可以是protected权限或者是public，当然给子类方法加protected修饰，IDEA会提示你这个可以省略不写，此时虽然不写protected修饰符，但是其实是被继承关系约束的，此时方法的权限是protected，不是public。如果你想修改方法权限为public，必须要显式写出public修饰符。
比如你的父类方法是private权限，这时候方法都对外不可见了，则没有重写这一说
如果你的父类方法不写权限修饰符，则按照kotlin的规则，此时是public权限，则子类方法权限也必须为public，又由于public是默认不写修饰符时候的权限，所以子类也可以不写出public，但是此时子类方法对外访问权限变成了public。

#### 13.以下的写法有什么区别?

```
activity as? NewActivity
activity as NewActivity?
activity as? NewActivity? 
```

其实关键在于理解可空类型和不可空类型。
对于as和as?来说，as是在强转失败时会抛出异常，但是as?不会。
有个细节在于如果activity是null的时候，用as去强转，肯定会抛出异常，但是用as?则不会。
activity as? NewActivity 这种情况，不会抛出异常。虽然你as?后面期望转换为NewActivity类型(不可空类型)，但是要注意的是由于是as?，所以转换的结果是可空的，必须用NewActivity?类型变量去接收。这种情况比较特殊，一般用在Java调用kotlin中的一个方法，虽然你在kotlin中声明接收一个非空参数，但是java可能给你传入个null，你使用as?转换会比较安全。
activity as NewActivity? 这种情况，activity为null时，强转失败，会抛出异常。如果顺利强转，由于你as后面的类型是可空的，所以转换的结果必须用NewActivity?类型变量去接收
activity as? NewActivity? 这种情况activity为null时也不会抛出异常，无论是因为强转失败还是由于as?的类型声明，转换的结果都必须用NewActivity?类型变量去接收

#### 14.注意

a.kotlin函数默认是val类型

b.val修饰的变量不能二次赋值，但是可以通过自定义变量的getter函数，返回动态获取的指

c.object:也可以用来创建匿名类的对象

d.String?不是Any的子类型，它是Any?的子类型；String才是Any的子类型。

#### 15.协变(covariance)

a.数组不支持协变

```
val strs: Array<String> = arrayof("a","b","c")

val anys: Array<Any> = strs //编译会报错
```

```
 val array1 = arrayOfNulls<String>(10)  //一定长度的数组
 val array2 = emptyArray<String>()    //空数组
```



b.list支持协变

```
val strs: List<String> = listOf("a","b","c")

val anys: List<Any> = strs//成功
```

#### 16.集合

a.List

b.Set

c.Map

```
val map = mutableMapOf("key1" to 1, "key2" to 2)
    👇
map.put("key1", 2)
   👇
map["key1"] = 2  
```

#### 17.可变集合/不可变集合

创建函数用的是 `mutableMapOf()` 而不是 `mapOf()`，因为只有 `mutableMapOf()` 创建的 `Map` 才可以修改。Kotlin 中集合分为两种类型：只读的和可变的。这里的只读有两层意思：

- 集合的 size 不可变
- 集合中的元素值不可变



以下是三种集合类型创建不可变和可变实例的例子：

- `listOf()` 创建不可变的 `List`，`mutableListOf()` 创建可变的 `List`。
- `setOf()` 创建不可变的 `Set`，`mutableSetOf()` 创建可变的 `Set`。
- `mapOf()` 创建不可变的 `Map`，`mutableMapOf()` 创建可变的 `Map`

```
val strList = listOf("a", "b", "c")
            👇
strList.toMutableList()
val strSet = setOf("a", "b", "c")
            👇
strSet.toMutableSet()
val map = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 3)s
         👇
map.toMutableMap()

```

#### 18.Sequence

Kotlin 还引入了一个新的容器类型 `Sequence`，它和 `Iterable` 特定的处理

```
 sequenceOf("a","b","c")
 val list = listOf("a")
 list.asSequence()
 //这里的 List 实现了 Iterable 接口
```

#### 19.可见性修饰符

- `public`：公开，可见性最大，哪里都可以引用。
- `private`：私有，可见性最小，根据声明位置不同可分为类中可见和文件中可见。内部类对外部类不可见
- `protected`：保护，相当于 `private` + 子类可见。
- `internal`：内部，仅对 module 内可见。但编译成jar后让可以被访问

Kotlin 允许同一个文件声明多个 `class` 和 top-level 的函数和属性，所以 Kotlin 中允许类和接口声明为 `private`，因为同个文件中的其它成员可以访问：

```
private class Sample {
    val number = 1
    fun method() {
        println("Sample method()")
    }
}
            // 👇 在同一个文件中，所以可以访问
val sample = Sample()

```

#### 20.constructor

```
class User constructor(var name: String) {
                                   // 👇  👇 直接调用主构造器
    constructor(name: String, id: Int) : this(name) {
    }
                                                // 👇 通过上一个次构造器，间接调用主构造器
    constructor(name: String, id: Int, age: Int) : this(name, id) {
    }
}
```

```
class User private constructor(name: String) {
//           👆 主构造器被修饰为私有的，外部就无法调用该构造器
}
```

```
class Constructors {
     //先
    init {
        println("Init block")
    }
    //后
    constructor(i: Int) {
        println("Constructor")
    }
}
```



#### 21.**:**符号

- 变量的声明：`var id: Int`
- 类的继承：`class MainActivity : AppCompatActivity() {}`
- 接口的实现：`class User : Impl {}`
- 匿名类的创建：`object: ViewPager.SimpleOnPageChangeListener() {}`
- 函数的返回值：`fun sum(a: Int, b: Int): Int`

#### 22.参数默认值

```
fun sayHi(name: String = "world") = println("Hi " + name)

sayHi("kaixue.io")
sayHi() // 使用了默认值 "world"
```

```
fun sayHi(name: String = "world", age: Int) {
    ...
}
​
sayHi(10) //报错


fun sayHi(name: String = "world", age: Int) {
    ...
}
      👇   
sayHi(age = 21)
```

```
fun sayHi(name: String = "world", age: Int) {
    ...
}
​
sayHi(name = "wo", 21) // 👈 IDE 会报错，Mixing named and positioned arguments is not allowed
sayHi("wo", age = 21) // 👈 这是正确的写法
```

#### 23.本地函数（嵌套函数）

```
fun login(user: String, password: String, illegalStr: String) {
    // 验证 user 是否为空
    if (user.isEmpty()) {
        throw IllegalArgumentException(illegalStr)
    }
    // 验证 password 是否为空
    if (password.isEmpty()) {
        throw IllegalArgumentException(illegalStr)
    }
}
```

该函数中，检查参数这个部分有些冗余，我们又不想将这段逻辑作为一个单独的函数对外暴露。这时可以使用嵌套函数，在 `login` 函数内部声明一个函数：

```
fun login(user: String, password: String, illegalStr: String) {
           👇 
    fun validate(value: String, illegalStr: String) {
      if (value.isEmpty()) {
          throw IllegalArgumentException(illegalStr)
      }
    }
   👇
    validate(user, illegalStr)
    validate(password, illegalStr)
}
```

稍微改进：

```
fun login(user: String, password: String, illegalStr: String) {
    fun validate(value: String) {
        if (value.isEmpty()) {
                                              👇
            throw IllegalArgumentException(illegalStr)
        }
    }
    ...
}
```

更简单的方式：

```
fun login(user: String, password: String, illegalStr: String) {
    require(user.isNotEmpty()) { illegalStr }
    require(password.isNotEmpty()) { illegalStr }
}
其中用到了 lambda 表达式以及 Kotlin 内置的 require 函数
```

24.raw string (原生字符串)

- `\n` 并不会被转义
- 最后输出的内容与写的内容完全一致，包括实际的换行
- `$` 符号引用变量仍然生效

原生字符串还可以通过 `trimMargin()` 函数去除每行前面的空格：

#### 25.数组与集合的操作符

1.map

遍历每个元素并执行给定表达式，最终形成新的集合

```
🏝️
//  [1, 2, 3]
       ⬇️
//  {2, 3, 4}
​
val newList: List = intArray.map { i ->
    i + 1 // 👈 每个元素加 1
}
```

2.flatmap

遍历每个元素，并为每个元素创建新的集合，最后合并到一个集合中

```
🏝️
//          [1, 2, 3]
               ⬇️
// {"2", "a" , "3", "a", "4", "a"}
​
intArray.flatMap { i ->
    listOf("${i + 1}", "a") // 👈 生成新集合
}
```

#### 26.Range

```
//[]
val range: IntRange = 0..1000
//[)
val range: IntRange = 0 until 1000
for (i in range) {
    print("$i, ")
}
```

#### 27.Sequence

```
val sequence = sequenceOf(1, 2, 3, 4)
val result: List = sequence
    .map { i ->
        println("Map $i")
        i * 2 
    }
    .filter { i ->
        println("Filter $i")
        i % 3  == 0 
    }
👇
println(result.first()) // 👈 只取集合的第一个元素
```

#### 28.范型

Java：

多态性：父类添加子类可以，子类添加父类不行

```
List<? super Button> buttons = new ArrayList<TextView>();
Object object = buttons.get(0); // 👈 get 出来的是 Object 类型
Button button = ...
buttons.add(button); // 👈 add 操作是可以的
```

```
List<? extends TextView> textViews = new ArrayList<Button>();
TextView textView = textViews.get(0); // 👈 get 可以
textViews.add(textView);
//             👆 add 会报错，no suitable method found for add(TextView)
```

Kotlin:

a.in out

```
var textViews: List<out TextView>
var textViews: List<in TextView>
```

b.reified

```
inline fun <reified T> printIfTypeMatch(item: Any) {
    if (item is T) { // 👈 这里就不会在提示错误了
        println(item)
    }
}
```

练习：
1.实现一个 `fill` 函数，传入一个 `Array` 和一个对象，将对象填充到 `Array` 中，要求 `Array` 参数的泛型支持逆变（假设 `Array` size 为 1）。

2.实现一个 `copy` 函数，传入两个 `Array` 参数，将一个 `Array` 中的元素复制到另外个 `Array` 中，要求 `Array` 参数的泛型分别支持协变和逆变。（提示：Kotlin 中的 `for` 循环如果要用索引，需要使用 `Array.indices`）

```
    fun <T> fill(arr:Array<in T>,ele:T){
        arr[0] = ele
        arr.plusElement(ele)
    }

    fun <T> copy(srcArray:Array<out T>,desArray:Array<in T>){

        for (i in srcArray.indices){
//            desArray[i] = srcArray[i]
            desArray.plus(srcArray[i])
        }
    }
```

一个不可变的例子：

```
open class Animal {
    open fun feed() {
        println("Animal is feeding.")
    }
}

class Herd<T : Animal> {
    val size: Int = 10
    fun get(index: Int): Animal? {
        return null
    }
}

fun feedAllAnimal(animals: Herd<Animal>) {
    for (i in 0 until animals.size) {
        animals.get(i)?.feed()
    }
}

class Cat : Animal() {
    override fun feed() {
        println("Cat is feeding.")
    }
}


fun takeCareOfCats(cats: Herd<Cat>) {
    feedAllAnimal(cats)//comile error
}
```

很遗憾，在上面的例子中我们不能把猫群当做动物群被照顾,改：

```
class Herd<out T : Animal> {
    val size: Int = 10
    fun get(index: Int): Animal? {
        return null
    }
}
```

涨知识：

```
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

```
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

#### 29.Class<T>

单独的T 代表一个类型 ，而 Class<T>代表这个类型所对应的类， Class<？>表示类型不确定的类

a.**如何创建一个Class类型的实例？**

 就像使用非泛型代码一样，有两种方式：调用方法 Class.forName() 或者使用类常量X.class。   Class.forName() 被定义为返 回 Class<?>。另一方面，类常量 X.class 被定义为具有类型 Class<X>，所 以 String.class 是Class<String> 类型的。

#### 30.协程

a.协程下上文

**Dispatchers.Main：**使用这个调度器在 Android 主线程上运行一个协程。可以用来更新UI 。**在UI线程中执行**

**Dispatchers.IO：**这个调度器被优化在主线程之外执行磁盘或网络 I/O。**在线程池中执行**

**Dispatchers.Default：**这个调度器经过优化，可以在主线程之外执行 cpu 密集型的工作。例如对列表进行排序和解析 JSON。**在线程池中执行**。

**Dispatchers.Unconfined：**在调用的线程直接执行。

b.开启一个协程的方法

```
   CoroutineScope(Dispatchers.Main).launch {
            Log.d(
                MainActivity.javaClass.simpleName,
                compute().toString() + MainActivity::class.java.simpleName
            )
        }

        GlobalScope.launch(Dispatchers.Main) {

        }

        runBlocking(Dispatchers.IO) {

        }
       
```

```
 private suspend fun compute(): Int{

     return withContext(Dispatchers.IO){
            for (i in 0..10){
                delay(500)
            }
            return@withContext 20
        }

    }
```

#### 31.延迟

lateinit 只用于变量 var，而 lazy 只用于常量 

lateinit 则用于只能生命周期流程中进行获取或者初始化的变量，比如 Android 的 onCreate()

```
val lazyValue: String by lazy {
    println("computed!")
    "Hello"s
}

fun main(args: Array<String>) {
    println(lazyValue)
    println(lazyValue)
}

打印结果
computed！
Hello

Hello
```

应用场景：

```
 private val mUserMannager: UserMannager by lazy {
        UserMannager.getInstance()
    }
```

```
class App : Application() {
    init {
        instance = this
    }

    @Inject lateinit var apiComponent: ApiComponent
    override fun onCreate() {
        super.onCreate()
        DaggerApiComponent.builder().apiModule(ApiModule()).appModule(AppModule(this)).build().inject(this)
    }

    companion object {
        lateinit var instance: App

    }
}
```

#### 32.Data Class

在 Kotlin 中，不需要自己动手去写一个 JavaBean，可以直接使用 Data Class，使用 Data Class 编译器会默默地帮我们生成以下函数:

```
equals()
hashCode()
toString()
componentN()
copy()
```

1.定义一个 data class 类

```
data class Country(var id: Int, var name: String, var continent: String)
```

参考：https://www.jianshu.com/p/90a3233b0a8a?utm_campaign=maleskine&utm_content=note&utm_medium=reader_share&utm_source=weibo

#### 33.库函数

1.with

```\
@kotlin.internal.InlineOnly
public inline fun <T, R> with(receiver: T, block: T.() -> R): R = receiver.block()
```

例子：

```
// java

TextView text = findViewById(R.id.tv_text)
text.("ymc")
text.setTextSize(23)


// kotlin

// with 函数
with(R.id.tv_text) {
    tv_text.text = "ymc"
    tv_text.textSize = 23f
}
```

2.run

```
@kotlin.internal.InlineOnly
public inline fun <T, R> T.run(block: T.() -> R): R = block()
```

例子：

```
// run 函数
R.id.tv_text.run {
    tv_text.text = "ymc"
    tv_text.textSize = 23f
}
```

3.let

```
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

例子：

```
// run 函数
string?.run {
      println("The length of this String is $length")
}

```

4.also

```
fun <T> T.also(block: (T) -> Unit): T
```

例子：

```
// also 函数
string?.also {
      println("The length of this String is ${it.length}")
}
```

联合：

```
// 平常的使用
fun makeDir(path: String): File  {
    val result = File(path)
    result.mkdirs()
    return result
}
// 组合使用 
fun makeDir(path: String) = path.let{ File(it) }.also{ it.mkdirs() }

```



5.apply

```
fun <T> T.apply(f: T.() -> Unit): T
```

例子:

```
//正常的
fun createIntent(intentData: String, intentAction: String): Intent {
    val intent = Intent()
    intent.action = intentAction
    intent.data=Uri.parse(intentData)
    return intent
}
// apply 函数
fun createIntent(intentData: String, intentAction: String) =
        Intent().apply { action = intentAction }
                .apply { data = Uri.parse(intentData) }

```

#### 34.RxJava

(1)zip

Zip是通过一个方法将多个上游（多个水管、多个Observable）发射的事件结合到一起，然后发射这个组合事件，是严格按照顺序的，它只发射与发射数据量最少的上游（最少的水管、最少的Observable）一样多的数据。

不断地出现InterruptedException这个异常,使用SystemClock.sleep(1000)

```
    /**
     * zip操作符：
     *      组合过程是分别从两根水管中各取一个事件进行组合，并且一个事件只能被使用一次，且严格按照事件发送的顺序，
     *      也就是说不会出现1与B、2与A进行组合
     */
    public static void demo1(){
        Observable<Integer> observable1 = Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                Log.e("TAG" , "emit 1") ;
                emitter.onNext(1);
                Thread.sleep(1000);

                Log.e("TAG" , "emit 2") ;
                emitter.onNext(2);
                Thread.sleep(1000);

                Log.e("TAG" , "emit 3") ;
                emitter.onNext(3);
                Thread.sleep(1000);

                Log.e("TAG" , "emit 4") ;
                emitter.onNext(4);
                Thread.sleep(1000);

                Log.e("TAG" , "emit complete1") ;
                emitter.onComplete();
            }
        }).subscribeOn(Schedulers.io()) ;    // 让上游1（第一个水管）在子线程中执行


        Observable<String> observable2 = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {
                Log.e("TAG" , "emit A") ;
                emitter.onNext("A");
                Thread.sleep(1000);

                Log.e("TAG" , "emit B") ;
                emitter.onNext("B");
                Thread.sleep(1000);

                Log.e("TAG" , "emit C") ;
                emitter.onNext("C");
                Thread.sleep(1000);

                Log.e("TAG" , "emit complete2") ;
                emitter.onComplete();

            }
        }).subscribeOn(Schedulers.io()) ;      // 让上游2（第二个水管）在子线程中执行


        Observable.zip(observable1, observable2, new BiFunction<Integer, String, String>() {
            @Override
            public String apply(Integer integer, String s) throws Exception {
                return integer + s;
            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {
                Log.e("TAG" , "subscribe") ;
            }

            @Override
            public void onNext(String value) {
                Log.e("TAG" , "next -> " + value) ;
            }

            @Override
            public void onError(Throwable e) {
                Log.e("TAG" , "error") ;
            }

            @Override
            public void onComplete() {
                Log.e("TAG" , "complete") ;
            }
        });
    }
```

2.fragmentManager

getFragmentManager()所得到的是所在fragment 的父容器的管理器。

getChildFragmentManager()所得到的是在fragment  里面子容器的管理器。

getSupportFragmentManager()用于获取继承自android.support.v4.app.Fragment的fragment的管理器

3.注解

```
@IntDef(LOGIN_ACTIVITY_TYPE, SECURITY_ACTIVITY_TYPE)//默认整型
@Target(AnnotationTarget.VALUE_PARAMETER)//作为参数
@Retention(AnnotationRetention.SOURCE)//编译是显示，运行时无
annotation class AnnAnnotationVerify {
    companion object {
        const val LOGIN_ACTIVITY_TYPE = 1
        const val SECURITY_ACTIVITY_TYPE = 2
    }
}

注解的附加属性可以通过注解类和元注解来指定：
- @Target 指定可以用注解进行注解的元素类型
- @Retention 指定注解是否存储在编译后的类文件中，以及是否在运行时通过反射可见（默认情况下，这两个都是真的）
- @Repeatable 允许在同一个元素上多次使用相同的注解
- @MustBeDocumented 指定注解是公共API的一部分，应包含在生成的API文档中所示的类或方法签名中

@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION,
        AnnotationTarget.VALUE_PARAMETER, AnnotationTarget.EXPRESSION)
@Retention(AnnotationRetention.SOURCE)
@MustBeDocumented
annotation class Fancy
@Fancy class Foo {
    @Fancy fun baz(@Fancy foo: Int): Int {
        return (@Fancy 1)
    }
}
```

#### 35.sealed classes

是一堆存在继承关系的类的集合

类似于java中的枚举。不同的是，每个枚举类型只能存在一个对象实例，
而sealed class的子类可以拥有多个对象实例

```
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()
```

```
fun eval(expr: Expr): Double = when(expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

Sealed class（密封类） 是一个有特定数量子类的类，看上去和枚举有点类似，所不同的是，在枚举中，我们每个类型只有一个对象（实例）；而在密封类中，同一个类可以拥有几个对象。

Sealed class（密封类）的所有子类都必须与密封类在同一文件中

Sealed class（密封类）的子类的子类可以定义在任何地方，并不需要和密封类定义在同一个文件中

Sealed class（密封类）没有构造函数，不可以直接实例化，只能实例化内部的子类

```
sealed class Mathematics(){
    data class Dou(val number: Double) : Mathematics()
    data class Sub(val e1: Mathematics, val e2: Mathematics) : Mathematics()
    object NotANumber : Mathematics()

    fun eval(m: Mathematics): Double = when(m) {
        is Dou -> {
            m.number
        }
        is Sub -> eval(m.e1) - eval(m.e2)
        NotANumber -> Double.NaN
    }
}

fun main(args:Array<String>){
    var ec1:Mathematics = Mathematics.Dou(5.0)
    var d1 = ec1.eval(ec1)
    println(d1)

    var ec2:Mathematics = Mathematics.Sub(ec1, Mathematics.Dou(3.0))
    var d2 = ec2.eval(ec2)
    println(d2)

    var ec3:Mathematics = Mathematics.NotANumber
    var d3 = ec3.eval(ec3)
    println(d3)
}

结果输出：

5.0
2.0
NaN
```



#### 36.数据类型

(1)数字类型字面常量的下划线

```
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

(2)装箱与拆箱

```
val numValue: Int = 128    
val numValueBox: Int? = numValue

/*
    比较两个数字
 */
var result: Boolean
result = numValue == numValueBox
println("numValue == numValueBox => $result")  // => true,其值是相等的

result = numValue === numValueBox
/*
  上面定义的变量是Int类型，大于127其内存地址不同，反之则相同。
  这是`kotlin`的缓存策略导致的，而缓存的范围是` -128 ~ 127 `。
  故，下面的打印为false
*/
println("numValue === numValueBox => $result")  
```

(3)位运算符

shr(bits) => 有符号向右移 (类似Java的>>)
 ushr(bits) => 无符号向右移 (类似Java的>>>)
 and(bits) => 位运算符 and (同Java中的按位与)
 or(bits) => 位运算符 or (同Java中的按位或)
 xor(bits) => 位运算符 xor (同Java中的按位异或)
 inv() => 位运算符 按位取反 (同Java中的按位取反)

```
/** Shifts this value left by [bits]. */
public infix fun shl(bitCount: Int): Int
/** Shifts this value right by [bits], filling the leftmost bits with copies of the sign bit. */
public infix fun shr(bitCount: Int): Int
/** Shifts this value right by [bits], filling the leftmost bits with zeros. */
public infix fun ushr(bitCount: Int): Int
/** Performs a bitwise AND operation between the two values. */
public infix fun and(other: Int): Int
/** Performs a bitwise OR operation between the two values. */
public infix fun or(other: Int): Int
/** Performs a bitwise XOR operation between the two values. */
public infix fun xor(other: Int): Int
/** Inverts the bits in this value. */
public fun inv(): Int
```

```
/*
    位运算符
    支持序列如下：shl、shr、ushr、and、or、xor
 */
var operaNum: Int = 4

var shlOperaNum = operaNum shl(2)
var shrOperaNum = operaNum shr(2)
var ushrOperaNum = operaNum ushr(2)
var andOperaNum = operaNum and(2)
var orOperaNum = operaNum or(2)
var xorOperaNum = operaNum xor(2)
var invOperaNum = operaNum.inv()

println("shlOperaNum => $shlOperaNum \n " +
        "shrOperaNum => $shrOperaNum \n " +
        "ushrOperaNum => $ushrOperaNum \n " +
        "andOperaNum => $andOperaNum \n " +
        "orOperaNum => $orOperaNum \n " +
        "xorOperaNum => $xorOperaNum \n " +
        "invOperaNum => $invOperaNum")
```

(4)数组型

创建数组的3个函数
arrayOf()
arrayOfNulls()
工厂函数（Array()）

```
var arr3 = arrayOfNulls<Int>(3)

//如若不予数组赋值则arr3[0]、arr3[1]、arr3[2]皆为null
for(v in arr3){
    print(v)
    print("\t")
}

println()

//为数组arr3赋值
arr3[0] = 10
arr3[1] = 20
arr3[2] = 30

for(v in arr3){
    print(v)
    print("\t")
}
//输出结果
null    null    null    
10  20  30
```

```
var arr4 = Array(5,{index -> (index * 2).toString() })
for (v in arr4){
    print(v)
    print("\t")
}
//输出结果
0   2   4   6   8
```

#### 37.inner关键

`inner关键字`修饰表示内部类
嵌套类属于静态类和外部类没任何关系
内部类使用this,访问外部类的变

```
//嵌套类属于静态类和外部类没任何关系
fun main(args : Array<String>){ 
    
    var ot = OutClass().innerClass()
    ot.hello()
}

class OutClass{
    var name ="李武"
    //inner表示内部类
    inner class innerClass{
        var name = "张三"
        fun hello(){
            println("你好$name")
                        //内部类使用this,访问外部类的变
            println("你好${this@OutClass.name}")  量
        }
    }
}
```

运行于jvm之上的一门语言,静态语言

### 

#### 38、::

```
fun main(args: Array<String>) {
    println(lock("param1", "param2", ::getResult))
}

/**
 * @param str1 参数1
 * @param str2 参数2
 */
fun getResult(str1: String, str2: String): String = "result is {$str1 , $str2}"

/**
 * @param p1 参数1
 * @param p2 参数2
 * @param method 方法名称
 */
fun lock(p1: String, p2: String, method: (str1: String, str2: String) -> String): String {
    return method(p1, p2)
}
```

#### 39、->

```
fun <T, R> Collection<T>.fold(
    initial: R, 
    combine: (acc: R, nextElement: T) -> R
): R {
    var accumulator: R = initial
    for (element: T in this) {
        accumulator = combine(accumulator, element)
    }
    return accumulator
}
```

参数 combine 具有函数类型 (R, T) -> R，因此 fold 接受一个函数作为参数， 该函数接受类型分别为 R 与 T 的两个参数并返回一个 R 类型的值。 在 for-循环内部调用该函数，然后将其返回值赋值给 accumulator.

#### 40、== 和 ===

code1

```
fun main(args: Array<String>) {
	val a : Int = 1000
	println(a == a) //true
	println(a === a) //true
	val a1 : Int = a
	val a2 : Int = a
	println(a1 == a2) //true
	println(a1 === a2) //true
}
```

code2

```
fun main(args: Array<String>) {
	val a : Int = 1000
	println(a == a) //true
	println(a === a) //true
	val a1 : Int? = a
	val a2 : Int? = a
	println(a1 == a2) //true
	println(a1 === a2) //false
}
```

code 1 中 a1 和 a2 都没有装箱，所以不是对象，只是数值，所以数值大小和地址都是相等的。而 code 2 中 a1 和 a2 涉及到装箱，已经变成了对象，此时它们的数值仍然相等，但地址已经不同了（因为是不同对象）

code 3

```
fun main(args: Array<String>) {
	val a : Int? = 1000
	println(a == a) //true
	println(a === a) //true
	val a1 : Int? = a
	val a2 : Int? = a
	println(a1 == a2) //true
	println(a1 === a2) //true
}
```

因为这里的a经过装箱后本身已经一个对象，所以赋给a1和a2的时候是把直接把对象a赋给它们，所以此时a1和a2指的是同一个对象（对象a）

code4

```
fun main(args: Array<String>) {
	val a : Int = 100
	println(a == a) //true
	println(a === a) //true
	val a1 : Int? = a
	val a2 : Int? = a
	println(a1 == a2) //true
	println(a1 === a2) //true
}
```

这里跟 Java 中是一样的，在范围是 [-128, 127] 之间的数装箱时并不会创建新的对象，所以这里a1和a2装箱后的对象是同一个，a1 === a2也就返回true了。这里改为128或-129就又会变成false了。

#### 41.调用超类实现

在一个内部类中访问外部类的超类，可以通过由外部类名限定的 *super* 关键字来实现：`super@Outer`：

```
class FilledRectangle: Rectangle() {
    fun draw() { /* …… */ }
    val borderColor: String get() = "black"
    
    inner class Filler {
        fun fill() { /* …… */ }
        fun drawAndFill() {
            super@FilledRectangle.draw() // 调用 Rectangle 的 draw() 实现
            fill()
            println("Drawn a filled rectangle with color ${super@FilledRectangle.borderColor}") // 使用 Rectangle 所实现的 borderColor 的 get()
        }
    }
}
```

覆盖规则：如果一个类从它的直接超类继承相同成员的多个实现，为了表示采用从哪个超类型继承的实现，我们使用由尖括号中超类型名限定的 *super*，如 `super`：

```
open class Rectangle {
    open fun draw() { /* …… */ }
}

interface Polygon {
    fun draw() { /* …… */ } // 接口成员默认就是“open”的
}

class Square() : Rectangle(), Polygon {
    // 编译器要求覆盖 draw()：
    override fun draw() {
        super<Rectangle>.draw() // 调用 Rectangle.draw()
        super<Polygon>.draw() // 调用 Polygon.draw()
    }
}
```

#### 42.lazy的线程安全模式

by lazy初始化一个常量，保证此常量不会多次初始化.**lazy默认是线程安全的**，会上锁（可以看Java字节码）,影响性能

1.LazyThreadSafetyMode有三种:

SYNCHRONIZED同步：只会调用一次初始化方法。单例模式：懒汉式，线程安全

PUBLICATION：会调用多次初始化方法，但只有第一次的有效

NONE：会调用多次，且会改变常量的值为最后一次的值。单例模式：懒汉式，线程不安全

#### 43.inline

```
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

优点：函数体比较小的时候, 内联该函数可以令目标代码更加高效. 对于存取函数以及其它函数体比较短, 性能关键的函数, 鼓励使用内联.

缺点：滥用内联将导致程序变慢. 内联可能使目标代码量或增或减

结论：内联超过 10 行的函数，内联那些包含循环或 switch 语句的函数常常是得不偿失

#### 44.高阶函数

https://www.jianshu.com/p/7a771317ab74

```
/**
 * 参数类型包含函数类型
 */
fun lambdaParam(block: (Int) -> Unit) {
  block(2)
}

/**
 * 返回值为函数类型
 */
fun lambdaReturn(): (Int) -> Int {
  return {
    it * 2
  }
}
```

```
val sum = { x: Int, y: Int -> x + y }

    val sum1: (Int, Int) -> Int = { x, y -> x + y }

    var action: () -> Unit = {}
    var a = {}
    var action1: (Int) -> Unit = {}
    var action2: () -> Int = { 0 }
    
    
```



### 八、杂

1，buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

2.# android checkout sdk
-keep class com.checkout.android_sdk.** { *; }

//android design

-dontwarn android.support.design.**
-keep class android.support.design.** { *; }
-keep interface android.support.design.** { *; }
-keep public class android.support.design.R$* { *; }

//https://github.com/google/volley

-keep class com.android.volley.** { *; }
-keep class com.android.volley.toolbox.** { *; }
-keep class com.android.volley.Response$* { *; }
-keep class com.android.volley.Request$* { *; }
-keep class com.android.volley.RequestQueue$* { *; }
-keep class com.android.volley.toolbox.HurlStack$* { *; }
-keep class com.android.volley.toolbox.ImageLoader$* { *; }
-keepclassmembers,allowshrinking,allowobfuscation class com.android.volley.NetworkDispatcher {
    void processRequest();
}
-keepclassmembers,allowshrinking,allowobfuscation class com.android.volley.CacheDispatcher {
    void processRequest();
}

3.release 和 debug 有个区别，checkout 的 public key配的不一样

```Stri
fun getBindCheckoutPubKey():String {
   return if
}
```

```
private fun initCheckout(){
   mCheckoutAPIClient = CheckoutAPIClient(
   this, //context
   OcbsCheckoutManager.getBindCheckoutPubKey(),//public key
   Environment.LIVE //the environment
   )
}
```

4.DecimalFormat

参考：https://www.jianshu.com/p/683b2406342f

BigDecimal一共有4个构造方法:

BigDecimal(int) 创建一个具有参数所指定整数值的对象。

BigDecimal(double) 创建一个具有参数所指定双精度值的对象。（不建议采用）

BigDecimal(long) 创建一个具有参数所指定长整数值的对象。

BigDecimal(String) 创建一个具有参数所指定以字符串表示的数值的对象 [1] 。

```
static void testDecimal(){
        double pi = 123.14159;//圆周率
        //以百分比方式计数，并取两位小数
        System.out.println(new DecimalFormat("#.##%", DecimalFormatSymbols.getInstance(Locale.ENGLISH)).format(pi));//314.16%
        System.out.println(new DecimalFormat("###.#########%").format(pi));//314.16%
        DecimalFormat decimalFormat = new DecimalFormat("###.#########%");
        decimalFormat.applyPattern("###.#########%");
        System.out.println(decimalFormat.format(pi));
    }
```

5.混淆

当工程从support库迁移至androidx后，若使用了混淆，则必须在混淆文件中添加以下配置，否则使用了androidx的地方都将有可能出问题：

```
-keep class com.google.android.material.** {*;}
-keep class androidx.** {*;}
-keep public class * extends androidx.**
-keep interface androidx.** {*;}
-dontwarn com.google.android.material.**
-dontnote com.google.android.material.**
-dontwarn androidx.**
```

```
//混淆开启一般跟zipAlignEnabled一起使用
zipAlignEnabled true 
minifyEnabled true
```

### 九、问题

1.requestWindowFeature(Window.FEATURE_NO_TITLE)失效

创建Activity时继承的是**AppCompatActivity**而非Activity

解决：supportActionBar?.hide()

2.转场动画TextView 返回时不行

textview中设置了center

3..java和.class的区别

.java文件，就是当前编写的代码文件
.class文件，就是编译过后的文件（jvm只识别.class文件）

点run的时候，都是将.java文件先编译成.class文件 然后才能运行的

4.AS导入eclipse乱码问题

转GB2312 reload，再转UTF-8 convert

5.webview加载不了https

```
setting.setDomStorageEnabled(true);//设置DOM Storage缓存

//而DomStorage包括Session Storage和Local Storage两种，你还可以这样设置。

webview.getSettings().setDatabaseEnabled(true);
webview.getSettings().setDatabasePath("/data/data");
webSettings.setDomStorageEnabled(true);

//webView中一下常用的Setting介绍


```



```
//Uri的getPathSegments()方法返回的是一个元素为String的List,每个元素都是从Uri截取出来的一部分

fun uriTest(){
        val uri = Uri.parse("http://en/buy-sell-crypto/buy-sell/result/buy/dfdw23ew32/worldpay?md=sdf32432f")
        val parameterNames = uri.queryParameterNames
        val preIndex = uri.pathSegments.indexOf("buy")
        if(parameterNames.contains("md") && preIndex>0){
            val firstParam = uri.pathSegments[preIndex + 1]
            Log.d("uriTest", "firstParam=$firstParam")
        }
        val md = uri.getQueryParameter("md")
        Log.d("uriTest", "md=$md")
    }
```

android在9.0以上手机webview加载不出来

```
<?xml version="1.0" encoding="utf-8"?>
    <application
        android:usesCleartextTraffic="true">
    </application>
</manifest>
```

https://blog.csdn.net/wj_12113114/article/details/104048644

```
最近在android9.0上发现一个webView的坑，部分9.0手机上webview不显示图片，或者干脆页面无法加载，
代码中混合加载也开启了，该有的配置也都配置了

webView.setWebViewClient(new WebViewClient(){   
		        public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error){   
		                  //handler.cancel(); 默认的处理方式，WebView变成空白页   
//		                  //接受证书   
		        	handler.proceed();
		                 //handleMessage(Message msg); 其他处理   
		       }
		 });

从Android5.0开始，WebView默认不支持同时加载Https和Http混合模式。如何解决呢：
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) { settings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW); }


setPluginsEnabled(true); //支持插件 

WebSettings settings = webView.getSettings();
settings.setAppCacheEnabled(true); //启用应用缓存
settings.setDomStorageEnabled(true); //启用或禁用DOM缓存。
settings.setDatabaseEnabled(true); //启用或禁用DOM缓存。
if (SystemUtil.isNetworkConnected()) { //判断是否联网
    settings.setCacheMode(WebSettings.LOAD_DEFAULT); //默认的缓存使用模式
} else {
    settings.setCacheMode(WebSettings.LOAD_CACHE_ONLY); //不从网络加载数据，只从缓存加载数据。
}
```

6.webview加载不出3ds bug

```
1.pauseTimer()
当应用程序被切换到后台时回调，该方法针对全应用程序的webview,它会暂停所有webview的layout，
parsing,javascripttimer.降低cpu功耗
2.resumeTimers()
恢复pauseTimers时的动作
```

7.webView重定向

```
@Override  //此方法API24（Android 7.0）之后已经过时，但是我们还是需要用到这个方法的，原因是API24之前的手机只执行此方法
public boolean shouldOverrideUrlLoading(final WebView view, final String url) {

    WebView.HitTestResult hit = view.getHitTestResult();
    //hit.getExtra()为null或者hit.getType() == 0都表示即将加载的URL会发生重定向，需要做拦截处理
    if (TextUtils.isEmpty(hit.getExtra()) || hit.getType() == 0) {
        //通过判断开头协议就可解决大部分重定向问题了，有另外的需求可以在此判断下操作
        Log.e("重定向", "重定向: " + hit.getType() + " && EXTRA（）" + hit.getExtra() + "------");
        Log.e("重定向", "GetURL: " + view.getUrl() + "\n" +"getOriginalUrl()"+ view.getOriginalUrl());
        Log.d("重定向", "URL: " + url);
    }

    if (url.startsWith("http://") || url.startsWith("https://")) { //加载的url是http/https协议地址
        view.loadUrl(url);
        return false; //返回false表示此url默认由系统处理,url未加载完成，会继续往下走

    } else { //加载的url是自定义协议地址
        try {
            Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
            context.startActivity(intent);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return true;
    }
}

 @Override  //只有在API24之后才会执行
        public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
            Log.d("yunchong", "shouldOverrideUrlLoading2:"+url);
```

8.webview拦截方法拦截

```
         @Override //此方法废弃于API21，调用于非UI线程,API21以下的AJAX请求会走onLoadResource，无法通过此方法拦截
        public WebResourceResponse shouldInterceptRequest(WebView view, String url) {
            //过时
            Log.d("yunchong", url);
            return super.shouldInterceptRequest(view, url);
        }
        
        //API21之后新增一个新的方法：
         @Override
        public WebResourceResponse shouldInterceptRequest(final WebView view, WebResourceRequest request) {
            view.post(new Runnable() {
                @Override
                public void run() {
                    Log.d("yunchong", "shouldInterceptRequest2:"+view.getUrl());
                }
            });
            return super.shouldInterceptRequest(view, request);
        }
```

9.webview 安全策略

```
  private fun webViewSafeSettings(webView: WebView) {
        webView.settings.savePassword = false
        webView.settings.allowFileAccess = false
        webView.settings.allowFileAccessFromFileURLs = false
        webView.settings.allowUniversalAccessFromFileURLs = false
    }
```

### 十、设计模式

深入浅出-设计模式/Android 源码解析设计模式/first head

#### 1.适配器模式

a.组合

采用组合的方式称为对象适配器

```
val threePlugin = AdapterThreePlugin(GBTwoPlugin())
        val noteBook = NoteBook(threePlugin)
        noteBook.charge()
```

b.继承

采用继承的方式称为对象适配器

```
val threePlugin1 = AdapterTwoPluginExtend()
        val noteBook1 = NoteBook(threePlugin1)
        noteBook1.charge()
```

### 十一、SDK开发

#### 1.打包的方式

https://www.jianshu.com/p/5578132d28bb

a .aar的打包

新建好了library的module,IDE右边的gradle,找到本library下build文件夹下的assemble运行,OK

b.jar打包

```
//gradle 6.5  Jar
task makeJar(dependsOn: ['compileReleaseJavaWithJavac'], type: Jar) {
        delete 'build/libs/kotlin_and_java.jar' //生成的jar存放路径
        archiveAppendix = "demo"
        archiveBaseName = "androidJar"
        archiveVersion = "1.0.0"
        archiveClassifier = "release"
        //后缀名
        archiveExtension = "jar"
        //最终的 Jar 包名，如果没设置，默认为 [baseName]-[appendix]-[version]-[classifier].[extension]
        archiveFileName = "kotlin_and_java.jar"
        //需打包的资源所在的路径集
        def srcClassesDir1 = [project.buildDir.absolutePath + "/tmp/kotlin-classes/release"] //Kotlin 生成的classes文件路径
        def srcClassesDir2 = [project.buildDir.absolutePath + "/intermediates/classes/release"] //Java 生成的classes文件路径
        //初始化资源路径集
        from srcClassesDir1, srcClassesDir2
        //去除路径集下部分的资源
        exclude "**/R.class"
        exclude "**/R\$*.class"
        //只导入资源路径集下的部分资源
        include "com/turbo/testapplication/**/*.class"
    }

    makeJar.dependsOn(build)
    
    buildTypes {
        release {
            minifyEnabled true //开启混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
```

十二、书籍

《代码整洁之道》/ 《Android设计模式源码解析》/《first head》



