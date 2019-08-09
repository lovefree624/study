# Maven入门知识

Maven 是 Apache 下的一个纯 Java 开发的项目管理工具，可以对 Java 项目进行构建、依赖管理。

## 约定配置

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

|                目录                |                             作用                             |
| :--------------------------------: | :----------------------------------------------------------: |
|             ${basedir}             |                  存放pom.xml和所有的子目录                   |
|      ${basedir}/src/main/java      |                       项目的java源代码                       |
|   ${basedir}/src/main/resources    |        项目的资源，比如说property文件，springmvc.xml         |
|      ${basedir}/src/test/java      |                项目的测试类，比如说Junit代码                 |
|   ${basedir}/src/test/resources    |                         测试用的资源                         |
| ${basedir}/src/main/webapp/WEB-INF | web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面 |
|         ${basedir}/target          |                         打包输出目录                         |
|     ${basedir}/target/classes      |                         编译输出目录                         |
|   ${basedir}/target/test-classes   |                       测试编译输出目录                       |

## Maven POM

POM( Project Object Model，项目对象模型 ) 是 Maven 工程的基本工作单元，是一个XML文件，包含了项目的基本信息，用于描述项目如何构建，声明项目依赖，等等。

执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。

所有 POM 文件都需要 project 元素和三个必需字段：groupId，artifactId，version。

样例

```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <!-- 模型版本 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成 -->
    <groupId>com.58suo.mes</groupId>
 
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>718mes</artifactId>
 
    <!-- 版本号 -->
    <version>1.0</version>
</project>
```



|     节点     |                             描述                             |
| :----------: | :----------------------------------------------------------: |
|   project    |                         工程的根标签                         |
| modelVersion |                    模型版本需要设置为 4.0                    |
|   groupId    | 这是工程组的标识。它在一个组织或者项目中通常是唯一的<br />一般是网站域名的反写 |
|  artifactId  |                        这是工程的标识                        |
|   version    |                         工程的版本号                         |

## Maven 构建生命周期

不同的生命周期一般还有不同的阶段，当我们执行 mvn post-clean 命令时，Maven 调用 clean 生命周期，它包含以下阶段：

- pre-clean：执行一些需要在clean之前完成的工作
- clean：移除所有上一次构建生成的文件
- post-clean：执行一些需要在clean之后立刻完成的工作

mvn clean 中的 clean 就是上面的 clean，在一个生命周期中，运行某个阶段的时候，它之前的所有阶段都会被运行，但不包含之后的周期。

重要的周期如下：

|       生命周期        |                             描述                             |
| :-------------------: | :----------------------------------------------------------: |
|       validate        | 检查工程配置是否正确，完成构建过程的所有必要信息是否能够获取到。 |
|      initialize       |                初始化构建状态，例如设置属性。                |
|   generate-sources    |             生成编译阶段需要包含的任何源码文件。             |
|    process-sources    |      处理源代码，例如，过滤任何值（filter any value）。      |
|  generate-resources   |               生成工程包中需要包含的资源文件。               |
|   process-resources   |      拷贝和处理资源文件到目的目录中，为打包阶段做准备。      |
|        compile        |                        编译工程源码。                        |
|    process-classes    |   处理编译生成的文件，例如 Java Class 字节码的加强和优化。   |
| generate-test-sources |            生成编译阶段需要包含的任何测试源代码。            |
| process-test-sources  |    处理测试源代码，例如，过滤任何值（filter any values)。    |
|     test-compile      |                编译测试源代码到测试目的目录。                |
| process-test-classes  |              处理测试代码文件编译后生成的文件。              |
|         test          |        使用适当的单元测试框架（例如JUnit）运行测试。         |
|    prepare-package    |        在真正打包之前，为准备打包执行任何必要的操作。        |
|        package        | 获取编译后的代码，并按照可发布的格式进行打包，例如 JAR、WAR 或者 EAR 文件。 |
| pre-integration-test  | 在集成测试执行之前，执行所需的操作。例如，设置所需的环境变量。 |
|   integration-test    |      处理和部署必须的工程包到集成测试能够运行的环境中。      |
| post-integration-test |      在集成测试被执行后执行必要的操作。例如，清理环境。      |
|        verify         |      运行检查操作来验证工程包是有效的，并满足质量要求。      |
|        install        |  安装工程包到本地仓库中，该仓库可以作为本地其他工程的依赖。  |
|        deploy         |  拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程。  |

## Maven 不同环境配置

maven pom文件中包含了`<profiles>`节点，以此来为不同的环境设置不同的配置文件，构建过程等，例如开发环境，测试环境，生产环境等。

profiles 节点样例：

```xml
 <profiles>
        <!--根据环境参数或命令行参数激活某个构建处理 -->
        <profile>
            <!--构建配置的唯一标识符。即用于命令行激活，也用于在继承时合并具有相同标识符的profile。 -->
            <id>  </id>
            <!--自动触发profile的条件逻辑。Activation是profile的开启钥匙。profile的力量来自于它 能够在某些特定的环境中自动使用某些特定的值；这些环境通过activation元素指定。activation元素并不是激活profile的唯一方式。 -->
            <activation>
                <!--profile默认是否激活的标志 -->
                <activeByDefault />
                <!--当匹配的jdk被检测到，profile被激活。例如，1.4激活JDK1.4，1.4.0_2，而!1.4激活所有版本不是以1.4开头的JDK。 -->
                <jdk />
                <!--当匹配的操作系统属性被检测到，profile被激活。os元素可以定义一些操作系统相关的属性。 -->
                <os>
                    <!--激活profile的操作系统的名字 -->
                    <name>Windows XP</name>
                    <!--激活profile的操作系统所属家族(如 'windows') -->
                    <family>Windows</family>
                    <!--激活profile的操作系统体系结构 -->
                    <arch>x86</arch>
                    <!--激活profile的操作系统版本 -->
                    <version>5.1.2600</version>
                </os>
                <!--如果Maven检测到某一个属性（其值可以在POM中通过${名称}引用），其拥有对应的名称和值，Profile就会被激活。如果值 字段是空的，那么存在属性名称字段就会激活profile，否则按区分大小写方式匹配属性值字段 -->
                <property>
                    <!--激活profile的属性的名称 -->
                    <name>mavenVersion</name>
                    <!--激活profile的属性的值 -->
                    <value>2.0.3</value>
                </property>
                <!--提供一个文件名，通过检测该文件的存在或不存在来激活profile。missing检查文件是否存在，如果不存在则激活 profile。另一方面，exists则会检查文件是否存在，如果存在则激活profile。 -->
                <file>
                    <!--如果指定的文件存在，则激活profile。 -->
                    <exists>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/
                    </exists>
                    <!--如果指定的文件不存在，则激活profile。 -->
                    <missing>/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production/workspace/
                    </missing>
                </file>
            </activation>
            <build>
            	<resources><!--构建项目所需要的信息。参见Maven构建一节。-->
            		<resource>
            			<directory>  <!--上面的约定配置--></directory>
            		</resource>
            	</resources>
            <build>
```

## Maven 构建

**pom.xml中的两种build**

- Project Build, `<project>`的直接子元素`<build>`
- Profile Build, `<profile>` 的子元素`<build>`

Profile Build包含了基本的build元素，而Project Build还包含两个特殊的元素，即各种<...Directory>和<…extensions>。

### 共有节点

#### 直接子节点

```xml
<build>
    <defaultGoal></defaultGoal>
    <directory></directory>
    <finalName></finalName>
</build>
```

说明：

- defaultGoal，执行构建时默认的goal或phase，如jar:jar或者package等
- directory，构建的结果所在的路径，默认为${basedir}/target目录
- finalName，构建的最终结果的名字，该名字可能在其他plugin中被改变

#### resources节点

资源往往不是代码，无需编译，而是一些properties或XML配置文件，构建过程中会往往会将资源文件从源路径复制到指定的目标路径。

```xml
<build>
    <filters>
        <filter></filter>
    </filters>
    <resources>
        <resource>
            <targetPath></targetPath>
            <filtering></filtering>
            <directory></directory>
            <includes>
                <include></include>
            </includes>
            <excludes>
                <exclude></exclude>
            </excludes>
        </resource>
    </resources>
    <testResources>
    </testResources>
</build>
```

说明：

- resources，build过程中涉及的资源文件

- targetPath，资源文件的目标路径
- filtering，构建过程中是否对资源进行过滤，默认false
- directory，资源文件的路径，默认位于${basedir}/src/main/resources/目录下
- includes，一组文件名的匹配模式，被匹配的资源文件将被构建过程处理
- excludes，一组文件名的匹配模式，被匹配的资源文件将被构建过程忽略。同时被includes和excludes匹配的资源文件，将被忽略。

- filters，给出对资源文件进行过滤的属性文件的路径，默认位于${basedir}/src/main/filters/目录下。属性文件中定义若干键值对。在构建过程中，对于资源文件中出现的变量（键），将使用属性文件中该键对应的值替换。
- testResources，test过程中涉及的资源文件，默认位于${basedir}/src/test/resources/目录下。这里的资源文件不会被构建到目标构件中

#### plugins节点

`<plugins>`给出构建过程中所用到的插件，例如maven-assembly这个插件, 可以打包成zip、tar.gz等格式：

```xml

<build>
	<plugins>
		<plugin>
        	<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-assembly-plugin</artifactId>							<version>2.4</version>
			<configuration>
				<encoding>UTF-8</encoding><!--编码格式-->
				<descriptors>
				<descriptor>src/main/assembly/package.xml</descriptor>
				</descriptors>
				<outputDirectory>c:\\</outputDirectory>
			</configuration>
			<executions>
				<execution>
					<id>make-assembly</id>
					<phase>package</phase>
						<goals>
							<goal>single</goal>
						</goals>
				 </execution>
			</executions>
		</plugin>
	</plugins>
</build>
```

## Project Build特有的<...Directory>

```xml
<build>
    <sourceDirectory>${basedir}/src/main/java</sourceDirectory>
    <scriptSourceDirectory></scriptSourceDirectory>
    <testSourceDirectory>src/test/java</testSourceDirectory>
    <outputDirectory></outputDirectory>
    <testOutputDirectory></testOutputDirectory>
</build>
```

目录可以使用绝对路径；如果使用相对路径，则所有的相对路径都是在${basedir}目录下。

## Project Build特有的`<extensions>`

`<extensions>`是执行构建过程中可能用到的其他工具，在执行构建的过程中被加入到classpath中。

也可以通过`<extensions>`激活构建插件，从而改变构建的过程。

通常，通过`<extensions>`给出通用插件的一个具体实现，用于构建过程。

## Maven仓库

Maven 仓库有三种类型：

- 本地（local）
- 中央（central）
- 远程（remote）

### 本地仓库

运行 Maven 的时候，Maven 所需要的任何构件都是直接从本地仓库获取的。如果本地仓库没有，它会首先尝试从远程仓库下载构件至本地仓库，然后再使用本地仓库的构件。

默认情况下，不管Linux还是 Windows，每个用户在自己的用户目录下都有一个路径名为 .m2/respository/ 的仓库目录。

Maven 本地仓库默认被创建在 %USER_HOME% 目录下。要修改默认位置，在 %M2_HOME%\conf 目录中的 Maven 的 settings.xml 文件中定义另一个路径。

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository>C:/MyLocalRepository</localRepository>
</settings>
```

### 中央仓库

Maven 中央仓库是由 Maven 社区提供的仓库，其中包含了大量常用的库。

中央仓库包含了绝大多数流行的开源Java构件，以及源码、作者信息、SCM、信息、许可证信息等。一般来说，简单的Java项目依赖的构件都可以在这里下载到。

中央仓库的关键概念：

- 这个仓库由 Maven 社区管理。
- 不需要配置。
- 需要通过网络才能访问。

要浏览中央仓库的内容，maven 社区提供了一个 URL：<http://search.maven.org/#browse>。使用这个仓库，开发人员可以搜索所有可以获取的代码库。

### 远程仓库

如果 Maven 在中央仓库中也找不到依赖的文件，它会停止构建过程并输出错误信息到控制台。为避免这种情况，Maven 提供了远程仓库的概念，它是开发人员自己定制仓库，包含了所需要的代码库或者其他工程中用到的 jar 文件。

举例说明，使用下面的 pom.xml，Maven 将从远程仓库中下载该 pom.xml 中声明的所依赖的（在中央仓库中获取不到的）文件。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>com.companyname.projectgroup</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>
   <dependencies>
      <dependency>
         <groupId>com.companyname.common-lib</groupId>
         <artifactId>common-lib</artifactId>
         <version>1.0.0</version>
      </dependency>
   <dependencies>
   <repositories>
      <repository>
         <id>companyname.lib1</id>
         <url>http://download.companyname.org/maven2/lib1</url>
      </repository>
   </repositories>
</project>
```

### Maven 依赖搜索顺序

当我们执行 Maven 构建命令时，Maven 开始按照以下顺序查找依赖的库：

- **步骤 1** － 在本地仓库中搜索，如果找不到，执行步骤 2，如果找到了则执行其他操作。
- **步骤 2** － 在中央仓库中搜索，如果找不到，并且有一个或多个远程仓库已经设置，则执行步骤 4，如果找到了则下载到本地仓库中以备将来引用。
- **步骤 3** － 如果远程仓库没有被设置，Maven 将简单的停滞处理并抛出错误（无法找到依赖的文件）。
- **步骤 4** － 在一个或多个远程仓库中搜索依赖的文件，如果找到则下载到本地仓库以备将来引用，否则 Maven 将停止处理并抛出错误（无法找到依赖的文件）。

在我们的maven配置里面访问的maven私服就属于中心仓库的一个镜像。如果联网一般会使用阿里的maven仓库：

```xml
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```

