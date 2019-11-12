## Java读写文件

### 缓存字节流读写

```java
public static void read_and_write_file(String in_file_path,
			String out_file_path) {
		try{
			BufferedInputStream in = new BufferedInputStream(new FileInputStream(
					in_file_path));
			BufferedOutputStream out = new BufferedOutputStream(
					new FileOutputStream(out_file_path));
			byte[] bytes = new byte[2048];
			int n = -1;
			while ((n = in.read(bytes, 0, bytes.length)) != -1) {
				String str = new String(bytes, 0, n, "utf-8");
				System.out.println(str);
				out.write(bytes, 0, n);
			}
			out.flush();
			in.close();
			out.close();
		} catch (IOException e){
			e.printStackTrace();
		}
	}
```

### 逐行读取

```java
public static void read_and_write_file_by_line(String in_file_path,
			String out_file_path) {
		try {
			BufferedReader in = new BufferedReader(new InputStreamReader(
					new FileInputStream(in_file_path), "utf-8"));
			BufferedWriter out = new BufferedWriter(new OutputStreamWriter(
					new FileOutputStream(out_file_path), "utf-8"));
			String str = null;
			while ((str = in.readLine()) != null) {
				System.out.println(str);
				out.write(str);
				out.newLine();
			}
			out.flush();
			in.close();
			out.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
```

