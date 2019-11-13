# Java中加 finally 和 不加 finally 的区别

**如果把异常都catch掉了，加不加 finally 没什么区别，如果需要抛出异常或者没有 catch  运行时异常 的话，finally 外的语句就不会被执行**

例如：

```java
public static void main(String[] args) {
	    System.out.println("测试函数开始！");
	    try {
	        int x[] = null;
	        x.toString();
	        System.out.println("try块语句执行结束！");
	    } catch (ArrayIndexOutOfBoundsException e) {
	        System.out.println(e.toString());
	        System.out.println("catch块语句执行结束！");
	    } finally {
	        System.out.println("finally块语句执行结束！");
	    }
	 
	    System.out.println("测试函数结束！");
	}
```

中 只catch 了`ArrayIndexOutOfBoundsException` 异常，而当触发`NullPointerException`异常时，`System.out.println("测试函数结束！");` 语句就不会被执行。

**在有 return 的情况下，finally 可能会改变 return 的值**，如：

```java
public static void main(String[] args) {
		System.out.println(method());
	}

	static int method() {
		try {
			System.out.println("1");
			return 1;
		} catch (Exception e) {
			return 2;
		} finally {
			System.out.println("2");
            return 3;
		}
	}
```

程序输入 1 2 3，且 方法 `method` 的返回值为：3

说明 **finally 优先于try 中的 ruturn 语句执行**

```java
public static void main(String[] args) {
		System.out.println(method());
	}

	static int method() {
		try {
			System.out.println("1");
			return 1;
		} catch (Exception e) {
			return 2;
		} finally {
			System.out.println("2");
		}
	}
```

中的 2 依旧被输出，更加说明了此问题

而在 不加 finally 的情况下：

```java
public static void main(String[] args) {
		System.out.println(method());
	}

	static int method() {
		try {
			System.out.println("1");
			return 1;
		} catch (Exception e) {
			return 2;
		System.out.println("2");
        return 3;
	}
```

2 不会被输入，且返回值为1