---
title: JDK动态代理的深入理解
thumbnail: /gallery/thumbnails/b6.jpg
toc: true
categories:
  - 设计模式
tags:
  - 代理模式
  - 设计模式
abbrlink: 43606
date: 2019-07-16 15:37:47
---

​	代理模式是应用框架中经常使用的一种模式，动态代理是AOP（面向切面编程）思想的一种重要的实现方式，在我们常用的框架中也经常遇见代理模式的身影<!--more-->，例如在Spring中事务管理就运用了动态代理，它将Service层原先应该进行的事务管理交给了Spring框架，大大简化了开发流程。在Hibernate中对象的懒加载模式，也运用了JDK的动态代理以及cglib代理。

## **静态代理**

​	在说动态代理之前，我们需要先了解一下静态代理。

​	静态代理通常用于对原有业务逻辑的扩充。比如持有第三方jar包中的某个类，并调用了其中的某些方法。然后出于某种原因，比如记录日志、打印方法执行时间，但是又不好将这些逻辑写入第三方jar包的方法里。所以可以创建一个代理类实现和这个类方法相同的方法，通过让代理类持有真实对象，然后在原代码中调用代理类方法，来达到添加我们需要业务逻辑的目的。

​	这其实也就是代理模式的一种实现，通过对真实对象的封装，来实现扩展性。

## **满足代理模式应用场景**

1. 两个角色：被代理者、执行者

2. 注重过程：被代理对象不想做或者不方便做，但是必须要做时使用代理模式

3. 代理对象：执行者必须持有被代理对象的引用

这里我们提出一个需求：我们在编写的Service类的每个方法中没有进行事务处理，但根据需要我们必须进行实物处理，那此时我们进行静态代理。

**ServiceInterface接口:**

```java
public interface ServiceInterface {
	void a();
	void b();
}
```

**接口的实现类：**

```java
public class ServiceImpl implements ServiceInterface{

	@Override
	public void a() {
		System.out.println("执行a方法");
	}

	@Override
	public void b() {
		System.out.println("执行b方法");
	}
}
```

**代理类:**

```java
public class ServiceImplProxy implements ServiceInterface{
	private ServiceInterface service;
        public ServiceImplProxy(ServiceInterface service) {
		super();
		this.service = service;
	}

	@Override
	public void a() {
		System.out.println("开启事务");
		service.a();
		System.out.println("提交事务");
	}

	@Override
	public void b() {
		System.out.println("开启事务");
		service.b();
		System.out.println("提交事务");
	}
}
```

**测试：**

```java
public class TestProxy {
	public static void main(String[] args) {
		//创建目标对象
		ServiceInterface service=new ServiceImpl();
		//创建代理对象，将目标对象传入代理对象
		ServiceInterface serviceProxy =new ServiceImplProxy(service);
		
		serviceProxy.a();
		serviceProxy.b();
	}
}
```

{% asset_img 1.jpg %}

## **静态代理的优点及缺点**

​	上面演示了静态代理的完整过程，现在我们来看看静态代理的优缺点。

​	**优点：**扩展原功能，不侵入原代码。

​	**缺点：**

1. 如果我们只需要调用代理对象的某一个方法，换句话说我们只需要代理对象代理委托类的某一个方法时，我们仍然需要实现接口中所有的方法，这样显得很浪费。当然在这里我们可以通过继承来实现静态代理，使用继承时，我们只需要覆写需要代理的方法即可。

2. 如果在接口中定义了很多方法，而这些方法都需要被代理，并且代理的逻辑都是相同的，比如说在WEB开发中Service层所有方法执行之前都需要打开事务，结束后关闭事务。那么此时我们使用静态代理，会导致大量代码重复（想想如果有50个方法，你去一个一个手敲吧）。

## **动态代理**

### **动态代理的原理：**

​	动态代理由程序在内存中动态生成一个对象，不需要我们手写代理对象，我们只需要指定代理方法的模板即可。

{% asset_img 2.png %}

### **JDK自带动态代理的核心类：**

#### java.lang.reflect.**Proxy类**：

​	 该类中的newInstance的静态方法，用于创建动态代理对象，该类也是所有生成的动态代理对象的父类。

​	**核心方法：**

​	 Proxy.**newProxyInstance**(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)

​	 **参数：**

- ClassLoader：传入被代理类的类加载器，实际上只需要传入类加载器即可，不一定必须传入“被代理类”的类加载器，也可以传入别的实例的类加载器，因为java中类加载器是一个对象。这个类加载器用于将生成的字节码文件加载进方法区，并生成字节码对象。

- Class<?>[] interfaces：这个参数是指定代理对象实现的接口，可以实现多个接口，这些接口中的所有方法都会按照invoke模板中的代码进行加强。

- InvocationHandler h：传入InvocationHandler接口的实现类，该类中的invoke方法是代理对象中所有方法的逻辑处理模板。

#### java.lang.reflect.**InvocationHandler**接口：

​	该接口有一个invoke方法，我们需要实现invoke方法，这个方法就是代理方法的模板。

​	**接口中需要实现的方法：**

​	 **invoke**(Object proxy, Method method, Object[] args)；

​	**参数：**

- Object proxy：代表代理对象本身，可以它调用代理对象的其他方法

- Method method：代表“被代理对象”对应方法的字节码对象

- Object[] args：传入“代理对象”对应方法的参数数组

### **动态代理的示例代码：**

```java
public class ServiceInvocationHandler implements InvocationHandler {
    private Object instance;//这是目标对象（被代理对象）

    public Object getInstance() {
        return instance;
    }

    public void setInstance(Object instance) {
        this.instance = instance;
    }

    
    /***
     * proxy:代表代理对象本身,可以通过它调用代理对象的其他方法
     * method:代表目标对象对应方法的字节码对象
     * args：代表目标对象相应的参数
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("开启事务");
        method.invoke(instance,args);
        System.out.println("提交事务");
        return null;
    }
}
```

​      通过Proxy类创建动态代理:

```java
public class TestMyProxy {
    public static void main(String[] args) throws Exception{
    	//创建目标对象
        ServiceInterface sale = new ServiceImpl();
        //创建模板对象
        ServiceInvocationHandler invocationHandler = new ServiceInvocationHandler();
        //将目标对象注入模板对象中
        invocationHandler.setInstance(sale);
        
        Class serviceClazz = sale.getClass();
        ServiceInterface proxy = (ServiceInterface) Proxy.newProxyInstance(serviceClazz.getClassLoader(), serviceClazz.getInterfaces(), invocationHandler);
        
        proxy.a();
        proxy.b();
    }
}
```

通过测试，动态代理对象与前面的静态代理结果相同:

{% asset_img 2.png %}



## **JDK动态代理的原理**

​	看到这里肯定很多人都会另一头雾水，动态代理底层到底是怎么实现的，我们想办法通过程序将代理对象字节码文件输出到磁盘上，然后通过jdgui反编译工具，查看动态代理对象的源码结构。

```java
public class TestMyProxy {
    public static void main(String[] args) throws Exception{
        byte[] buffer = ProxyGenerator.generateProxyClass("$Proxy0", new Class[]{ServiceInterface.class});
        try {
            FileOutputStream output = new FileOutputStream("C:/Users/Lenovo/Desktop/$Proxy0.class");
            output.write(buffer);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

注：使用ProxyGenerator类需要将JRE中lib目录下的一个jar包导入到项目中

{% asset_img 3.png %}

通过反编译工具（推荐jd-gui）可以看到代理对象源码结构如下（下列代码对源码进行了部分删减，如果有需要可以自行生成源代码查看）：

```java
public final class $Proxy0
  extends Proxy
  implements ServiceInterface
{
  private static Method m1;
  private static Method m4;
  private static Method m3;
  private static Method m2;
  private static Method m0;
  
  public $Proxy0(InvocationHandler paramInvocationHandler)
  {
    super(paramInvocationHandler);
  }
  
 
  
  public final void b()
  {
    try
    {
      this.h.invoke(this, m4, null);
      return;
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }
  
  public final void a()
  {
    try
    {
      this.h.invoke(this, m3, null);
      return;
    }
    catch (Error|RuntimeException localError)
    {
      throw localError;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }


  static
  {
    try
    {
      m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] { Class.forName("java.lang.Object") });
      m4 = Class.forName("com.proxy.ServiceInterface").getMethod("b", new Class[0]);
      m3 = Class.forName("com.proxy.ServiceInterface").getMethod("a", new Class[0]);
      m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
      m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);
      return;
    }
    catch (NoSuchMethodException localNoSuchMethodException)
    {
      throw new NoSuchMethodError(localNoSuchMethodException.getMessage());
    }
    catch (ClassNotFoundException localClassNotFoundException)
    {
      throw new NoClassDefFoundError(localClassNotFoundException.getMessage());
    }
  }
}
```

我们分析动态生成的代理类源码可知，代理对象中的代理方法，都执行了this.h.invoke()方法，this.h实际上是代理类的父类中（Proxy类）中的属性，它保存了在调用Proxy.newInstance()方法时传入的InvocationHandler实例，而InvocationHandler中的invoke方法就是，代理类的代理逻辑。

{% asset_img 4.jpg %}

 也就是说在`生成的一个动态代理对象中，对目标对象中所有方法都进行了相同的前处理和后处理过程，因为都执行相同的invoke“模板”方法`，也就是说如果我们想对目标对象中每个方法进行不同的前处理和后处理，我们需要编写不同的InvocationHandler实现类，然后创建不同的动态代理对象才能实现这一需求。



## **总结**

动态代理实际上就是在内存中直接生成字节码文件（通常程序员写的java文件编译后生成的字节码文件都在硬盘中），然后将字节码加载进方法区生成字节码对象，生成字节码对象后通过反射就可以创建代理对象的实例了。生成字节码文件就必定需要生成这个代理类的方法，那程序是怎么知道这个代理对象需要实现哪些方法呢，这就是为什么在Proxy.newProxyInstance()需要传入接口的数组，传入几个接口，这个代理就会实现这些接口，程序自然就知道它需要实现哪些方法了。此时就会开始循环生成代理类中的方法，那这个方法的具体实现代码又是什么呢？由于生成的代理类继承了Proxy类，在Proxy类中有一个h字段，保存的是一个InvocationHandler实现类的对象，而我们通过Proxy.newInstance()方法时，传入了一个InvocationHandler实例，所以在代理对象中存放着一个InvocationHandler对象，代理对象的每个方法都会调用父类中存放的InvocationHandler实例中的invoke方法。



## **说明**

本文由听到微笑CSDN博客移植而来，原文地址： [原文](https://blog.csdn.net/tianjindong0804/article/details/84072096)

