---
layout: post
title: "单例模式的Java实现"
date: 2014-05-05 21:37
comments: true
categories: 
---
说起单例模式，大家应该都不会陌生。下面介绍一种最常见的Java实现方式。
##Eager Initialization
这个实现方式是我们平时最容易写出来的实现方式。代码如下：
    public class EagerSingleton{
	  private static volatile EagerSingleton instance = new EagerSingleton();
	  private EagerSingleton(){}
	  public static EagerSingleton getInstance(){
		  return instance;
	  }
    }
该方法有一个明显的不足之处在于不管我们是否真的需要使用该实例，该类都会有一个实例存在。
##Lazy Initialization
为了克服上面的缺陷，我们使用另外一种实现方式。
    public class LazySingleton{
      private static volatile LazySingleton instance = null;
      private LazySingleton(){}
      public LazySingleton getInstance(){
      	  if(instance==null){
      	  	synchronized(LazySingleton.class){
      	  		instance = new LazySingleton();
      	  	}
      	  }
      	  return intance;
      }
    }
该方法虽然解决上述问题，但是却引入另外一个问题，那就是该方式并不是线程安全，当有多个线程的时候，会出现多个实例的情况。为了解决这个，我们可以使用双重检验的方式来实现。代码如下：
    public class LazySingleton{
      private static volatile LazySingleton instance = null;
      private LazySingleton(){}
      public LazySingleton getInstance(){
      	  if(instance==null){
      	  	synchronized(LazySingleton.class){
      	  		if(instance==null)
      	  			instance = new LazySingleton();
      	  	}
      	  }
      	  return intance;
      }
    }
在该实现中，instance必须要定义成volatile，否则该方法还是会出现多个实例的情况。
<!--More-->
##Static Block Initialization
如果我们清楚了Java的初始化顺序，则可以使用静态代码块来实现单例模式。
    public class StaticBlockSingleton{
    	private static final StaticBlockSingleton instance = null;
    	private StaticBlockSingleton(){}
    	static{
    		instance = new StaticBlockSingleton();
    	}
    	public static StaticBlockSingleton getIntance(){
    		return instance;
    	}
    }
该实现方式也有一个缺点，就是加入该类中有多个静态变量，而我们只想初始化其中部分变量的时候，我们不得不初始化不需要的变量。
##Bill Pugh Solution
为了解决上述问题，可以使用静态内部类来实现。因为静态内部类只要在真正使用的时候才会被初始化。代码如下：
	public class BillPughSingleton{
		private BillPughSingleton(){}
		private static class LazyHolder{
			public static final BillPughSingleton instance = new BillPughSingleton();
		}
		public static BillPughSingleton getInstance(){
			return LazyHolder.instance;
		}
	}
##Using Enum
	public enum EnumSingleton{
		INSTANCE;
		public void someMethod(String param){}
	}
##Adding readResolver()
当我们的类需要被序列化的时候，这种情况很容易造成反序列化的类与instance不一致，为了解决这个问题，需要加入readResolver()方法，该方法在readObject()之后被调用，作用是得到单例模式的值。代码如下：
    protected Object readResolver(){
    	return instance;
    }
##Adding Serial Version Id
而当序列化之后我们改变了原始的类，这是进行反序列化的时候，是会出现错误的，这个时候就需要制定Serial Version Id.代码如下：
	private static final long serialVersionUID = 1L;
##Conclusion
所以，结合上述所说，我们可以使用如下代码来更好地实现单例模式。
	public class Singleton{
		private static final long serialVersionUID = 1L;
		private Singleton(){}
		private static class SingletonHolder{
			public static final Singleton instance = new Singleton();
		}
		public static Singleton getInstance(){
			return SingletonHolder.intance;
		}
		protected Object readResolver(){
			return getInstace();
		}
	}
