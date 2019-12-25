#### Android gradle自动化构建相关知识点学习

##### 学习参考资料
* Android Gradle 权威指南

###### Groovy基本语法
* 函数

* 闭包

###### Gradle任务

###### Gradle插件
* 二进制插件

* 应用脚本插件

* 应用第三方发布的插件

* 应用plugins DSL应用插件

###### 自定义插件


###### Java Gradle插件

* 源集的操作


###### Android Gradle插件

###### 自定义Android Gradle工程


###### Android Gradle 高级自定义
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



###### Android Gradle 多项目构建

* 库项目单独发布到Maven库


###### Android Gradle 多渠道构建

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
