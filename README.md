#### Android gradle自动化构建相关知识点学习

##### 学习参考资料
* Android Gradle 权威指南

###### 1、Gradle基本入门介绍
* 1、gradle/wrapper
    * (1) wrapper 就是对gradle的一层封装，便于团队开发过程中统一Gradle的构建版本
    * (2) 生成wrapper: 在项目的根目录下运行 gradlew wrapper(mac下运行 ./gradlew wrapper )命令
* 2、gradle 常用命令行
    * (1) 查看所有可执行的任务 gradlew tasks
    * (2) 查看task的帮助信息 gradlew help --task :your_taskName
    * (3) 强制刷新依赖 gradlew--refresh-dependencies assemble  这种可解决缓存引起的，依赖库在代码里已经修改可以运行后依赖还是旧版本的问题。

###### 1、Groovy基本语法

* Groovy完全兼容Java,你可以在build脚本文件里面写任何的java代码，非常灵活方便

* 1、字符串
    * (1) Groovy中单引号双引号都能表示字符串
    * (2) 区别: 单引号的字符串中没用运算能力，双引号字符串则具有运算能力。
        * 列子:
        ```java
           def name = "张三"
           println '单引号的变量计算: ${name}'
           println "双引号的变量计算: ${name}"

           输出结果:
           单引号的变量计算: ${name}
           双引号的变量计算: 张三
        ```
* 2、集合
    * (1) List
        ```java
            task pintList{
                def numList = [1,2,3,4,5,6,7]
                println numList.getClass().name
                println numList[1] ///访问第二个元素
                println numList[-1] ///访问第最后一个元素
                println numList[-2] ///访问第倒数第二个元素
                println numList[1..3] ///访问第二个到第四个元素

                ///利用each函数迭代元素
                numList.each{
                    println it
                }
            }
        ```
    * (2) Map
        task printlnMap{
            def map1 = ['width': 1024, 'height':768]
            println map1.getClass().name

            println map1['width']
            println map1.height

            map1.each{
                println "Key:${it.key}, Value:${it.value}"
            }
        }
* 3、函数
    * Groovy中函数的定义声明的语法，跟其他面向函数编程语言的语法是非常的相似的，列如kotlin方法的声明和定义。
    * (1) 函数的括号是可以省略的
        ```java
            task invokeMethod{
                method1(1,2)
                method1 1,2 ///括号可以省略
            }
            def method1(int a, int b){
                println a+b
            }
        ```
    * (2) return关键字是可以省略的
      ```java
        task printlnMethodReturn{
            def add1 = method2 1,2
            def add2 = method2 5,3
            println "add1:${add1},add2:${add2}"
        }
        def method2(int a, int b){
            if(a>b){
                a
            }else{
                b
            }
        }
      ```
    * (3) 代码块可以做为参数传递，其实这就是闭包参数的用法
        ```java
            ///numList是一个List
            numList.each({ println it})

            ///如果方法的最后一个参数是闭包，则闭包可以放到方法外面
            numList.each(){
                println it
            }

            ///方法的扩号可以省略，就变成了我们平时经常看到的样式了
            numList.each{
                println it
            }
        ```


* 4、闭包
    * 闭包其实就是一段代码块
    * (1) 集合的each方法我们前面已经用过了，下面我们就以其伪例，实现一个类似的闭包功能。
      ```java
        task helloClosure{
            customEach{
                println it
            }
        }

        def customEach(closure){
            ///模拟一个十个元素的集合，开始迭代
            for( int i in 1..10){
                closure(i) /// 这里调用的就是上面{ println it} 代码块
            }
        }
      ```
###### 2、Gradle任务
 * Gradle中有很多创建task的方式，这主要是依赖于Project给我们提供的快捷方法以及TaskContainer  
 提供的相关Create方法。下面我们就来看下到底有哪些创建task的方式。
 
 * 1、直接以一个任务名字创建任务的方式
   ```groovy

     def Task createTask1 = task(createTask1)
     createTask1.doLast{
         println "....."
     }
   ```
* 2、以一个任务名+任务配置的map
   ```groovy

   def createTask2 = task(createTask2, group:BasePlugin.BUILD_GROUP)
   createTask2.doLast {
       println "创建任务的方法原型为 Task task(Map<String, ?> args, String name) throws InvalidUserDataException"
   }
   ```
* 3、名字+闭包的形式
   ```groovy
    ///创建task的方式三 名字+闭包
    task creatTask3{
        description "演示任务创建"
        doLast{
            println "创建任务的原型为 Task task(String name, Closure configureClosure);"
            println "任务描述: $description"
        }
    }
   ```
* 4、使用TaskContainer对象的create方法来创建
   ```groovy
    tasks.create("createTask4") {
        description "演示任务创建"
        doLast {
            println "创建任务的原型为 Task task(String name, Closure configureClosure);"
            println "任务描述: $description"
        }
    }
   ```


###### 3、Gradle插件
* 二进制插件

* 应用脚本插件

* 应用第三方发布的插件

* 应用plugins DSL应用插件

###### 4、自定义插件


###### 5、Java Gradle插件

* 源集的操作


###### 6、Android Gradle插件

###### 7、自定义Android Gradle工程


###### 8、Android Gradle 高级自定义
* 分模块管理gradle文件内容 ext的用法

* gradle中执行shell脚本命令
    * git中 动态获取版本号和版本名称(实际中好像不太实用)
    ```java
    def getAppVersionName() {
        def stdout = new ByteArrayOutputStream()
        exec {
            //获取当前最新的tag
            commandLine 'git', 'describe', '--abbrev=0', '--tags'
            standardOutput = stdout
        }
        return stdout.toString()
    }

    def getVersionCode() {
        def stdout = new ByteArrayOutputStream()
        exec {
            commandLine 'git', 'tag', '--list'
            standardOutput = stdout
        }
        return stdout.toString().split("\n").size()
    }

    task VersionTask {
        println " version ${getAppVersionName()} veriosnCode = ${getVersionCode()}"
    }
    ```
* 属性文件中动态获取版本信息

* 隐藏签名文件信息
    * 1、签名文件信息存放在服务器上，打包的时候从打包服务器动态获取设置。
    * 2、签名信息以环境变量的形式配置到打包服务器中，打包的时候通过 System.getenv("Name")函数去获取
    * 3、示例展示
    ```java
    signingConfigs{
        def appStoreFile = System.getenv("STORE_FILE")
        def appStorePassword = System.getenv("STORE_PASSWORD")
        def appKeyAlias = System.getenv("KEY_ALIAS")
        def appKeyPassword = System.getenv("KEY_PASSWORD")
        release{
            storeFile file(appStoreFile)
            storePassword appStorePassword
            keyAlias appKeyAlias
            keyPassword appKeyPassword
        }
    }

    buildTypes{
        release{
            signingConfig signingConfigs.release
        }
    }
    ```



###### 9、Android Gradle 多项目构建

* 库项目单独发布到Maven库


###### 10、Android Gradle 多渠道构建

* 理解dimension的作用
    * 给Flavor分组，把不同Flavor中的相同部分抽取出来。避免写重复的代码

    * 维度的优先级
    ```java
    android{
        defaultConfig{
            applicationId "com.app.example"
            minSdkVersion 14
            targetSdkVersion 23
            versionCode 1
            versionName '1.0.0'
        }

        flavorDimensions 'abi', 'version'
    }
    这里的优先级 abi>version>defaultConfig
    优先级非常重要，优先级高的flavor会替换掉优先级低的资源、代码、配置等。
    (variant)名字的构成 = ProductFlavor(dimension优先级高的在前面)+buildType
    ```
* 提高多渠道构建的效率


------多种方式访问任务
