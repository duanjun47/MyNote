# 精简 jre

## jre 文件结构

-bin (启动jvm虚拟机中必要的exe和dll)

​	--java.exe (java主程序，用于启动**窗口程序或非阻塞程序** )

​	--javaw.exe (用于启动 **窗口程序或非阻塞程序** 程序)

​	--其他exe (基本都可删除)

​	--server (jvm虚拟机的存放目录)

​		---jvm.dll(jvm 虚拟机)

​	--其他dll

-lib (Java 运行环境所使用的核心类库、属性设置和资源文件)

​	--rt.jar (java提供的基本类库，包含了 Java 平台的核心 API。重点精简对象)

​	--其他资源

## 精简jre

### 准备jre

- 将需要精简的版本的 jre 拷贝一份到一个你能找到的地方（安装Java后，jre一般位于 C:\Program Files\Java 下 ）
- 拷贝需要运行的项目到jre同级目录

### 精简jre目录

在jre目录下，除了bin目录，lib目录的其他文件均可删除

### 精简bin目录

为了方便操作可删除 jre 文件夹名称中的版本信息，如 jre1.8.0_191 删除后为 jre

- 打开 cmd，进入jre 文件夹所在的目录，使用本目录的jre运行你的项目，如：

  一般情况运行你的项目是：java -jar xxx.jar 或 javaw -jar xxx.jar

  现在你应该：jre\bin\java.exe -jar xxx.jar 或 jre\bin\javaw.exe -jar xxx.jar

- 使用文件管理器打开bin文件夹

- 在运行项目的同时，逐一删除bin文件夹下的文件，能删除的表示不需要，可以都删除

- 删除完成后 bin 目录的精简完成，如下：

![](E:/%E7%AC%94%E8%AE%B0/%E6%AF%8F%E6%97%A5%E5%B0%8F%E7%BB%93/img/2019-11-11-%E7%B2%BE%E7%AE%80bin.png)



### 精简lib文件夹

在jre1.8中  rt.jar 类库就占了52.1 MB，所以精简lib文件的主要任务就是精简 rt.jar

#### 精简除 rt.jar 外的其他文件（如果对精简要求不高，或者嫌麻烦的可省略本步骤）

逐个将 lib 下的文件剪切至其它地方，让后运行项目，若项目无法成功运行，则将该文件粘贴回本地

#### 精简rt.jar

##### 提取需要的类

可借助 `-ferbose` 命令，查看虚拟机在运行 Java 程序时所加载的所有类

```bash
java -jar -verbose:class xxx.jar >>class.txt
```

然后运行一遍项目的所有功能（尽可能的所有功能都运行一遍）

在 class.txt 中可以查看到该项目所需要的所有类

##### 重新打包 rt.jar

###### 得到rt需要的类的路径

class.txt 中的条目都形同 [Loaded java.lang.Object from C:\Users\john\Desktop\a\jre\lib\rt.jar] ，我们需要获取每个类的路径，如 java\lang\Object ,以方便我们后面复制所需要的类

- 仅读取 含有 rt.jar 的条目
- 提取条目中的 java.lang.Object
- 将 . 修改为 \

代码如下：

###### java:

```java
public static void get_class_path_file(String class_file_path,
			String re_class_file_path) {
		try {
			BufferedReader in = new BufferedReader(new InputStreamReader(
					new FileInputStream(class_file_path), "utf-8"));
			BufferedWriter out = new BufferedWriter(new OutputStreamWriter(
					new FileOutputStream(re_class_file_path), "utf-8"));
			String str = null;
			while ((str = in.readLine()) != null) {
				if((str.indexOf("rt.jar") != -1) && (str.indexOf("Loaded") != -1)) {
					String class_name = str.substring(8, str.indexOf("from")-1);
					String class_path = class_name.replace(".", "\\");
					out.write(class_path);
		            out.newLine();
				}
			}
			out.flush();
			in.close();
			out.close();
		} catch (IOException e) {
			e.printStackTrace();
		}

	}
```

提取之后结果如下：

```
java\lang\Object
java\io\Serializable
java\lang\Comparable
java\lang\CharSequence
java\lang\String
java\lang\reflect\AnnotatedElement
java\lang\reflect\GenericDeclaration
java\lang\reflect\Type
java\lang\Class
java\lang\Cloneable
java\lang\ClassLoader
java\lang\System
java\lang\Throwable
java\lang\Error
java\lang\ThreadDeath
java\lang\Exception
......
```

###### 解压rt.jar

使用压缩工具解压 rt.jar 得到 rt 文件夹

###### 复制所需要的类到新的文件夹

代码如下：

**Java:**

```java
public static int deal_class(String class_path_file, String rt_dir, String new_rt_dir) {  
        int sum = 0; 
		File used_class = new File(class_path_file);  
        if (used_class.canRead()) {  
            String line = null;  
            try{
            	LineNumberReader reader = new LineNumberReader(new InputStreamReader(new FileInputStream(used_class),  
                        "UTF-8"));  
                while ((line = reader.readLine()) != null) {  
                    line = line.trim();  
                    int dirpos = line.lastIndexOf("/");  
                    if (dirpos > 0) {  
                        String dir = new_rt_dir + line.substring(0, dirpos);  
                        File fdir = new File(dir);  
                        if (!fdir.exists())  
                            fdir.mkdirs();  
                        String sf = rt_dir + line + ".class";  
                        String of = new_rt_dir + line + ".class";  
                        boolean copy_ok = copy_file(sf.trim(), of.trim());  
                        if (copy_ok)  
                        	sum++;  
                        else {  
                            System.out.println(line);  
                        }  
      
                    }  
      
                }
            } catch (IOException e){
            	e.printStackTrace();
            }
        }  
        return sum;  
    } 
public static boolean copy_file(String file1, String file2) {  
        try
        {    
            java.io.File file_in = new java.io.File(file1);  
            java.io.File file_out = new java.io.File(file2);  
            FileInputStream in1 = new FileInputStream(file_in);  
            FileOutputStream out1 = new FileOutputStream(file_out);  
            byte[] bytes = new byte[1024];  
            int c;  
            while ((c = in1.read(bytes)) != -1)  
                out1.write(bytes, 0, c);  
            in1.close();  
            out1.close();  
            return (true);
        } catch (IOException e) {  
            e.printStackTrace();
            return (false);
        }  
    }
```

###### 重新压缩新的 rt.jar 

使用压缩工具将新的 rt 文件下的文件压缩成rt.zip (注意：只能是zip格式，而且压缩后的文件点开后不能有 rt 文件夹)

然后将文件名修改为 rt.jar

或者直接使用下面的代码压缩：

```java
public static void get_rt_jar(String rt_dir,String rt_jar_path){
        try {
        	FileOutputStream fos3 = new FileOutputStream(new File(rt_jar_path));
            File file = new File(rt_dir);
            ZipOutputStream zos = new ZipOutputStream(fos3);
            ZipUtils.dir_to_zip(file, zos, "");
            zos.close();
        } catch(Exception e) {
        	e.printStackTrace();
        }
    }
    private static void dir_to_zip(File sourceFile, ZipOutputStream zos, String name)
            throws Exception {
        boolean KeepDirStructure = true;
        byte[] buf = new byte[2048];
        if (sourceFile.isFile()) {
            zos.putNextEntry(new ZipEntry(name));
            int len;
            FileInputStream in = new FileInputStream(sourceFile);
            while ((len = in.read(buf)) != -1) {
                zos.write(buf, 0, len);
            }
            // Complete the entry
            zos.closeEntry();
            in.close();
        } else {
            File[] listFiles = sourceFile.listFiles();

            if (listFiles == null || listFiles.length == 0) {
                if (KeepDirStructure) {
                    zos.putNextEntry(new ZipEntry(name + "/"));
                    zos.closeEntry();
                }

            } else {
                for (File file : listFiles) {

                    if (KeepDirStructure) {
                        if (!name.equals("")){
                        	dir_to_zip(file, zos, name + "/" + file.getName());
                        }else {
                        	dir_to_zip(file, zos, file.getName());
                        }
                    } else {
                    	dir_to_zip(file, zos, file.getName());
                    }

                }
            }
        }
    }
```

使用方式如下：

```java
public static void get_rt(){
    	String rt_path = "C:\\Users\\john\\Desktop\\jre\\lib\\rt";
    	String jar_path = rt_path + ".jar";
    	get_rt_jar(rt_path, jar_path);
    }
```

让后将得到的 rt.jar替换掉原来 lib 文件夹下的 rt.jar

**运行程序，运行成功**

**我们就得到了一个精简版的 jre, 大小一般在 5MB 左右**

#### 附：运行程序提示无法找到或加载类的解决办法：

有时候由于在 **提取需要的类** 的时候我们没有将所有功能执行完，或者其他原因，在使用精简 jre 运行我们的程序时会提示 **无法找到或加载类** ，这时我们应该：

- 解压 精简 jre 中的rt.jar
- 在 未精简的 rt 文件夹中找到 **无法找到或加载类** 提示中的类
- 将其拷贝至 精简后的 rt 文件夹中
- 重新压缩 rt.jar

运行程序，重复以上步骤，直到不在有该提示即可