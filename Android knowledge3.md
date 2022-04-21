#### 一、flutter

##### enviroment config

**step1**:Download the flutter 1.22.1 zip file

URL: https://storage.googleapis.com/flutter_infra/releases/stable/macos/flutter_macos_1.22.1-stable.zip

**step2**:setup your /User/{user}/.bash_profile file

**step3**:add path into .bash_profile file as follow:

export FLUTTER="{flutterDirectory}/flutter" ,

export DART_HOME="$FLUTTER/bin/cache/dart-sdk/bin"

export PATH=${PATH}:${FLUTTER}/bin:${DART_HOME}

**step4**:run flutter --version   (check whether it is installed successfully)

**step5**:config dart sdk path in AS

**step6**:add flutter plugin from plugin market in AS

##### grammar

######        1.Asynchronous 

   A.Future.whenComplete 

   Same as finally in java

   B.Future.wait

   Wait for multiple asynchronous tasks to finish before doing anything.for example:

  ```
Future.wait([
  // 2秒后返回结果  
  Future.delayed(new Duration(seconds: 2), () {
    return "hello";
  }),
  // 4秒后返回结果  
  Future.delayed(new Duration(seconds: 4), () {
    return " world";
  })
]).then((results){
  print(results[0]+results[1]);
}).catchError((e){
  print(e);
});
  ```

C.Callback Hell

One:use Future resolve Callback Hell,as follow:

```
login("alice","******").then((id){
      return getUserInfo(id);
}).then((userInfo){
    return saveUserInfo(userInfo);
}).then((e){
   //执行接下来的操作 
}).catchError((e){
  //错误处理  
  print(e);
});
```

Two:use Async/await resolve Callback Hell,as follow:

```
task() async {
   try{
    String id = await login("alice","******");
    String userInfo = await getUserInfo(id);
    await saveUserInfo(userInfo);
    //执行接下来的操作   
   } catch(e){
    //错误处理   
    print(e);   
   }  
}
```

D.Stream

It can receive the results of multiple asynchronous operations

```
Stream.fromFutures([
  // 1秒后返回结果
  Future.delayed(new Duration(seconds: 1), () {
    return "hello 1";
  }),
  // 抛出一个异常
  Future.delayed(new Duration(seconds: 2),(){
    throw AssertionError("Error");
  }),
  // 3秒后返回结果
  Future.delayed(new Duration(seconds: 3), () {
    return "hello 3";
  })
]).listen((data){
   print(data);
}, onError: (e){
   print(e.message);
},onDone: (){

});
the output:
I/flutter (17666): hello 1
I/flutter (17666): Error
I/flutter (17666): hello 3
```



#### 二、协程

interface calblack

1.await

```
@Throws(Throwable::class)
suspend fun <T> Observable<ResponseWrapper<T>>.awaitThrows(dispatcher: CoroutineDispatcher = Dispatchers.IO): T? {
    return withContext(dispatcher) {

        suspendCancellableCoroutine<T?> { cont ->

            doOnSubscribe { disposable ->
                cont.invokeOnCancellation {
                    disposable.dispose()
                }
            }.subscribeWith(object : HttpSubscriber<T>() {
                override fun success(result: T?) {
                    cont.resume(result)
                }

                override fun error(e: Throwable) {
                    cont.resumeWithException(e)
                }
            })
        }
    }
}

suspend fun <T> Observable<ResponseWrapper<T>>.await(dispatcher: CoroutineDispatcher = Dispatchers.IO): BncResponse<T> {
    return try {
        val result = awaitThrows(dispatcher)
        BncResponse(result = result)
    } catch (e: Throwable) {
        BncResponse(error = e)
    }
}

suspend fun <T> Observable<ResponseWrapper<T>>?.await(onSuccess: (T?) -> Unit, onError: (Throwable) -> Unit) {
    if (this == null) {
        onSuccess(null)
        return
    }
    try {
        val result: T? = awaitThrows()
        onSuccess(result)
    } catch (e: Throwable) {
        onError(e)
    }
}

suspend fun <T> Observable<ResponseWrapper<T>>?.await(onSuccess: (T?) -> Unit) {
    await(onSuccess, onError = {
        LogUtil.logException(it)
    })
}
```

2.callback data

```
data class BncResponse<T>(
    val result: T? = null,
    val error: Throwable? = null
) {
    fun onSuccess(onSuccess: (T) -> Unit): BncResponse<T> {
        result?.let(onSuccess)
        return this
    }

    fun onError(onError: (Throwable) -> Unit): BncResponse<T> {
        error?.let(onError)
        return this
    }

    fun onEmpty(onEmptyResponse: () -> Unit): BncResponse<T> {
        if (result == null && error == null) {
            onEmptyResponse()
        }
        return this
    }
}
```

#### 三、dos命令

1.**vim .zshrc**  

**click esc exit edit mode**

2.source .zshrc

click i 去edit



export JAVA_8_HOME="/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home"

export JAVA_11_HOME="/Library/Java/JavaVirtualMachines/microsoft-11.jdk/Contents/Home"

export JAVA_15_HOME="/Users/user/Library/Java/JavaVirtualMachines/openjdk-15.0.1/Contents/Home"



alias java8='export JAVA_HOME=$JAVA_8_HOME'

alias java11='export JAVA_HOME=$JAVA_11_HOME'

alias java15='export JAVA_HOME=$JAVA_15_HOME'

**default to Java 11**

java11

3.open . 大佬文件

4.curl -L -u "worldpay_aw3ds_eric.yi":">t2iY3}yK$<g,40R0Qg?" https://cardinalcommerceprod.jfrog.io/artifactory/android/org/jfrog/cardinalcommerce/gradle/cardinalmobilesdk/2.2.5-4/cardinalmobilesdk-2.2.5-4.aar --output cardinalsdk.aar

5.cat /etc/hosts

6.cd .gradle

7.sudo vim /etc/hosts

8.java --version  //java11  java -version //1.8以下

9.echo $JAVA_HOME   echo $PATH

10.ls  /usr/local/bin|grep java          ls  /usr/bin|grep java

11./usr/bin/java  -version

12.brew list       brew list |grep jdk     brew list |grep java

13.rm  /usr/bin/java*      sudo rm  /usr/bin/java*

14.open  /usr/bin

15.sdk  list  java

16.sdk install java 11.0.13-ms

17.sdk home java     sdk home java 11.0.13-ms

18.q 退出

19.配置java11

export JAVA_11_HOME="/Users/user/.sdkman/candidates/java/11.0.13-ms"

alias java11='export JAVA_HOME=$JAVA_11_HOME'

default to Java 11

java11

### 四、Unit test

官网:https://mockk.io/

Binance command:

./gradlew :MavenDep:fiat-paymentsdk:coverage

./gradlew :MavenDep:fiat-paymentsdk:bnbTest

1.验证带参数的私有方法

```kotlin
InternalPlatformDsl.dynamicSet(autoBannerViewPagerMock, "mBannerList", list)
every { autoBannerViewPagerMock.invoke("loadCoverImage") withArguments listOf(any<Int>(), any<Int>(), any<ImageView>(), any<stMetaBanner>()) } returns Unit
```

2.执行带参数的私有方法

```
      // 调用私有方法设置值
InternalPlatformDsl.dynamicCall(mockStudent, "setName", arrayOf("newName")) { mockk() } // 后面参数是协程相关的参数这里用不上
      // 调用私有方法返回值
 value = InternalPlatformDsl.dynamicCall(mockStudent, "getName", arrayOf()) { mockk() }
```

3.设置私有属性的值

```
  val mockStudent = spyk(Student(), recordPrivateCalls = true)
        // 设置私有属性的值
        InternalPlatformDsl.dynamicSet(mockStudent, "name", "Nancy")
        // 获取私有属性的值
        Assert.assertEquals(InternalPlatformDsl.dynamicGet(mockStudent, "name"), "Nancy")
```

### 五、script

```
//usr/bin/env echo '
/**** BOOTSTRAP kscript ****\'>/dev/null
command -v kscript >/dev/null 2>&1 || source /dev/stdin <<< "$(curl -L https://git.io/fpF1K)"
exec kscript $0 "$@"
\*** IMPORTANT: Any code including imports and annotations must come after this line ***/
```

Command:

1.touch text.kts

2.kscript --idea test.kts

3.kscript  src/text.kts

4.chmod a+s

5.cp  scr/text.kts  /usr/local/bin/testk

6.
