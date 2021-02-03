# Flutter Dart

```shell
# 移动端开发

FLutter  核心 Dart + C++ +Skia

https://dart.gooddev.org

# 安装Dart


# 选择开发工具
	# idea
	# WebStrorm
	# Visual Studio Code
```

```dart
// 变量
// 默认值 null

var a ;
dynamic c;  
final d = 1
    
// 常量
const //编译时就获取
```



```dart
//
	numbers //num(int double)
//

    strings  //utf-16 ' '  " "   ''' '''
        ${expression} //获取表达式	$var1 and $var2
        r xxx //输出原始字符
//

    booleans  // 只能是 true  false
        bool  isTrue = false;
				// ==
//

    lists //arrarys   []

    maps  // 键值对
    
    runes //unicode  UTF-32
    
    symbols //常用于反射
        var a = Symbol("name");
		var b = #b
		
```



```dart
// 运算符
	is // 判断-对象
    is! // 判断-对象
    as // 类型转换
    
    var c;    
    c ??= b //类似三元赋值
        
        
    !expr // 取反
    ||
    &&
        
    // 级联运算
```

```dart
// 流程控制
if else
for while do-while
break continue
switch case
assert

```



```dart
// 方法
Function
    String getName(String name){
    	return "";
	}

	// dynamic
	getName(name){
    	return "";
	}

	String getName(String name)=>{return ""};




// 必须参数 

getProduct(num id){
}


// 可选参数

// 命名参数
getProduct(num id,{bool flag, String name}){
}
getProduct(1,flag:true,name:"哇哈哈")

// 位置参数
getProduct(num id,[bool flag, String name]){
}
// 严格按照顺序执行
getProduct(1,true,"哇哈哈")
// 占位 null
getProduct(1,null,"哇哈哈")


// 方法参数默认值-定义
{ bool:flag = true,String name = "hello" }


// 匿名方法
(){
}

var getName = (){
  print("hello");
}
```

```dart
// 作用域
// 变量作用域


// 函数闭包
// 一个闭包： 是一个方法对象；
// 不管改对象在何处被调用，改对象都能访问自己作用域内的变量

count(){
  var n = 0;
  return (){
    n++;
    print(n);
  }
}

void main(){
  var func = count();
  func();
  func();
}
```

```dart
// 面向对象
OOA
OOD
OOP
  
class Animal{
  // 构造方法 一个构造类的对象的方法
  Animal(){
    
  }
  
  // 构造方法详解
  // 类的构造方法 只有一个默认的构造方法 
  // 不支持方法的重载
  // 但是 可通过可选参数实现 重载 功能
  
  
  // 作用
  // 生成类的对象 生成类的对象前对类中的实例属性进行初始化
  Animal(String name, int age){
    this.name = name;
    this.age = age;
  } // new Animal("",1)
  
  // 可以简写
  Animal(this.name,this.age,this.gender);
  
  // 命名构造方法
  Animal.withName(){
  }
  
  // 常量构造方法
  // 如果需要生成的对象不可以改变 类中的构造方法改为常量构造方法
  // 类中的所有实例属性 必须是final
  /**
  final String name;
  final String _names;
  */
  const   Animal(this.name,this.age,this.gender);

  
  // 类的私有成员设置
  /**
   权限控制
   String name;
   String pass;
   int salary;
   
   
   // 私有 针对当前.dart文件
   int _salarys;
  */
  
  /**
  setter getter
  int _age;
  
  int get age{
  	return this._age;
  }
  
  set age(int age){
  	return this._age= age;
  }
  
  
  */
    
  // 成员变量 
   String name;
   String _names;
   int age;
   final int gender;
  // 成员方法
   eat(){
     print("$name is eating")
   }	
  
  // 类变量 类方法
  // 属于类本身 不属于具体某个实例
  static List tags；
    
   // 描述类本身
  static sleep(){
    print("xxxxxxxx is sleep");
  }
     
    
}


void main(){
  var cat = new Animal();
  print(cat.name);
  print(cat.eat());
  print(Animal.tags= ["xxx"]);
  
  
  
  
  
  const a1 = Animal();
}





// 	类
```



## package

```dart
 pub.dev
 dart.dev
 pubspec.yaml
```

```yaml
name: myflutter
description: A new Flutter project.
version: 1.0.0+1

dependencies:
  flutter:
    sdk: flutter
  dio: ^3.0.0

# publish_to: 'none'
publish_to: 'pub.dev' 

dev_dependencies:
  flutter_test:
    sdk: flutter

environment:
  sdk: ">=2.7.0 <3.0.0"
  
  cupertino_icons: ^0.1.3
flutter:
  uses-material-design: true
```

# Flutter

```dart
// fuchsia 全新操作系统
组件化开发
  statelessWidget  // 无状态组件 复用组建
  	
  statefullWidget  // 有状态组件 数据处理
  
```



## Container

```dart
// Container 布局组件
Container({
  
})
```

## 依赖管理

pubspec.yaml

```dar
dio
```



# FQA

## Flutter

```dart
1. 检查环境
  flutter doctor
  	/**[✓] Android toolchain - develop for Android devices (Android SDK version 30.0.2)
    [✓] Xcode - develop for iOS and macOS (Xcode 12.3)
    [✓] Android Studio (version 4.0)
    [✓] Connected device (1 available)*/
2. 打开模拟器 
   open -a Simulator
/**
Waiting for another flutter command to release the startup lock...
  
关闭Android Studio
打开flutter安装目录/bin/cache
删除lockfile文件
此时可在命令行再执行flutter相关命令，完美解决
```



# 环境搭建

## Dart

```shell
dart --version
```

## Flutter