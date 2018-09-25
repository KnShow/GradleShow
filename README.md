GradleCode
   Gradle的学习之路


   
 #Gradle入门
 
 ##Gradle 版HelloWorld
    task hello{
		doLast{
			println'Hello World!'
		}
	}
	
   切换到指定文件夹下打开终端使用 gralde -q hello 命令来执行构建脚本:   Hello World!
	
    这个构建脚本定义一个任务task，任务名字hello
	并且给任务hello添加一个Action，Gradle源码中的一段Grovy语言实现的闭包。
	doLast就意味着Task执行完毕后要回调doLast的这部分闭包的代码实现
  
	gralde -q hello>?
       执行build.gradle脚本中定义的名为hello的task -q参数用于控制输出的日志级别
	   以及哪些日志可以输出被看到
	println'Hello World!'？
		System.out.println("hello world")的简写，Gradle可以识别
		因为Groovy 把println()这个方法添加到java.lang.Object 而Groovy中可以省略签名中括号
		注意：Groovy中的单引号和双引号所包含的内容都是字符串。
     	
		
 ##Gradle Wrapper
    Wrapper 就是对Gradle的一层包装，便于开发统一Gradle构建的版本
	当我们使用Wrapper启动Gradle的时候，Wrapper会检查Gradle有没有被下载关联,没有将会从配置的地址
	进行下载并运行构建
    
	###生成Wrapper
	   项目根目录终端输入: gradle wrapper 
       生成文件:
			gradle ->wrapper -> gradle-wrapper.jar、gradle-wrapper.properties
			gradlew
			gradlew.bat
        gradlew和gradlew.bat分别是Linux和windows下的可执行脚本,他们的用法和原生的gradle的用法是一样的
		gradle-wrapper.jar 是具体业务逻辑实现的jar包，gradlew最终还是使用java执行的这个jar包来执行相关Gradle操作
    
	###wrapper配置
	    参照AndroidStudio中的默认配置		
		    distributionBase=GRADLE_USER_HOME   下载的gradle压缩包解压后的存储的主目录
			distributionPath=wrapper/dists		相对于distributionBase的解压后的Gradle压缩包的路径
			zipStoreBase=GRADLE_USER_HOME       同 distributionBase，只不过是存放zip压缩包的
			zipStorePath=wrapper/dists             distributionPath 
			distributionUrl=https://services.gradle.org/distributions/gradle-3.5-all.zip gradle发行的压缩包的下载地址
			org.gradle.jvmargs=-Xmx512m
		 distributionUrl	https://services.gradle.org/distributions/gradle-3.5-bin.zip我们通常会将bin改成all，这样在开发过程中
		 就可以查看gradle源码了
		 
	 
	###自定义Wrapper Task
	   前面提过gradle-wrapper.properties是由Wrapper Task生成的。那么我们就可以自定义配置Wrapper Task来达到我们配置gradle-wrapper.properties
	   在build.gradle中录入以下脚本:
	    // 配置gradle版本
	    task wrapper(type: Wrapper){
			gradleVersion = '2.4'
		}
		同样的也可以配置其他参数
		
  ##Gradle日志
	###日志级别
     	error  quiet warning lifecycle info debug
		
	###	输出错误堆栈信息
	    错误堆栈开关选项
       :-s|--stacktrace          输出关键性的堆栈信息
       :-S|--full-stacktrace	    输出全部堆栈信息
	      
	### 自己使用日志信息调试
	   println '日志信息'|logger.quiet('日志信息');
	  

  ##Gradle 命令行
	 ###使用帮助命令
	  :gradlew -?
	  :gradlew -h
	  :gradlew -help
	  
	 ###查看所有可执行的Tasks
	    为什么:有时候我们不知道如何构建一个功能,不知道该执行哪个task，使用gradlew tasks 可以查看哪些task可执行都具备哪些功能
	  :gradlew tasks
		
     ### Gradle Help任务          
       帮助我们了解每一个task的使用帮助
      :	gradlew help -task   
	   从这些帮助信息中我们可以查看这个task作用、类型、属于哪个分组、有哪些可使用的参数
	 
     ###强制刷新依赖 	 
		强制更行第三方库的缓存
	   :gradlew --refresh -dependencies assemble
	   
	 ###多任务调用
       	 执行jar之前先进行clean   多个任务之间用空格分开
		 : gradlew clean  jar,
		 
     ###通过任务名字缩写执行 
	    gradle 提供了基于驼峰命名法的缩写调用 比如 connectCheck
		:gradlew connectCheck
		    缩写
		:gradlew cc
	   
 
 #Groovy基础
	介绍：
		基于JVM虚拟机的一种动态脚本语言,语法同java类似
		Groovy完全兼容Java又在此基础上增加了很多动态类型和灵活的特性 例:支持闭包、支持DSL
		每个Gradle的build脚本文件都是一个Groovy脚本文件
		
	##字符串
		在Groovy中分好不是必须的
		单引号和双引号都可以定义一个字符串常量，不同的是双引号可以对字符串里的表达式做运算，而单引号不行
		case 1: 打印单引号、双引号修饰的字符的类型
			task printStringClass <<{
				def str1 = '单引号'
				def str2 = "双引号"
				println "单引号的字符串类型: "+str1.getClass().name
				println "双引号的字符串类型: "+str2.getClass().name
			}	
		   result:
				  单引号的字符串类型java.lang.String
				  双引号的字符串类型java.lang.String
	
		case 2: 对单引号、双引号修饰的字符进行运算
			task printStringvar <<{
				def name = "张三"
				println '单引号的变量计算: ${name}'
				println "双引号的变量计算: ${name}"
			}
			result:  
					${name}
				    张三	
					
	##集合
	  ###List
		task printList <<{
			//定义一个集合
			def numList = [1,2,3,4,5,6];
			println numList.getClass().name
			println numList[1]//访问第二个元素 2
			println numList[-1]//访问最后一个元素 6
			println numList[-2]//访问倒数第二个元素 5
			println numList[1..3]//访问第二个到第四个元素  [2,3,4]
			
			numList.each{
				println it
			}
		}
		1..3 表示一个范围索引
		each 集合遍历的方法，接受一个闭包作为参数,可以访问List里的每个元素
		it 变量就是正在迭代的元素，这里有闭包的知识，后面讲解
	  
	  ##Map 基本同List
		task printMap <<{ 
			def map = ['Java':18,'Android':10]
			println map.getClass().name
			
			println map['Java']
			println map.Android
			
			map.each{
				println "key: ${it.key} value: ${it.value}"
			}
		}
		
	
	##方法	
	   这里主要讲Groovy方法和Java方法的不同
	   1、括号可以省略
		 Java中方法调用 invokeMethod(param1,param2);
		 Groovy         invokeMethod param1 param2
		 task invokeMethod <<{
			method(1,2)
			method 1,2
		 }

		def method(int a,int b){
			println a+b
		}
		上面两种方法的调用结果是一样的,但Groovy更简洁
		
	   2、return是可以不写的,当没有return的时候,Groovy会把执行过程中的最后一句代码作为其返回值
		task printMethodReturn{
			int add1 = method2 1,2
			int add2 = method2 2,1
			println "add1: ${add1} add2: ${add2}"
		}
		def method2(int a,int b){
			if(a>b)
				a
			else
				b
		}
		 
	   3、代码块是可以作为参数传递的
	      代码块:一段被花括号包围的代码,其实就是闭包,Groovy是允许其作为参数传递的,这里有点类似于Java8的Lambda表达式,我们结合上面的Groovy特性来用,最后就会发现特别简洁易读,以集合的each方法为例
		  
			numList.each({
				println it
			})
	
	
			numList.each(){
				println it
			}
			//最终简化版
			numList.each{
				println it
			}
		  
	##JavaBean			    
        JavaBean是一个非常好的概念,你现在看到的组件化、插件化、配置集成等
		都是基于JavaBean.在Java中为了访问JavaBean的属性,我们不得不重复生成getter/setter方法。
		这样太繁琐,这在Groovy中得到很大的改善	
			task helloJavaBean{
				Person p = new Person()
				println p.name
				p.name = "张三"
				println p.name
			}
			class Person{
				private String name
			}	 
		通过上面的例子我们发现我们并没有定义age这一成员变量,但是一样可以通过p.age获取到该值，因为我们定义了getAge()方法  而如果我们要修改值话就要定义setAge方法
		 
	##闭包
	   闭包是Groovy的一个非常重要的特性。可以说它是DSL(百度)的基础,Java中是匿名内部类,但是闭包相对来说
	   更加灵活、轻便、可复用
	   
	   ###初识闭包
		 前面我们讲过闭包就是花括号括起来的一段代码块，了解闭包的it变量的由来。
		 集合的each方法我们已经非常熟悉了。下面我们以其为例实现一个类似闭包功能
			task helloClosure{
				cuttomEach{
					println it
				}
			}

			def cuttomEach(closure){
				//模拟一个有10个元素的迭代
				for(int i  in 1..10){
					closure(i)
				}
			}
		 customEach(closure)方法接收一个闭包(代码块)， 闭包的执行很简单，跟上一对花括号就可以了,closure(i)就是该闭包就收的参数，如果只有一个参数就是我们的it变量了
	   ###向闭包传递参数
	     当闭包只有一个参数时,默认就是it;当以后多个参数时就需要一一列出
			task helloClosure{
				//多个参数
				eachMap{k,v->
					println "$k  $v"
				}
			}

			def eachMap(closure){
				def map1 = ["name":"张三","age":18]
				closure(map1.name,map1.age)
			}
			k,v ->用于把参数和主体区分开来
		
	   ###闭包委托
	     Groovy的强大之处在于它支持闭包方法的委托
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
	   
 
 
 
 