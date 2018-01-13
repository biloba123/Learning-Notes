## Kotlin学习笔记

### NULL检查机制

```kotlin
//类型后面加?表示可为空
var age: String? = "23" 
//忽略NULL检查，为空时抛出空指针异常
val ages = age!!.toInt()
//age不为空转换为Int，为空不做处理返回 null
val ages1 = age?.toInt()
//age不为空转换为Int，为空返回-1
val ages2 = age?.toInt() ?: -1
//为空返回-1
val age3 = age?:-1
//不为空执行某操作
val age4 = age?.let{ ... }
//不为空执行某操作，为空默认值
val age4 = age?.let{ ... }?:-1
```

### 需要泛型类型信息的泛型函数的简便形式

```kotlin
//一般写法
fun <T> String.jsonToList(classOfT: Class<T>): List<T>{
    val jsonArr= JSONArray(this)
    return List(jsonArr.length()){
        GSON.fromJson(jsonArr[it].toString(), classOfT)
    }
}

//简便写法
inline fun <reified T> String.jsonToList(): List<T>{
    val jsonArr= JSONArray(this)
    return List(jsonArr.length()){
        GSON.fromJson(jsonArr[it].toString(), T::class.java)
    }
}
```

### 单例模式

1. 不带参数

```kotlin
class Singleton private constructor(){
  ...
  
    companion object {
      	//默认LazyThreadSafetyMode.SYNCHRONIZED模式，线程安全
        val instance: MyOkHttp by lazy {
            Singleton()
        }
}
```

2. 带参数

```kotlin
class Singleton private constructor(c: Context){
  ...
  
    companion object {
    	private var single: Singleton?=null
      	
        @Synchronized
        fun getInstance(context: Context): Singleton {
            if (single == null) {
                single = Singleton(context)
            }
            return single as Singleton
        }
}
```



