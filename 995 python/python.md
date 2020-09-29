# Python

https://docs.python.org/zh-cn/3/

```python
# 异常
	# https://docs.python.org/zh-cn/3/library/exceptions.html#bltin-exceptions
    
	# 异常处理
    
    # 抛出异常
    	raise
    
    while True:
        try:
            x=int(input("test the number: "))
            break
        except ValueError:
            print("this is Exception")
# namespace
# 作用域

# 类
	class MyClass
    	# 初始化赋值
    	def __init__(self,x,y):
            self.s=x
            self.q=y
```

## 函数

```python
def 函数名 (参数列表):
    return [表达式]  # None

# 参数
  # 类型属于对象 变量是没有类型的
	# 可变类型
    	# 字典 列表
    # 不可变类型 
    	# 字符串 元组 数字 
 # 形参
 # 实参
    
    
 # 必须参数 一致性
 # 关键字参数  
    # python会自动识别
   def defName(var1='',var2='')
 # 默认值参数
    # 默认参数 参数列表最后
    def defName(var1='',var2='value2')
 # 不定长参数
	# 类似java重的可变参数
    # *var2  收集到元组重
    # **var2 接收字典类型的值
    def defName(var1='',*var2)
 # 组合参数
	# 以上参数组合
 
 # 匿名函数
    Lamada var1,var2:var1+var2
 # 变量
	global var # 函数中对该变量做的其他操作也是全局性的（提升为全局变量）
```



## 异常

```shell
# 异常处理
try/except

# 1.捕捉异常

# 2.捕捉多个异常
	try:
		xxx
	except ZeroDrivisionError:
		print("yyy")
	except Exception:
		print("xxx")
# 3.块捕捉异常
	try:
		xxx
	except （ZeroDrivisionError,TypeError）:
		print("yyy")
		
# 4.捕捉对象
	try:
		xxx
	except （ZeroDrivisionError,TypeError）as e:
		print(e)
# 5.全捕捉
	try:
		xxx
	except:
		print("")

# 6.else语句
	try:
    	print("")
	except:
    	print("")
	else:
		print("")
		
# 7.try-finally
	try:
		print()
	except:
		print()
	else:
		print("")
	finally:
		print("")
```

## 文件IO

```python
# open(filename[mode[buffering]])

# 文件打开
# 以特定编码打开
# fileopen('filePath','r',encoding='utf-8')

# 文件关闭
	# 首先刷新缓存区到文件中
file.close();


# with语句
	with expression as target:
        with-body
        
	with open() as file:

# 写文件
file.wirte()


# 读取文件
# 读取指定该字符串
	# file.read([size])

    file.seek(index) # 从执行开始处读取
	file.readline(size) # 读取个数
# 读取一行
# file.readline()
	with open() as file:
        number = 0
        where True:
            number+=1
            line=file.readline()
            if line==''
            	break
            print(number,line,end="\n")
    
    
    

# 读取全部行
file.readlines()
	with open() as file:
        message=file.readlines()
        print(message)
        
# 目录
# os
import os
os.name # nt
os.linesep # \r\n
os.sep # \\

listdir
nkdir

# os.path
import os.path

 # 路径
    os.getcwd()
    # 相对路径
    	
    # 绝对路径
    	os.path.abspath(path)
    # 拼接路径 如果拼接的路径中存在多个绝对路径以最后出现的一次为准，（会出现覆盖现象）
    	os.path.join(path1[path2[...]])
        
        
 # 目录操作
	os.path.exists("") # True
    os.mkdir("")
    os.mkdirs("")
    os.rmdir("")
    os.walk(top[topdown][onerror][rollowlinks])
```

## 类/对象

```python
# 封装
# 继承
# 多态


# 类
	# 定义
    class ClassName:
        statement
    
    class Hero(Object): # 继承关系
        name=""			# 属性
        def info(self): # 实例方法 一般使用self
            print("")
         
     # 构造方法   
    __init()__(self,attribute,attribute1): # 魔法方法
        self.attribute=attribute
        self.attribute1=attribute1
    # 访问限制
      # _fo
      # __foo
      # __foo__
  

   # 属性 ???
    # 装饰器 @property
    
   # 继承 ???
	class Hero(Object,...,): # 继承关系
    # 方法重写
    # setters getters
```

模块



## 

# 数据驱动测试

依赖数据的维护情况

数据库中获取数据

## CSV

逗号分隔符

内置了csv模块

## Excel



# 





# 测试理论篇

基础认识

# 软件测试过程



# 需求分析过程



# 软件测试过程



# 用例设计



# Selenium

## Unittest



## WebDriver



## Page Object



# Jmeter

```shell
# 配置元件
	# 创建http请求默认值，用来管理公共参数配置
# 断言
	# 添加断言
# 两种参数设置（get/post）
	# get  参数
	# post 消息体
		# http信息头管理器
		
# 参数化设置
	# 前置处理器-增加线程
		# 用户自定义变量
		# 变量引用${variable}
		
	# 函数生成变量
		# 函数生成助手对话框
		
	# BeanShell变量
	 # http://www.beanshell.org/manual/bshmanual.html
	
	# csv数据文件变量
		# 创建csv文件


# 正则匹配
	# 上一个接口的返回值 作为下一个测试的入参
	
```



## 添加线程组

setup

testdown

## 添加http请求

## 确认url 请求方式 参数

## 查看结果树

## 调用接口 查看返回值

# LoadRunner12

# 

# 接口测试

# Request库

# 移动端测试理论

## 测试工具

是否支持toast（用户点击出现的弹窗会自动）

是否支持native(函数)  webview 网络视图

是否支持款应用 

### Robotium

支持 java

仅支持 Android

不支持 跨应用

### Macaca

支持 Java Python Node.js

支持 Android IOS

支持 跨应用

### Appium

支持  Java Python C# php perl reby Node.js

支持 Android IOS

支持 跨应用

## Appium

http://appium.io/

Appium类库封装了标准selenium客户端类库，增加了移动设备控制相关的命令

Applium-client -> Applium-Server -> 移动设备

### 环境搭建

Python

Selenium

.netframework45

JDK8

Android SDK

### Appium元素定位

## 特有API





# 测试实战

## 视频网站测试

## 内容管理系统测试



# 自动化测试综合

# 性能测试







# 禅道
