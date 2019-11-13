# Java中throw 和 throws

### throws

抛出异常，让调用者处理

```java
public class TestException {
 
    public static void main(String[] args) {
        method1();
 
    }
 
    private static void method1() {
        try {
            method2();
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
 
    }
 
    private static void method2() throws FileNotFoundException {
 
        File f = new File("d:/LOL.exe");
 
        System.out.println("试图打开 d:/LOL.exe");
        new FileInputStream(f);
        System.out.println("成功打开");
 
    }
}
```

中 方法2 就抛出了 `FileNotFoundException`, 在方法1 调用 方法2 是就处理了该异常，当然，方法1 仍旧可以继续抛出

```java
public class TestException {
 
    public static void main(String[] args) throws FileNotFoundException {
        method1();
    }
 
    private static void method1() throws FileNotFoundException {
        method2();
    }
 
    private static void method2() throws FileNotFoundException {
 
        File f = new File("d:/LOL.exe");
 
        System.out.println("试图打开 d:/LOL.exe");
        new FileInputStream(f);
        System.out.println("成功打开");
 
    }
}
```

### throw

引发一个异常，一般用于创建自定义异常

例如：如果年龄低于18 岁，则引发异常（“未成年人拒接访问”）。如果年龄在18岁或以上，输出“可以访问”：

```java
public class MyClass {
  static void checkAge(int age) {
    if (age < 18) {
      throw new ArithmeticException("未成年人拒接访问");
    }
    else {
      System.out.println("可以访问");
    }
  }

  public static void main(String[] args) {
    checkAge(15); 
  }
}
```

#### throw 和 throws 的区别

从本质上来说，**throw 和 throws 完全是两个东西**

**throw 是引发一个异常**，是指在此处会触发一个异常，比如上面的例子，年龄低于18岁就会触发一个异常，就像你在执行：

```java
public static void main(String[] args) 
    { 
        System.out.println(1/0); 
    } 
```

会触发 `ArithmeticException: / by zero` 一样

而 **throws** 是抛出异常，是指在一段可能会引发异常的代码中，不对异常进行处理而将异常交给调用者处理

当然 throws 也能抛出 throws 引发的异常，如：

```java
public class Test {
	static void checkAge(int age) throws ArithmeticException {
		if (age < 18) {
			throw new ArithmeticException("未成年人拒接访问");
		} else {
			System.out.println("可以访问");
		}
	}

	public static void main(String[] args) {
		try {
			checkAge(15);
		} catch (ArithmeticException e) {
			System.out.print("你是未成年人");
		}
	}
}
```

中程序输入的是 **你是未成年人**， 而非 未成年人拒接访问 ，说明 我们在 `checkAge` 中引发的异常 `ArithmeticException("未成年人拒接访问")` 被 `main` 方法中`tyr` `catch` 捕获