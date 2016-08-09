# Annotation

标签（空格分隔）： Java 笔记

---
### Annotation

#### 作用 ####
替代配置文件
注解是由框架来读取使用的

#### Annotation的使用
* 定义注解类 （框架的工作）
* 使用注解
* 读取注解 -- 反射   （框架的工作）

#### **注解的定义**
语法： public @interface 

#### **注解的使用**

* 定义注解类：框架的工作
* 使用注解：我们的工作
* 读取注解(反射)：框架的工作

#### **定义注解**
@interface A{}   //所有的Annotation都implements Annotation

#### **使用注解** 

##### **注解的作用目标**
* 类(接口或枚举)
* 属性
* 方法
* 构造器
* 参数
* 局部变量
* 包 

#### **注解的属性**
* 定义属性
  格式：
```
@interface MyAnno1{
    int age();
    String name();
}
```
* 使用注解时给属性赋值
```
@MyAnno1(age=100, name="Tom")
```
* 注解属性的默认值：在定义注解时，可以给注解指定默认值
```
@interface MyAnno2{
    int age() default 100;
    String name();
} 
```
在使用注解时，可以不给带有默认值的属性赋值

* 名为value的属性的特权
    当使用注解时，如果只给名为value的属性赋值时，可以省略"value="；e.g:
```
@MyAnno1(value="hello")
```
可以写成
```
@MyAnno1("hello")
```
    
* 注解属性的类型
 * 8中基本类型
 * String
 * Enum
 * Class
 * 注解类型
 * 以上类型一维数组类型
 
 ```
 @interface MyAnno2 {
        int a();
        Strnig b();
        MyEnum c();
        Class d();
        MyAnno2 e();
        String[] f();
}
 ```
 ```
 @MyAnno1(
        a=100,
        b="hello",
        c=MyEnum.A,
        d=String.class,
        e=@MyAnno2(aa=200, bb="world"),
        f={"", ""}
)
 ```
 *当给数组类型的属性赋值时，若数组元素的个数为1时，可以省略大括号*

#### **注解的作用目标限定及保存策略限定**
1. 使一个注解的作用目标只能在类上，不能在方法上，这就叫作用目标的限定
在定义注解时，给注解添加注解，这个注解是@Target

    ```
    @Target(value={ElementType.TYPE, ElementType.METHOD})
    @interface MyAnno {
    }
    ```
2. 保留策略
  - 源代码文件（SOURCE）：Annotation只在源代码中存在，编译时就被忽略了
 - 字节码文件（CLASS）：Annotation在源代码中存在，编译时会把注解信息放到class文件中，但JVM在加载类时会忽略注解
 - JVM中（RUNTIME）：Annotation在源代码、字节码文件中存在，并且在JVM加载类时，会把注解加载到JVM内存中
3. 限定注解但保留策略

    ```
    @Retention(RetentionPolicy.RUNTIME) 
    @interface MyAnno { 
    } 
    ```
    
#### **反射泛型信息**

反射泛型信息

Class --> Type getGenericSupperclass()
Type --> ParameterizedType，把Type强转成ParameterizedType类型！！！
ParameterizedType --> 参数化类型 = A<String>
ParameterizedType：Type[] getActualTypeArguments()，A<String>中的String
Type[]就是Class[]，我们就得到了类型参数了！

```
abstract class A<T> {
	public A() {
		/*
		 * 在这里获取子类传递的泛型信息，要得到一个Class！
		 */
//		Class clazz = this.getClass();//得到子类的类型
//		Type type = clazz.getGenericSuperclass();//获取传递给父类参数化类型
//		ParameterizedType pType = (ParameterizedType) type;//它就是A<String>
//		Type[] types = pType.getActualTypeArguments();//它就是一个Class数组
//		Class c = (Class)types[0];//它就是String
//		
		Class c = (Class)((ParameterizedType)this.getClass()
				.getGenericSuperclass()).getActualTypeArguments()[0];
		
		System.out.println(c.getName());
	}
}

class B extends A<String> {
	
}

class C extends A<Integer> {
	
}
``` 
    
#### **读取注解(反射)**
 1. 要求
 注解但保留策略必须是RUNTIME
 2. 反射注解需要从作用目标上返回
 - 类上的注解，需要使用Class来获取
 - 方法上的注解，需要Method来获取
 - 构造器上的注解，需要Construcator来获取
 - 成员上的，需要使用Field来获取


  它们都有一个方法：
  * Annotation getAnnotation(Class)，返回目标上指定类型的注解！
  * Annotation[] getAnnotations()，返回目标上所有注解！

```
public class Demo1 {

	public void fun1() {
		/*
		 * 1. 得到作用目标
		 */
		Class<A> c = A.class;
		/*
		 * 2. 获取指定类型的注解
		 */
		MyAnno1 myAnno1 = c.getAnnotation(MyAnno1.class);
		System.out.println(myAnno1.name() + ", " + myAnno1.age() +
				", " + myAnno1.sex());
	}
	
	public void fun2() throws NoSuchMethodException, SecurityException {
		/*
		 * 1. 得到作用目标
		 */
		Class<A> c = A.class;
		Method method = c.getMethod("fun1");
		/*
		 * 2. 获取指定类型的注解
		 */
		MyAnno1 myAnno1 = method.getAnnotation(MyAnno1.class);
		System.out.println(myAnno1.name() + ", " + myAnno1.age() +
				", " + myAnno1.sex());
	}
}

@MyAnno1(name="A类", age=1, sex="男")
class A {
	@MyAnno1(name = "fun1方法", age=2, sex="女")
	public void fun1() {
		
	}
}

@interface MyAnno1 {
	String name();
	int age();
	String sex();
}
```
