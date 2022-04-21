###### 一、CoordinatorLayout、AppbarLayout介绍

1.CoordinatorLayout协调布局
 作用是操作其中的子View，子View具体的操作，取决于app:layout_behavior属性。

协调布局的使用包含三个部分：
a.Dependency View
b.child View
c.behavior类

2.什么是AppbarLayout

a.继承LinearLayout，是Coordinatorlayout的子view，实现酷炫的滑动效果

b.某个指定的view（NestedScrollView/RecyclerView/ViewPager---listview/SrcollView）滚动时，AppBarLayout内部的子view会实现一些动作

-scroll
滚出屏幕，不设置则固定屏幕
-enteerAlways
快速返回
-enterAlwaysCollapsed
设置minHeight属性时，以最小高度进入
滚动视图到达顶部时，才扩大到完整高度
-exitUtilCollapsed
当视图滚动时，会滚动到设置的minHeig时才完全隐藏
-snap
回弹效果

enterAlwaysCollapsed、exitUtilCollapsed只有在CollapsingToolbarLayout才有用
###### 二、 dpi、px、dp介绍##
1.dpi全称是dots per inch，对角线每英寸的像素点的个数
  公式：dpi=height2+width2  √size
2.dp/dip,device independent pixels,不依赖像素，屏幕可适配
  320x480分辨率，像素密度为160，1dp=1px
  480x800分辨率，像素密度为240，1dp=1.5px
  计算公式：px = dp*（dpi/160）
3.dp转换为px
  public int dp2px(Context context,float dpValue){
   final float scale = context.getResources().getDisplayMetrics().density;
    return (int)(dpValue * scale + 0.5f);
  }

###### 三、懒加载 ######
1.当加载数据比较耗时、占用大量内存时--->使用懒加载

//这里实现懒加载
publ void setUserVisibleHint(boolean isVisibleToUser){
     super.setUserVisibleHInt(isVisibleToUser);
     if(getUserVisibleHint()){
        isVisible = true;
        onVisible();
     }else{
        isVisible = false;
        onInvisible(); 
     }
}

void onVisible(){
  lazyLoad();
}

abstract void lazyLoad();//子类实现

void onInvisible(){}


//标志位，标志已经初始化完成
boolean isPrepared;

public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        Log.d(LOG_TAG, "onCreateView");
        View view = inflater.inflate(R.layout.fragment_open_result, container, false);
        //XXX初始化view的各控件
        isPrepared = true;
        lazyLoad();
        return view;
    }

 protected void lazyLoad() {
        if(!isPrepared || !isVisible) {
            return;
        }
        //填充各控件的数据
    }

## 四、dialogFragment ##
activity存储他的状态之前调用commit(),存储 之后调用会抛出异常
可以使用commitAllowingStateLoss() 方法

## 五、EditText ##  

1.setOnEditorActionListener
//搜索和回车触发软键盘
mEditor.setOnEditorActionListener(new OnEditorActionListener() {  
            @Override  
            public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {  
               if (actionId == EditorInfo.IME_ACTION_SEARCH ||（event！=null&&event.getKeyCode()==KeyEvent.KEYCODE_ENTER）) {   
                  // 按下完成按钮，这里和上面imeOptions对应
                 page = 1
                getData();//发起请求
                KeyBoardUtils.closekeyboard(v,mContent);//关闭键盘
                return true;   
                }
                return false；
            }  
        });  

注意：返回false，隐藏软键盘，返回true，保留软键盘

actionUnspecified 未指定 EditorInfo.IME_ACTION_UNSPECIFIED
actionNone  动作 EditorInf.IME_ACTION_NONE
actionGO 去往 EditorInfo.IME_ACTION_GO
actionSearch 搜索 Editor.IME_ACTION_SEARCH
actionSend 发送 Editor.IME_ACTION_SEND
actionNext 下一项 Editor.IME_ACITON_NEXT
actionDone 完成 Editor.IME_ACTION_DONE

2.表情过滤
public class EmojiExcludeFilter implements InputFilter {

    @Override
    public CharSequence filter(CharSequence source, int start, int end, Spanned dest, int dstart, int dend) {
        for (int i = start; i < end; i++) {
            int type = Character.getType(source.charAt(i));
            if (type == Character.SURROGATE || type == Character.OTHER_SYMBOL) {
                return "";
            }
        }
        return null;
    }
}

mEditor.setFilters(new InputFilter[]{new EmojiExcludeFilter()});

## 六、recycleView局部刷新 ##
1.刷新局部位置，增加表示 
notifyItemChanged(lastPosition, refresh_part_mark);
notifyItemChanged(lastPosition, refresh_part_mark1);

2.重写
public void onBindViewHolder(@NonNull BaseRvViewHolder holder, int position, @NonNull List<Object> payloads) {
        if (payloads.isEmpty()) {
            onBindViewHolder(holder, position);
        } else {
            int widget = (int) payloads.get(0);
            if (widget == refresh_part_mark) {
                SwitchButton switchButton = holder.getView(R.id.switch_shoes);
                if (switchButton != null) {
                    switchButton.setChecked(false);
                }
            }else if(widget == refresh_part_mark1){
                //
            }
        }
    }

“字节”是一个8位的物理存贮单元，
而“字符”则是一个文化相关的符号。

3.从某个位置刷新接下来的几个item

```
notifyItemRangeChanged(int positionStart, int itemCount) 
```

4.更多

```
loadMoreFooterView = (LoadMoreFooterView) iRecyclerView.getLoadMoreFooterView();
loadMoreFooterView.setEndTextParams(R.string.bullMarket_theEnd, 12, DipUtil.getIntDip(5));
```

## 七、Java 单例模式同步锁this与Class的区别 ##

synchronized(this) 表示的是所有线程需要排队获取当前类的实例的锁；而 synchronized(xxx.class) 表示的是所有线程需要排队获取当前类的锁；区别就在于 this 是一个实例，而 xxx.class 是一个整个的 class 信息。

##  八、java快捷键 ##

1.alt+shift+m 抽取本地变量的方法

2.alt+shift+L 抽取方法

3.AS格式化布局 勾选   Use custom formatting settings for Android XML files

4.alt+enter  if和switch 切換

5.ctrl+alt+l 格式化代码  xml代码格式化---file-->setting-->code style-->xml-->use custom formatting

## 九、文件 ##

 **数据的写入**：Context类提供了openFileOutput(String  name  ContextMode  model)方法； 

**name**指的是文件名，（不可以包含路径）

文件默认存储到data/data/<packname>/files/目录下，故不用写路径；

model主要有两种模式，MODE_PRIVATE(同名文件覆盖) MODE_APPEND(同名文件追加)

## 十、smartRefreshLayout ##

1.刷新原理

```
smartrefresh = (SmartRefreshLayout) findViewById(R.id.smartrefresh);
//默认风格样式
smartrefresh.setRefreshFooter(new ClassicsFooter(this));
smartrefresh.setRefreshHeader(new ClassicsHeader(this));
//初始化list
@Override
public void onNext(Bean bean) {
    beans = bean.getResult().getProduct();
    size = beans.size();
    for (int i = 0; i < 4; i++) {
        list.add(beans.get(i));
    }
    adapter = new MyAdapter(R.layout.item, list);
    main2_recy01.setLayoutManager(new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL));
    main2_recy01.setAdapter(adapter);
   setRefreshListener();  //自己写一个 上拉监听下拉监听方法

```

```
//刷新监听方法
private void setRefreshListener() {
    smartrefresh.setOnRefreshListener(new OnRefreshListener() {
        @Override
        public void onRefresh(RefreshLayout refreshLayout) {
            Toast.makeText(Main2Activity.this, "下拉刷新成功", Toast.LENGTH_SHORT).show();
            //设置刷新时长
            refreshLayout.finishRefresh(1000);
            //清空集合
            list.clear();
            //重新添加
            for (int i = 0; i < 4; i++) {
                list.add(beans.get(i));
            }
            //刷新视图
            adapter.notifyDataSetChanged();
        }
    });
```

```
//加载更多监听
 smartrefresh.setOnLoadMoreListener(new OnLoadMoreListener() {
        @Override
        public void onLoadMore(RefreshLayout refreshLayout) {
            Toast.makeText(Main2Activity.this, "上拉加载成功", Toast.LENGTH_SHORT).show();
            //设置加载时长
            refreshLayout.finishLoadMore(1000);
            //获取试图总条目
            int count = adapter.getItemCount();
            //如果当前试图总条目==集合长度
            if (count == size) {
                Toast.makeText(Main2Activity.this, "数据以空", Toast.LENGTH_SHORT).show();
            }
         //当前试图总条目+4就是 一次加载4条目 
            if (count + 4 < size) {
                for (int i = count; i < (count + 4); i++) {
                    list.add(beans.get(i));
                }
            } else {
                for (int i = count; i < size; i++) {
                    list.add(beans.get(i));
                }
            }
            adapter.notifyDataSetChanged();
        }
    });
}
```

2.footer

```
    ((CustomClassicsFooter)(getSmartRefreshLayout().getRefreshFooter())).setTextSizeTitle(12);
}

@Override
protected String getNoMoreText() {
    return getResources().getString(R.string.bullMarket_theEnd);
}
```

## 十一、webview ##

(1) loadurl

```
@SuppressLint("SetJavaScriptEnabled")
    public static void setWebSettings(Context context, WebSettings webSettings) {
        webSettings.setJavaScriptEnabled(true);
        webSettings.setCacheMode(WebSettings.LOAD_NO_CACHE);
        String ua = webSettings.getUserAgentString();
        String str = ua + " platform/app";
        webSettings.setUserAgentString(str);
        webSettings.setPluginState(WebSettings.PluginState.ON);
        webSettings.setJavaScriptCanOpenWindowsAutomatically(true);
        // 开启LocalStorage缓存权限
        webSettings.setAllowFileAccess(true);
        webSettings.setDomStorageEnabled(true);
        webSettings.setAppCacheMaxSize(1024 * 1024 * 8);
        String appCachePath = context.getApplicationContext().getCacheDir().getAbsolutePath();
        webSettings.setAppCachePath(appCachePath);
        webSettings.setAppCacheEnabled(true);
        // 特别注意：5.1以上默认禁止了https和http混用，以下方式是开启
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            webSettings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
        }
    }
```

```
  private void loadLink() {
        if (!NetUtil.isNetworkAvailable(this)) {
            ToastUtil.showErrorToast(getResources().getString(R.string.error_network));
            mWebView.loadUrl("file:///android_asset/error.html");
        } else {
            mWebView.loadUrl(shareurl);
        }
    }

```



方法一：不适用与IOS

1.初始化webview

```
WebSettings settings = mWbView.getSettings();
//允许在webview中使用js
settings.setJavaScriptEnabled(true);
//6.在WebView初始化代码中执行如下代码
JavaScriptMethod javaScriptMethod = new JavaScriptMethod(this, mWbView);
        mWbView.addJavascriptInterface(javaScriptMethod,JavaScriptMethod.JAVAINTERFACE);
```



2.创建一个类JavaScriptMetod，专门用来给js提供可调用的方法 

```
 public class JavaScriptMethod {
     //创建一个字符串常量
     public static final String JAVAINTERFACE = "javaInterface";

     private Context context;
     private WebView webView;
     //创建该类的构造方法，提供两个参数
     public JavaScriptMethod(Context context, WebView webView) {
         this.context = context;
         this.webView = webView;
     }
     //法必须使用注解@JavascriptInterface
     @JavascriptInterface
     public void showToast(String json) {
         Toast.makeText(context, json, Toast.LENGTH_SHORT).show();
     }

 }
```

3.js调用JavaScriptMethod方法

```
//参数一般为json格式

var json = {"name":"javascript"};

//javaInterface是上面所说的字符串映射对象

window.javaInterface.showToast(JSON.stringify(json));

```

方法二：js发出一个携带参数的url请求，Android端用webview.setWebViewClient()拦截url，解析其中参数，实现跳转

1.允许使用js

WebSettings  settings = webview.getSetting();

settings.setJavaScriptEnable(true);

2.js 中的代码

```
$("#showtoast").click(function () {

var json = {"data": "I am a toast"};

window.location.href="protocol://android?code=toast&data="+JSON.stringify(json);

});

$("#call").click(function () {

var json = {"data": "10086"};

window.location.href="protocol://android?code=call&data="+JSON.stringify(json);

});
```

3.android中的daim

```
    mWbView.setWebViewClient(new WebViewClient() {
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                String url;
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
                    url = request.getUrl().toString();
                } else {
                    url = request.toString();
                }
                String pre = "protocol://android";
                if (!url.contains(pre)) {
                    view.loadUrl(url);
                    return false;
                }
                ArrayMap<String, String> map = getParamsMap(url, pre);
                String code = map.get("code");
                String data = map.get("data");
}
```

## 十二、自定义view

1.类

```
public class CustomView extends View {
        private String title;
        private int size;
        
        private int height;
        private int width;
        
        public CustomView(Context context) {
            this(context, null);
        }
        
        public CustomView(Context context, @Nullable AttributeSet attrs) {
            this(context, attrs, 0);
        }
        
        
        @Override
        protected void onSizeChanged(int w, int h, int oldw, int oldh) {
      	  super.onSizeChanged(w, h, oldw, oldh);
            height = h;
            width = w;
        }

        public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
            
            TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.CustomView, defStyleAttr, defStyleAttr);
            title = a.getString(R.styleable.CustomView_title);
            size = a.getInteger(R.styleable.CustomView_size, 20);
            //回收資源
            a.recycle();
   
            initView();
        }

```

2.自定义属性

```
注注：在values文件的attrs.xml中

<resources>
    <declare-styleable name="CustomView">
        <attr name="title" format="string" />  //字符串
        <attr name="size" format="integer" />  //整形
    </declare-styleable>
</resources>

```

3.draw() 方法

```
    @Override
    public void draw(Canvas canvas) {
        super.draw(canvas);
        Paint paint = new Paint();
        paint.setTextSize(size);
        paint.setColor(getResources().getColor(R.color.colorAccent));
        canvas.drawText(title, 30, 30, paint);
    }
```

4.构造函数

a.一个参数：在代码中直接new一个Custom View实例的时候,会调用第一个构造函数.

b.两个参数：在xml布局文件中调用Custom View的时候,会调用第二个构造函数.

c.三个参数：在接受一个style资源的时候调用，一般不会用到.

5.dp转px

```
   //dp转px
    private int dpToPx(int dp){
        final float scale = getContext().getResources().getDisplayMetrics().density;
        return (int) (dp * scale + 0.5f);
    }
```

​       paint.setTextSize(size);//size为px

## 十三、Android1 ##

### 0.报错总结

(1) The application could not be installed: INSTALL_FAILED_CONFLICTING_PROVIDER
Installation failed due to: 'null' 

answer: 可能存在了同名的包名，将原apk卸载

(2)**Android Studio出现:Cause: unable to find valid certification path to requested target**

answer:https://www.jianshu.com/p/0fd7ed2ffe82

```
allprojects {
    repositories {
        //国内最快的镜像源
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/google' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/gradle-plugin' }
        mavenCentral()
        maven { url "https://jitpack.io" }
        maven { url 'https://dl.google.com/dl/android/maven2/' }
        google()
    }
}
```

(3)Failed to resolve: com.android.support:appcompat-v7:28.0.3

降低gradle中API版本号，与依赖的api版本号相同

参考简书：https://www.jianshu.com/p/fa3902b788ee

(4)AS资源引入爆红

清理缓存

(5)主题配置错误，引用v7后，`AppCompatActivity ， Application 配置主题，需要使用`Theme.AppCompat.Light.NoActionBar

否则会crash。

(6)lateinit property application has not been initialized

App: Application() 需要在manifest中注册 操！

###  1.注解  ###

@SuppressLint("NewApi"）屏蔽一切因版本而导致新api中才能使用的方法报的android lint错误

.@TargetApi()  屏蔽部分因版本而导致新api中才能使用的方法报的android lint错误

### 2.RTSP和RTMP协议 ###

(1)RTSP:实时流媒体协议：传输数据可以通过传输层的tcp和udp协议，rtsp也提供了基于rtp传输机制的一些有效方法

(2)RTMP:实时数据传输协议：基于tcp协议

(3)TCP:传输控制协议：面向连接，可靠的字节流服务，tcp提供超时重发，丢弃重复数据，检验数据，流量控制等，保证数据从一端传到另一端。

(4)UDP:用户数据报协议，是一个简单点的面向数据包的运输层协议，udp提供不可靠，只把程序传给IP层的数据报发出去，但并不保证能到达目的地，由于udp在传输数据报前不用与客户和服务器之间建立一个连接，没有超时重发等，故传输速度快。

(5)TCP和UDP基本区别

a.基于连接与无连接

b.TCP要求系统资源较多，UDP较少

c.UDP程序结构胶简单

d.流模式(TCP)与数据报模式(UDP)

e.TCP保证数据正确性，UDP可能丢包

f.TCP 保证数据顺序，UDP不保证

(6)TCP与UDP区别总结

a.tcp面向连接(如打电话要先拨号建立连接)，udp是无连接的，即发送数据前不需要连接

b.tcp提供可靠的服务，通过tcp连接传送的数据，无差错，不丢失，不重复，且按序到达；udp尽最大努力交付，即不保证可靠交付

c.tcp面向字节流，tcp把数据看成一连串无结构的字节流；udp是面向报文的，udp没有拥塞控制，因此网路出现拥塞不会使源主机的发送速率降低(对实时应用很有用，如IP电话，实时视频会议)

d.每一条tcp连接只能是**点到点**的，udp支持一对一，一对多，多对一和多对多的交互通信

e.tcp首部开销20字节；udp的首部开销小，只有8个字节

f.tcp的逻辑通信信道是**全双工**的可靠信道，udp则是不可靠信道

3.new ArrayList<>()  并初始化数据

1. ArrayList<String> obj = new ArrayList<String>() {
2. ​			{
3. ​				add("1");
4. ​				add("2");
5. ​			}
6. ​		};

### 3.线程池

(1)为什么使用线程池

a.线程创建所需时间为T1，线程执行任务时间为T2，线程销毁时间为T3，而往往T1+T3>T2.所以频繁创建线程池，性能较差

b.如果有任务来了，再去创建线程的话效率较低

c.线程池可以管理控制线程，线程池是稀缺资源，如果无休止的创建会消耗系统资源，还会降低系统稳定性，使用线程池可以进行统一分配，方便调优和监控。

d.线程池提供队列，存放缓冲等执行任务。

### 4.进程优先级

a.前台进程

~拥有用户交互的activity

~拥有用户交互的activity上的service

~拥有前台运行的service

~拥有正在执行onReceive()方法的broadreceiver

b.可见进程

~不在前台依然可见的activity，如：activity启动一个对话框

c.服务进程

~绑定一个可见activity的service，如：后台播放器、在后天下载

d.后台进程

~activity调用onstop()方法后

e.空进程

~不拥有任何active组件

进程内没有任何东西在运行，保留这种进程的目的是用作缓存，以缩短应用下次在其中运行组件所需的启动时间

### 5.折叠布局(包引入对比)

(1) android.support.design.widget.CoordinatorLayout

#androidx.coordinatorlayout.widget.CoordinatorLayout

(2) android.support.design.widget.AppBarLayout

#com.google.android.material.appbar.AppBarLayout

(3) android.support.design.widget.CollapsingToolbarLayout #com.google.android.material.appbar.CollapsingToolbarLayout

(4) android.support.v7.widget.Toolbar

#androidx.appcompat.widget.Toolbar

(5) com.android.support:appcompat-v7 #androidx.appcompat:appcompat:1.0.0

(6) com.android.support:design #com.google.android.material:material:1.0.0-rc01

### 6.activity属性(adjustPan)

防止底部控件在输入框弹起时，被顶上去

```
<activity  
android:name=".ui.community.SendMsgActivity"  
android:screenOrientation="portrait"   
android:windowSoftInputMode="adjustPan" />
```

### 7.CPU和GPU

CPU:是电脑的中央处理器

GPU:是电脑的图形处理器

### 8.TextView中drawableright触摸事件

```
    mText.setOnTouchListener((v, event) -> {
            Drawable drawable = mText.getCompoundDrawables()[2];
            if (drawable == null) {
                return false;
            }
            if (event.getX() > mText.getWidth() - drawable.getBounds().width()) {
                //处理触摸事件
                //设置TextView的drawable
            mText.setCompoundDrawablesWithIntrinsicBounds(null, null,                                 getResources().getDrawable(R.drawable.icon), null);
                return false;
            }
            return false;
        });
        
        //设置图片的大小再放进去
        TextView textView = new TextView(mContext);
        Drawable drawable = getResources().getDrawable(R.drawable.icon_friend);
        // 设置图片的大小
        drawable.setBounds(0, 0, 20, 20);
        // 设置图片的位置，左、上、右、下
        textView.setCompoundDrawables(null, null, drawable, null);
        
        //textview中插入图片
        //SpannableString implements CharSequence
        Drawable drawable=mContext.getResources().getDrawable(R.mipmap.bangtang);
        //必须为 Drawable 设置边界,才可以显示出来
      drawable.setBounds(0,0,drawable.getIntrinsicWidth()/2,drawable.getIntrinsicHeight()/2);
        ImageSpan imageSpan=new ImageSpan(drawable,ImageSpan.ALIGN_BOTTOM);
        SpannableString spanString=new SpannableString(""+"textView 的 text");
        spanString.setSpan(imageSpan,0,1, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
        textView.setText(spanString);
```

### 9.Dialog状态栏变黑问题

问题：

Window window = getWindow();
WindowManager.LayoutParams lp = window.getAttributes();
lp.width = WindowManager.LayoutParams.MATCH_PARENT;
lp.height = WindowManager.LayoutParams.MATCH_PARENT;

 *// 如果将高度设置为MATCH_PARENT，那么就会出现状态栏变黑的问题* 

解决办法：

动态去计算手机的高度

lp.height = 手机高度 - 状态栏高度; 

源码解析：

```
public void setWindowBackground(Drawable drawable) {
    if (getBackground() != drawable) {
        setBackgroundDrawable(drawable);
        if (drawable != null) {
            mResizingBackgroundDrawable = enforceNonTranslucentBackground(drawable,
                    mWindow.isTranslucent() || mWindow.isShowingWallpaper());
        } else {
            mResizingBackgroundDrawable = getResizingBackgroundDrawable(
                    getContext(), 0, mWindow.mBackgroundFallbackResource,
                    mWindow.isTranslucent() || mWindow.isShowingWallpaper());
        }
        if (mResizingBackgroundDrawable != null) {
            mResizingBackgroundDrawable.getPadding(mBackgroundPadding);
        } else {
            mBackgroundPadding.setEmpty();
        }
        drawableChanged();
    }
}


/**
 * Enforces a drawable to be non-translucent to act as a background if needed, i.e. if the
 * window is not translucent.
 */
private static Drawable enforceNonTranslucentBackground(Drawable drawable,
        boolean windowTranslucent) {
    if (!windowTranslucent && drawable instanceof ColorDrawable) {
        ColorDrawable colorDrawable = (ColorDrawable) drawable;
        int color = colorDrawable.getColor();
        if (Color.alpha(color) != 255) {
            ColorDrawable copy = (ColorDrawable) colorDrawable.getConstantState().newDrawable()
                    .mutate();
            copy.setColor(
                    Color.argb(255, Color.red(color), Color.green(color), Color.blue(color)));
            return copy;
        }
    }
    return drawable;
}

备注：
当windowTranslucent == false,enforceNonTranslucentBackground会强制将背景更改为不透明背景（PS: 即使windowBackground 是 透明的 ）
```



### 10.Dialog弹出状态设置

```
<style name="dialogstyle" parent="@android:style/Theme.Dialog">
<item name="android:windowFrame">@null</item><item //提示框是否有边框 name="android:windowIsFloating">true</item><item  //是否悬浮  name="android:windowIsTranslucent">false</item><item //为否，强制将背景设为透明 name="android:windowNoTitle">true</item><item //是否有标题 name="android:windowBackground">@color/nero_transparent</item><item //背景颜色name="android:backgroundDimEnabled">false</item><item //弹窗背景是亮的 name="android:windowAnimationStyle">@style/animTop</item> //动画
```

### 11.Window 和 WindowManager

(1)window是一个抽象类，表示一个窗口，具体实现是phonewindow，实现位于windowmanagerservice中

(2)windowManagerService是FrameWork层的窗口管理服务，管理系统中的所有窗口。窗口的本质是一块显示区域，在Android中就是绘制的画布，Surface，当一块Surface显示在屏幕上时，就是用户看到的窗口了。

WMS添加一个W的过程，就是WMS为其分配一块Surface的过程，一块块Surface在WMS的管理下，呈现出 多彩的界面。

UI框架层：负责管理窗口中View控件的布局与绘制，以及响应用户输入事件   ImageButton/listview...

WMS：负责管理窗口Surface的布局与次序    Window123...

SurfaceFlinger：将WMS维护的窗口Surface按照一定的次序混合后显示到屏幕上    screen

### 12.使用WindowManager设计一个系统浮动的dialog

```
   //申请权限
   public void checkPermission() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            if (!Settings.canDrawOverlays(getApplicationContext())) {
                //启动Activity让用户授权
                Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION);
                intent.setData(Uri.parse("package:" + getPackageName()));
                startActivityForResult(intent, 100);
            } else {
                showDialog();
            }
        }
    }
```

```
//显示弹窗
public void showDialog() {
        WindowManager window = getWindowManager();
        View view = View.inflate(this, R.layout.item_fruit, null);
        WindowManager.LayoutParams layoutParams = new WindowManager.LayoutParams(WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams.WRAP_CONTENT, 0, 0, PixelFormat.RGB_565);
        // flag 设置 Window 属性
//        layoutParams.flags = WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL|WindowManager.LayoutParams.FLAG_WATCH_OUTSIDE_TOUCH;
        layoutParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE|WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON;
//        layoutParams.flags = WindowManager.LayoutParams.FLAG_LOCAL_FOCUS_MODE;
        // type 设置 Window 类别（层级）
        int LAYOUT_FLAG = 0;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            LAYOUT_FLAG = WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY;
        } else {
            LAYOUT_FLAG = WindowManager.LayoutParams.TYPE_PHONE;
        }
        layoutParams.type = LAYOUT_FLAG;
        layoutParams.gravity = Gravity.RIGHT;
        window.addView(view, layoutParams);
    }
```

```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        //申请完权限处理返回
        if (requestCode == 100) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                if (Settings.canDrawOverlays(this)) {
                    showDialog();
                } else {
                    Toast.makeText(this, "ACTION_MANAGE_OVERLAY_PERMISSION权限已被拒绝", Toast.LENGTH_SHORT).show();
                }
            }
        }
    }
```

### 13.Android 上下抖动动画

a.补间动画

```
 public Animation shakeAnimation(int CycleTimes) {
        //控制上下左右平移
        Animation translateAnimation = new TranslateAnimation(0, 0, 0, 8);
        //控制抖动的速率
        translateAnimation.setInterpolator(new CycleInterpolator(CycleTimes));
        //动画持续时间
        translateAnimation.setDuration(1000);
        //无限次循环
        translateAnimation.setRepeatCount(Animation.INFINITE);
        return translateAnimation;
    }
```

b.属性动画

```
        ObjectAnimator objectAnimator = ObjectAnimator.ofFloat(view,"translationY",0f,6f);
        objectAnimator.setRepeatCount(Animation.INFINITE);
        objectAnimator.setDuration(1000);
        objectAnimator.setInterpolator(new CycleInterpolator(5));
        objectAnimator.setStartDelay(200);
        objectAnimator.start();
```

c.xml配置

```
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:valueTo="200"
    android:valueType="floatType"
    android:propertyName="y"
    android:repeatCount="1"
    android:repeatMode="reverse"/>
```

### 14.Android监测后台到前台

1.Application中注册activity生命周期回调

```
public class MainApplication extends Application {

    private int count = 0;
    private Activity sActivity;
    @Override
    public void onCreate() {
        super.onCreate();
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityStopped(Activity activity) {
                Log.v("LifecycleCallbacks", activity + "onActivityStopped");
                count--;
                if (count == 0) {
                    Log.v("LifecycleCallbacks", ">>>>>>>>>>>>>>>>>>>切到后台  lifecycle");
                }
            }
            @Override
            public void onActivityStarted(Activity activity) {
                Log.v("LifecycleCallbacks", activity + "onActivityStarted");
                if (count == 0) {
                    Log.v("LifecycleCallbacks", ">>>>>>>>>>>>>>>>>>>切到前台  lifecycle");
                }
                count++;
            }
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                Log.v("LifecycleCallbacks", activity + "onActivityCreated");
            }
            @Override
            public void onActivityResumed(Activity activity) {
                sActivity = activity;
                //得到当前activity的上下文
                Log.v("LifecycleCallbacks", activity + "onActivityResumed");
            }
            @Override
            public void onActivityPaused(Activity activity) {
                Log.v("LifecycleCallbacks", activity + "onActivityPaused");
            }
            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
                Log.v("LifecycleCallbacks", activity + "onActivitySaveInstanceState");
            }
            @Override
            public void onActivityDestroyed(Activity activity) {
                Log.v("LifecycleCallbacks", activity + "onActivityDestroyed");
            }
        });
    }
}
注：此方式不适合 旋转设备时activity重建，onStart方法会再次被调起
```

2.只要判断前一个页面退出后有没有新页面进入即可 

(1)这里涉及到几个问题：
 1.采用何种方式监测Activity状态变化：基类方式还是Application里面？
 2.在哪些方法里面做手脚：不用说，肯定是onStart和onStop
 3.怎么判断前一个页面结束之后后一个页面进入？
 问题1很简单，肯定是采用Application，因为可能会有第三方Activity被调起，如果采用基类方式，是无法监测第三方Activity的状态变化的，剩下的就是问题3了，方案是这样的，在前一页面结束后，开始计时，在有限时间内，新页面如果没有出现，那么就认为App进入了后台，否则则继续保持在前台。

(2)解决办法：



### 15.ScrollView滚动到指定位置

1.这个onWindowFocusChanged指的是这个Activity得到或者失去焦点的时候 就会call 

2.使用一个view的getWidth() getHeight() 方法来获取该view的宽和高，返回的值却为0 

```
    //重写此方法，得到布局的高度
    public void onWindowFocusChanged(boolean hasFocus) {
        super.onWindowFocusChanged(hasFocus);
        textView.setText(mLL.getBottom()+"");
    }
```

3.相关执行打印:

(1) entry: onStart---->onResume---->onAttachedToWindow----------->onWindowVisibilityChanged--visibility=0---------->onWindowFocusChanged(true)------->

(2) exit: onPause---->onStop---->onWindowFocusChanged(false) ---------------------- (lockscreen)

(3) exit : onPause----->onWindowFocusChanged(false)-------->onWindowVisibilityChanged--visibility=8------------>onStop(to another activity)

### 16.监听滑动上下左右监听

```
private void setOnLayoutTouchListener(){
	view.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()){
                    case MotionEvent.ACTION_DOWN:
                        posX = event.getX();
                        posY = event.getY();
                        break;
                    case MotionEvent.ACTION_MOVE:
                        curPosX = event.getX();
                        curPosY = event.getY();
                        break;
                    case MotionEvent.ACTION_UP:
                        if ((curPosX - posX > 0) && (Math.abs(curPosX - posX) > 25)){
                            Log.v(TAG,"向左滑动");
                        }
                        else if ((curPosX - posX < 0) && (Math.abs(curPosX-posX) > 25)){
                            Log.v(TAG,"向右滑动");
                        }
                        if ((curPosY - posY > 0) && (Math.abs(curPosY - posY) > 25)){
                            Log.v(TAG,"向下滑动");
                        }
                        else if ((curPosY - posY < 0) && (Math.abs(curPosY-posY) > 25)){
                            Log.v(TAG,"向上滑动");
                        }
                        break;
                }
                return true;
            }
        });
}
```

### 17.获取年月日

(1)

```
   public static String stampToDate(long str) {
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
        String format1 = format.format(new Date(str));
        return format1;
    }
```

(2)

```
      Calendar calendar = Calendar.getInstance();
      SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
      String date = sf.format(calendar.getTime());
```

### 18.大环套小环

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape
            android:shape="oval"
            android:useLevel="true">
            <size
                android:width="@dimen/dp_38"
                android:height="@dimen/dp_38" />
            <stroke
                android:width="@dimen/dp_1"
                android:color="@color/color_f0f0f0" />
        </shape>
    </item>

    <item android:gravity="center">
        <shape
            android:shape="oval"
            android:useLevel="true">
            <size
                android:width="@dimen/dp_36"
                android:height="@dimen/dp_36" />
            <solid android:color="@color/color_f5f5f5" />
        </shape>
    </item>

</layer-list>
```

### 19.serializable 报错

 java.lang.RuntimeException: Parcelable encountered IOException writing serializable object (name = com.etechs.eyee.mvp.bean.community.PostingDetailVO) 

分析： 在传数据对象的时候，使用Serializable的时候，出现了这个错误 ， 这个主要是自己bean里面有一个还有一个实体类， 这个实体类没有添加序列化引起的错误 ， 解决方法就是在bean里面的实体类在添加一个Seraializable 即可 

### 20.ViewPager 报错

 在初始化ViewPager时，应先给Adapter初始化内容后再将该adapter传给ViewPager，如果不这样处理，在更新  adapter的内容后，应该调用一下adapter的notifyDataSetChanged方法，否则在ADT22以上使用会报这个错。 

### 21.瀑布流顶部空白

```
     StaggeredGridLayoutManager staggeredGridLayoutManager = new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
        staggeredGridLayoutManager.setGapStrategy(StaggeredGridLayoutManager.GAP_HANDLING_NONE);
        mRvRecommendList.setLayoutManager(staggeredGridLayoutManager);
        mRecommendAdapter = new PostDetailRecommendAdapter(mActivity);
        mRvRecommendList.addItemDecoration(new StaggeredDividerItemDecoration(mActivity, 2, DipUtil.getIntDip(10), DipUtil.getIntDip(10)));
        mRvRecommendList.setIAdapter(mRecommendAdapter);
 mRvRecommendList.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(@NonNull RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                if (newState == RecyclerView.SCROLL_STATE_IDLE) {
                    RecyclerView.LayoutManager layoutManager = recyclerView.getLayoutManager();
                    if (layoutManager instanceof StaggeredGridLayoutManager) {
                        //防止第一行到顶部有空白区域 滑动停止，刷新布局与分割线
                        ((StaggeredGridLayoutManager) recyclerView.getLayoutManager()).invalidateSpanAssignments();
                        recyclerView.invalidateItemDecorations();
                    }
                }
            }
        });
```

### 22.AppCompatXX视图组件在5.0以下系统使用的问题

```
上面虽然解决了设置android:onClick属性崩溃的问题，但是还有一个更加严重的问题，那就是上面提到的getContext()函数的返回值问题！
由于在5.0以下使用AppCompat组件时，getContext()方法返回的是TintContextWrapper这样一个类的实例，所以类似getContext() instanceOf XXActivity的调用全部为false，会导致已有代码需要不小的改动。
https://www.jianshu.com/p/7b47180f1937

class WidgetUtil {
    static View onCreateView(View view, String name, Context context, AttributeSet attrs) {
        //5.0以上系统没有问题，直接返回
        if (Build.VERSION.SDK_INT >= 21) {
            return view;
        }

        //查找是否设置了android:onClick属性，没有设置直接返回
        TypedArray a = context.obtainStyledAttributes(attrs, new int[]{android.R.attr.onClick});
        String handlerName = a.getString(0);
        if (handlerName == null) {
            return view;
        }

        //view一般来说都是null
        if (view == null) {
            //这里注意，LayoutInflater.from(context)一定要使用这个context，不要使用BaseActivity作为参数，否则可能出问题。
            view = WidgetUtil.getViewByName(LayoutInflater.from(context), name, attrs);
            if (view == null) {
                return null;
            }
        }

        //重新给view设置监听器
        view.setOnClickListener(new DeclaredOnClickListener(view, handlerName));
        a.recycle();
        return view;
    }

    @Nullable
    private static View getViewByName(LayoutInflater inflater, String name, AttributeSet attrs) {
        if (TextUtils.isEmpty(name)) {
            return null;
        }

        /*
        过滤自己App中定义的各个View的基类
         */
//        String[] parts = name.split("\\.");
//        String viewName = parts[parts.length - 1];
//        if (!viewName.startsWith("Custom")) {
//            return null;
//        }

        try {
            //调用LayoutInflater的方法来创建view，其实就是通过反射来创建
            return inflater.createView(name, null, attrs);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return null;
    }

    //监听器的源码拷贝自5.0以上View#DeclaredOnClickListener类
    private static class DeclaredOnClickListener implements View.OnClickListener {
        private final View mHostView;
        private final String mMethodName;

        private Method mResolvedMethod;
        private Context mResolvedContext;

        DeclaredOnClickListener(@NonNull View hostView, @NonNull String methodName) {
            mHostView = hostView;
            mMethodName = methodName;
        }

        @Override
        public void onClick(@NonNull View v) {
            if (mResolvedMethod == null) {
                resolveMethod(mHostView.getContext(), mMethodName);
            }

            try {
                mResolvedMethod.invoke(mResolvedContext, v);
            } catch (IllegalAccessException e) {
                throw new IllegalStateException(
                        "Could not execute non-public method for android:onClick", e);
            } catch (InvocationTargetException e) {
                throw new IllegalStateException(
                        "Could not execute method for android:onClick", e);
            }
        }

        private void resolveMethod(@Nullable Context context, @NonNull String name) {
            while (context != null) {
                try {
                    if (!context.isRestricted()) {
                        Method method = context.getClass().getMethod(name, View.class);
                        if (method != null) {
                            mResolvedMethod = method;
                            mResolvedContext = context;
                            return;
                        }
                    }
                } catch (NoSuchMethodException e) {
                    // Failed to find method, keep searching up the hierarchy.
                }

                if (context instanceof ContextWrapper) {
                    context = ((ContextWrapper) context).getBaseContext();
                } else {
                    // Can't search up the hierarchy, null out and fail.
                    context = null;
                }
            }

            int id = mHostView.getId();
            String idText = id == View.NO_ID ? "" : " with id '"
                    + mHostView.getContext().getResources().getResourceEntryName(id) + "'";
            throw new IllegalStateException("Could not find method " + name
                    + "(View) in a parent or ancestor Context for android:onClick "
                    + "attribute defined on view " + mHostView.getClass() + idText);
        }
    }
}
```

### 23.图片

```
setImageDrawable(mActivity.getResources().getDrawable(R.drawable.circle_f5f5f5_bg))
```

### 24.viewpager图片等于2的问题

解决方法：

```
1.当图片为2时
解决方法一:
(1)viewpager 
adapter.addAll(list);
 adapter.addAll(list);
(2)MallBannerCustomeNavigator   
customNavigator.setCircleCount(list.size());
解决方法二:
View view = inflate.inflate(,);//去除Array<View>(),去除判空
```

### 25.Android 沉浸式状态栏实践

1.顶部是ImageView这种需要将其填充到状态栏 

2.顶部是ActionBar这种不需要填充到状态栏 

第一步： 在BaseActivity里面实现如下代码 ：

```
public abstract class BaseActivity extends AppCompatActivity  {

    @Override
    protected final void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setStatusBar();
    }

    protected void setStatusBar() {
        //这里做了两件事情，1.使状态栏透明并使contentView填充到状态栏 2.预留出状态栏的位置，防止界面上的控件离顶部靠的太近。这样就可以实现开头说的第二种情况的沉浸式状态栏了 
        StatusBarUtil.setTransparent(this);
    }
}
```

 看下`StatusBarUtil.setTransparent(this)`具体的实现 

```
    /**
     * 设置状态栏全透明
     *
     * @param activity 需要设置的activity
     */
    public static void setTransparent(Activity activity) {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.KITKAT) {
            return;
        }
        transparentStatusBar(activity);
        setRootView(activity);
    }

    /**
     * 使状态栏透明
     */
    @TargetApi(Build.VERSION_CODES.KITKAT)
    private static void transparentStatusBar(Activity activity) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
            activity.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
            //需要设置这个flag contentView才能延伸到状态栏
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION);
            //状态栏覆盖在contentView上面，设置透明使contentView的背景透出来
            activity.getWindow().setStatusBarColor(Color.TRANSPARENT);
        } else {
            //让contentView延伸到状态栏并且设置状态栏颜色透明
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        }
    }

   /**
     * 设置根布局参数
     */
    private static void setRootView(Activity activity) {
        ViewGroup parent = (ViewGroup) activity.findViewById(android.R.id.content);
        for (int i = 0, count = parent.getChildCount(); i < count; i++) {
            View childView = parent.getChildAt(i);
            if (childView instanceof ViewGroup) {
                childView.setFitsSystemWindows(true);
                ((ViewGroup) childView).setClipToPadding(true);
            }
        }
    }
```

经过这样的设置基本上大部分界面都适配了沉浸式状态栏了，针对需要适配imageView填充到状态栏或者需要单独设置状态栏颜色和透明度的我们可以在BaseActivity 的子类重写setStatusBar方法来单独适配。

 **第二步：** 

1. 先说简单的，单独设置状态栏颜色和透明度这种情况 

   ```
       //重写这个方法
       protected void setStatusBar() {
           StatusBarUtil.setColor(...);
       }
   
       /**
        * 设置状态栏颜色
        *
        * @param activity       需要设置的activity
        * @param color          状态栏颜色值
        * @param statusBarAlpha 状态栏透明度
        */
   
       public static void setColor(Activity activity, @ColorInt int color, @IntRange(from = 0, to = 255) int statusBarAlpha) {
           //5.0以上版本直接设置状态栏颜色透明度
           if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
               activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
               activity.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
               activity.getWindow().setStatusBarColor(calculateStatusColor(color, statusBarAlpha));
           //4.4-5.0版本通过伪装一个状态栏来设置颜色和透明度
           } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
               activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
               ViewGroup decorView = (ViewGroup) activity.getWindow().getDecorView();
               View fakeStatusBarView = decorView.findViewById(FAKE_STATUS_BAR_VIEW_ID);
               if (fakeStatusBarView != null) {
                   if (fakeStatusBarView.getVisibility() == View.GONE) {
                       fakeStatusBarView.setVisibility(View.VISIBLE);
                   }
                   fakeStatusBarView.setBackgroundColor(calculateStatusColor(color, statusBarAlpha));
               } else {
                   decorView.addView(createStatusBarView(activity, color, statusBarAlpha));
               }
               setRootView(activity);
           }
       }
   
       /**
        * 计算状态栏颜色
        *
        * @param color color值
        * @param alpha alpha值
        * @return 最终的状态栏颜色
        */
       private static int calculateStatusColor(@ColorInt int color, int alpha) {
           if (alpha == 0) {
               return color;
           }
           float a = 1 - alpha / 255f;
           int red = color >> 16 & 0xff;
           int green = color >> 8 & 0xff;
           int blue = color & 0xff;
           red = (int) (red * a + 0.5);
           green = (int) (green * a + 0.5);
           blue = (int) (blue * a + 0.5);
           return 0xff << 24 | red << 16 | green << 8 | blue;
       }
   
       /**
        * 生成一个和状态栏大小相同的半透明矩形条
        *
        * @param activity 需要设置的activity
        * @param color    状态栏颜色值
        * @param alpha    透明值
        * @return 状态栏矩形条
        */
       private static View createStatusBarView(Activity activity, @ColorInt int color, int alpha) {
           // 绘制一个和状态栏一样高的矩形
           View statusBarView = new View(activity);
           LinearLayout.LayoutParams params =
                   new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, getStatusBarHeight(activity));
           statusBarView.setLayoutParams(params);
           statusBarView.setBackgroundColor(calculateStatusColor(color, alpha));
           statusBarView.setId(FAKE_STATUS_BAR_VIEW_ID);
           return statusBarView;
       }
   
       /**
        * 获取状态栏高度
        *
        * @param context context
        * @return 状态栏高度
        */
       public static int getStatusBarHeight(Context context) {
           // 获得状态栏高度
           int resourceId = context.getResources().getIdentifier("status_bar_height", "dimen", "android");
           return context.getResources().getDimensionPixelSize(resourceId);
       }
   ```

   2.imageView这种需要填充到状态栏的情况 

   ```
       //重写这个方法
       protected void setStatusBar() {
           StatusBarUtil.setTransparentForImageView(...);
       }
   
       /**
        * 为头部是 ImageView 的界面设置状态栏全透明
        *
        * @param activity       需要设置的activity
        * @param needOffsetView 需要向下偏移的 View
        */
       public static void setTransparentForImageView(Activity activity, View needOffsetView) {
           setTranslucentForImageView(activity, 0, needOffsetView);
       }
   
      /**
        * 为头部是 ImageView 的界面设置状态栏透明
        *
        * @param activity       需要设置的activity
        * @param statusBarAlpha 状态栏透明度
        * @param needOffsetView 需要向下偏移的 View
        */
       public static void setTranslucentForImageView(Activity activity, @IntRange(from = 0, to = 255) int statusBarAlpha,
                                                     View needOffsetView) {
           if (Build.VERSION.SDK_INT < Build.VERSION_CODES.KITKAT) {
               return;
           }
           setTransparentForWindow(activity);
           addTranslucentView(activity, statusBarAlpha);
           //虽然imageView需要填充到状态栏，但是上面的文字按钮等控件并不需要填充到状态栏，所以需要设置偏移
           if (needOffsetView != null) {
               Object haveSetOffset = needOffsetView.getTag(TAG_KEY_HAVE_SET_OFFSET);
               if (haveSetOffset != null && (Boolean) haveSetOffset) {
                   return;
               }
               ViewGroup.MarginLayoutParams layoutParams = (ViewGroup.MarginLayoutParams) needOffsetView.getLayoutParams();
               layoutParams.setMargins(layoutParams.leftMargin, layoutParams.topMargin + getStatusBarHeight(activity),
                       layoutParams.rightMargin, layoutParams.bottomMargin);
               needOffsetView.setTag(TAG_KEY_HAVE_SET_OFFSET, true);
           }
       }
   
       /**
        * 设置透明
        */
       private static void setTransparentForWindow(Activity activity) {
           if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
               activity.getWindow().setStatusBarColor(Color.TRANSPARENT);
               activity.getWindow()
                       .getDecorView()
                         //两个 flag 要结合使用，表示让应用的主体内容占用系统状态栏的空间
                       .setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_STABLE | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
           } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
               activity.getWindow()
                       .setFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS, WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
           }
       }
   
       /**
        * 添加半透明矩形条
        *
        * @param activity       需要设置的 activity
        * @param statusBarAlpha 透明值
        */
       private static void addTranslucentView(Activity activity, @IntRange(from = 0, to = 255) int statusBarAlpha) {
           ViewGroup contentView = (ViewGroup) activity.findViewById(android.R.id.content);
           View fakeTranslucentView = contentView.findViewById(FAKE_TRANSLUCENT_VIEW_ID);
           if (fakeTranslucentView != null) {
               if (fakeTranslucentView.getVisibility() == View.GONE) {
                   fakeTranslucentView.setVisibility(View.VISIBLE);
               }
               fakeTranslucentView.setBackgroundColor(Color.argb(statusBarAlpha, 0, 0, 0));
           } else {
               contentView.addView(createTranslucentStatusBarView(activity, statusBarAlpha));
           }
       }
   
       /**
        * 创建半透明矩形 View
        *
        * @param alpha 透明值
        * @return 半透明 View
        */
       private static View createTranslucentStatusBarView(Activity activity, int alpha) {
           // 绘制一个和状态栏一样高的矩形
           View statusBarView = new View(activity);
           LinearLayout.LayoutParams params =
                   new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, getStatusBarHeight(activity));
           statusBarView.setLayoutParams(params);
           statusBarView.setBackgroundColor(Color.argb(alpha, 0, 0, 0));
           statusBarView.setId(FAKE_TRANSLUCENT_VIEW_ID);
           return statusBarView;
       }
   ```

    **最后小结一下思路：** 

   首先在基类设置状态栏透明，让contentView填充到状态栏，利用contentView的背景作为状态栏背景直接实现沉浸的效果，然后利用setFitsSystemWindows(true)和setClipToPadding(true)对控件预留出状态栏的位置。

   如果需要单独设置状态栏颜色或者透明度，比如contentView的背景色和ActionBar背景不一致的情况，5.0以上通过设置setStatusBarColor实现，4.4-5.0通过伪装状态栏实现。

   最后如果需要实现类似图片这种需要填充到状态栏的情况，通过setTransparentForWindow实现，然后对其他控件重设MarginLayoutParams来实现预留出状态栏位置的效果。

   经过上面的一些设置已经覆盖了绝大部分情况，对于Fragment，DrawerLayout，CoordinatorLayout等可能需要特殊处理的这里贴上gitgub地址： https://github.com/laobie/StatusBarUtil 
   
   

## 十四、Android2

### 1.自定义recycleview刷新与加载更多

1.获取数据

```
 public void showRecommendList(List<RecommendPost> result, int total) {
        if (result != null) {
           //有数据移除loading
            loadMoreFooterView.setStatus(LoadMoreFooterView.Status.GONE);
            totalSize = total;
            if (recommendPageIndex == 1) {
                if (result.size() == 0) {
                    mLlRecommendTitleLayout.setVisibility(View.GONE);
                } else {
                    mLlRecommendTitleLayout.setVisibility(View.VISIBLE);
                    mRecommendAdapter.clear();
                    mRecommendAdapter.addAll(result);
                    mRecommendAdapter.notifyDataSetChanged();
                }
            } else {
                int count = mRecommendAdapter.getItemCount();
                mRecommendAdapter.addAll(result);
                mRecommendAdapter.notifyItemRangeChanged(count,                                        mCommentAdapter.getItemCount());
            }
        } else {
            loadMoreFooterView.setStatus(LoadMoreFooterView.Status.THE_END);
        }
        mRvRecommendList.setRefreshing(false);
    }
```

2.加载更多

```
mRvRecommendList.setOnLoadMoreListener(() -> {
            if (loadMoreFooterView.canLoadMore()) {
                if (mRecommendAdapter.getItemCount() >= totalSize) {
                    if (totalSize == 0) {
                        loadMoreFooterView.setStatus(LoadMoreFooterView.Status.GONE);
                    } else {
                      loadMoreFooterView.setStatus(LoadMoreFooterView.Status.THE_END);
                    }
                } else {
                    getRecommendList();
                    loadMoreFooterView.setStatus(LoadMoreFooterView.Status.LOADING);
                }
            }
        });
```

```
 public void setStatus(Status status) {
        this.mStatus = status;
        change();
    }
```

```
 private void change() {
        switch (mStatus) {
            case GONE:
                mLoadingView.setVisibility(GONE);
                mTheEndView.setVisibility(GONE);
                break;

            case LOADING:
                mLoadingView.setVisibility(VISIBLE);
                mTheEndView.setVisibility(GONE);
                break;

            case ERROR:
                mLoadingView.setVisibility(GONE);
                mTheEndView.setVisibility(GONE);
                break;

            case THE_END:
                mLoadingView.setVisibility(GONE);
                mTheEndView.setVisibility(VISIBLE);
                break;
        }
    }
```

### 2.recycleview 双流

```
loadMoreFooterView = (LoadMoreFooterView) mRvRecommendList.getLoadMoreFooterView();
        StaggeredGridLayoutManager staggeredGridLayoutManager = new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
        staggeredGridLayoutManager.setGapStrategy(StaggeredGridLayoutManager.GAP_HANDLING_NONE);
        mRvRecommendList.setLayoutManager(staggeredGridLayoutManager);
        mRecommendAdapter = new PostDetailRecommendAdapter(mActivity);
        mRvRecommendList.addItemDecoration(new StaggeredDividerItemDecoration(mActivity, 2, DipUtil.getIntDip(10), DipUtil.getIntDip(10)));
        mRvRecommendList.setIAdapter(mRecommendAdapter);
        mRvRecommendList.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(@NonNull RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                try {
                    if (newState == RecyclerView.SCROLL_STATE_IDLE) {
                        RecyclerView.LayoutManager layoutManager =     recyclerView.getLayoutManager();
                        if (layoutManager instanceof StaggeredGridLayoutManager) {
                            //防止第一行到顶部有空白区域 滑动停止，刷新布局与分割线
                            ((StaggeredGridLayoutManager)                          recyclerView.getLayoutManager()).invalidateSpanAssignments();
                            recyclerView.invalidateItemDecorations();
                            StaggeredGridLayoutManager linearManager =    (StaggeredGridLayoutManager) layoutManager;
                            int[] firstVisiblePositionArray =    linearManager.findFirstVisibleItemPositions(new int[3]);
                            int[] lastVisiblePositionArray =   linearManager.findLastVisibleItemPositions(new int[3]);
                            int first = firstVisiblePositionArray[0];
                            if (first == 1) {
                                first = 0;
                            }
                            if (first > 1) {
                                first -= 2;
                            }
                            if (first < mRecommendAdapter.getItemCount()) {                        statisticalBrowseCount(mRecommendAdapter.get(first).getPost().getId());
                            }
                            int last1 = lastVisiblePositionArray[0];
                            int last2 = lastVisiblePositionArray[1];
                            int last = Math.max(last1, last2);
                            if (last > 1) {
                                last -= 2;
                            }
                            if (last < mRecommendAdapter.getItemCount()) {                                   statisticalBrowseCount(mRecommendAdapter.get(last).getPost().getId());
                            }
                            int middle = last - first;
                            if (middle > 1 && first + 1 <       mRecommendAdapter.getItemCount()) {
                                statisticalBrowseCount(mRecommendAdapter.get(first + 1).getPost().getId());
                            }
                        }
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
```

### 3.git新建分支

1.git branch fix-target  //新建分支

2.git checkout -b fix-target //创建一个分支，并切换到这个分支

3.git branch -d fix-target  //删除分支

4.git branch -D fix-target  //强制删除，不需要旧分支上的所有东西

5.git checkout -B fix-target  //强制删除原有分支，新建一个同名分支

6.git status  //分支代码状态

7.git add .  //添加分支所有代码

8.git commit -m "提交分支说明"

9.git checkout fix-target //切换分支

10.git merge --no-ff -m "merge mk-wyj --->market" mk-wyj    //添加到market

11.git push // 上传代码

12.git reset --soft HEAD^ //git commit 后想要撤销 

git  add  readme.txt //添加指定文件
git restore  --staged readme.txt //恢复添加过的指定文件
git restore reset.txt //恢复未添加的文件
git reset --hard HEAD^ //恢复到上个版本
git reset --hard commit_id //恢复到commit版本
git reflog //查看命令历史
git log//查看提交历史

13.保护场景
git stash
git stash list
       stash@{0}
git checkout market
git pull
       14.有数据 
git checkout mk-wyj
git merge --no-ff -m "merge market--->mk-wyj" market
git stash list
       stash@{0}
git stash apply stash@{0}

15.git 更新main branch

  a. git commit -m ""xxx"   //first commit

  b. git fetch origin   //更新远程分支

  c. git pull --rebase --log origin {main branch}  //rebase remote main branch, not local main branch   

### 4.WheelView及其他简介

https://www.jianshu.com/p/1be7c404e628

https://github.com/SenhLinsh/Android-Hot-Libraries

### 5.RxView

```
RxView.clicks(mFlToTopLayout).throttleFirst(800, TimeUnit.MILLISECONDS)
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(unit -> {
            //置顶
            mAppBarLayout.offsetTopAndBottom(0);
        });
```

### 6.折叠布局滑到顶部

```
mAppBarLayout.setExpanded(true, false);
behavior.setTopAndBottomOffset(-scrollDistanceY);
mRvRecommendProduct.scrollToPosition(0);
```

### 7.post()

```
 handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
        tv_word.setVisibility(View.GONE);
                    }
                },3000);
```

1.开启的runnable会在这个handler所依附线程中运行，而这个handler是在UI线程中创建的，所以
自然地依附在主线程中了。

2.postDelayed(new Runnable()) 而没有重新生成新的 New Thread（）

3.通常来说handler.postDelayed(new Runnable()){ }是为了刷新UI用的，handler实例化在主线程，postDelayed(new Runnable()){ }方法又依附于handler所在线程，所以就运行于主线程。即使这样我还是不理解，**new Runnable（）不是新开了一个线程吗**？

4.**原来我弄错了，如果是implements Runnable，像extends Thread一样，是新开了线程，但是现在只是new Runnable（）{},是把Runnable对象以Message形式post到UI线程里的Looper中执行**，现在真相大白了，知识点太容易混淆了。

8.DialogFragment崩溃 Can not perform this action after onSaveInstanceState

```
        //重写DialogFragment的show方法
        //可以查看showAllowingStateLoss不过被hide
        //通过反射拿到这两个变量进行设置
       try {
            val mDismissed = DialogFragment::class.java.getDeclaredField("mDismissed")
            mDismissed.isAccessible = true
            mDismissed.set(this, false)
            val mShownByMe = DialogFragment::class.java.getDeclaredField("mShownByMe")
            mShownByMe.isAccessible = true
            mShownByMe.set(this, true)
            manager.beginTransaction()
                    .add(this, tag)
                    .commitAllowingStateLoss()
            }
        } catch (e: Throwable) {
            LogUtils.eTag(TAG, e.toString())
        }
```

### 8.SpannableString

```
 *
 * @param content        文本内容
 * @param tagName        话题tag
 * @param highlightColor 文本高亮颜色，默认蓝色(#0096FF)
 * @param listener       话题tag点击事件
 * @return rich text with with highlight text by "#"
 */
public static SpannableString getStringWithTag(String content, String tagName, int highlightColor, OnTagClickListener listener) {
    String tagContent = "#" + tagName + "# ";
    String allContent = tagContent + content;
    SpannableString spannableString = new SpannableString(allContent);

    int startIndex = 0;
    int endIndex = tagContent.length() - 1;

    ClickableSpan clickableSpan = new ClickableSpan() {
        @Override
        public void onClick(@NonNull View widget) {
            if (listener != null) {
                listener.onTagClick();
            }
        }

        @Override
        public void updateDrawState(@NonNull TextPaint ds) {
            ds.setColor(highlightColor == 0 ? Color.parseColor("#0096FF") : highlightColor);
        }
    };

    spannableString.setSpan(clickableSpan, startIndex, endIndex, Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
    return spannableString;
}
```

a.(new StyleSpan(style)  // Typeface.BOLD粗、Typeface.BOLD_ITALIC粗斜体 

b.new UnderlineSpan()  //下划线

c.new StrikethroughSpan() //设置字体删除线 

d.new ScaleXSpan(scale) //放大系数

e.new SubscriptSpan() //设置下标

f.(new BackgroundColorSpan(Color.parseColor(color)) //背景色

g.new ForegroundColorSpan(color) //前景色

h.new RelativeSizeSpan(relativeSize) //相对大小

i.new AbsoluteSizeSpan(DisplayUtil.sp2px(context, spSize)) //设置字体

### 9.appBarLayout是否折叠

```
float alpha = (float) Math.abs(verticalOffset) / (float) appBarLayout.getTotalScrollRange();
```

### 10.setClipToPadding

```
mRecyclerView.setPadding(0, DipUtil.getIntDip(5), 0, 0);
mRecyclerView.setClipToPadding(false);
```

### 11.过滤表情

```
    /**
     * 新过滤Emoji标签
     *
     * @param context   Context
     * @param editTexts EditText
     */
    public static void setEmojiExcludeFilter(Context context, EditText... editTexts) {
        for (EditText editText : editTexts) {
            editText.setFilters(new InputFilter[]{new EmojiExcludeFilter(context)});
        }
    }
```

```
public class EmojiExcludeFilter implements InputFilter {
    private Context mContext = null;

    public EmojiExcludeFilter(Context context) {
        mContext = context;
    }

    @Override
    public CharSequence filter(CharSequence source, int start, int end, Spanned dest, int dstart, int dend) {
        for (int i = start; i < end; i++) {
            int type = Character.getType(source.charAt(i));
            if (type == Character.SURROGATE || type == Character.OTHER_SYMBOL) {
                Toast.makeText(mContext.getApplicationContext(), "不支持输入表情", Toast.LENGTH_SHORT).show();
                return "";
            }
        }
        return null;
    }

}

```

### 12.图片加载

```
 /**
     * 加载圆形图片带外框，颜色,圆角，listener
     * @param context 上下文
     * @param url 图片地址
     * @param imageView 目标ImageView
     * @param borderWith 外圈圆宽度
     * @param borderColor 外圈圆颜色
     * @param radius 圆角
     * @param listener 监听
     */
    @Override
    public void loadImageByRoundBorder(Object context, String url, ImageView imageView, float borderWith, int borderColor, float radius, IResourceTarget listener) {
        RequestOptions options = new RequestOptions()
                .error(R.drawable.loading_error)
                .diskCacheStrategy(DiskCacheStrategy.RESOURCE)
                .centerCrop()
                .transforms(TranformUtil.withRoundRectBorder(borderWith, ContextCompat.getColor((Context) context, borderColor), radius));
        getRequestManager(context)
                .load(url)
                .apply(options)
                .addListener(new RequestListener<Drawable>() {
                    @Override
                    public boolean onLoadFailed(@Nullable GlideException e, Object model, Target<Drawable> target, boolean isFirstResource) {
                        if (listener != null) {
                            listener.onLoadFailed(e, model, target, isFirstResource);
                        }
                        return false;
                    }

                    @Override
                    public boolean onResourceReady(Drawable resource, Object model, Target<Drawable> target, DataSource dataSource, boolean isFirstResource) {
                        if (listener != null) {
                            listener.onResourceReady(resource, model, target, dataSource, isFirstResource);
                        }
                        return false;
                    }
                })
                .transition(withCrossFade())
                .into(imageView);
    }
```

```
public class GlideRoundRectBorderTransform extends BitmapTransformation {
```

13.Matrix做变换

(1) 变换方式

a.创建Matrix对象

b.调用Matrix的pre/postTranslate/rotate/scale/skew()(错切)方法来设置几何变换

c.Canvas.setMatrix)(matrix)或Canvas.concat(matrix)来把几何变换应用到canvas

```
Matrix matrix = new Matrix();

...

matrix.reset();
matrix.postTranslate();
matrix.postRotate();

canvas.save();
canvas.concat(matrix);
canvas.drawBitmap(bitmap, x, y, paint);
canvas.restore();
```

d.canvas.setMatrix(matrix) : 用Matrix直接替换Canvas当前的变换矩阵，即抛弃canvas当前的变换，改用当前的变换（）不同的系统中 `setMatrix(matrix)` 的行为可能不一致，所以还是尽量用 `concat(matrix)` 

e.Canvas.concat(matrix) : 用canvas当前的变换矩阵和matrix相乘，及基于canvas当前的变换，叠加上matrix中的变换

### 13.图片宽高比

```
    /**
     * 获取帖子图片宽高比
     *
     * @param imgUrl 图片地址
     * @return int
     */
    public static float getPostPhotoAspectRatio(String imgUrl) {
        if (imgUrl != null && !"".equals(imgUrl)) {
            String aspectRatio;
            if (imgUrl.toLowerCase().contains(".jpg") || imgUrl.toLowerCase().contains("jpeg") || imgUrl.toLowerCase().contains("png")) {
                aspectRatio = imgUrl.substring(imgUrl.lastIndexOf("-") + 1, imgUrl.lastIndexOf("."));
            } else {
                aspectRatio = imgUrl.substring(imgUrl.lastIndexOf("-") + 1, imgUrl.length());
            }
            return strToObject(aspectRatio, 1.0f);
        }
        return 1f;
    }
```

```
 @SuppressWarnings("unchecked")
    public static <T extends Number> T strToObject(String s, T defaultValue) {
        try {
            if (defaultValue instanceof Integer) {
                return (T) Integer.valueOf(Integer.parseInt(s));
            } else if (defaultValue instanceof Long) {
                return (T) Long.valueOf(Long.parseLong(s));
            } else if (defaultValue instanceof Short) {
                return (T) Short.valueOf(Short.parseShort(s));
            } else if (defaultValue instanceof Float) {
                return (T) Float.valueOf(Float.parseFloat(s));
            } else if (defaultValue instanceof Double) {
                return (T) Double.valueOf(Double.parseDouble(s));
            }
        } catch (NullPointerException e) {
            e.printStackTrace();
        } catch (NumberFormatException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return defaultValue;
    }
```

### 14.SparseArray

(1) 什么是SparseArray？

应用场景是相对稀少的数据，一般是几百以内的数据性能相对HashMap要好，大概提升0-50%的性能

(2) SparseArray采用说明数据结构？

SparseArray并不想HashMap采用一维数据+单链表结构，而是采用两个一维数组，一个存储key，一个存储value

```
private int[] mKeys;
private Object[] mValues;
```

(3) SparseArray默认容量多大？

答案是10，SparseArray并没有定义默认容量的大小，而是在默认构造函数中，指定其默认容量大小

```
public SparseArray() {
        this(10);
}
```

(4) SparseArray最大容量？每次扩容多少？

SparseArray不像HashMap一样定义有最大容量是多少，最大可以达到Integer.MAX_VALUE，可能会报oom.每次扩容时如果当前容量小于5则扩容是8，采用的是二分法算法

(5) SparseArray和HashMap的应用场景选取

a.如果对内存要求比较高，而对查询效率没有什么大的要求，可以使用SparseArray

b.数量在百级别的SparseArray比HashMap有更好的优势

c.要求key是int类型的，因为HashMap会对int自定装箱变成Integer类型

d.要求key是有序的且是升序

### 15.Camera三位变换

(1) 四个方法：rotateX(deg) rotateY(deg) rotateZ(deg) rotate(x,y,z)

```
canvas.save();

camera.rotateX(30); // 旋转 Camera 的三维空间
camera.applyToCanvas(canvas); // 把旋转投影到 Canvas

canvas.drawBitmap(bitmap, point1.x, point1.y, paint);
canvas.restore();
```

另外，`Camera` 和 `Canvas` 一样也需要保存和恢复状态才能正常绘制，不然在界面刷新之后绘制就会出现问题。所以上面这张图完整的代码应该是这样的：

```
canvas.save();

camera.save(); // 保存 Camera 的状态
camera.rotateX(30); // 旋转 Camera 的三维空间
camera.applyToCanvas(canvas); // 把旋转投影到 Canvas
camera.restore(); // 恢复 Camera 的状态

canvas.drawBitmap(bitmap, point1.x, point1.y, paint);
canvas.restore();
```

(2) 

### 16.设计模式

单一职责原则告诉我们实现类要职责单一；

里氏替换原则告诉我们不要破坏继承体系；

依赖倒置原则告诉我们要面向接口编程；

接口隔离原则告诉我们在设计接口的时候要精简单一；

迪米特法则告诉我们要降低耦合。

而开闭原则是总纲，他告诉我们要对扩展开放，对修改关闭。

### 17.recycleView的优化

当知道Adapter内Item的改变不会影响RecyclerView宽高的时候，可以设置为true让RecyclerView避免重新计算大小。

首先是onMeasure里用到，这个和自定义LayoutManager相关，先不管它。然后是triggerUpdateProcessor()方法用到了，请看源码：

```
        void triggerUpdateProcessor() {  
            if (POST_UPDATES_ON_ANIMATION && mHasFixedSize && mIsAttached) {  
                ViewCompat.postOnAnimation(RecyclerView.this, mUpdateChildViewsRunnable);  
            } else {  
                mAdapterUpdateDuringMeasure = true;  
                requestLayout();  
            }  
        }
```

然后看一下triggerUpdateProcessor()方法被哪些调用法：

onItemRangeChanged()

onItemRangeInserted()

onItemRangeRemoved()

onItemRangeMoved()

这样看就很明白了，当调用Adapter的增删改插方法，最后就会根据mHasFixedSize这个值来判断需要不需要requestLayout()；所以这4个方法不会重新绘制。
 那我们再来看一下notifyDataSetChanged()执行的代码，最后是调用了onChanged，调用了requestLayout()，会去重新测量宽高，所以这也是为什么我们设置为true时，大小还是重新计算了的原因。

```
@Override  
public void onChanged() {  
    assertNotInLayoutOrScroll(null);  
    mState.mStructureChanged = true;  
  
    setDataSetChangedAfterLayout();  
    if (!mAdapterHelper.hasPendingUpdates()) {  
        requestLayout();  
    }  
} 
```

总结：

当我们确定Item的改变不会影响RecyclerView的宽高的时候可以设置setHasFixedSize(true)，并通过Adapter的增删改插方法去刷新RecyclerView，而不是通过notifyDataSetChanged()。（其实可以直接设置为true，当需要改变宽高的时候就用notifyDataSetChanged()去整体刷新一下）

### 18.Gradle

1.build.gralde里的classpath 'com.android.tools.build:gradle:3.0.1'指的是Android Studio的gradle插件版本，

而gradle-wrapper.properties里的distributionUrl=https\://services.gradle.org/distributions/gradle-4.4-all.zip才是指定的gradle版本！

Gradle是开源的自动化构建工具，

而Gradle插件是google开发的在Android Studio中使用Gradle的插件。

2.gradle 插件版本、gradle版本 和buildToolsVersion之间的对应关系:

https://www.jianshu.com/p/df8d7b872487

3、Gradle插件又是什么？

Gradle插件是针对Gradle发行版和Android SDK Build Tools封装的一个工具，主要有两大功能：

1. 调用Gradle本身的代码和批处理工具来构建项目
2. 调用Android SDK的编译、打包功能

### 19.targetSDKVersion

版本号的大小关系就是：compileSdkVersion > targetSdkVersion > minSdkVersion

        简单来说就代表着你的App能够适配的系统版本，意味着你的App在这个版本的手机上做了充分的 前向 兼容性处理和实际测试。其实我们写代码时都是经常干这么一件事，就是 if(Build.VERSION.SDK_INT >= 23) { … } ，这就是兼容性处理最典型的一个例子。如果你的target设置得越高，其实调用系统提供的API时，所得到的处理也是不一样的，甚至有些新的API是只有新的系统才有的
Android6.0普通权限normal permission 和 危险权限dangerous permission
Normal Permission：写在xml文件里，那么App安装时就会默认获得这些权限，即使是在Android6.0系统的手机上，用户也无法在安装后动态取消这些normal权限，这和以前的权限系统是一样的，不变。
Dangerous Permission：还是得写在xml文件里，但是App安装时具体如果执行授权分以下几种情况：
1、targetSDKVersion < 23 & API(手机系统) < 6.0 ：安装时默认获得权限，且用户无法在安装App之后取消权限。
3、targetSDKVersion < 23 & API(手机系统) >= 6.0 ：安装时默认获得权限，但是用户可以在安装App完成后动态取消授权（ 取消时手机会弹出提醒，告诉用户这个是为旧版手机打造的应用，让用户谨慎操作 ）。
2、targetSDKVersion >= 23 & API(手机系统) < 6.0 ：安装时默认获得权限，且用户无法在安装App之后取消权限。
4、targetSDKVersion >= 23 & API(手机系统) >= 6.0 ：安装时不会获得权限，可以在运行时向用户申请权限。用户授权以后仍然可以在设置界面中取消授权，用户主动在设置界面取消后，在app运行过程中可能会出现crash。

### 20.为何SurfaceView能够在非UI线程中刷新界面

SurfaceView与View的刷新方法都是一样的，通过lockCanvas和unlockCanvasAndPost方法来进行画的，但SurfaceView能在UI线程中刷新，也能在其它线程中刷新，而View只能在UI线程中刷新，View的刷新有一个checkThread(在ViewRootImp.java中)的判断，如果不是在UI线程中就会抛异常， 这是google人为这样设计的，不让其它线程刷新View，SurfaceView就不会进行判断，这样它就可以在其它线程中进行刷新。

### 21.Handler中有loop死循环，为什么没有阻塞主线程，原理是什么

对于线程即是一段可执行的代码，当可执行代码执行完成后，线程生命周期便该终止了，线程退出。而对于主线程，我们是绝不希望会被运行一段时间，自己就退出，那么如何保证能一直存活呢？简单做法就是可执行代码是能一直执行下去的，死循环便能保证不会被退出，例如，binder线程也是采用死循环的方法，通过循环方式不同与Binder驱动进行读写操作，当然并非简单地死循环，无消息时会休眠。但这里可能又引发了另一个问题，既然是死循环又如何去处理其他事务呢？通过创建新线程的方式。真正会卡死主线程的操作是在回调方法onCreate/onStart/onResume等操作时间过长，会导致掉帧，甚至发生ANR，looper.loop本身不会导致应用卡死。

主线程的死循环一直运行是不是特别消耗CPU资源呢？ 其实不然，这里就涉及到Linux pipe/epoll机制，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。 Gityuan–Handler(Native层)

### 22.new handler的内存泄露

原因：我们知道在Java中，非静态内部类会隐性地持有外部类的引用，二静态内部类则不会。在下面的代码中，Message在消息队列中延时了10分钟，然后才处理该消息。而这个消息引用了Handler对象，Handler对象又隐性地持有了Activity的对象，当发生GC是以为 message – handler – acitivity 的引用链导致Activity无法被回收，

```
public class LeakCanaryActivity extends AppCompatActivity

    private  Handler mHandler;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mHandler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);

            }
        };

        Message message = Message.obtain();
        message.what = 1;
        mHandler.sendMessageDelayed(message,10*60*1000);
    }
}
```

解决方法一：使用WeakReference

```
public class NoLeakActivity extends AppCompatActivity {

    private NoLeakHandler mHandler;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mHandler = new NoLeakHandler(this);

        Message message = Message.obtain();

        mHandler.sendMessageDelayed(message,10*60*1000);
    }

    private static class NoLeakHandler extends Handler{
        private WeakReference<NoLeakActivity> mActivity;

        public NoLeakHandler(NoLeakActivity activity){
            mActivity = new WeakReference<>(activity);
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
        }
    }
}
```

解决方法二：及时清除消息

```
 @Override
    protected void onDestroy() {
        super.onDestroy();
        mHandler.removeCallbacksAndMessages(null);
    }
```

### 23.**SurfaceView和View的区别**

1.View 主要适用于主动更新的情况，而 surfaceView 主要适用于被动更新，例如频繁的刷新。
2.View 在主线程中对画面进行刷新，而 surfaceView 通常会通过一个子线程来进行页面的刷新
3.View 在绘图时没有使用双缓冲机制，而 surfaceView 在底层实现机制上就已经实现了双缓冲机制。
总结就是，如果你的自定义 View 需要频繁刷新，或者刷新时数据处理量很大，考虑用 SurfaceView 来替代 View。

4.SurfaceView拥有独立的Surface（绘图表面），即它不与其宿主窗口共享同一个Surface。
 一般来说，每一个窗口在SurfaceFlinger服务中都对应有一个Layer，用来描述它的绘图表面。对于那些具有SurfaceView的窗口来说，每一个SurfaceView在SurfaceFlinger服务中还对应有一个独立的Layer或者LayerBuffer，用来单独描述它的绘图表面，以区别于它的宿主窗口的绘图表面。
 因此SurfaceView的UI就可以在一个独立的线程中进行绘制，可以不会占用主线程资源。

### 24.ThreadPool的四种拒绝策略

RejectedExecutionHandler的四种拒绝策略：

1.ThreadPoolExecutor.AbortPolicy(默认策略):
当线程池中的数量等于最大线程数时抛出java.util.concurrent.RejectedExecutionException异常.涉及到该异常的任务也不会被执行.
2.ThreadPoolExecutor.CallerRunsPolicy():
当线程池中的数量等于最大线程数时,重试添加当前的任务;它会自动重复调用execute()方法
3.ThreadPoolExecutor.DiscardOldestPolicy():
当线程池中的数量等于最大线程数时,抛弃线程池中工作队列头部的任务(即等待时间最久Oldest的任务),并执行新传入的任务
4.ThreadPoolExecutor.DiscardPolicy():
当线程池中的数量等于最大线程数时,丢弃不能执行的新加任务

### 25.glide和imageLoader的区别

glide支持：图片加载优先级、使用okhttp加载、图片url和缓存key解耦（cdn请求case）、缓存跳过（模板图片更新case）、生命周期、缩略图支持、setTag问题

### 26.app启动过程

(1)冷启动

当启动应用时，后台没有该应用的进程，这时系统会重新创建一个新的进程分配给该应用。

特点：因为系统会重新创建一个新的进程分配给它，所以会创建和初始化Application，再创建和初始化它的Launch Activity(onCreate onMesure onLayout,onDraw)，最后展示在界面上

(2)热启动

当启动应用时，后台存在该应用的进程（back键，home键，应用退出，但是没有销毁），从已有的进程中启动

特点：从已有的进程中启动，不需要创建和初始化Application ,直接创建和初始化它的Launch Activity

两者区别：后台进程是否存在相应的进程，创建进程是耗时操作

(3)冷启动启动过程

包括：分配进程－>创建和初始化Application->创建和初始化Launch Activity

```undefined
整个应用程序的启动过程要执行很多步骤，但是整体来看，主要分为以下五个阶段：

一. Step1 - Step 11：
Launcher通过Binder进程间通信机制通知ActivityManagerService，
它要启动一个Activity；

二. Step 12 - Step 16：
ActivityManagerService通过Binder进程间通信机制通知Launcher进入Paused状态；

三. Step 17 - Step 24：
Launcher通过Binder进程间通信机制通知ActivityManagerService，它已经准备就绪进入Paused状态，
于是ActivityManagerService就创建一个新的进程，用来启动一个ActivityThread实例，
即将要启动的Activity就是在这个ActivityThread实例中运行；

四. Step 25 - Step 27：
ActivityThread通过Binder进程间通信机制将一个ApplicationThread类型的Binder对象传递给ActivityManagerService，
以便以后ActivityManagerService能够通过这个Binder对象和它进行通信；

五. Step 28 - Step 35：
ActivityManagerService通过Binder进程间通信机制通知ActivityThread，
现在一切准备就绪，它可以真正执行Activity的启动操作了。
```

`ActivityManagerService就创建一个新的进程，用来启动一个ActivityThread实例` --可以理解成Zygote进程中fork一个新的进程分配给应用.

Zygote详解：https://www.cnblogs.com/innost/archive/2011/01/26/1945769.html

### 27.app启动优化

a.很多第三方SDK都放在application初始化，我们可以将初始化的代码放到使用到的地方再进行相关操作，比如科大讯飞、百度地图这种SDK

b.SplashActivity中的布局尽量减少复杂性以及布局深度，因为在View绘制的过程中，测量也是很耗费性能的。也可以采用ViewStub的形式进行懒加载优化

c.不要在这两处方法中进行耗时操作。这些I/O操作也应该放到使用到的地方或子线程中执行。

d.对于多进程的应用来说，每新建一个进程就会初始化application一次，这样会造成application的onCreate()方法执行多次，所以需要考虑哪些初始化操作只要执行1次

```
public static String getProcessName(int pid) {  
    BufferedReader reader = null;   
    try {    
        reader = new BufferedReader(new FileReader("/proc/" + pid + "/cmdline"));    
        String processName = reader.readLine();     
        if (!TextUtils.isEmpty(processName)) {      
            processName = processName.trim();     
        }     
            return processName;  
        } catch (Throwable throwable) {  
            throwable.printStackTrace();  
        } finally {     
            try {       
              if (reader != null) {     
                reader.close();        
            }     
        } catch (IOException exception) {     
               exception.printStackTrace();    
        }   
    }  
          return null;
}

```

e.Multidex的使用，也是拖慢启动速度的元凶，必须要做优化。

### 28.Android程序Crash时的异常上报

为了捕获Android中的crash信息

Thread类中的一个方法#setDefaultUncaughtExceptionHandler源码：

```
/** 
 * Sets the default uncaught exception handler. This handler is invoked in 
 * case any Thread dies due to an unhandled exception. 
 * 
 * @param handler 
 *            The handler to set or null. 
 */  
public static void setDefaultUncaughtExceptionHandler(UncaughtExceptionHandler handler) {  
    Thread.defaultUncaughtHandler = handler;  
}  
```

当crash发生的时候，我们可以**捕获到异常**信息，把异常信息**存储到SD卡**中，然后在合适的时机通过网络将crash信息**上传到服务器**上，这样开发人员就可以分析用户crash的场景从而在后面的版本中修复此类crash.

(1)建立异常处理Handler

新建一个类，比如叫CrashHandler.java，代码如下，代码我就不做解释了，注释相当清晰了:

```
public class CrashHandler implements UncaughtExceptionHandler {  
    private static final String TAG = "CrashHandler";  
    private static final boolean DEBUG = true;  
  
    private static final String PATH = Environment.getExternalStorageDirectory().getPath() + "/ryg_test/log/";  
    private static final String FILE_NAME = "crash";  
  
    //log文件的后缀名  
    private static final String FILE_NAME_SUFFIX = ".trace";  
  
    private static CrashHandler sInstance = new CrashHandler();  
  
    //系统默认的异常处理（默认情况下，系统会终止当前的异常程序）  
    private UncaughtExceptionHandler mDefaultCrashHandler;  
  
    private Context mContext;  
  
    //构造方法私有，防止外部构造多个实例，即采用单例模式  
    private CrashHandler() {  
    }  
  
    public static CrashHandler getInstance() {  
        return sInstance;  
    }  
  
    //这里主要完成初始化工作  
    public void init(Context context) {  
        //获取系统默认的异常处理器  
        mDefaultCrashHandler = Thread.getDefaultUncaughtExceptionHandler();  
        //将当前实例设为系统默认的异常处理器  
        Thread.setDefaultUncaughtExceptionHandler(this);  
        //获取Context，方便内部使用  
        mContext = context.getApplicationContext();  
    }  
  
    /** 
     * 这个是最关键的函数，当程序中有未被捕获的异常，系统将会自动调用#uncaughtException方法 
     * thread为出现未捕获异常的线程，ex为未捕获的异常，有了这个ex，我们就可以得到异常信息。 
     */  
    @Override  
    public void uncaughtException(Thread thread, Throwable ex) {  
        try {  
            //导出异常信息到SD卡中  
            dumpExceptionToSDCard(ex);  
            //这里可以通过网络上传异常信息到服务器，便于开发人员分析日志从而解决bug  
            uploadExceptionToServer();  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
  
        //打印出当前调用栈信息  
        ex.printStackTrace();  
  
        //如果系统提供了默认的异常处理器，则交给系统去结束我们的程序，否则就由我们自己结束自己  
        if (mDefaultCrashHandler != null) {  
            mDefaultCrashHandler.uncaughtException(thread, ex);  
        } else {  
            Process.killProcess(Process.myPid());  
        }  
  
    }  
  
    private void dumpExceptionToSDCard(Throwable ex) throws IOException {  
        //如果SD卡不存在或无法使用，则无法把异常信息写入SD卡  
        if (!Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {  
            if (DEBUG) {  
                Log.w(TAG, "sdcard unmounted,skip dump exception");  
                return;  
            }  
        }  
  
        File dir = new File(PATH);  
        if (!dir.exists()) {  
            dir.mkdirs();  
        }  
        long current = System.currentTimeMillis();  
        String time = new SimpleDateFormat("yyyy-MM-dd HH🇲🇲ss").format(new Date(current));  
        //以当前时间创建log文件  
        File file = new File(PATH + FILE_NAME + time + FILE_NAME_SUFFIX);  
  
        try {  
            PrintWriter pw = new PrintWriter(new BufferedWriter(new FileWriter(file)));  
            //导出发生异常的时间  
            pw.println(time);  
  
            //导出手机信息  
            dumpPhoneInfo(pw);  
  
            pw.println();  
            //导出异常的调用栈信息  
            ex.printStackTrace(pw);  
  
            pw.close();  
        } catch (Exception e) {  
            Log.e(TAG, "dump crash info failed");  
        }  
    }  
  
    private void dumpPhoneInfo(PrintWriter pw) throws NameNotFoundException {  
        //应用的版本名称和版本号  
        PackageManager pm = mContext.getPackageManager();  
        PackageInfo pi = pm.getPackageInfo(mContext.getPackageName(), PackageManager.GET_ACTIVITIES);  
        pw.print("App Version: ");  
        pw.print(pi.versionName);  
        pw.print('_');  
        pw.println(pi.versionCode);  
  
        //android版本号  
        pw.print("OS Version: ");  
        pw.print(Build.VERSION.RELEASE);  
        pw.print("_");  
        pw.println(Build.VERSION.SDK_INT);  
  
        //手机制造商  
        pw.print("Vendor: ");  
        pw.println(Build.MANUFACTURER);  
  
        //手机型号  
        pw.print("Model: ");  
        pw.println(Build.MODEL);  
  
        //cpu架构  
        pw.print("CPU ABI: ");  
        pw.println(Build.CPU_ABI);  
    }  
  
    private void uploadExceptionToServer() {  
        //TODO Upload Exception Message To Your Web Server  
    }  
  
}  
```

(2)为ui线程添加默认**异常事件Handler**

```
//Thread类中标识默认异常事件Handler的成员

private static UncaughtExceptionHandler defaultUncaughtHandler;
```

这个**defaultUncaughtHandler**是Thread类中一个**静态的成员**,按道理，我们为任意一个线程设置异常处理，所有的线程都应该能共用这个异常处理器,为了在ui线程中添加异常处理Handler，我们推荐大家在**Application中添加**而不是在Activity中添加.

```
public class TestApp extends Application {  

    private static TestApp sInstance;  
  
    @Override  
    public void onCreate() {  
        super.onCreate();  
        sInstance = this;  
  
        //在这里为应用设置异常处理程序，然后我们的程序才能捕获未处理的异常  
        CrashHandler crashHandler = CrashHandler.getInstance();  
        crashHandler.init(this);  
    }  
  
    public static TestApp getInstance() {  
        return sInstance; 
    }  
  
}  
```

## 十五、Java

### 1.Java内存模型

(1) 本地方法栈

与 Java 虚拟机栈所发挥的作用是非常相似的，其区别不过是虚拟机栈为虚拟机执行 Java 方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的 Native 方法服务。虚拟机规范中对本地方法栈中的方法使用的语言、使用方式与数据结构并没有强制规定，因此具体的虚拟机可以自由实现它。

**Navtive 方法是 Java 通过 JNI 直接调用本地 C/C++ 库**，可以认为是 Native 方法相当于 C/C++ 暴露给 Java 的一个接口，Java 通过调用这个接口从而调用到 C/C++ 方法。当线程调用 Java 方法时，虚拟机会创建一个栈帧并压入 Java 虚拟机栈。然而当它调用的是 native 方法时，虚拟机会保持 Java 虚拟机栈不变，也不会向 Java 虚拟机栈中压入新的栈帧，虚拟机只是简单地动态连接并直接调用指定的 native 方法。

(2) 虚拟机栈

虚拟机栈主要的作用就是为执行java方法服务的，是Java方法执行的动态内存模型。

(3) 程序计数器

*当前线程执行字节码的行号指示器

*处于线程独占区

*如果是执行的是java代码,当前值为字节码指令的地址，如果是Native，值为undefined

(4) 堆

*存放对象的实例

*垃圾收集器管理的主要区域

*分代管理对象

*会导致内存溢出(OutOfMemoryError)

(5) 方法区

*存放虚拟机加载的类信息，常量，静态变量，编译后的代码和数据

*GC主要对方法区进行常量回收和类卸载

*会出现内存溢出(OutOfMemoryError)

### 2.内存泄漏

(1) 单例模式

```
 public class AppManager {
    private static AppManager instance;
    private Context context;
    private AppManager(Context context) {
          this.context = context;
    }
    public static AppManager getInstance(Context context) {
          if (instance != null) {
                instance = new AppManager(context);
          }
          return instance;
    }
}

```

当我们传递进来的是Context，那么当前对象就会持有第一次实例化的Context，如果Context是Activity对象，那么就会产生内存泄漏。因为当前对象AppManager是静态的，生命周期和应用是一样的，只有应用退出才会释放，导致Activity不能及时释放，带来内存泄漏。

解决方法一：

```
public class AppManager {
    private static AppManager instance;
    private Context context;
    private AppManager(Context context) {
         // 使用Application 的context  
          this.context = context.getApplicationContext();
  }
    public static AppManager getInstance(Context context) {
          if (instance != null) {
                instance = new AppManager(context);
          }
          return instance;
    }
}

```

解决方法二：

在你的 Application 中添加一个静态方法，getContext() 返回 Application 的 context，

```
public classAppManager{   
    private static AppManager instance;
    private Context context;
    private AppManager() {
         // 使用Application 的context
          this.context = MyApplication.getContext();
}
    public static AppManager getInstance() {
          if (instance != null) {
                instance = new AppManager();
          }
          return instance;
    }
}
```

### 3.java中的join

內部使用wait实现

```
public class TestJoin { 
	public static void main(String[] args) throws InterruptedException {	
        // TODO Auto-generated method stub					              System.out.println(Thread.currentThread().getName()+" start");	
        ThreadTest t1=new ThreadTest("A");	
        ThreadTest t2=new ThreadTest("B");		
        ThreadTest t3=new ThreadTest("C");	
        System.out.println("t1start");		
        t1.start();	
        System.out.println("t1end");
        System.out.println("t2start");	
        t2.start();		
        System.out.println("t2end");		
        t1.join();	
        System.out.println("t3start");	
        t3.start();	
        System.out.println("t3end");	
        System.out.println(Thread.currentThread().getName()+" end");	
	} 
}

```

```
main start
t1start
t1end
t2start
t2end
A-1
B-1
A-2
A-3
A-4
A-5
B-2
t3start
t3end
B-3
main end
B-4
B-5
C-1
C-2
C-3
C-4
C-5
```

主线程在t1.join()方法处停止，并需要等待A线程执行完毕后才会执行t3.start()，然而，并不影响B线程的执行。因此，可以得出结论，t.join()方法只会使**主线程**进入等待池并等待t线程执行完毕后才会被唤醒。并不影响同一时刻处在运行状态的其他线程。

### 4.volatile

volatile可见性和禁止指令重排序这里就不再赘述，主要是讨论一下为什么volatile修饰的变量不能够保证原子性，最常见的就是volatile变量的自增测试

### 5.hashmap

1.可以手动指定扩容因子

2.Map m = Collections.synchronizedMap(new HashMap(...)); //获取一个可以同步的hashmap

扩容的条件：　

HashMap.Size >= Capacity * LoadFactor



1、默认初始化容量大小1<<4 = 16；

2、最大容量1<<30 = 2的30次方；

3、默认负载因子0.75f；

4、链表树形化阈值为8(用于hash冲突产生链表还是产生红黑树的一个判断值)；TREEIFY_THRESHOLD =8

   如果哈希函数不合理，即使扩容也无法减少箱子中链表的长度，因此 Java 的处理方案是当链表太长时，转换成红黑树。这个值表示当某个箱子中，链表长度大于 8 时，有可能(第6点是确定因素)会转化成树；

5、在哈希表扩容时，如果发现链表长度小于 6，则会由树重新退化为链表；UNTREEIFY_THRESHOLD =6

6、在转变成树之前，还会有一次判断，只有键值对数量大于 64 才会发生转换。这是为了避免在哈希表建立初期，多个键值对恰好被放入了同一个链表中而导致不必要的转化；MIN_TREEIFY_CAPACITY =64

注意：

当get()方法返回null值时，即可以表示Map中没有该键，也可以表示该键所对应的值为null。因此，在Map中不能由get()方法来判断Map中是否存在某个键，而应该用containsKey()方法来判断。

### 6.hashcode

定义：hashCode是jdk根据对象的地址或者字符串或者数字算出来的int类型的数值. 详细了解请 参考 public int hashCode()返回该对象的哈希码值。支持此方法是为了提高哈希表（例如 java.util.Hashtable 提供的哈希表）的性能。

1.两个对象没有重写equal和hashcode方法   相等，那么hashcode就相等

### 7.linkhashmap

HashMap取出数据的顺序(数据的存储顺序)，应该是跟key的hashCode有关的。

1、属于一种hashMap

public class LinkedHashMap **extends *HashMap***  implements Map {

2、相比于HashMap，linkedHashMap结构中还维护着一个双向链表，用于记录顺序。所以可以做到跟存入顺序一样取出元素。

3、hashMap键只能允许为一条为空，value可以允许为多条为空，键唯一，但值可以多个。linkedHashMap键和值都不可以为空。

4、linkedHashMap在于存储数据你想保持存进入的顺序与被取出的顺序一致的话，优先考虑LinkedMap。



### 8.HashMap和Hashtable的区别

HashMap和Hashtable都实现了Map接口，但决定用哪一个之前先要弄清楚它们之间的分别。主要的区别有：线程安全性，同步(synchronization)，以及速度。

1. HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行)。
2. HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。Java 5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
3. 另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
4. 由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。如果你不需要同步，只需要单一线程，那么使用HashMap性能要好过Hashtable。
5. HashMap不能保证随着时间的推移Map中的元素次序是不变的。

### 9.HashSet 

1.HashSet底层由HashMap实现

2.HashSet的值存放于HashMap的key上

3.HashMap的value统一为PRESENT

### 10.System.arrayCopy 和 Arrays.copyOf 两个方法的区别和联系？

```
public static native void arrayCopy(Object src, int srcPos, Object dest, int destPos, int length);
```

Systerm.arrayCopy()是一个系统的原生方法，它的参数中需要一个目标数组，然后将原数组拷贝到目标数组中。可以选择拷贝的起点和长度。

```
public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}

public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0, Math.min(original.length, newLength));
    return copy;
}
```

### 11.ArrayList

ArrayList实现了List接口，继承了AbstractList，底层是数组实现的，一般我们把它认为是可以自增扩容的数组

1.new Array<Integer>()	

2.size和modCount

size是ArrayList的变量。modCount是ArrayList的父类AbstractList中的变量，默认值为0。

size记录了ArrayList中元素的数量，modCount记录的是关于元素的数目被修改的次数。modCount在ArrayList的普通操作里可能并没有看出多大用处，但是在涉及到fail-fast就主要是依靠它了。

3.ArrayList 性能相对Vector 会好些，它实现了Serializable接口

4.扩容：Vector每次扩容容量是翻倍，即为原来的2倍，而ArrayList是1.5倍

a.新增元素后的大小minCapacity是否超过**当前集合的容量**elementData.length，如果超过，则调用**grow**方法进行扩容

```
private status final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

if(minCapacity > elementData.length){
  grow(minCapacity);
}

private void grow(int minCapacity){
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity	+ (oldCapacity >> 1);//扩容1.5倍
  if(newCapacity - minCapacity < 0){
     newCapacity = minCapacity;
  }
  if(newCapacity > MAX_ARRAY_SIZE ){
     newCapacity = hugeCapacity(minCapacity);
  }
  elementData = Array.copyOf(elementData,newCapacity); 		
}
```

b.判断当前新容量是否超过最大的容量 if (newCapacity - MAX_ARRAY_SIZE > 0)，如果超过，则调用hugeCapacity方法

```
private status int hugeCapacity(int minCapacity){
    if(minCapacity < 0)
       throw new OutOfMenoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```

### 12.多态

多态存在的三个必要条件：继承，重写，父类引用指向子类对象

### 13.synchronized

a.  synchronized是对类的当前实例进行加锁，防止其他线程同时访问该类的该实例的所有synchronized块，注意这里是“类的当前实例”， 类的两个不同实例就没有这种约束了。

b. static synchronized恰好就是要控制类的所有实例的访问了，static synchronized是限制线程同时访问jvm中该类的所有实例同时访问对应的代码快。

### 14.FileWriter和BufferedWriter区别和用法

(1)使用BufferedWriter，一定会要用到FileWriter

```
毕竟它是以FileWriter为parameter(参数)的！

Public BufferedWriter(Writerout)

Public BufferedWrieter(Writerout , int sz)
```

(2)使用的BufferedWriter的效率要比FileWriter高很多

前者有效的使用了缓存器,将缓存写满以后（或者close以后）才输出到文件中

后者是没写一次数据，磁盘就会进行一次写操作，性能差得一匹

```
 FileWriter out = new FileWriter(file);  //文件写入流
   BufferedWriter bw=new BufferedWriter(out);
   
   for(int i=0;i<n;i++){
       bw.write((int)result[i] + "\t");
       bw.newLine();
   }
   
   bw.close();
   out.close();
```

FileWriter从类OutputStreamWriter继承的
1、public void write(int c) throws IOException   写入单个字符。
c - 指定要写入字符的ASCII。

这里data数组中的整数是作为字符的ASCII，最终显示的是ASCII值为这些的字符，并不是这些数字本身。

要显示这些数字本身，可改为：

```
fos.write(String.valueOf(data[i])); 
```

注意valueOf（）方法的大小写。或者用\t分割.

15.日期格式

a.UTC日期格式：yyyy-MM-dd'T'HH:mm:ss.SSS'Z'

b.普通日期格式：yyyy-MM-dd HH:mm:ss

```
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class UTCTimeFormatTest {
    //UTC就是世界标准时间，与北京时间相差八个时区。所以只要将UTC时间转化成一定格式的时间，再在此基础上加上8个小时就得到北京时间了。
    public static void main(String[] args) throws ParseException {
        //Z代表UTC统一时间:2017-11-27T03:16:03.944Z
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'");
        Date date = new Date();
        System.out.println(date);
        String str = format.format(date);
        System.out.println(str);

        SimpleDateFormat dayformat = new SimpleDateFormat("yyyy-MM-dd");
        String source ="2018-09-18";
        //先将年月日的字符串日期格式化为date类型
        Date day = dayformat.parse(source);
　　　　 //然后将date类型的日期转化为UTC格式的时间
        String str2= format.format(day);
        System.out.println(str2);
    }
}
```



## 十六、数据结构

### 1.快速排序

快排推荐这篇文章[https://www.cnblogs.com/CBDoctor/p/4077574.html](https://link.jianshu.com/?t=https%3A%2F%2Fwww.cnblogs.com%2FCBDoctor%2Fp%2F4077574.html)

小--->大  

左边的数为基准 

右边j先开始(为啥？否则右边无法与左边交换)，向左移，找比基准小的数，

左边i向右移，找比基准大的数

当j == i时，和基准交换，这样就分成两部分，然后左右两部分再按照原来的方式交换排序，直到停止

### 2.sql中的replace

插入替换：将id=6的name字段值改为wokou
 replace into test_tb VALUES(6,'wokou','新九州岛','日本')

总结：向表中“替换插入”一条数据，如果原表中没有id=6这条数据就作为新数据插入(相当于insert into作用)；如果原表中有id=6这条数据就做替换(相当于update作用)。对于没有指定的字段以默认值插入。

### 3.数据库索引

包括：磁盘块、数据项、指针(P1,P2,P3)

数据存储在叶子节点中

**避免使用索引**：

a.索引不应该使用在较小的表上

b.索引不应该使用在有频繁的大批量的更新或插入操作的表上

c.索引不应该使用在含有大量的 NULL 值的列上

d.索引不应该使用在频繁操作的列上

## 十七、自定义view

1.获取view的宽高

a.Activity/View的onWindowsChanged()方法

```
 public void onWindowFocusChanged(boolean hasWindowFocus) {
         super.onWindowFocusChanged(hasWindowFocus);
       if(hasWindowFocus){
         int width=view.getMeasuredWidth();
         int height=view.getMeasuredHeight();
      }      
  }
```

b.View.post(runnable)方法

```
@Override  
protected void onStart() {  
    super.onStart();  
    view.post(new Runnable() {  
        @Override  
        public void run() {  
            int width=view.getMeasuredWidth();  
            int height=view.getMeasuredHeight();  
        }  
    });  
}  
```

c.ViewTreeObsever

```
@Override  
  protected void onStart() {  
    super.onStart();  
    ViewTreeObserver viewTreeObserver=view.getViewTreeObserver();  
    viewTreeObserver.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {  
        @Override  
        public void onGlobalLayout() {  
            view.getViewTreeObserver().removeOnGlobalLayoutListener(this);  
            int width=view.getMeasuredWidth();  
            int height=view.getMeasuredHeight();  
        }  
    });  
} 
```

2.视图重绘

a.requestLayout重新绘制视图

子View调用requestLayout方法，会标记当前View及父容器，同时逐层向上提交，直到ViewRootImpl处理该事件，ViewRootImpl会调用三大流程，从measure开始，对于每一个含有标记位的view及其子View都会进行测量、布局、绘制。

b.invalidate在UI线程中重新绘制视图

当子View调用了invalidate方法后，会为该View添加一个标记位，同时不断向父容器请求刷新，父容器通过计算得出自身需要重绘的区域，直到传递到ViewRootImpl中，最终触发performTraversals方法，进行开始View树重绘流程(只绘制需要重绘的视图)。

c.postInvalidate在非UI线程中重新绘制视图

这个方法与invalidate方法的作用是一样的，都是使View树重绘，但两者的使用条件不同，postInvalidate是在非UI线程中调用，invalidate则是在UI线程中调用。

3.硬件加速

(1)简介

a.硬件加速是从API 11引入，API 14之后才默认开启。对于标准的绘制操作和控件都是支持的，但是对于自定义View的时候或者一些特殊的绘制函数就需要考虑是否需要关闭硬件加速。

b.从Android 3.0开始，Android 2D渲染管道支持硬件加速，这意味着在**View画布上**执行的所有绘图操作都使用GPU。由于启用硬件加速所需的资源增加，您的应用程序将消耗更多的RAM。

c.如果您的目标API级别 >= 14，则默认情况下启用硬件加速，但也可以显式启用。此外，Android还允许在多个级别启用或禁用硬件加速。

(2)开启硬件加速的3种方式

a.**Application内开启硬件加速**

```
<application
    android:hardwareAccelerated="true">
</application>
```

b.**Activity中开启硬件加速**

```
<activity 
    android:hardwareAccelerated="true" >
</activity>
```

c.**代码的 Window 方式开启硬件加速**

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setContentView(R.layout.act_main);

    getWindow().setFlags(
        WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED,
        WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED);
}
```

(3)其它方法

a.**查看并获取是否支持硬件加速**

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setContentView(R.layout.act_main);

     //returns true if the View is attached to a hardware accelerated window
    boolean isOpen1 = View.isHardwareAccelerated();

    // returns true if the Canvas is hardware accelerated
    boolean isOpen2 = Canvas.isHardwareAccelerated();
}
```

b.**代码关闭View控件的硬件加速**

```
View.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
```

c.**xml配置关闭View控件属性**

```
在xml中，使用android:layerType=”software”来关闭硬件加速

<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:background="#ffffff"
    android:text="11111111"
    android:layerType="software"
    android:textSize="15sp" />
```

注意1：在API14之后，硬件加速默认是开启的。
注意2：目前不允许在window级别关闭硬件加速，但是可以在运行时对指定的View关闭硬件加速。

(4)View Layer

硬件加速可以使用 setLayerType() 来关闭硬件加速，但这个方法其实是用来设置View Layer 的:

a.参数为 LAYER_TYPE_SOFTWARE 时，使用软件来绘制 View Layer，绘制到一个Bitmap，并顺便关闭硬件加速

b.参数为 LAYER_TYPE_HARDWARE 时，使用 GPU 来绘制 View Layer，绘制到一个OpenGL texture（如果硬件加速关闭，那么行为和 VIEW_TYPE_SOFTWARE 一致）

c.参数为 LAYER_TYPE_NONE 时，关闭 View Layer。View Layer 可以加速无 invalidate() 时的刷新效率，但对于需要调用invalidate() 的刷新无法加速。

4.**setPathEffect(PathEffect effect)**

注意： PathEffect 在有些情况下不支持硬件加速，需要关闭硬件加速才能正常使用：

a.Canvas.drawLine()` 和 `Canvas.drawLines()` 方法画直线时，`setPathEffect()` 是不支持硬件加速的；

b.PathDashPathEffect` 对硬件加速的支持也有问题，所以当使用 `PathDashPathEffect` 的时候，最好也把硬件加速关了。

## 十八、view动画

1.evaluator

a.位置渐变

```
private class PointEvaluator implements TypeEvaluator<Point>{
     Point point = new Point();
     
     @Override
     public Point evaluate(float fraction,Point startValue,Point endValue){
            float x = startValue.x + fraction * (endValue.x - startValue.x);
            float y = startValue.y + fraction * (endValue.y - startValue.y);
            point.set(x,y);
            return point;
     }
}

ObjectAnimator animator = ObjectAnimator.ofObject(view,"position",new PointEvaluator(),new Point(1,1));
animator.start();

```

b.颜色渐变

```
private class HsvEvaluator implement TypeEvaluator<Integer>{
     
     @Override
     public Integer evaluate(float fraction,Integer startValue,Integer endValue){
            //把ARGB 转化为HSV
            Color.colorToHSV(startValue,startHSV);
            Color.colorToHSV(endValue,endHSV);
            //计算当前动画完成度所对应的颜色和透明度
            
            outHSV[0] = startHSV[0] + (endHSV[0] - startHSV[0]) * fraction;
            outHSV[1] = startHSV[1] + (endHSV[1] - startHSV[1]) * fraction;
            outHSV[2] = startHSV[2] + (endHSV[2] - startHSV[2]) * fraction;
            int alpha = 1;
            return Color.HSVToColor(alpha,outHSV);
     }
}

ObjectAnimator animator = ObjectAnimator.ofObject(view,"color",0xffff0000,0xff00ff00;
//animator.setEvaluator(new ArgbEvaluator());
animator.setEvaluator(new HsvEvaluator());
animator.start();
```

2.**ObjectAnimator**

使用方式：

a.如果是自定义控件，需要添加 `setter` / `getter` 方法；**注： invalidate()**   原生里没有使用硬件加速

b.用 `ObjectAnimator.ofXXX()` 创建 `ObjectAnimator` 对象；

c.用 `start()` 方法执行动画。

```
public class SportsView extends View {

    float progress = 0;
    
    ......
    
    // 创建 getter 方法
    public float getProgress() {
        return progress;
    }

    // 创建 setter 方法
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }
    
    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        ......
        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);
        ......
    }
}
......
// 创建 ObjectAnimator 对象
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);
// 执行动画
animator.start();
```

3.Button的宽度增加

Button继承TextView，才又getWidth和setWidth方法，其方法是设置最大宽度和最小宽度，

1.用一个类来包装原始对象，间接为其实现属性的改变

```
private static class ViewWrapper{
   private View mTarget;
   
   public ViewWrapper(View target){
      mTarget = target;
   }
   
  public void setWidth(int width) {
            mTarget.getLayoutParams().width = width;
            mTarget.requestLayout();
        }

        public int getWidth() {
            return mTarget.getLayoutParams().width;
        }
}
```

```
     ObjectAnimator.ofInt(new ViewWrapper(view),"width",500)
                .setDuration(3000).start();
```

2.旋转

技巧：项目中一般存在返回箭头向左的，如果此时需要选择又懒得找策划要切图，可以将此按钮进行旋转即可。

```
  mJianTouImageView = (LinearLayout) v.findViewById(R.id.jiantou_image);
  mJianTouImageView .setRotation(270f);
```



## 十九、Service

1.如何保证服务不被杀死

a.`onStartCommand中返回START_STICKY`

**START_STICKY:**系统重新创建服务并且调用`onStartCommand()`方法，但并不会传递最后一次传递的`intent`，只是传递一个空的`intent`。除非存在将要传递来的`intent`，那么就会传递这些`intent`。这个适合播放器一类的服务，不需要执行命令，只需要独自运行，等待任务。

**注意**：手动返回START_STICKY，亲测当service因内存不足被kill，当内存又有的时候，service又被重新创建，比较不错，但是不能保证任何情况下都被重建，比如进程被干掉了....

**START_NOT_STICKY**:系统不重新创建服务，除非有将要传递来的`intent`。这是最安全的选项，可以避免在不必要的时候运行服务

**START_REDELIVER_INTENT**:系统重新创建服务并且调用`onStartCommand()`方法，传递最后一次传递的`intent`。其余存在的需要传递的`intent`会按顺序传递进来。这适合像下载一样的服务，立即恢复，积极执行。

```
@Override  
public int onStartCommand(Intent intent, int flags, int startId) {  
    flags = START_STICKY;  
    return super.onStartCommand(intent, flags, startId);  
}  
```

2.提升Service优先级

前台进程必须发一个notification在状态栏中显示，知道进程被杀死。因为前台服务一直消耗一部分资源，但不像一般服务那样会在需要的时候被杀掉，所以为了节约资源，保护电池寿命，一定要在建前台服务的时候发送notification，提示用户。当然系统提供的方法就必须有notification参数的，所以不要想着怎么把notification隐藏掉。

```
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    // TODO Auto-generated method stub
    Intent notificationIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0);
    Notification noti = new Notification.Builder(this)
                .setContentTitle("Title")
                .setContentText("Message")
                .setSmallIcon(R.drawable.ic_launcher)
                .setContentIntent(pendingIntent)
                .build();
    startForeground(123456,noti);
    return Service.START_STICKY;
}
```

`startForeground()`方法就是将服务设置为前台服务，参数123456就是这个通知的唯一的id，只要不为0即可。

3.onDestory()中发送广播开启自己

service+broadcast方式，就是当service调用到`ondestory()`的时候，发送一个自定义的广播，当收到广播的时候，重新启动service；

```
<receiver android:name="com.example.demo.MyReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.USER_PRESENT"/>
        <action android:name=com.example.demo.destroy"/>// 这个是自定义的action
    </intent-filter>
</receiver>
```

在service中的`ondestroy()`时候：

```
@Override
public void onDestroy(){
    stopForeground(true);
    Intent intent = new Intent("com.example.demo.destroy");
    sendBroadcast(intent);
    super.onDestroy();
}
```

在MyReceiver中:

```
public class MyReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent.getAction().equals("com.example.demo.destroy")){
            Intent sevice = new Intent(this, MyService.class);  
            this.startService(sevice); 
        }
    }
}
```

当然，从理论上来讲这个方案是可行的，实验一下结果也是可行的。但是有些情况下，发送的广播在消息队列中排的靠后，就有可能服务还没有接收到广播就销毁了（只是猜想）。所以为了能让这个机制完美运行，可以开启两个服务，相互监听，相互启动。服务A监听B的广播来启动B，服务B监听A的广播来启动A。经过实验，这个方案是可行的。

## 二十、设计模式

1.MVVM与MVP区别

mvvm模式将Presener改名为View Model，基本上与MVP模式完全一致，唯一的区别是，它采用双向绑定(data-binding):   View的 变动，自动反映在View Model，反之亦然。这样开发者就不用处理接收事件和View更新的工作，框架已经帮你做好了。

## 二十一、jetpack

1、好处

1.消除大量重复样板式的代码。
2.简化复杂的任务。
3.提供了强健的向后兼容的能力。
4.加速Android的开发进程。

2、组成

(1) Foundation：基础

- AppCompat（向后兼容）
- Android KTX（编写更加简洁的Kotlin代码）
- Multidex （多处理dex的问题）
- Test（测试）

(2) Architecture (Compinents)： 体系结构(组件)

Data Bingding（数据绑定）

Room（数据库）

WorkManager（后台任务管家）

Lifecycle（生命周期）

Navigation（导航）

Paging（分页）

Data Binding（数据绑定）

**LiveData**（底层数据通知更改视图）

是一个具有生命周期感知特性的可观察的数据保持类。

a.LiveData只通知活跃状态( STARTED or RESUMED )的Observer更新，并在 DESTROYED状态时自动移除Observers，来避免内存泄漏。

b.始终保持最新数据。举例：1.退后台的Activity在返回前台后会立即收到最新数据。2. 配置变更导致Activity重建后也会立即收到最新数据。

c.共享资源。单例模式共享同一个LiveData共享资源。单例模式共享同一个LiveData

**ViewModel**（以注重生命周期的方式管理界面的相关数据）

a.可用ViewModel存储数据，它能安全度过手机旋转等配置变更场景。

b.ViewModel能很好的实现多个Fragment之间的数据共享

(3) UI：视觉交互

Animation & transitions（动画和过渡）

Auto（Auto组件）

Emoji（标签）

Fragment（Fragment）

Layout（布局）

Palette（调色板）

TV（TV）

Wear OS by Google（穿戴设备）

(4) Behavior：行为

Download manager（下载给管理器）

Media & playback（媒体和播放）

Notifications（通知）

Permissions（权限）

Preferences（偏好设置）

Sharing（共享）

Slices（切片）

(5)其他数据恢复

a .使用 onSaveInstanceState 与 onRestoreInstanceState

b.使用 Fragment 的 setRetainInstance

c.使用 onRetainNonConfigurationInstance 与 getLastNonConfigurationInstance

在 Androidx 中的 Activity 的最新代码中，官方重写了 onRetainNonConfigurationInstance 方法，在该方法中保存了 `ViewModelStore` (ViweModelStore 中存储了 ViewModel )，进而也保存了 ViewModel，具体代码如下所示：

```
    public final Object onRetainNonConfigurationInstance() {
        Object custom = onRetainCustomNonConfigurationInstance();

        ViewModelStore viewModelStore = mViewModelStore;
        if (viewModelStore == null) {
            NonConfigurationInstances nc =
                    (NonConfigurationInstances) getLastNonConfigurationInstance();
            if (nc != null) {
                viewModelStore = nc.viewModelStore;
            }
        }

        if (viewModelStore == null && custom == null) {
            return null;
        }

        //将ViewModel存储在 NonConfigurationInstances 对象中
        NonConfigurationInstances nci = new NonConfigurationInstances();
        nci.custom = custom;
        nci.viewModelStore = viewModelStore;
        return nci;
    }
```

当新的 Activity 重新创建，并调用 ViewModelProviders.of(this).get(xxxModel.class) 时，又会在 `getViewModelStore()` 方法中获取老 Activity 保存的 ViewModelStore。那么也就拿到了 ViewModel。具体代码如下所示：

```
    public ViewModelStore getViewModelStore() {
        if (getApplication() == null) {
            throw new IllegalStateException("Your activity is not yet attached to the "
                    + "Application instance. You can't request ViewModel before onCreate call.");
        }
        if (mViewModelStore == null) {
            //👇获取保存的NonConfigurationInstances，
            NonConfigurationInstances nc =
                    (NonConfigurationInstances) getLastNonConfigurationInstance();
            if (nc != null) {
                //👇从该对象中获取ViewModelStore
                mViewModelStore = nc.viewModelStore;
            }
            if (mViewModelStore == null) {
                mViewModelStore = new ViewModelStore();
            }
        }
        return mViewModelStore;
    }
```

ViewModel 何时判断是否被移除:

```
    public ComponentActivity() {
        Lifecycle lifecycle = getLifecycle();
        //省略更多....
        getLifecycle().addObserver(new LifecycleEventObserver() {
            @Override
            public void onStateChanged(@NonNull LifecycleOwner source,
                    @NonNull Lifecycle.Event event) {
                if (event == Lifecycle.Event.ON_DESTROY) {
                    if (!isChangingConfigurations()) {
                       //👇在配置没发生改变且走到onDestory方法时，清除所有的ViewModel
                        getViewModelStore().clear();
                    }
                }
            }
        });
    }
```

参考：https://www.jianshu.com/p/39ef3e0a5829

## 二十二、retrofit

Retrofit 是一个 RESTful 的 HTTP 网络请求框架的封装，网络请求的工作本质上是 OkHttp 完成，而 Retrofit 仅负责 网络请求接口的封装

0.RESTful 风格

```
增加一个朋友，uri: generalcode.cn/v1/friends 接口类型：POST

删除一个朋友，uri: generalcode.cn/va/friends 接口类型：DELETE

修改一个朋友，uri: generalcode.cn/va/friends 接口类型：PUT

查找朋友，uri: generalcode.cn/va/friends 接口类型：GET
```

上面我们定义的四个接口就是符合REST协议的，请注意，这几个接口都没有动词，只有名词friends，都是通过Http请求的接口类型来判断是什么业务操作。

举个反例：generalcode.cn/va/deleteFriends 该接口用来表示删除朋友，这就是不符合REST协议的接口。

### 1.实例创建

```
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://localhost:4567/")
        .build();
```

注1：Retrofit2 的baseUlr 必须以 **/（斜线）** 结束，不然会抛出一个`IllegalArgumentException`

住2：有些特殊情况可以不以**/**结尾，如：这个 URL `https://www.baidu.com?key=value`，而后面的`?key=value`在拼装请求时会被丢掉所以写上也没用，Retrofit 2 在文档上要求必须以 **/（斜线）** 结尾的要求想必是要消除歧义以及简化规则

### 2.接口定义

```
public interface BlogService {
    @GET("blog/{id}")
    Call<ResponseBody> getBlog(@Path("id") int id);
}
```

注意，这里是`interface`不是`class`，所以我们是无法直接调用该方法，我们需要用Retrofit创建一个`BlogService`的代理对象。

```
BlogService service = retrofit.create(BlogService.class);
```

### 3.接口调用

```
Call<ResponseBody> call = service.getBlog(2);
// 用法和OkHttp的call如出一辙,
// 不同的是如果是Android系统回调方法执行在主线程
call.enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        try {
            System.out.println(response.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {
        t.printStackTrace();
    }
});
```

### 4.注解详解

```
public interface Api{
  @GET
  Call<Response> getdata(Request data);//请求通过注解标识
   
}
```

1.通过动态代理的方式，创建出一个Api对象

2.再通过invoke方法，其中有个method参数

3.通过service方法中解析method参数，获取请求方式，请求参数等

```
private void parseMethodAnnotation(Annotation annotation){
   if(annotaton instanceof GET){
      parseHttpMethodandpath('GET',((GET)annotaton).value,false);
   }
}
```

### 4.拦截器的类型转换

a.log拦截器需要添加依赖，然后addLOGIntecept

```
public class DateConverter extends Converter{
      ///
      public static class DateConverterFactory extends Factory{
         stringConverter();
      }
}
```

### 5.动态修改baseUrl

通过反射修改

```
Field fieldHost = Field.class.getDeclareField("host");
fieldHost.setAccesssible(true);
fielfHost.set(httpUrl,host);
```

对于静态成员的修改,需要修改modifiers

```
Field modifiersField = Field.class.getDeclaredField("modifiers");
modifiersField.setAccessible(true);

Field cField = ConsTest.class.getDeclaredField("c");
modifiersField.setInt(cField,modifiersField.getInt(cField) ^ Modifild.FINAL);

cField.setAccessible(true);
cField.set(null,2);
System.out.println(ConstTest.c);
```

### 6.添加rxJava

```
.addConverterFactory(GsonConverterFactory.create())
.addCallAdapterFactory(RXjavaCallAdapterFactory.create())
```

探索 observeOn 的奥秘:

a.在 Android 中线程间传递消息会使用 Handler,这里是否使用？又是如何使用的？

b.AndroidSchedulers.mainThread() 做了什么 ?

c.下游任务是如何保证被分配到指定线程的

```

    private void multiThread() {
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                e.onNext("This msg from work thread :" + Thread.currentThread().getName());
                sb.append("\nsubscribe: currentThreadName==" + Thread.currentThread().getName());
            }
        })
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        Log.e(TAG, "accept: s= " + s);
                    }
                });
    }

```

这里通过observeOn(AndroidSchedulers.mainThread())将下游线程切换到了我们非常熟悉的 Android UI 线程。

需要注意的是 AndroidSchedulers 并不是 RxJava 的一部分，是为了在 Android 中方便的使用 RxJava 而专门设计的一个调度器实现，源码[RxAndroid](https://github.com/ReactiveX/RxAndroid) 设计非常巧妙；使用前记得在gradle文件中配置依赖。
