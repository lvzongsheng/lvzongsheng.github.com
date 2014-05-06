---
layout: post
title: "有限缓冲区问题"
date: 2014-05-06 18:40
comments: true
categories: 
---
有限缓冲区是很基本的同步问题。当给定一个大小固定的缓冲区时，如何同步充值对缓冲区的插入和删除，也是面试或者平时容易遇到的问题。在操作系统中我们通常会采用信号量来实现有限缓冲区问题。<br/><br/>

首先我们定义三个信号量，分别为
	empty - 表示有限缓冲区可以插入的数据数量，初始化时等于有限缓冲区的大小
	full - 表示有限缓冲区中已经插入的数据数量，初始化时等于0
	mutex - 简单的理解就是互斥量，每次只允许插入或者删除操作。
	根据以上三个信号量，我们可以简单的实现有限缓冲区，代码如下：
	//插入操作
	empty.acquire();
	mutex.acquire();
	//do something;
	mutex.release();
	full.release();
	
	//删除操作
	full.acquire();
	mutex.acquire();
	//do something;
	mutex.release();
	empty.release();
	
而对于信号量，一个重要的原理就是必须保证PV操作的原子性，我们可以通过硬件或者软件的方式来实现。<br/><br/>
<!--More-->
接下来我们使用Java来实现有限缓冲区。
	//插入操作
	public synchronized void insert(Object o){
		while(count==Buffer_Size){
			try{
				wait();
			}catch(InterruptedException e){}
		}
		count++;
		buffer[in] = 0;
		in = (in+1)%Buffer_Size;
		notify();
	}
	
	//删除操作
	public synchronized Object remove(){
		while(count==0){
			try{
				wait();
			}catch(InterruptedException e){}
		}
		count--;
		Object o = buffer[out];
		out = (out+1)%Buffer_Size;
		notify();
		return o;
	}
	
	public class BoundedBuffer implements Buffer{
		private static final int Buffer_Size = 5;
		public in, out, count;
		public Object[] buffer;
		
		public BoundedBuffer(){
			in = 0;
			out = 0;
			count = 0;
			buffer = new Object[Buffer_Size];
		}
		
		public synchronized void insert(Object o){}
		
		public synchronized Oject remove(){}
	}
	
	public class Product implements Runnable{
		BoundedBuffer bb;
		public Product(BoundedBuffer bb){
			this.bb = bb;
		}
		public void run(){
			while(true)
				bb.insert(new String("test"));
		}
	}
	
	public class Consumer implements Runnable{
		BoundedBuffer bb;
		public Consumer(BoundedBuffer bb){
			this.bb = bb;
		}
		public void run(){
			while(true)
				bb.remove();
		}
	}
	
	public class Test{
		BoundedBuffer bb = new BoundedBuffer();
		public static void main(String[] args){
			Product p = new Product(bb);
			Consumer c = new Consumer(bb);
			new Thread(p).start();
			new Thread(c).start();
		}
	}
