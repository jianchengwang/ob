---
title: gradle
createdAt: 2020-06-09
categories: 
- java
- tool
tags: 
- gradle
---

软件行业的蓬勃发展，新旧交替，版本构建工具从 **ant -> maven -> gradle** 进行演变，个人不喜欢 maven 的 xml 的配置文件，基于  **Groovy** 语言作为构建脚本语言，相对于 JAVA 开发人员来说比较容易上手，动态语言也比 xml 配置文件更灵活，构建速度也更加的快速，所以入了 gradle 的坑。当然，目前，很多公司还是使用 maven 作为构建工具，所以建议maven 还是要懂点的。

本文仅供自己学习 gradle 的记录笔记，如果你想深入的学习，请戳 [**官方文档**](https://docs.gradle.org/current/userguide/userguide.html)

个人觉得，官方文档是最好的学习文档，我简单过了一遍，感觉官方文档的例子都很精炼，总结得很到位，底下很多代码实例也是来自官方文档。

<!--more-->

## 基本概念

每一次  Gradle  构建都是由一个或者多个 **projects** 组成，

比如一个  **root projects**  依赖多个 **sub projects** **(module)** 项目，每个 projects 又由编译，打包，生成javadoc，发布等多个 **tasks** 组成。**tasks** 之间也可以相互依赖，形成一个依赖关系图以确保他们的执行顺序。

### 项目 Projects

一个项目代表一个正在构建的组件，比如一个jar文件，当构建启动的时候，**Gradle** 会基于 **build.gradle** 实例化一个 **org.gradle.api.Project** 类，并且能够通过 project 变量使其隐式可用。

属性: **group name version**

方法: **apply denpendencies repositories task**

其他配置方式: **ext gradle.properties**

### 任务 Tasks

任务对应 **org.gradle.api.Task** ，主要包括任务动作和任务依赖。任务工作定义了一个最小的工作单元，可以定义依赖其他任务，动作序列和动作执行条件等。

方法: **dependsOn doFirst do Last(<<)**  

### 构建生命周期 Build Lifecycle

A Gradle build has three distinct phases.

- Initialization

  Gradle supports single and multi-project builds. During the initialization phase, Gradle determines which projects are going to take part in the build, and creates a [Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html) instance for each of these projects.

- Configuration

  During this phase the project objects are configured. The build scripts of *all* projects which are part of the build are executed.

- Execution

  Gradle determines the subset of the tasks, created and configured during the configuration phase, to be executed. The subset is determined by the task name arguments passed to the `gradle` command and the current directory. Gradle then executes each of the selected tasks.

### 配置文件 Settings file

简要概述，就是如果你是多项目，那么你要在 root project 里创建 **settings.gradle** 里去定义跟 sub project的关系，这个文件会在所有 project build.gradle 执行前执行。

每个 project 都有一个构建脚本 **build.gradle** 来完成每个 project 的构建。

在构建的时候，你如果需要对 gradle 运行一些通用的构建配置，你可以定义在 **gradle.properties** 里，比如全局的 group 跟 name 等。

另外，gradle 是插件配置，你可以通过 **apply** 引入各种现成的或者自定义的插件构建脚本。

关于 gradle 项目的几种构建脚本或者配置文件描述如下

#### settings.gradle

This is executed during the **initialization** phase.

The main role of settings.gradle is to define all included submodules and to mark the directory root of a tree of modules, so you can only have one `settings.gradle` file in a multi-module project.

```java
rootProject.name = 'project-x'

include 'sub-a', 'sub-b'
```

The settings file is also written in groovy, and submodule lookup can be adapted alot.

#### build.gradle

This is executed during the **configuration** phase.

There is one such file per module, it contains the build logic for this module.

In the `build.gradle` file of the **main module**, you can use `allprojects {}` or `subprojects {}`to define settings for all other modules.

In the `build.gradle` file of the submodules, you can use `compile project(':sub-a')` to make one submodule depend on the other.

#### gradle.properties

This is optional, it's main purpose is to provide startup options to use for running gradle itself, e.g.

```java
org.gradle.jvmargs=-Dfile.encoding=UTF-8 ...
org.gradle.configureondemand=true
```

#### gradle/utils.gradle

(Any name of folder or file is possible.) You can define additional custom gradle files to reuse definitions, and include them in other gradle files via

```java
apply from: "$rootDir/gradle/utils.gradle"
```

## Groovy 基础

Groovy是一门jvm语言，它最终是要编译成class文件然后在jvm上执行，所以Java语言的特性Groovy都支持，我们完全可以混写Java和Groovy。

既然如此，那Groovy的优势是什么呢？简单来说，Groovy提供了更加灵活简单的语法，大量的语法糖以及闭包特性可以让你用更少的代码来实现和Java同样的功能。比如解析xml文件，Groovy就非常方便，只需要几行代码就能搞定，而如果用Java则需要几十行代码。

### 基本语法

深入学习或者有问题请戳 [**Groovy Api文档**](http://docs.groovy-lang.org/latest/html/groovy-jdk/index-all.html)

```groovy
// 完全兼容 java 的语法

// 分号可选

// 类，方法默认都是 public

// 编译器给属性自动添加 getter/setter 方法

// 属性可以直接通过 . 号获取 

// == 调用 equals 方法，判断对象是否同一个使用.is()
Object a = new Object()
Object b = a.clone()

assert a == b
assert !a.is(b)

// 弱类型语言，可以直接用 def 定义
def version = 1

// assert 语句
version = null
assert version == 2

// 括号可选
println version

// 方法返回值可省略
def hello() {
    1;
}

// 字符串

// .1 单引号仅仅表示字符
def s1 = 'hello world'

// .2 双引号可以引用变量
def world = 'world'
def s2 = "hello ${world}"

// .3 三个引号可以换行
def s3 = ''' hello 
world
'''

// 循环
for (i in 1..5){
    println("hello world")
}
4.times {
    println it
}

// 集合api

// .1 list -> ArrayList
def buildTools = ['ant', 'maven']
buildTools << 'gradle'
assert buildTools.getClass() == ArrayList
assert buildTools.size

// .2 map -> LinkedHashMap
def map = 'a':'b'
def buildYears = ['ant': 2000, 'maven': 2004]
buildYears.gradle = 2009
println buildYears.ant
println buildYears['ant']
println buildYears.getClass()

// 闭包 -> 类似于c语言的函数指针，可以作为方法的参数和返回值，也可以作为一个变量而存在。
def c1 = {
    v ->
    	println v
}
def c2 = {
    println 'world'
}
def method1(Closure closure) {方法: apply denpendencies repositories task


    closure('hello')
}
def method2(Closure closure) {
    closure()
}

c1.call('hello')
c1('hello')
c2.call()
method1(c1)
method2(c2)

// io
def file = new File("a.txt")

println "read file using two parameters"
file.eachLine { line, lineNo ->

    println "${lineNo} ${line}"

}

println "read file using one parameters"
file.eachLine { line ->

    println "${line}"

}

file.eachLine("utf-8") {
    println it
}

// wich 操作符
Book bk = new Book()
bk.id = 1
bk.name = "android art"
bk.press = "china press"

Book bk = new Book() 
bk.with {
    id = 1
    name = "android art"
    press = "china press"
}

// 判断是否为真
if (name) {}

// 三元表达式
def result = name ?: "Unknown"

// 非空判断
println order?.customer?.address

// switch
def x = 1.23
def result = ""
switch (x) {
    case "foo": result = "found foo"

    // lets fall through

    case "bar": result += "bar"
    case [4, 5, 6, 'inList']: result = "list"
    break

    case 12..30: result = "range"
    break

    case Integer: result = "integer"
    break

    case Number: result = "number"
    break

    case { it > 3 }: result = "number > 3"
    break

    default: result = "default"
}
assert result == "number"

```

### 构建脚本解析

详细介绍或者想深入了解请戳 [**官方api**](https://docs.gradle.org/current/javadoc/org/gradle/api/Project.html#apply(java.util.Map))

```groovy

//  PluginAware.apply(java.util.Map) 
apply plugin:'java'

version = '0.1'

// void	repositories​(Closure configureClosure)	
// Configures the repositories for this project.
repositories {
    mavenCentral()
}

// void dependencies​(Closure configureClosure)
// Configures the dependencies for this project.
dependencies {
    // https://mvnrepository.com/artifact/com.google.guava/guava
	compile group: 'com.google.guava', name: 'guava', version: '27.1-jre'
}
```

## Todo

### 使用插件

Gradle的设计理念是，所有有用的特性都由[Gradle插件](http://www.gradle.org/docs/current/userguide/plugins.html)提供，一个Gradle插件能够：

- 在项目中添加新任务

- 为新加入的任务提供默认配置，这个默认配置会在项目中注入新的约定（如源文件位置）。

- 加入新的属性，可以覆盖插件的默认配置属性。

- 为项目加入新的依赖。

Gradle 用户手册提供了 [**一系列标准的gradle插件**](https://docs.gradle.org/current/userguide/plugin_reference.html)  

#### The Java Plugin

比如 **The Java Plugin**， 提供了 java 相关的目录结构，属性配置，构建任务等等

如果我们使用标准的gradle插件，

注意这个不能在配置在 **multi-project configurations(subprojects`, `allprojects)**

```groovy
plugins: {
    id: 'java'
}
```

当然我们也可以使用 **apply** 方法，就比较灵活了

```groovy
apply plugin: 'java'
```

我们还可以定义 jvm 的编译级别

```groovy
sourceCompatibility: 1.8
targetCompatibility: 1.8
```

##### Project Layout

当我们使用 java 这个插件的时候，它默认的项目结构是

- `src/main/java`

  Production Java source.

- `src/main/resources`

  Production resources, such as XML and properties files.

- `src/test/java`

  Test Java source.

- `src/test/resources`

  Test resources.

- `src/*sourceSet*/java`

  Java source for the source set named *sourceSet*.

- `src/*sourceSet*/resources`

  Resources for the source set named *sourceSet*.

当然我们也可以更改它

```groovy
sourceSets {
    main {
        java {
            srcDirs = ['src/java']
        }
        resources {
            srcDirs = ['src/resources']
        }
    }
}
```

##### Dependency management

- `compile`(Deprecated)

  Compile time dependencies. Superseded by `implementation`.

- `implementation` extends `compile`

  Implementation only dependencies.

- `compileOnly`

  Compile time only dependencies, not used at runtime.

- `compileClasspath` extends `compile, compileOnly, implementation`

  Compile classpath, used when compiling source. Used by task `compileJava`.

- `annotationProcessor`

  Annotation processors used during compilation.

- `runtime`(Deprecated) extends `compile`

  Runtime dependencies. Superseded by `runtimeOnly`.

- `runtimeOnly`

  Runtime only dependencies.

- `runtimeClasspath` extends `runtimeOnly, runtime, implementation`

  Runtime classpath contains elements of the implementation, as well as runtime only elements.

- `testCompile`(Deprecated) extends `compile`

  Additional dependencies for compiling tests. Superseded by `testImplementation`.

- `testImplementation` extends `testCompile, implementation`

  Implementation only dependencies for tests.

- `testCompileOnly`

  Additional dependencies only for compiling tests, not used at runtime.

- `testCompileClasspath` extends `testCompile, testCompileOnly, testImplementation`

  Test compile classpath, used when compiling test sources. Used by task `compileTestJava`.

- `testRuntime`(Deprecated) extends `runtime, testCompile`

  Additional dependencies for running tests only. Used by task `test`. Superseded by `testRuntimeOnly`.

- `testRuntimeOnly` extends `runtimeOnly`

  Runtime only dependencies for running tests. Used by task `test`.

- `testRuntimeClasspath` extends `testRuntimeOnly, testRuntime, testImplementation`

  Runtime classpath for running tests.

- `archives`

  Artifacts (e.g. jars) produced by this project. Used by tasks `uploadArchives`.

- `default` extends `runtime`

  The default configuration used by a project dependency on this project. Contains the artifacts and dependencies required by this project at runtime.

##### jar

插件也会提供很多构建任务，比如 **compileJava** ， **processResources**，**classes**，**jar**，**clean**，**test** 等等，这里只简要地提一下 **jar** 构建的 **Manifest**

Each jar or war object has a `manifest` property with a separate instance of [Manifest](https://docs.gradle.org/current/javadoc/org/gradle/api/java/archives/Manifest.html). When the archive is generated, a corresponding `MANIFEST.MF` file is written into the archive.

```groovy
jar {
    from { configurations.compile.collect { it.isDirectory() ? it : zipTree(it) } }
    manifest {
        attributes 'Main-Class': 'cn.jianchengwang.todo.gradle.HelloWorld'
    }
}
```

#### The War Plugin

The War plugin extends the Java plugin to add support for assembling web application WAR files. It disables the default JAR archive generation of the Java plugin and adds a default WAR archive task.

这里只显示一些简单的配置脚本

```groovy
apply plugin: 'war'

configurations {
   moreLibs
}

repositories {
   flatDir { dirs "lib" }
   jcenter()
}

dependencies {
    implementation module(":compile:1.0") {
        dependency ":compile-transitive-1.0@jar"
        dependency ":providedCompile-transitive:1.0@jar"
    }
    providedCompile "javax.servlet:servlet-api:2.5"
    providedCompile module(":providedCompile:1.0") {
        dependency ":providedCompile-transitive:1.0@jar"
    }
    runtimeOnly ":runtime:1.0"
    providedRuntime ":providedRuntime:1.0@jar"
    testImplementation "junit:junit:4.12"
    moreLibs ":otherLib:1.0"
}

war {
    from 'src/rootContent' // adds a file-set to the root of the archive
    webInf { from 'src/additionalWebInf' } // adds a file-set to the WEB-INF dir.
    classpath fileTree('additionalLibs') // adds a file-set to the WEB-INF/lib dir.
    classpath configurations.moreLibs // adds a configuration to the WEB-INF/lib dir.
    webXml = file('src/someWeb.xml') // copies a file to WEB-INF/web.xml
}
```

其他还有很多官方标准插件这里就不都叙述了，还有怎么编写 gradle 插件，官方文档里有详细的介绍，有兴趣可以去了解下。

### 自定义任务

```groovy

task hello {
    doLast -> {
        println 'hello world'
    }
}

task count {
    << {
        4.times { print "$it " }
    }
}

// Task dependencies
task intro {
    dependsOn hello
    doLast {
        println "I'm Gradle"
    }
}

// Dynamic tasks
4.times { counter ->
    task "task$counter" {
        doLast {
            println "I'm task number $counter"
        }
    }
}

// Manipulating existing tasks
hello.doFirst {
    println 'Hello Venus'
}
hello.configure {
    doLast {
        println 'Hello Mars'
    }
}
hello.configure {
    doLast {
        println 'Hello Jupiter'
    }
}

// Extra task properties
task myTask {
    ext.myProperty = "myValue"
}

task printTaskProperties {
    doLast {
        println myTask.myProperty
    }
}

// Default tasks
// Gradle allows you to define one or more default tasks that are executed if no other tasks are specified.
defaultTasks 'clean', 'run'

task clean {
    doLast {
        println 'Default Cleaning!'
    }
}

task run {
    doLast {
        println 'Default Running!'
    }
}

task other {
    doLast {
        println "I'm not a default task!"
    }
}

// working with files
// .1 Copy
task copyReport(type: Copy) {
    from file("$buildDir/reports/my-report.pdf")
    into file("$buildDir/toArchive")
}

task copyReportsForArchiving(type: Copy) {
    from "$buildDir/reports/my-report.pdf", "src/docs/manual.pdf"
    into "$buildDir/toArchive"
}

task copyPdfReportsForArchiving(type: Copy) {
    from "$buildDir/reports"
    include "*.pdf"
    into "$buildDir/toArchive"
}

// .2 Archive or UnPack
task packageDistribution(type: Zip) {
    archiveFileName = "my-distribution.zip"
    destinationDirectory = file("$buildDir/dist")

    from "$buildDir/toArchive"
}

task unpackFiles(type: Copy) {
    from zipTree("src/resources/thirdPartyResources.zip")
    into "$buildDir/resources"
}

// .3 Creating "uber" or "fat" JARs
plugins {
    id 'java'
}

version = '1.0.0'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'commons-io:commons-io:2.6'
}

task uberJar(type: Jar) {
    archiveClassifier = 'uber'

    from sourceSets.main.output

    dependsOn configurations.runtimeClasspath
    from {
        configurations.runtimeClasspath.findAll { it.name.endsWith('jar') }.collect { zipTree(it) }
    }
}
```

### 管理依赖

#### 依赖类型

```groovy
// module dependencies
dependencies {
    runtime group: 'org.springframework', name: 'spring-core', version: '2.5'
    implementation 'org.springframework:spring-web:5.+'
    implementation('org.ow2.asm:asm:6.0') {
        because 'we require a JDK 9 compatible bytecode generator'
    }
}

// file dependencies
dependencies {
    runtime files('libs/a.jar', 'libs/b.jar')
    runtime fileTree('libs') { include '*.jar' }
}

// project dependencies
dependencies {
    implementation project(':shared')
}
```

#### 仓库类型

```groovy
// flat dir
repositories {
    flatDir {
        dirs 'lib'
    }
    flatDir {
        dirs 'lib1', 'lib2'
    }
}

// maven repo
repositories {
    mavenCentral()
  	jcenter()
	google()
    mavenLocal()
    maven {
        url "http://repo.mycompany.com/maven2"
    }
    maven {
        url "http://repo.mycompany.com/snapshots"
        mavenContent {
            snapshotsOnly()
        }
    }
    maven {
        url "sftp://repo.mycompany.com:22/maven2"
        credentials {
            username "user"
            password "password"
        }
    }
    
}
```

#### 版本冲突

如果存在依赖的版本冲突，那么 gradle 默认会选择最高版本的的依赖以解决依赖冲突，当然我们也可以手动解决，比如排除依赖，或者手动设置一个版本。当然，一般使用 gradle 默认的处理就可以了。

```groovy
//  Excluding transitive dependency for a particular dependency declaration
dependencies {
    implementation('log4j:log4j:1.2.15') {
        exclude group: 'javax.jms', module: 'jms'
        exclude group: 'com.sun.jdmk', module: 'jmxtools'
        exclude group: 'com.sun.jmx', module: 'jmxri'
    }
}

// Excluding transitive dependency for a particular configuration
configurations {
    implementation {
        exclude group: 'javax.jms', module: 'jms'
        exclude group: 'com.sun.jdmk', module: 'jmxtools'
        exclude group: 'com.sun.jmx', module: 'jmxri'
    }
}

dependencies {
    implementation 'log4j:log4j:1.2.15'
}

// Enforcing a dependency version
dependencies {
    implementation 'org.apache.httpcomponents:httpclient:4.5.4'
    implementation('commons-codec:commons-codec:1.9') {
        force = true
    }
}
```

### 多项目构建

多项目构建就是在 **root project**配置一个 **settings.gradle** 引入需要的子项目即可，

比如我这边 **root project** 为  todo-gradle，有三个 **sub project** ，

则 **settings.gradle** 配置如下

```groovy
rootProject.name = 'todo-gradle'
include 'web'
include 'model'
include 'dao'
```

如果，**sub project** 很多配置项一样，那我们可以在 **root project** 的 **build.gradle** 构建脚本里进行通用的配置，使其对子项目生效，

```groovy
allprojects {

    apply plugin: 'java'

    sourceCompatibility = 1.8
}

subprojects {

    repositories {
        mavenCentral()
    }


    dependencies {
        testCompile group: 'junit', name: 'junit', version: '4.12'
    }


}
```

如果自定义的配置的任务或者选项，则在每个 **project** 的 **build.gradle** 构建脚本里配置即可，比如我 **web sub project** 还引入了 **The War Plugin**，那么我在 **web sub project** 的**build.gradle** 构建脚本可以配置如下

```groovy
apply plugin: 'war'
```

最后就是项目依赖配置了，比如我 **dao sub project** 依赖 **model sub project**

```groovy
dependencies {
    implementation project(':model')
}
```

因为依赖是可以传递的，所以假如我 **web sub project** 依赖 **dao sub project** 跟 **model sub project**，而 **dao sub project** 又依赖 **model sub project**，实际上我只要在 **web sub projec**t 构建脚本里面依赖 **dao sub project** 即可

```groovy

dependencies {
    implementation project(':dao')
}

```

如果存在依赖关系，显而易见，那么被依赖的项目或者任务都会先执行。

### 测试

一般只要类继承 **junit.framework.TestCase** 或者 **groovy.util.GroovyTestCase** 

或是假如使用 springboot 框架，带有 **@RunWith** 注解的类

最常见的就是任何至少一个包含 **@Test** 注解的类

当然，不同测试框架使用大同小异，你可以具体使用的时候查阅下资料即可，

下面官方的代码实例就显示了 **test task** 的相关配置。 

Executes JUnit (3.8.x, 4.x or 5.x) or TestNG tests. Test are always run in (one or more) separate JVMs. The sample below shows various configuration options.

```groovy
apply plugin: 'java' // adds 'test' task

test {
  // enable TestNG support (default is JUnit)
  useTestNG()
  // enable JUnit Platform (a.k.a. JUnit 5) support
  useJUnitPlatform()

  // set a system property for the test JVM(s)
  systemProperty 'some.prop', 'value'

  // explicitly include or exclude tests
  include 'org/foo/**'
  exclude 'org/boo/**'

  // show standard out and standard error of the test JVM(s) on the console
  testLogging.showStandardStreams = true

  // set heap size for the test JVM(s)
  minHeapSize = "128m"
  maxHeapSize = "512m"

  // set JVM arguments for the test JVM(s)
  jvmArgs '-XX:MaxPermSize=256m'

  // listen to events in the test execution lifecycle
  beforeTest { descriptor ->
     logger.lifecycle("Running test: " + descriptor)
  }

  // Fail the 'test' task on the first test failure
  failFast = true

  // listen to standard out and standard error of the test JVM(s)
  onOutput { descriptor, event ->
     logger.lifecycle("Test: " + descriptor + " produced standard out/err: " + event.message )
  }
}
```

一般测试的最终结果，会显示在 **project** 的 **build/reports** 目录里面

### 发布

假如我们开发完一个功能模块，打包成一个 jar 包，需要提供给他人依赖引用，那么我们就可以发布出去了，一般 gradle 没有自己的仓库，如果是java 项目的话一般都是发布到 **maven** 仓库，因为 **lvh** 现在用的人比较少了，这里就简单的介绍一下怎么发布到 maven 仓库

#### Maven Publish Pluging

```groovy
apply plugin: 'maven-publish'

publishing {
    
    publications {
        maven(MavenPublication) {
            groupId = 'org.gradle.sample'
            artifactId = 'project1-sample'
            version = '1.1'

            from components.java
        }
    }
    
    repositories {
        maven {
            name = 'myRepo'
            def releasesRepoUrl = "$buildDir/repos/releases"
            def snapshotsRepoUrl = "$buildDir/repos/snapshots"
            url = version.endsWith('SNAPSHOT') ? snapshotsRepoUrl : releasesRepoUrl
        }
    }
}
```

## 相关链接

[**官方用户手册**](https://docs.gradle.org/current/userguide/userguide.html)

[**慕课网新一代构建工具gradle**](https://www.imooc.com/learn/833)

[**TODO 代码**](https://github.com/jianchengwang/todo-java)