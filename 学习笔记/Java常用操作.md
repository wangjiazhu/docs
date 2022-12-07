# Java常用操作

## 字符串操作

> **说明：**
>
> 1. 有关字符串的操作围绕 `String`类、`StringBuffer`和`StringBuilder`类进行，区别在于`String`类创建的字符串对象是不可变的(final修饰),`StringBuffer`和`StringBuilder`类对字符串变化操作是在原对象本身上进行操作的；
> 2. `StringBuilder`是线程不安全的，`StringBuffer`是线程安全的，因此`StringBilder`的速度优于`StringBuffer`.

### `String`字符串操作

1. 创建字符串

   ```java
   String str1 = "hello";
   
   String str2 = new String("hello");
   
   char[] ch = {'h', 'e', 'l', 'l', 'o'};
   String str3 = new String(ch);
   ```

   

2. 查询字符串长度

   ```java
   int size = str1.length();
   ```

   

3. 查询指定指定位置的字符

   ```java
   // 查找字符串中索引为1的字符
   char ch = str1.charAt(1);
   ```

   

4. 判断两字符串相等

   ```java
   boolean isEqual = str1.equals("wjz");
   /*输出： true */
   ```

   

5. 格式化字符串

   ```java
   String fm = String.format("str1 = %s; str2 = %s; str3 = %s",str1, str2, str3);
   /*输出： str1 = wjz; str2 = hello; str3 = world */
   ```

   

6. 根据正则表达式匹配字符串

   ```java
   String s = "reboot is a name abbreviation";
           String[] s_split = s.split(" ");
           for (String ss: s_split
                ) {
               System.out.println(ss);
           }
   ```

   

7. 将字符串转化为数组

   ```java
   String ss = "reboot";
           char[] ch_list = ss.toCharArray();
           for (char ch: ch_list
                ) {
               System.out.println(ch);
           }
   ```

   

8. 将字符串中的所有字符转化为大写或小写

   ```java
   // 将字符串s中的所有字符转化为大写
   String upStr = s.toUpperCase();
   
   // 将字符串s中的所有字符转化为小写
   String lowStr = s.toLowerCase();
   ```

   

9. 判断字符串是否为空

   ```java
   boolean emptyFlag = s.isEmpty();
   ```

---

### `StringBuilder`字符串操作

1. 创建`StringBuilder`对象

   ```java
   // 方式1： 构造一个空的字符串生成器，默认容量是16个字符
   StringBuilder sbd1 = new StringBuilder();
   
   // 方式2：构造一个初始化为指定字符串的内容的字符串生成器(类似于预定义了一个字符串在StringBuilder中)
   StringBuilder sbd2 = new StringBuilder("wjz");
   
   // 方式3：构造一个字符串生成器没有字符和一个初始容量为 20 个字符的字符串生成器
   StringBuilder sdb3 = new StringBuilder(20);
   ```

   

2. 获取当前```StringBuilder```中的长度（字符的个数）

   ```java
   int size = sbd1.length();
   ```

   

3. 向```StringBuilder```末尾追加字符串

   ```java
   sbd1.append("hello world");
   /*输出：hello world */
   ```

   

4. 向```StringBuilder```指定位置插入字符串

   ```java
   // 在下标6出插入字符串
   sbd1.insert(6, "wjz ");
   /*输出：hello wjz world */
   ```

   

5. 将```StringBuilder```中字符串顺序进行反转

   ```java
   sbd.reverse();
   /*输出：dlrow zjw olleh */
   ```

   

6. 获取`StringBuiler`中指定位置的字符

   ```java
   // 获取位置1处的字符
   char getChar = sbd1.charAt(1);
   ```

   

7. 修改`StringBuilder`中指定位置的字符

   ```java
   // 将位置1处的字符设置为‘w’
   sbd1.setCharAt(1, 'w');
   ```

   

8. 删除`StringBuilder`中指定位置的字符

   ```java
   // 删除索引为1处的字符
   sbd1.deleteCharAt(1);
   /*输出：hllo wjz world */
   ```

   

9. 删除`StringBuilder`中指定范围中的字符

   ```java
   // 删除索引在1~5之间的字符（左闭右开）
   sbd1.delete(1, 5);
   /*输出：hwjz world */
   sbd1.delete(0, sdb1,length())		// 清空StringBuilder中的字符串
   ```

   

10. 替换```StringBuilder```中某部分字符串

    ```java
    // 将字符串 “hwjz world” 中的 “hwjz” 替换为 “hello”
    sbd1.replace(0,4, "hello");
    /*输出：hello world */
    ```

    

11. 获取```StringBuilder```中的一个子串

    ```java
    // 取StringBuilder字符串中索引为5到末尾的字符串
    String subStr = sbd1.substring(5);
    
    // 取StringBuilder字符串中索引在5~8范围中的字符串
    String subStr2 = sbd1.substring(6, 8);
    ```

    

## 数组

### 定义数组

1. 一维数组

   ```java
   // 方法一：定义的时候初始化
   int[] arrayInt1 = {6,3,5,8,3,1,9,6,2};
   
   // 方法二：使用new关键字创建指定大小的数组（不进行初始化）
   int[] arrayInt2 = new int[10];
   ```

   

2. 二维数组

   ```java
   // 方式一：使用new关键字创建固定大小的数组
   String[][] arrayStr1 = new String[3][4];
   
   // 方式二：从最高维开始，分别为每一维分配空间
   String[][] s = new String[2][];
   s[0] = new String[2];
   s[1] = new String[3];
   s[0][0] = new String("Good");
   s[0][1] = new String("Luck");
   s[1][0] = new String("to");
   s[1][1] = new String("you");
   s[1][2] = new String("!");
   ```

---

### `Array`类方法

1. 排序

   ```java
   int[] arrayInt = {6,3,5,8,3,1,9,6,2};
   Arrays.sort(arrayInt);
   ```

   

2. 二分查找

   ```java
   int[] arrayInt = {1,3,5,7,9}
   int index = Arrays.binarySearch(arrayInt, 3);
   ```

   

3. 初始化数组

   > 说明：将数组array中的所有元素初始化为value

   ```java
   int[] arratInt = new int[3];
   Arrays.fill(arratInt, 100);
   ```

   

4. 判断两个数组是否相同

   > 说明：两个数组相同，即两个数组中元素的个数和元素值相同

   ```java
   int[] arr1 = {1,3,5};
   int[] arr2 = {2,4,6};
   boolean isSame = Arrays.equals(arr1, arr2);
   ```

   

## 日期和时间

### `Date`类

1. 获取当前时间

   ```java
   import java.util.Date;
   
   public class DateDemo {
      public static void main(String[] args) {
          // 初始化 Date 对象
          Date date = new Date();
           
          // 使用 toString() 函数显示日期时间
          System.out.println(date.toString());
      }
   }
   ```

   

2. 比较日期

   ```java
   // 使用getTime()方法获取毫秒数，比较两个毫秒数的大小
   long date_secs = date.getTime();
   
   
   // 使用 after、before、equals方法比较
   boolean isAfter = new Date().after(new Date(122, 8, 1));
   boolean isBefore = new Date().before(new Date(122, 8, 1));
   boolean isEqual = new Date().equals(new Date(122, 8, 1));
   
   // 使用compareTo()方法
   System.out.println(date3.compareTo(new Date()));
   ```

---

### `SimpleDateFormat`类

> **说明：**
>
> ​		`SimpleDateFormat`类主要用于格式化日期，允许你选择任何用户自定义日期时间格式来运行。
>
> ​		参考链接：https://www.runoob.com/java/java-date-time.html

​	![image-20220809144810732](https://s2.loli.net/2022/12/07/MGv49VForie3fLw.png)

1. 获取当前时间

   > 说明：按照 `年-月-日 时:分:秒`的形式输出日期

   ```java
   import  java.util.*;
   import java.text.*;
    
   public class DateDemo {
      public static void main(String[] args) {
    
         Date dNow = new Date( );
         SimpleDateFormat ft = new SimpleDateFormat ("yyyy-MM-dd hh:mm:ss");
    
         System.out.println("当前时间为: " + ft.format(dNow));
      }
   }
   ```

   

2. 将字符串解析为时间对象

   > 说明：将字符串`2022-08-15`解析为时间对象

   ```java
   import java.text.ParseException;
   import java.text.SimpleDateFormat;
   import java.util.Date;
   
   public class DateDemo {
   	public static void main(String[] args) {
           String time = "2022-08-15";
           Date T;			// 声明一个Date类的时间对象
           try {
               T = ft.parse(time);
               System.out.println("解析成功：" + T.toString());
           } catch (ParseException e) {
               System.out.println("解析失败：" + ft);
           }
       }
   }
   ```

---

### `printf`格式化输出日期

![image-20220809144712814](https://s2.loli.net/2022/12/07/U6wq1vJFztoLjKP.png)

```java
import java.util.Date;
 
public class DateDemo {
 
  public static void main(String[] args) {
     // 初始化 Date 对象
     Date date = new Date();
 
     //c的使用  
    System.out.printf("全部日期和时间信息：%tc%n",date);          
    //f的使用  
    System.out.printf("年-月-日格式：%tF%n",date);  
    //d的使用  
    System.out.printf("月/日/年格式：%tD%n",date);  
    //r的使用  
    System.out.printf("HH:MM:SS PM格式（12时制）：%tr%n",date);  
    //t的使用  
    System.out.printf("HH:MM:SS格式（24时制）：%tT%n",date);  
    //R的使用  
    System.out.printf("HH:MM格式（24时制）：%tR",date);  
  }
}
```

![image-20220809145207971](https://s2.loli.net/2022/12/07/Bc5bHsLUVXo4YOu.png)

> ​		如果你需要重复提供日期，那么利用这种方式来格式化它的每一部分就有点复杂了。因此，可以利用一个格式化字符串指出要被格式化的参数的索引。索引必须紧跟在%后面，而且必须以$结束。

```java
import java.util.Date;
  
public class DateDemo {
 
   public static void main(String[] args) {
       // 初始化 Date 对象
       Date date = new Date();
        
       // 使用toString()显示日期和时间
       System.out.printf("%1$s %2$tB %2$td, %2$tY", 
                         "Due date:", date);
   }
}
/*输出：Due date: February 09, 2014 */
```

---

### 日期格式转换符格式化日期

> 说明：该方式可通过转换符生成新的日期字符串

```java
import java.util.*;
  
public class DateDemo {
   public static void main(String[] args) {
       Date date=new Date();                                      
        //b的使用，月份简称  
        String str=String.format(Locale.US,"英文月份简称：%tb",date);       
        System.out.println(str);                                                                              
        System.out.printf("本地月份简称：%tb%n",date);  
        //B的使用，月份全称  
        str=String.format(Locale.US,"英文月份全称：%tB",date);  
        System.out.println(str);  
        System.out.printf("本地月份全称：%tB%n",date);  
        //a的使用，星期简称  
        str=String.format(Locale.US,"英文星期的简称：%ta",date);  
        System.out.println(str);  
        //A的使用，星期全称  
        System.out.printf("本地星期的简称：%tA%n",date);  
        //C的使用，年前两位  
        System.out.printf("年的前两位数字（不足两位前面补0）：%tC%n",date);  
        //y的使用，年后两位  
        System.out.printf("年的后两位数字（不足两位前面补0）：%ty%n",date);  
        //j的使用，一年的天数  
        System.out.printf("一年中的天数（即年的第几天）：%tj%n",date);  
        //m的使用，月份  
        System.out.printf("两位数字的月份（不足两位前面补0）：%tm%n",date);  
        //d的使用，日（二位，不够补零）  
        System.out.printf("两位数字的日（不足两位前面补0）：%td%n",date);  
        //e的使用，日（一位不补零）  
        System.out.printf("月份的日（前面不补0）：%te",date);  
   }
}
```

![image-20220809145634411](https://s2.loli.net/2022/12/07/6bcro9Hs7U8ylM1.png)

---

### `Calender`类

> **说明：**
>
> ​		`Calender`类可以完成设置和获取日期数据的特定部分，如小时、日、分钟等，并对各个部分进行加减运算。`Calendar`是一个抽象类，在实际的使用过程中需要实现特定子类的对象(调用`getInstance`方法)。

1. 创建`Calender`日期对象

   ```java
   // 创建代表系统当前日期的Calendar对象
   Calendar c1 = Calendar.getInstance();//默认是当前日期
   
   // 创建指定日期的Calendar对象
   Calendar c2 = Calendar.getInstance();   // 创建Calendar对象
   c2.set(2011, 10, 1);    // 设置对象具体日期
   ```

   

2. 获取时间的指定区域

   ```java
   System.out.println(c1.getTime());   // 获取当前时间
   /* getTime()返回的是一个日期对象，需要Date对象接收
   Date date;
   date = c1.getTime()
   */
   
   System.out.println(c1.get(Calendar.YEAR));  // 获取年份
   System.out.println(c1.get(Calendar.MONTH)+1);   //获取月份
   System.out.println(c1.get(Calendar.DAY_OF_MONTH));  //获取日
   System.out.println(c1.get(Calendar.HOUR));  //获取小时
   System.out.println(c1.get(Calendar.MINUTE));  //获取分钟
   System.out.println(c1.get(Calendar.SECOND));  //获取秒
   /* get()方法获取时间指定部分的结果时候，返回的类型都是int型
   eg:	int day = c1.get(Calendar.YEAR);
   */
   ```

   

3. 对指定日期部分进行算数运算

   ```java
   Calendar c2 = Calendar.getInstance();   // 创建Calendar对象
   c2.set(2011, 10, 1);    // 设置对象具体日期
   System.out.println(c2.getTime());
   c2.set(Calendar.YEAR, 2022);    // 修改对象的年份为2022
   c2.set(Calendar.MONTH, 9);      // 修改对象的月份为9
   c2.set(Calendar.DAY_OF_MONTH, 18);  //修改对象的日期为18
   System.out.println(c2.getTime());
   
   // 对年份进行 加15 运算
   c2.add(Calendar.YEAR, 15);   // 年份增加5年
   c2.add(Calendar.DAY_OF_MONTH, 22);  // 日增加22天，发现月份会发生变化
   ```

## 判断对象的类型

> 语法：( Object reference variable ) `instanceof`  (class/interface type)

```java
// 判断 name 对象是否是 String 类型
String name = "James";
boolean result = name instanceof String; // 由于 name 是 String 类型，所以返回真
```

## 枚举类型

> 说明：`java`中的枚举类型是通过类名和关键字```enum```实现的

```java
public class EnumDemo {
    public static void main(String[] args) {
        JuiceType jc = new JuiceType();
        jc.size = JuiceType.JuiceSize.SMALL;

        System.out.println(jc.size);
    }
}


class JuiceType {
    enum JuiceSize{SMALL, MIDDLE, LARGE}
    JuiceSize size;
}
```

![image-20220807113816259](https://s2.loli.net/2022/12/07/rpmhHlYKL92dwxP.png)

## 遍历操作

#### 增强for循环

> 说明：增强for循环主要用于遍历数组

```java
public class Test {
   public static void main(String[] args){
      int [] numbers = {10, 20, 30, 40, 50};
 	 // 增强for循环遍历int类型的数组 
      for(int x : numbers ){		// 声明的局部变量与数组类型保持一致  （int x）
         System.out.print( x );
         System.out.print(",");
      }
      System.out.print("\n");
      // 增强for循环遍历String类型数组 
      String [] names ={"James", "Larry", "Tom", "Lacy"};
      for( String name : names ) {	// 声明的局部变量与数组类型保持一致		(String name)
         System.out.print( name );
         System.out.print(",");
      }
   }
}
```

## `Math`类运算

> **说明：**
>
> ​		Java 的 Math 包含了用于执行基本数学运算的属性和方法，如初等指数、对数、平方根和三角函数；Math 的方法都被定义为 static 形式，通过 Math 类可以在主函数中直接调用。

```java
public class Test {  
    public static void main (String []args)  
    {  
        System.out.println("90 度的正弦值：" + Math.sin(Math.PI/2));  
        System.out.println("0度的余弦值：" + Math.cos(0));  
        System.out.println("60度的正切值：" + Math.tan(Math.PI/3));  
        System.out.println("1的反正切值： " + Math.atan(1));  
        System.out.println("π/2的角度值：" + Math.toDegrees(Math.PI/2)); 
        System.out.println(Math.ceil(10.4));	// 向上取整
        System.out.println(Math.floor(10.4));	// 向下取整
        System.out.println(Math.PI);  
    }
}
```

## 用户输入

> **说明：**
>
> ​		`java.util.Scanner` 是 `Java5` 的新特征，我们可以通过 Scanner 类来获取用户的输入。

```java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        // 1.创建Sacnner对象
        Scanner sc = new Scanner(System.in);
        if (sc.hasNextLine()) {	//使用hasNext或hasNextLine判断是否还有输入的数据
            // 2.使用nextLine()方法获取字符串
            String str1 = sc.nextLine();
            System.out.println(str1);
        }
    }
}
```

> **注意：**
>
> - `next()`遇到空格作为结束符，`nextLine()`以Enter【回车】作为结束符
> - `next()`只能得到有效字符串，不能得到带有空格的字符串；`nextLine()`可以得到带有空格的字符串
> - `Scanner`还支持用户其他数据类型的输入，如int型的`nextInt()`、byte类型的`nextByte() `。。。只需要进入`hashNextxxx()`判断。

## 文件IO操作

> **说明：**
>
> ​		对文件IO操作是比较常见的，最常用的是`FileInputStream`和`FileOutputStream`类来构建输入输出流对象，完成从文件中读数据，并将数据写到另一个指定文件中。

1. 构建输入流对象

   ```java
   // 方法1：直接通过指定文件路径创建文件输入流对象
   InputStream in = new FileInputStream("F:/hello.txt");
   
   // 方法2：通过文件对象构建文件输入流对象
   File file = new File("F:/hello.txt");
   InputStream in = new FileInputStream(file);
   ```

   

2. 构建输出流对象

   ```java
   // 方法1：直接通过指定文件路径创建文件输出流对象
   FileOutputStream out = new FileOutputStream("F:/hello2.txt");
   
   // 方法2：通过文件对象构建文件输出流对象
   File file = new File("F:/hello2.txt");
   FileOutputStream out = new FileOutputStream(file);
   ```

   

3. 案例

   > 说明：从文件`F:/hello.txt`中读取内容，并将内容写到`F:/hello2.txt`中

   ```java
   import java.io.*;
   
   public class FileIO {
       public static void main(String[] args) {
           try {
               // 1.创建文件输入输出流对象
               FileInputStream in = new FileInputStream("F:/hello.txt");
               FileOutputStream out = new FileOutputStream("F:/hello2.txt");
   
               // 2.构建文件输入输出器对象,参数可以指定编码,默认为操作系统默认编码,windows上是gbk
               InputStreamReader reader = new InputStreamReader(in, "UTF-8");
               OutputStreamWriter writer = new OutputStreamWriter(out, "UTF-8");
   
               // 3.循环从输入流读数据，并写到输出流中
               while (reader.ready()) {
                   writer.write(reader.read());
               }
               
               // 4.关闭流
               writer.close();
               reader.close();
               out.close();
               in.close();
           } catch (IOException e) {
               System.out.println("文件输入输出流失败！");
           }
       }
   }
   ```

   > 总结：从以上案例中，可以得到完成一个文件IO操作的步骤：
   >
   > - 创建`FileInputStream`、`FileOutputStream` 对象
   > - 创建 `InputStreamReader`、`OutputStreamWriter`对象
   > - 读、写文件
   > - 关闭流

## 异常

![image-20220823170121744](https://s2.loli.net/2022/12/07/1devBZkmH5AGQwO.png)

> **说明：** 异常类有两个主要的子类：<font color="red">```IOException```</font> 类和<font color="red"> ```RuntimeException```</font> 类。