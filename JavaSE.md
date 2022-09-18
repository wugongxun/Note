# 第一章 java基础

## 一，快速入门

1. JDK（Java Development Kit   java开发工具包）
2. JRE （Java Runtime Environment   java运行环境）
3. JVM （Java Virtual Machine   java虚拟机）

4. JDK，JRE，JVM的包含关系

* JDK=JRE+开发工具集
* JRE=JVM+java SE标准类库
* JDK=JVM+java SE标准类库+开发工具集

5. .java源文件
6. .class字节码文件
7. ==一个源文件中最多只能有一个public类==
8. ==编译后，每一个类，都对应一个.class==
9. ==如果源文件包含一个public类，则文件名必须按类名命名==
10. ==一个源文件只能有一个public类，其他类个数不限，也可以将main方法写在非public类中，然后指定运行public类，这样入口方法就是非public的main方法== 

```java 
public class hello{
	public static void main(String[] args){
	System.out.println("hello world!");
	}
}
class dog{
    public static void main(String[] args){
	System.out.println("wangwangwang");
	}
}
```

![](F:\截图\捕获.PNG)

## 二，如何快速学习技术或知识点

1. 需求

- 工作需要
- 对方要求
- 技术控

2. 看看能否使用传统技术解决

- 能解决，但是不完美

- 解决不了

  ---

引出我们学习新技术和知识点

1. 学习新技术或者知识点的基本原理和基本语法（不要考虑细节）

2. 快速入门（基本程序，crud）
3. 开始研究技术的注意事项，使用细节，使用规范，如何==优化==>>无止境

## 三，转义字符 

```java 
//演示转义字符的使用
public class ChangeChar{
     public static void main(String[] args){
     	System.out.println("北京\t天津\t上海");
         //\t:一个指表位，实现对齐功能
     	System.out.println("北京\n天津\n上海");
         //\n:换行符
        System.out.println("C:\\Windows\\System32\\cmd.exe");
         //\\:一个\
         //\':一个'
         //\":一个"
         System.out.println("北京天津\r上海");
         //\r:一个回车
     }
}
```

![](F:\截图\捕获1.PNG)

## 四，注释

```java
//单行注释

/*
多行注释
*/

文档注释
/**
 * @author 吴功勋
 * @version 1.0
 */
public class comment{
	public static void main(String[] args){
	System.out.println("hello world!");
	}
}
```

![](F:\截图\捕获3.PNG)

## 五，DOS命令

相对路径：从当前目录开始定位，形成的一个路径

绝对路径：从顶级目录开始定位，形成的一个路径

常用的DOS命令

* 查看当前目录下有什么内容dir   dir f:\javacode

* 切换到其他盘 cd 

  切换到C盘：cd /D c:

- 切换到当前盘其他目录下

  cd f:javacode\

- 切换到根目录 cd \

- 切换到上一级 cd ..

- 查看目录下所有的子级目录 tree

- 清屏 cls

- 退出DOS exit

- 创建目录 md

- 删除目录 rd

- 拷贝文件 copy

- 删除文件 del

- 输入内容到文件 echo

- 剪切 move

  ---

# 第二章 变量



## 一，变量

- 变量是程序的基本组成单位

## 二，加号的使用

- 当左右两边都是数值型时，则做加法运算
- 当左右两边有一方位字符串，则做拼接运算
- 运算顺序从左到右

## 三，数据类型

![](F:\截图\捕获4.PNG)

1. 整型

![](F:\截图\捕获5.PNG)

- 1byte = 8bit

- ~~long n = 1;~~

  long n = 1L；

  2. 浮点型

     ![](F:\截图\捕获6.PNG)

- ~~float n = 1.1;~~

  float n = 1.1F;

  double n = 1.1;

- double n = 0.123;

  double n = .123;*//等价于0.123，0可以省略*

  ---

  浮点数使用陷阱

```java 
double n = 2.7;//2.7
double m = 8.1/3;//接近2.7的一个小数，而不是2.7
//得到以恶搞重要的使用点：当对运算结果是小数进行相等判断时，要注意
//应该是两个数的差值的绝对值，在某个范围内判断
//错误写法
if (n == m){
    System.out.println("相等");
}
//正确写法
if ( Math.abs(n - m) < 0.000001 ){
    System.out.println("相等");
}
```

3. 字符型

   ![](F:\截图\捕获7.PNG)

   [Unicode码转换](http://tool.chinaz.com/Tools/Unicode.aspx)

   ![](F:\截图\捕获8.PNG)

   4. 布尔类型（boolean）

      布尔类型数据只允许取值true和false，无null

      boolean类型占一个字节

## 四，Java API 文档

[matools](https://www.matools.com)

## 五，数据类型转换

1. 自动类型转换

   ![](F:\截图\捕获9.PNG)

- 有多种类型的数据混合运算时，系统自动将所有数据转换成容量最大的那种数据类型然后再进行计算

- byte,short和char之间不能自动转换

- byte，short，char之间可以计算，在计算时先转换成int类型

  ~~byte n1 = 1;~~

  ~~byte n2 = 1;~~

  ~~byte n3 = n1 + n2;~~*//会先转换成int类型*

- boolean不参与类型的自动转换

- 自动提升原则：表达式结果的类型自动提升为操作数中最大的类型

2. 强制类型转换

- 强转符号只对最近的操作数有效，往往使用小括号提高优先级

- char类型可以保存int的常量值，但不能保存int的变量值，需要强转

3. 基本数据类型和String类型的转换

```java 
//基本数据类型转String型
int n1 = 10;
float n2 = 1.1F;
double n3 = 2.2;
boolean n4 = true;
String s1 = n1 + "";
String s2 = n2 + "";
String s3 = n3 + "";
String s4 = n4 + "";

//String类型转基本数据类型
//调用基本数据类型对应的包装类的相应方法
String s5 = "123";
int n5 = Integer.parseInt(s5);
double n6 = Double.parseDouble(s5);
float n7 = Float.parseFloat(s5);
long n8 = Long.parseLong(s5);
boolean n9 = Boolean.parseBoolean(s5);

//String转成字符char >> 把字符串的第一个字符得到
System.out.println(s5.charAt(0)");
```

# 第三章 运算符

## 一，运算符

- 运算符是一种特殊的符号，用以表示数据的运算，赋值，比较等

1. 算术运算符

   - 算术运算符是对数值类型的变量进行运算的，在java程序中使用的非常多

   - ![](F:\截图\捕获10.PNG)

   - ==%的本质是一个公式：a%b=a - a / b * b==(如果a为小数则公式变为a%b = a - (int)a / b * b)

   - ==i++是先赋值后自增==

   - ==++i是先自增后赋值== 

   - ```java 
     int i = 1;
     i = i++;//规则使用临时变量：1.temp=i;2.i=i+1;3.i=temp;
     System.out.println(i);//结果为1
     ```

   - ```java  
     int i = 1;
     i = ++i;//规则使用临时变量：1.i=i+1;2.temp=i;3.i=temp;
     System.out.println(i);//结果为2
     ```

     

2. 关系运算符

   - 关系运算符的结果都是boolean型
   - 关系表达式经常用在if结果的条件中或循环的条件中
   - ![](F:\截图\捕获11.PNG)

3. 逻辑运算符

   - 用于连接多个条件（多个表达式），最终的结果也是一个boolean值
   - ![](F:\截图\捕获12.PNG)
   - ==&&短路与：如果第一个条件为false，则第二个条件不会判断，最终结果为false，效率高==
   - ==&逻辑与：不管第一个条件是否为false，第二个条件都要判断，效率低==
   - ==||短路或：如果第一个条件为true，则第二个条件不会判断，最终结果为true，效率高==
   - ==|逻辑或：不管第一个条件是否为true，第二个条件都要判断，效率低==

4. 赋值运算符

   - 赋值运算符就是将某个运算后的值，赋给指定变量
   - a += b;等价于a = a + b;    （-,*,/相同）
   - 符合运算符会进行类型转换（byte b = 1;b += 1;//不会报错，结果为2）
   - 运算顺序从右往左

5. 三元运算符

   - 条件表达式？表达式1：表达式2；

     条件表达式为true，运算结果为表达式1

     条件表达式为false，运算结果为表达式2

6. 运算符优先级

![](F:\截图\捕获13.PNG)

7. 位运算符

   - ==原码，反码，补码==![](F:\截图\捕获14.PNG)

   - 位运算符![](F:\截图\捕获15.PNG)![](F:\截图\捕获16.PNG)

     1>>2本质上是1除以2在除以2,结果为0

     1<<2本质上是1乘以2在乘以2,结果为4

# 第四章 控制结构

## 一，顺序控制

程序从上到下逐行执行，中间没有任何判断和跳转

## 二，分支控制

1. 单分支

   - ```java 
     if (){
         //代码块;
     }
     ```

2. 双分支

   - ```java 
     if (){
         //代码块1;
     }
     else{
         //代码块2;
     }
     ```

3. 多分支

   - ```java 
     if (//条件表达式1){
         //代码块1;
     }
     else if(//条件表达式2){
         //代码块2;
     }
     ...
     else{
         //代码块n;
     }
     ```

4. 嵌套分支

   - ```java
     if(){
         if(){
         }
         else{
         }
     }
     else{
     }
     ```

5. switch分支结构

   - switch关键字，表示switch分支
   - 表达式对应一个值
   - case常量1：当表达式的值等于常量1，就执行语句块1
   - break：表示退出switch
   - 如果和case常量1匹配，就执行语句块1，如果没有匹配，就匹配case常量2
   - 如果一个都没有匹配上，就执行default
   - ![](F:\截图\捕获17.PNG)

## 三，循环控制

1. for循环控制

   - for(循环遍历初始化;循环条件;循环变量迭代){

     ​            循环操作(可以多条语句)

     }

2. while循环控制

   - 循环变量初始化;

     while(循环条件){

     ​         循环体;

     循环变量迭代;

     }

3. do..while循环控制

   - 循环变量初始化;

     do{

     ​         循环体;

     ​         循环变量迭代;

     }while(循环条件)==;==

4. 多重循环控制

   - 嵌套循环就是把内层循环当成外层循环的循环体，当只有内层循环的循环条件为false是才会完全跳出内层循环，才可以借宿外层的当次循环，开始下一次循环

   - ==空心金字塔==

     ```java 
     //实心金字塔
     public class Stars{
     	public static void main(String[] args){
     		for (int i = 1;i<=5;i++){  //表示层数
     			for (int k = 1;k <= 5 - i;k++){  //k表示输出的空格数
     				System.out.print(" ");
     			}
     			for (int j = 1;j <= 2 * i - 1;j++){   //j表示输出的*数
     				System.out.print("*");
     			}
     		System.out.print("\n");
     		}
     	}
     }
     //空心金字塔
     public class Stars{
     	public static void main(String[] args){
     		for (int i = 1;i<=5;i++){
     			for (int k = 1;k <= 5 - i;k++){
     				System.out.print(" ");
     			}
     			for (int j = 1;j <= 2 * i - 1;j++){
     				if (j == 1 || j == 2 * i - 1 || i == 5){   //只打印第一个*和最后一个*，且i=5时全部输出*
     					System.out.print("*");
     				}
     				else {
     					System.out.print(" ");  //中间换成空格
     				}
     			}
     		System.out.print("\n");
     		}
     	}
     }
     ```

     

## 四，跳转控制

1. 跳转控制语句-break

   - break语句用于终止某个语句块的执行，一般使用在switch或者循环中

   - {    .....

     ​     break;

     ​     .....

     }

   - break语句中出现多层嵌套的语句中时，可以通过标签指明要终止的是哪一层语句块

     ```java
     label1:{...
     label2:    {...
     label3:        {...
                     break label1; 
                     ...
                    }    
                }
             }
     ```

     

2. 跳转控制语句-continue

   - continue语句用于结束本次循环，==继续执行下一次循环==

   - {  ......

        continue;

        ......

     }

   - continue语句出现在多层循环语句体重时，可以通过标签指明要跳过的是哪一层循环

3. 跳转控制语句-return

   - return使用在方法，表示跳出所在的方法==（如果return写在main方法，表示退出程序）==

# 第五章 数组，排序和查找

## 一，数组

- 数组可以存放对个同一类型的数据。数组本身也是一种数据类型，是引用类型

  **数组就是一组数据**

- 动态初始化1

  数组类型 数组名[] = new 数据类型[大小]

  int a[] = new int[5];*//创建了一个数组，名字a，存放5个Int*

- 动态初始化2

  先声明数组

  数据类型 数组名[];(也可以 数据类型[] 数组名;)

  int a[];或者 int[] a;

  再创建数组

  数组名 = new 数据类型[大小];

  a = new int[10];

- 静态初始化

  数据类型 数组名[] = {元素值，元素值，···}

  int a[] = {1, 2, 3, 4, ···}

- ==基本数据类型赋值，这个值就是具体的数据，而且互相不影响==

  ==数组在默认情况下是引用传递，赋的值是地址==

  int n1 = 10;

  ==int n2 = n1;==

  n2 = 80;

  ---

  int arr1[] = {1, 2, 3};

  ==int arr2[] = arr1;==

  arr2[0] = 10;//arr1[0]也会变成10

  ![](F:\截图\捕获18.PNG)

- 数组的拷贝

  int arr1[] = {1, 2, 3};

  int arr2[] = new int[arr1.length];

  for (int i = 0;i < arr1.length;i++){

  ​       arr2[i] = arr1[i];

  }

  arr2[0] = 10;//不会对arr1产生影响

- 数组的反转

  1. 通过找规律反转
  2. 使用逆序赋值法

- 数组扩容

  ```java
  import java.util.Scanner;
  public class Array03{
  	public static void main(String[] args){
  		int a[] = {1, 2, 3};
  		char answer = ' ';
  		int n = 0;
  		do {
  				int b[] = new int[a.length + 1];
  				for (int i = 0 ; i < a.length;i++){
  					b[i] = a[i];
  				}//将a数组拷贝到b数组
  				Scanner scanner1 = new Scanner(System.in);
  				System.out.println("请输入需要添加的数");
  				n = scanner1.nextInt();//接受输入的数
  				b[b.length - 1] = n;
  				a = b;//a数组指向b数组
  				System.out.println("a[]中的元素为");
  				for (int i = 0;i < a.length;i++){
  					System.out.print(a[i] + "\t");
  				}
  				Scanner scanner2 = new Scanner(System.in);
  				System.out.println("是否继续提添加（y/n）");
  				answer = scanner2.next().charAt(0);
  			}while (answer == 'y');
  	}
  }
  ```

- 数组的缩减

  ```java
  import java.util.Scanner;
  public class Array04{
  	public static void main(String[] args){
  		int arr[] = {1, 2, 3, 4, 5};
  		char answer = ' ';
  		do {
  			//如果只剩下一个元素，使用break跳出循环
  			if (arr.length == 1){
  				System.out.println("只剩下一个元素，不能缩减");
  				break;
  			}
  			//创建一个新数组arrNew,将arr去掉最后一个元素拷贝到arrNew
  			int arrNew[] = new int[arr.length - 1];
  			for (int i = 0;i < arrNew.length;i++){
  				arrNew[i] = arr[i];
  			}
  			//让arr指向arrNew
  			arr = arrNew;
  			//显示arr的元素
  			for (int i = 0;i < arr.length;i++){
  				System.out.print(arr[i] + "\t");
  			}
  			//问是否继续缩减
  			System.out.println("是否继续缩减（y/n）");
  			Scanner scanner = new Scanner(System.in);
  			answer = scanner.next().charAt(0);
  		}while (answer == 'y');
  	}
  }
  ```


## 二，排序

- 排序是将多个数据，依指定的顺序进行排序的过程

1. 内部排序

   指将需要处理的所有数据都加载到内部存储器中进行排序，包括（交换式排序、选择式排序法和插入式排序法）

2. 外部排序法

   数据量过大，无法全部加载到内存中，需要借助外部储存进行排序，包括（合并排序法和直接合并排序法）

- 冒泡排序（BubbleSort）

  通过对待排序序列从后向前（下标大的元素开始），一次比较相邻元素的值，若发现逆序则交换，是值较大的元素逐渐从前移向后部

## 三，查找

1. 顺序查找
2. 二分查找

## 四，多维数组

1. 二维数组

   ```java 
   public class TwoDimensionlArray01{
   	public static void main(String[] args){
   		int[][] arr = {{0, 0, 0, 0},{0, 0, 0, 1},{0, 0, 0, 3},{0, 0, 0, 4}};
   		for (int i = 0;i < arr.length;i++){
       		for (int j = 0;j < arr[i].length;j++){
   		        System.out.print(arr[i][j] + "\t");
   		    }
   		    System.out.print("\n");
   		}
   	}
   }
   ```

2. 使用方法

   - 动态初始化1

     类型【】【】 数组名 = new 类型【大小】【大小】

     ![](F:\截图\捕获19.PNG)

   - 动态初始化2

     先声明：类型 数组名【】【】；

     再定义：数组名 = new 类型【大小】【大小】

   - 动态初始化3-列数不确定

     ```java 
     /*创建下面二维数组
     1
     2 2
     3 3 3*/
     int[][] arr = new int[3][];
     for (int i = 0;i < arr.length;i++){
         arr[i] = new int[i + 1];
         for (int j = 0;j < arr[i].length;j++){
             arr[i][j] = i+1;
         }
     }
     ```

   - 静态初始化

     类型 数组名【】【】 = {{值1， 值2，···}，{值1， 值2，···}，···}；

   - 杨辉三角

     ```java 
     public class YangHui{
     	public static void main(String[] args){
     		int[][] arr = new int[10][];
     		for (int i = 0;i < arr.length;i++){
     			arr[i] = new int[i + 1];
     			for (int j = 0;j < arr[i].length;j++){
     				if (j == 0 || j == arr[i].length - 1){
     					arr[i][j] = 1;
     				}
     				else {
     					arr[i][j] = arr[i - 1][j] + arr[i - 1][j - 1];
     				}
     			}
     		}
     		for (int i = 0;i < arr.length;i++){
     			for (int j = 0;j < arr[i].length;j++){
     				System.out.print(arr[i][j] + "\t");
     			}
     		System.out.println();
     		}
     	}
     }
     ```

     ![](F:\截图\捕获20.PNG)

# 第六章 面向对象(基础)

## 一，类与对象

1. 类与对象的区别和联系
   - 类是抽象的，概念的，代表一类事物，比如人类，猫类··，即它是数据类型
   - 对象是具体的，实际的代表一个具体事物，即是实例
   - 类是对象的模板，对象是类的一个个体，对应一个实例

2. 对象的存在形式
   - ![](F:\截图\捕获21.PNG)

3.  属性/成员变量
   - 从概念或叫法上：成员变量=属性=field（字段）
   - 属性是类的一个组成部分，一般是基本数据类型，也可以是引用类型（对象，数组）

4. 创建对象

   - 先声明再创建

     Cat cat;//声明对象cat

     cat = new Cat();//创建

   - 直接创建

     Cat cat = new Cat();

5. 如何访问属性

   - 对象名.属性名

     cat.name;

6. ==类与对象的内存分配机制==

   - 栈：一般存放基本数据类型

     堆：存放对象（Cat cat，数组等）

     方法区：常量池（常量，比如字符串），类加载信息

   - ![](F:\截图\捕获22.PNG)

## 二，成员方法

1. ==方法的调用过程==

   - [视频连接](https://www.bilibili.com/video/BV1fh411y7R8?p=204&spm_id_from=pageDriver)

   - 1. ==当程序执行到方法是，就会开辟一个独立空间（栈空间）==

     2. 当方法执行完毕，或者执行到return语句时，就会返回

     3. 返回到调用方法的地方

     4. 返回后，继续执行方法后面的代码

     5. 当main方法（栈）执行完毕，整个程序退出

2. 方法的定义

   - 访问修饰符 返回值类型 方法名 (形参列表..){

     ​            语句；

     ​             return 返回值；//return语句不是必须的

     }

3. 方法的注意事项和使用细节

   - 访问修饰符（作用是控制方法使用的范围）

     如果不写就是默认，（有四种public，protected，默认，private）

   - 返回数据类型

     1. 一个方法最多一个返回值（如果返回多个值，可以返回数组）

     2. 返回类型可以为任何类型，包括基本类型和引用类型（数组，对象）

     3. 如果方法要求有返回数据类型，则方法中最后的执行语句必须为return值；

     ​       且要求返回值类型必须和return的一致或兼容

     4. 如果方法为void，则方法体中可以没有return语句，或者只写return

   - 形参列表

     1. 一个方法可以有0个参数，也可以多个参数，中间用逗号隔开
     2. 参数类型可以为任意类型，包含基本类型或引用类型
     3. 调用带参数的方法时，一定对应着参数列表传入相同类型或兼容类型的参数
     4. 方法定义时的参数称为形式参数，简称形参；方法调用时的参数称为实际参数，简称实参。形参和实参的类型要一致或兼容，个数、顺序必须一致

   - 方法体中不能嵌套定义方法

   - 方法调用

     1. 同一个类中的方法调用，直接调用即可
     2. 跨类中的方法A类调用B类方法，需要通过对象名调用
     3. 跨类的方法调用和访问修饰符相关

4. ==方法的传参机制==

   - [视频连接](https://www.bilibili.com/video/BV1fh411y7R8?p=211&spm_id_from=pageDriver)

     基本数据类型，传递的是值（值拷贝），形参的任何改变不影响实参

   - [视频连接](https://www.bilibili.com/video/BV1fh411y7R8?p=212&spm_id_from=pageDriver)

     引用类型传递的是地址（传递也是值，但是值是地址），可以通过形参影响实参
     
   - 克隆对象
   
     ```java 
     public Person copyPerson(Person p){
         Person p2 = new Person();
         p2.name = p.name;
         p2.age = p.age;
         return p2;
     }
     ```
   
5. ==方法递归调用==

   - 递归就是自己调用自己，每次调用时传入不同的变量。递归有助于编程者解决复杂问题，同时让代码变得简洁

   - ![](F:\截图\捕获23.PNG)

   - 递归的重要规则

     执行一个方法时，就创建一个新的受保护的独立空间（栈）

     方法的局部变量是独立的，不会互相影响

     如果方法中所使用的是引用类型变量（比如数组，对象），就会共享该引用类型的数据

     递归必须向退出递归的条件逼近，否则就是无限递归（StackOverflowError）

     当一个方法执行完毕或者遇到了return，就会返回，遵守谁调用就讲结果返回给谁

   - ==老鼠走迷宫（MiGong.java）==

     ```java
     public class MiGong{
     	public static void main(String[] args){
     		//定义数组
     		int[][] map = new int[8][7];
     		//将最右和最左设置为1
     		for (int i = 0;i < 8;i++){
     			map[i][0] = 1;
     			map[i][6] = 1;
     		}
     		//将最上和最下设置为1
     		for (int i = 0;i < 7;i++){
     			map[0][i] = 1;
     			map[7][i] = 1;
     		}
     		//将map[3][1],map[3][2]设置为1
     		map[3][1] = 1;
     		map[3][2] = 1;
     		//当前地图
     		System.out.println("默认地图");
     		for (int i = 0;i < map.length;i++){
     			for (int j = 0;j < map[i].length;j++){
     				System.out.print(map[i][j] + " ");
     			}
     		System.out.println();
     		}
     		//创建对象，调用findway方法
     		FindWay f = new FindWay();
     		f.findWay(map, 1, 1);
     		//输出走完的后的地图
     		System.out.println("走完后的地图");
     		for (int i = 0;i < map.length;i++){
     			for (int j = 0;j < map[i].length;j++){
     				System.out.print(map[i][j] + " ");
     			}
     		System.out.println();
     		}
     	}
     }
     //0代表能走通，1代表不能走通，2代表能走通且走过了，3代表能走通但是走不出去
     class FindWay{
     	public boolean findWay(int[][] map, int i, int j){
     		//如果map[6][5]为2，说明已经走出去了
     		if (map[6][5] == 2){
     			return true;
     		}
     		else {
     			if (map[i][j] == 0){//当前位置如果为0，说明可以走
     				map[i][j] = 2;//能走通，且走过，将该位置设置为2
     				if (findWay(map,i + 1,j)){//向下走
     					return true;
     				}
     				else if (findWay(map,i,j + 1)){//向右走
     					return true;
     				}
     				else if (findWay(map,i - 1,j)){//向上走
     					return true;
     				}
     				else if (findWay(map,i,j - 1)){//向左走
     					return true;
     				}
     				else {
     					map[i][j] = 3;
     					return false;//走不出去，设置为3
     				}
     			}
     			else {
     				return false;
     			}
     		}	
     	}
     }
     ```

     ![](F:\截图\捕获24.PNG)

## 三，方法的重载

- java中允许同一个类中，多个同名方法的存在，但要求形参列表不一致

## 四，可变参数

- java允许将同一个类中同名同功能但是参数个数不同的方法，封装成一个方法

- 基本语法

  访问修饰符 返回类型 方法名(数据类型... 形参名){

  }

- 使用可变参数时，可以看做数组来使用 

- 可变参数可以和普通类型的参数一起放在形参列表，但必须保证可变参数在最后

- 一个形参列表中只能出现一个可变参数

## 五，作用域

- 局部变量一般指成员方法中定义的变量

- 全局变量，也就是属性，作用域为整个类体

- ==全局变量（属性），可以不赋值，直接使用，因为有默认值==

  ==局部变量必须赋值后才能使用，因为没有默认值==

- 属性和局部变量可以重名，访问值遵循就近原则

- 在同一个作用域中两个局部变量不能重名

- 属性生命周期较长，伴随对象的创建而创建，伴随对象的销毁而销毁。

  局部变量生命周期较短，伴随它的代码块的执行而创建，伴随代码块的结束而销毁

- 属性可以加修饰符

  局部变量不能加修饰符

## 六，构造方法/构造器

- 构造方法又叫构造器(constructor)，是类的一种特殊的方法，它的主要作用是完成对新对象的初始化

  1. 方法名和类名相同
  2. 没有返回值
  3. 在创建对象时，系统会自动的调用该类的构造器完成对对象的初始化

- 构造器细节

  1. 一个类可以定义多个不同的构造器，即构造器的重载
  2. 构造器名和类名要相同
  3. 构造器没有返回值
  4. 对象创建的时候，系统自动调用该类的构造器
  5. 如果程序员没有定义构造器，系统会自动给类生成一个默认无参构造器(也叫默认构造器)
  6. 一旦定义了自己的构造器，默认的构造器就覆盖了，就不能再使用默认的无参构造器

- ==对象创建的流程分析==

  ```java
  class Person{
      int age = 90;
      String name;
      Person(String n, int a){
          name = n;
          age = a;
      }
  }
  Person p = new Person("wgx", 20);
  ```

  

  1. 加载类信息(Person.class)，只会加载一次
  2. 在堆中分配空间(地址)
  3. ==完成对象的初始化==
     - 默认初始化(age = 0; name = null;)
     - 显式初始化(age = 90; name = null;)
     - 构造器初始化(age = 20; name = wgx;)
  4. 在对象在堆中的地址，返回给p(p是对象名，也可以叫对象的引用)

## 七，this关键字

- 哪个对象调用，this就代表哪个对象

  [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=248&spm_id_from=pageDriver)

- this关键字可以用来访问本类的属性、方法、构造器

- this用于区分当前类的属性和局部变量

- 访问成员方法的语法：this.方法名(参数列表);

- 访问构造器语法：this(参数列表);==注意只能在构造器中使用，且必须放在第一条语句（即，在构造器中访问另外一个构造）==

- this不能在类定义的外部使用，只能在类定义的方法中使用

- ==匿名对象只能用一次==

  [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=258&spm_id_from=pageDriver)

# 第七章 面向对象(中级)

- IDEA快捷键
  1. 删除当前行Ctrl+Y
  2. 复制当前行Ctrl+D
  3. 补全代码Alt+/
  4. 添加注释和取消注释Ctrl+/
  5. 导入该行需要的类Alt+Enter
  6. 快速格式化代码Ctrl+Alt+L
  7. 快速运行代码Shift+F10
  8. 生成构造器等Alt+Insert
  9. 查看一个类的层级关系Ctrl+H
  10. 定位到方法Ctrl+B，或者按住Ctrl右键单击
  11. 自动分配变量名Alt+Enter，或者加.var
  12. 模板在file->settings->editor->Live templates查看

## 一，包

1. 包的作用

   - 区分相同名字的类
   - 当类很多时，可以很好的管理类
   - 控制访问范围

2. 包的基本语法

   - package com.wgx;
   - package 关键字
   - com.wgx表示包名

3. 包的本质实际上就是创建不同的文件夹/目录来保存类文件

4. 包的命名

   - 只能包含数字，字母，下划线，小圆点，但是不能用数字开头，不能关键字或者保留字

   - 一般是小写字母+小圆点

     com.公司名.项目名.业务名

5. 常用的包

   - java.lang.* //lang包是基本包，默认引入，不需要再引入
   - java.util.* //util包是系统提供的工具包，工具类
   - java.net.* //网络包，网络开发
   - java.awt.* //是做java的界面开发，GUI

6. 包使用细节

   - package的作用是声明当前类所在的包，需要放在类的最上面，一个类中最多只有一句package
   - import指令放在package下面，类定义的前面，可以有多句且没有顺序要求

## 二，访问修饰符

- 基本介绍

  java提供四种访问修饰符号，用于控制方法和属性的访问权限

  1. 公开级别：public修饰，对外公开
  2. 受保护级别：protected修饰，对子类的同一个包中的类公开
  3. 默认级别：没有修饰符，向同一个包的类公开
  4. 私有级别：用private修饰，只有类本身可以访问，不对外公开

  ![](F:\截图\捕获25.PNG)

## 三，封装

1. 封装的介绍

   - 封装就是把抽象出的数据[属性]和对数据的操作[方法]封装在一起，数据被百货在内部，程序的其他部分只有通过被授权的操作[方法]，才能对数据进行操作

2. 封装的理解和好处

   - 隐藏实现细节：方法(连接数据库)<- 调用(传入参数..)
   - 可以对数据进行验证，保证安全合理

3. 封装的实现步骤

   - 将属性私有化（不能直接修改属性）

   - 提供一个公共的set方法，用于对属性判断并赋值

     public void setXxx(类型 参数名){

     ​       //加入数据验证的业务逻辑

     ​       属性 = 参数名

     }

   - 提供一个公共的get方法，用于获取属性的值

     public 数据类型 getXxx(){

     ​       //权限判断

     ​       return xx;

     } 

4. 将构造器和setXxx结合

## 四，继承

1. 继承的介绍

   - 继承可以解决代码的复用性，让我们的编程更加靠近人类思想，当多个类存在相同属性和方法的时候，可以从这些类中抽出父类，在父类中定义这些相同的属性和方法，所有的子类不需要重新定义这些属性和方法，只需要通过extends来声明继承父类即可

2. 继承的基本语法

   class 子类 extends 父类{

   }

3. 继承细节

   - 非私有属性和方法可以直接在子类访问，但是私有属性和方法不可以直接在子类访问，要通过提供公共的方法区访问
   - 子类必须调用父类的构造器，完成父类的初始化
   - 创建子类对象时，不管使用，默认情况下总会去调用父类的无参构造器，如果父类没有提供无参构造器，则必须在子类的构造器中用super去指定使用父类的哪个构造器完成对父类的舒适化工作，否则，编译不会通过
   - 如果希望指定去调用父类的某个构造器，则显式的调用一下：super（参数列表）
   - super使用时，必须放在构造器的第一行（super只能在构造器中使用）
   - super（）和this（）都只能放在构造器第一行，因此这两个方法不能共存在一个构造器
   - java所有类都是Object类的子类，Object是所有类的基类
   - 父类构造器的调用不限于直接父类，将一直往上追溯直接Object类（顶级父类）
   - 子类最多只能直接继承一个父类，即java中时单继承机制
   - 不能滥用继承，子类和父类之间必须满足is-a的逻辑关系

4. ==继承的本质分析==

   - [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=294&spm_id_from=pageDriver)
   - ![](F:\截图\捕获26.PNG)

5. super关键字

   - super代表父类的引用，用于访问父类的属性、方法、构造器

   - 访问父类的属性，但不是不能访问父类的private属性

     super.属性名;

   - 访问父类的方法，不能访问父类的private方法

     super.方法名(参数列表)

   - 访问父类的构造器

     super(参数列表);只能放在构造器的第一句，只能出现一句

   - 当子类中有和父类的成员（属性和方法）重名时，为了访问父类的成员，必须通过super

   - super和this的比较

     ![](F:\截图\捕获27.PNG)

## 五，方法重写(覆盖)

1. 基本介绍
   - 方法覆盖（重写）就是子类有一个方法，和父类的某个方法的名称，返回类型，参数一样，那么我们就说子类的这个方法覆盖了父类的方法
2. 使用细节
   - 子类的方法的==形参列表，方法名==，要和父类的方法的形参列表，方法名完全一样
   - 子类方法的返回类型和父类的方法返回类型一样，或者是父类返回类型的子类
   - 子类方法不能缩小父类方法的访问权限

## 六， 多态

1. 基本介绍

   - 方法或对象具有多种形态，是面向对象的第三大特征，多态是建立在封装和继承基本之上

2. 对象的多态

   - 一个对象的编译类型和运行类型可以不一致
   - 编译类型在定义对象时，就确定了，不能改变
   - 运行类型是可以变化的
   - 编译类型看定义时=号的左右，运行类型看=号的右边

3. 使用细节

   - 使用多态的前提是两个对象（类）存在继承关系

   - ==向上转型==

     本质：父类的引用指向了子类的对象

     语法：父类类型 引用名 = new 子类类型();

     特点：可以调用父类的所有成员（需要遵守访问权限）

     ​           不能调用子类中特有成员

   - ==向下转型==

     本质：把指向子类的对象的父类引用，转成指向子类的子类引用

     语法：子类类型 引用名 = （子类类型）父类引用

     只能强转父类的引用，不能强转父类的对象
     要求父类的引用必须指向的是当前目标类型的对象

     当想下转型后就可以调用子类类型中所有的成员

   - ==属性没有重写之说，属性的值看编译类型==

   - instanceOf 比较操作符，用于判断对象的==运行类型==是否为XX类型或XX类型的子类型

4. ==java的动态绑定机制==

   - 当调用对象方法的时候，该方法会和该对象的内存地址/运行类型绑定
   - 当调用属性时，没动态绑定机制，哪里声明，哪里使用

5. 多态的应用

   - 多态数组

   - 多态参数

     方法定义的形参类型为父类类型，实参类型允许为子类类型

## 七，Object类详解

1. equals方法

   - == 和equals的对比

     ==是一个运算符

     ==：既可以判断基本类型，又可以判断引用类型

     ==：如果判断基本类型，判断的是值是否相等

     ==：如果判断引用类型，判断的是地址是否相等，即判断是不是同一个对象

     equals：是Object类中的方法，只能判断引用类型

     equals：默认判断的是地址是否相等，子类中往往重写该方法，用于判断内容是否相等

2. hashCose方法
   - 提高具有哈希结构的容器效率
   - 两个引用，如果指向的是同一个对象，则哈希值肯定是一样的
   - 两个引用，如果指向的是不同对象，则哈希值是不一样的
   - 哈希值主要根据地址号来的（不能完全将哈希值等价于地址）
3. toString方法
   - 默认返回：全类名+@+哈希值的十六进制
   - 重写toString方法，打印或拼接对象，都会自动调用该对象的toString形式
   - ==当直接输出一个对象时，toString方法会被默认调用==

4. finalize方法
   - 当对象被回收时，系统自动调用该对象的finalize方法。子类可以重写该方法，做一些释放资源的 操作
   - 当某个对象没有任何引用时，则jvm就会认为这个对象是一个垃圾对象，就会使用垃圾回收机制来销毁对象，在销毁该对象前，会先调用finalize方法
   - 垃圾回收机制的调用，是由系统来决定，也可以通过System.gc()主动触发垃圾回收机制

5. 断点调试（debug）

   - 基本介绍

     断点调试是指在程序的某一行设置一个断点，调试时，程序运行到这一行就会停住，然后你可以一步步往下调试，调试过程中可以看各个变量当前的值，出错的话，调试带出错的代码行即显示错误，停下

   - 断点调试的快捷键

     F7（跳入）F8（跳过）shift+F8（跳出）F9（resume,执行到下一个断点）

# 第八章 面向对象(高级)

## 一，类变量和类方法

1. 类变量基本介绍

   - 类变量也叫静态变量/静态属性，是该类的所有对象共享的变量，任何一个该类的对象去访问它时都是相同的值，同样任何一个该类的对象去修改它时，修改的也是同一个变量

2. 如何定义类变量

   - 访问修饰符 static 数据类型 变量名;(推荐)

   - static 访问修饰符 数据类型 变量名;

3. 如何访问类变量

   - 类名.类变量名
   - 对象名.类变量名

4. 类变量使用细节

   - 类变量与实例变量(普通变量)区别

     类变量是该类的所有对象共享的，而实例对象是每个对象独享的

     加上static成为类变量或静态变量，否则称为实例变量/普通变量/静态变量

   - 类变量可以通过 类名.变量名 或者 对象.类变量名 来访问

   - 实例变量不能通过 类名.类变量 来访问

   - 类变量是在类加载时就初始化了，也就是说，即使你没有创建对象，只要类加载了，就可以使用类变量

   - 类变量的生命周期是随类的加载开始，随着类消亡而销毁

5. 类方法基本介绍

   - 访问修饰符 static 数据返回类型 方法名(){}   [推荐]

     static 访问修饰符 数据返回类型 方法名(){}

6. 类方法的调用

   - 类名.类方法名

     对象名.类方法名

7. 类方法使用细节

   - 类方法和普通方法都是随着类的加载而加载，将结构信息储存在方法区：

     类方法中无this的参数

     普通方法中隐含着this的参数

   - 类方法可以通过类名调用，也可以通过对象调用

   - 普通方法和对象有关，需要通过对象调用，不能通过类名调用

   - 类方法中不允许使用和对象有关的关键字，比如this和super，普通方法可以

   - 类方法中只能访问静态变量或静态方法

   - 普通成员方法，既可以访问普通变量（方法），也可以访问静态变量（方法）

## 二，main方法语法

1. 基本介绍
   - main方法是jvm调用的
   - java虚拟机需要调用类的main()方法，所以该方法的访问权限必须是public
   - java虚拟机在执行main()方法时不必创建对象所以该方法必须是static
   - 该方法接受String类型的数组参数，该数组中保存执行java命令时传递给所运行的类的参数
   - java 执行程序 参数1 参数2 参数3
2. 使用细节
   - 在main()方法中，我们可以直接调用main方法所在的静态方法或静态属性
   - 但是不能直接访问该类中的非静态成员，必须创建该类的一个实例对象后，才能通过这个对象去访问类中的非静态成员

3. 代码块

   - 基本介绍

     代码块又称初始化块，属于类中的成员(即，是类的一部分)，类似于方法，将逻辑语句封装在方法体中，通过{}包围起来

     但是和方法不同，没有方法名，没有返回，没有参数，只有方法体，而且不用通过对象或者类显式调用，而是加载类时，或创建对象时隐式调用

   - 基本语法

     [修饰符]{

      代码

     };

     注：1. 修饰符可选，要写的话，也只能写static

     ​        2. 代码块分两类，只用static修饰的叫静态代码块，没有static修饰的，叫普通代码块

     ​        3. 逻辑语句可以为任何语句（输入、输出、方法调用、循环、判断等）

     ​        4. ；可以写上，也可以省略

   - 使用细节

     1. static代码块也叫静态代码块，作用就是对类进行初始化，而它随着==类的加载==而执行，并且==只会执行一次==，如果是普通代码块，每创建一个对象，就执行一次

     2. ==类什么时候被加载==

        创建对象实例时（new）

        创建子类对象实例，父类也会被加载

        使用类的静态成员时（静态属性，静态方法）

     3. 普通代码块，在创建对象实例时，会被隐式的调用。

        被创建一次，就会调用一次

        如果只是使用类的静态成员，普通代码块不会执行

     4. ==创建一个对象时，在一个类调用的顺序是：==

        a. 调用静态代码块和静态属性初始化（注意：静态代码块和静态属性初始化调用优先级一样，如果有多个静态代码块和多个静态变量初始化，则按他们定义的顺序调用）

        b. 调用普通代码块和普通属性初始化（注意：普通代码块和多个普通属性初始化的优先级一样，如果有多个普通代码块和多个普通属性初始化，则按定义顺序调用）

        c. 调用构造器

     5. 构造器的最前面其实隐含了super()和调用普通代码块

     6. 创建一个子类对象时（继承关系），他们的静态代码块，静态属性初始化，普通代码块，普通属性初始化，构造器的调用顺序如下

        a. 父类的静态代码块，静态属性（按定义顺序执行）

        b. 子类的静态代码块，静态属性（按定义顺序执行）

        c. 父类的普通代码块，普通属性（按定义顺序执行）

        d. 父类的构造器

        e. 子类的普通代码块，普通属性（按定义顺序执行）

        f. 子类的构造器

     7. 静态代码块只能直接调用静态成员（静态属性和静态方法），普通代码块可以调用任意成员

 ## 三，final关键字

1. 基本介绍

   - final可以修饰类、属性、方法和局部变量

2. 使用场景

   - 当不希望被继承时，可以用final修饰
   - 当不希望父类的某个方法被子类覆盖/重写时，可以使用final关键字修饰
   - 当不希望类的某个属性的值被修改，可以使用final修饰
   - 当不希望某个局部变量被修饰，可以使用final修饰

3. 使用细节

   - final修饰的属性又叫常量一般用XX_XX_XX来命名

   - final修饰的属性在定义时，必须赋初值，并且不能再修改，赋值可以在如下位置质之一

     定义时

     构造器中

     在代码块中

   - 如果final修饰的属性是静态的，则初始化的位置只能是

     定义时

     在静态代码块，不能在构造器中赋值

   - final类不能继承，但是可以实例化对象

   - 如果类不是final类，但是含有final方法，则该方法虽然不能重写，但是可以被继承

   - 一般来说，如果一个类已经是final类了，就没有必要将方法修饰成final方法

   - final不能修饰构造器

   - final和static往往配合使用，效率更高，不会导致类加载，底层编译器做了优化处理

   - 包装类(Integer,Double,Float,Boolean等都是final)，String也是final类

## 四，抽象类

1. 基本介绍

   - 用abstract关键字来修饰一个类时，这个类就叫抽象类

     访问修饰符 abstract 类名{

     }

   - 用abstract关键字来修饰一个方法时，这个方法就是抽象方法

     访问修饰符 abstract 返回类型 方法名(参数列表);//没有方法体

   - 抽象类的价值更多作用是在于设计，是设计者设计好后，让子类继承并实现抽象类

2. 使用细节

   - 抽象类不能被实例化
   - 抽象类不一定要包含abstract方法
   - 一旦包含了abstract方法，则这个类必须声明为abstract
   - abstract只能修饰类和方法，不能修饰属性和其他
   - 抽象类可以有任意成员（抽象类本质还是类）
   - 抽象方法不能有主体
   - 如果一个类继承了抽象类，则它必须实现抽象类的所有抽象方法，除非它自己也声明为抽象类
   - 抽象方法不能使用private、final和static来修饰，因为这些关键字都是和重写相违背的

## 五，接口

1. 接口的基本介绍

   - 接口就是给出一些没有实现的方法，封装到一起，到某个类要使用的时候，再根据具体情况吧这些方法写出来

   - 语法

     interface 接口名{

     ​        //属性

     ​        //方法（1.抽象方法2.默认实现方法，需要用default关键字修饰3.静态方法）

     }

     class 类名 implements 接口{

     ​             自己的属性；

     ​             自己的方法；

     ​             必须实现的接口的抽象方法

     }

2. 使用细节

   - 接口不能被实例化
   - 接口中所有的方法是public方法，接口中抽象方法，可以不用abstract修饰
   - 一个普通类实现接口，就必须将接口的所有方法都实现
   - 抽象类实现接口，可以不用实现接口的方法
   - 一个类同时可以实现多个接口
   - 接口中的属性只能是final的，而且是public static final 修饰符
   - 接口中属性的访问形式：接口名.属性名
   - 一个接口不能继承其他类，但是可以继承多个别类的接口
   - 接口的修饰符只能是public和默认，这点和类的修饰符是一样的
   
3. 实现接口VS继承类

   - 接口和继承解决的问题不同

     继承的价值主要在于：解决代码的复用性和维护性

     接口的价值主要在于：设计，设计好的各种规范（方法），让其它类去实现这些方法

   - 接口比继承更加灵活，继承是满足is-a的关系，而接口只要满足like-a的关系

   - 接口在一定程度上实现代码解耦（即 接口规范性+动态绑定）

4. 接口的多态特性

   - 多态参数

     接口引用可以指向实现了接口的类的对象

   - 多态数组

   - 接口存在动态传递现象

 ## 六，内部类

1. 基本介绍

   - 一个类的内部有完整的嵌套了另一个类结构，被嵌套的类成为内部类（inner class），嵌套其他类的类又成为外部类（outer class）。是我们类的第五大成员，内部类的最大特点就是可以直接访问私有属性，并且可以体现类与类之间的包含关系

2. 基本语法

   - class Outer{//外部类

     ​          class Inner{//内部类

     ​       }

     }

3. 内部类的分类

   - 定义在外部类局部位置上（比如方法、代码块内）：

     局部内部类（有类名）

     匿名内部类（没有类名，重点）

   - 定义在外部类的成员位置上：

     成员内部类（没有static修饰）

     静态内部类（有static修饰）

4. 局部内部类的使用

   - 可以直接访问外部类的所有成员
   - 不能添加修饰符，因为它的地位就是一个局部变量。局部变量是不能使用修饰符的，但是可以使用final修饰，因为局部变量也可以使用final
   - 作用域：仅仅在定义它的方法或代码块中
   - 局部内部类可以直接访问外部成员
   - 外部类在方法中，可以创建Inner对象，然后调用方法
   - 外部其他类不能访问内部类
   - 如果外部类和局部内部类的成员重名，默认遵循就原则，如果想访问外部类成员可以使用（外部类名.this.成员）去访问

5. 匿名内部类的使用

   - [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=416&spm_id_from=pageDriver)
   - [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=417&spm_id_from=pageDriver)
   - 匿名内部类即使一个类的定义同时本身也是一个对象，它既有定义类的特征，也有创建对象的特征
   - 可以直接访问外部类的所有成员，包含私有的
   - 不能添加访问修饰符，因为他的地位就是一个局部变量
   - 作用域：仅仅在定义它的方法或代码块中
   - 匿名内部类访问外部类成员直接访问
   - 外部类不能访问匿名内部类
   - 如果外部类和匿名内部类重名时，默认遵循就近原则，如果想访问外部类的成员可以使用（外部类名.this.成员）

6. 成员内部类的使用

   - 可以直接访问外部类所有成员，包含私有的

   - 可以添加任意的访问修饰符，因为它的地位就是一个成员

   - 作用域和外部类的其他成员一样，为整个类体

   - 成员内部类可以直接访问外部类成员

   - 外部类访问成员内部类，先创建对象，再访问

   - 外部其他类，使用成员内部类的两种方式

     Outer.Inner inner = Outer.Inner();（相当于把Inner()当做是Outer的成员）

     在外部类中写一个方法可以返回Inner对象实例

   - 如果外部类和成员内部类重名时，默认遵循就近原则，如果想访问外部类的成员可以使用（外部类名.this.成员）

7. 静态内部类的使用

   - 可以直接访问外部类的所有静态成员，包含私有的，但是不能直接访问非静态成员

   - 可以添加任意访问修饰符，因为他的地位就是一个成员

   - 作用域：同其他成员，为整个类体

   - 静态内部类可以直接访问外部类所有静态成员

   - 外部类访问静态内部类，先创建对象，再访问

   - 外部其他类访问访问静态内部类的两种方式

     Outer.Inner inner = new Outer.Inner();

     在外部类中写一个方法可以返回Inner对象实例

   - 如果外部类和静态内部类重名时，默认遵循就近原则，如果想访问外部类的成员可以使用（外部类名.this.成员）

# 第九章 枚举和注解

## 一，枚举

1. 自定义类实现枚举
   - 构造器私有化
   - 本类内部创建一组对象
   - 对外暴露对象（通过为对象添加public final static修饰符）
   - 可以提供get方法，但是不要提供set方法

2. enum关键字实现枚举

   [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=427)

3. enum关键字实现枚举注意事项

   - 当我们使用enum关键字开发一个枚举类时，默认会继承Enum类，而且是一个final类
   - 传统的public static final Season SPRING = new Season（"春天"， "温暖"）；简化成SPRING("春天"， "温暖")
   - 如果使用无参构造器创建枚举对象，则实参列表和小括号都可以省略
   - 当有多个枚举对象时，使用","间隔，最后一个分号结尾
   - 枚举对象必须放在枚举类的行首
   - 使用enum关键字后，就不能再继承其他类，因为enum会隐式继承Enum
   - 枚举类和普通类一样，可以实现接口

## 二，注解

1. 基本介绍

   - 注解也被称为元数据，用于修饰解释包、类、方法、属性、构造器、局部变量等数据信息

   - 和注释一样，注解不影响程序逻辑，但注解可以被编译或运行，相当于嵌入在代码中的补充信息

   - 使用注解时要在其前面增加@符号，并把该注解当成一个修饰符使用，用于修饰它支持的程序元素

   - 三个基本的Annotation：

     @Override：限定某个方法，是重写㺑方法，该注解只能用于方法

     @Deprecated：用于表示某个程序元素（类、方法等）已过时

     @SuppressWarnings：拟制编译器警告

2. Override

   - @Override表示指定重写父类的方法，如果父类没有该方法，则会报错
   - 如果不写@Override注解，也构成重写
   - @Override只能修饰方法，不能修饰其他包、类、属性等
   - @Target是修饰注解的注解，称为元注解

3. @Deprecated

   - 用于表示某个程序元素（类、方法等）已经过时了
   - 可以修饰方法、包、类、字段、参数等等
   - @Deprecated的作用可以做到新旧版本的兼容和过度

4. @SuppressWarning

# 第十章 异常

## 一，异常的基本概念

1. 基本介绍
   - java语言中，将程序执行中发生的不正常情况称为“异常”
2. 执行过程中所发生的异常可分为两大类
   - Error（错误）：java虚拟机无法解决的严重问题
   - Exception：其他因编程错误或偶然的外在因素导致的一般性问题，可以使用针对性的代码进行处理，Exception分为两大类：运行时异常[程序运行时，发生的异常]和编译时异常[编程时，编译器检查出的异常]

## 二，异常体系图

![](F:\截图\捕获28.PNG)

- 异常分为两大类，运行时异常和编译时异常
- 运行时异常，编译器不要求强制处理的异常，一般是指编程的逻辑错误
- 对于运行时异常可以不做处理，因为这类异常很普通，若全处理可能会对程序的可读性和运行效率产生影响
- 编译时异常，是编译器要求必须处理的异常

## 三，常见的运行时异常

1. NullPointerException空指针异常

   当应用程序试图在需要对象的地方使用null时，抛出该异常

2. ArithmeticException数学运算异常

   当出现异常的运算条件时，抛出此异常

3. ArrayOutBoundsException数组越界异常

   用非法索引访问数组时抛出的异常

4. ClassCastException类型转换异常

   当试图将对象强制转换为不是实例的子类时，抛出该异常

5. NumberFormatException数字格式不正确异常

   当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出异常

## 四，异常处理

1. 基本介绍

   异常处理就是当异常发生时，对异常处理的方式

2. 异常处理的方式

   - try - catch - finally

     程序员在代码中捕获发生的异常，自行处理

   - throws

     将发生的异常抛出，交给调用者（方法）来处理，最顶级的处理者就是JVM

3. try - catch - finally

   ```java
   try{
       //代码块，可能有异常
   }catch(Exception e){
       /*当异常发生时
         系统将异常封装成Exception对象e，传递给catch
         得到异常后，程序员自己处理
         如果没有发生异常catch代码块不执行
       */
   }finally{
       //不管try代码块有没有异常发生，都要执行finally
       //通常将释放资源的代码放在finally
   }
   ```

4. throws

   ![](F:\截图\捕获29.PNG)

5. try - catch方式处理异常注意事项

   - 如果异常发生了，则异常发生后面的代码块不会执行，直接进入到catch块

   - 如果异常没有发生，则顺序执行try代码块，不会进入到catch

   - 如果希望不管是否发生异常，都要执行某段代码，则使用finally{}

   - 可以有多个catch语句，捕获不同的异常（进行不同的业务处理），要求父类异常在后，子类异常在前

     try{

     }catch(NullPointerException e){

     }catch(Exception e){

     }finally{

     }

   - 可以进行try - finally配合使用，相当于没有捕获异常，因此程序会直接崩掉

6. throws方式处理异常注意事项
   - 在方法声明中用throws语句可以声明抛出异常的列表，throws后面的异常类型可以是方法中产生的异常类型，也可以是它父类
   - 对于编程异常，程序中必须处理
   - 对于运行异常，程序中如果没有处理，默认就是throws的方式处理
   - 子类重写父类的方法时，所抛出的异常类型要么和父类抛出的异常一致，要么为父类抛出的异常的类型的子类型
   - 在throws过程中，如果有方法try - catch，就相当于处理异常，就可以不必throws

7. 自定义异常

   - 自定义异常类名继承Exception或RuntimeException，如果继承Exception属于编译异常，如果继承RuntimeException属于运行异常

8. throw和throws的区别

   |        | 意义                     | 位置       | 后面的东西 |
   | ------ | ------------------------ | ---------- | ---------- |
   | throws | 异常处理的一种方式       | 方法声明处 | 异常类型   |
   | throw  | 手动生成异常对象的关键字 | 方法体中   | 异常对象   |

# 第十一章 常用类

## 一，包装类

1. 包装类的分类

   ![](F:\截图\捕获30.PNG)

2. ![](F:\截图\捕获31.PNG)

   ![](F:\截图\捕获32.PNG)

   ![](F:\截图\捕获33.PNG)

3. 包装类和基本数据类型的转换

   - jdk5前的手动装箱和拆箱方式，装箱：基本类型->包装类型，反之拆箱
   - jdk5以后（含jdk5）的自动装箱和拆箱方式
   - 自动装箱底层调用的是valueOf方法，例如Integer.valueOf()
   - ![](F:\截图\捕获34.PNG)

4. 包装类和String的转换

   - 包装类转成String

   - ```java
     //包装类转成String
             Integer i = 1;
             //方式一
             String str1 = i + "";
             //方式二
             String str2 = i.toString();
             //方式三
             String str3 = String.valueOf(i);
     ```

   - String转成包装类

   - ```java
     //String转成包装类
             String str = "123";
             //方式一
             Integer i1 = new Integer(str);//构造器
             //方式二
             Integer i2 = Integer.parseInt(str);//使用到自动装箱
     ```

5. ![](F:\截图\捕获35.PNG)

## 二，String类

1. 基本介绍

   - String对象用于保存字符串，也就是一组字符序列
   - 字符串常量对象是用双引号括起来的字符序列（"你好"， "123"等）
   - 字符串的字符使用Unicode字符编码，一个字符（不区分字母和汉字）占两个字节
   - String类实现了接口Serializable,String可以串行化：可以在网络传输
   - String类实现了接口Comparable，String对象可以比较大小
   - String是final类
   - String有属性private final char value[]；用于存放字符串内容
   - 注意：value是一个final类型，不可修改（地址不可修改，但是单个字符的内容是可以变化的）
   - 构造器![](F:\截图\捕获36.PNG)

2. String对象的创建

   - 直接赋值String s = "wgx";

     先从常量池查看是否有“wgx”数据空间，如果有，直接指向；如果没有则重新创建，然后指向。s最终指向的是常量池的空间地址

   - 调用构造器String s = new String("wgx");

     先在堆中创建空间，里面维护了value属性，指向常量池的wgx空间。如果常量池没有“wgx”，重新创建，如果有，直接通过value指向，最终指向的是堆中的空间地址

3. 字符串的特性

   - String a = "123" + "123";

     编译器会优化，判断创建的常量池对象，是否有引用指向

     String a = "123" + "123"; ->String a = "123123";

   - String a = "123";

     String b = "456";

     String c = a+ b;

     底层是StringBuilder sb = new StringBuilder();

     sb.append(a);sb.append(b);sb是在堆中，并且append是在原来的字符串的基础上追加的

     String c1 = "123" + "456";常量相加，看的是池。String c2 = a + b;变量相加，是在堆中

4. String类的常见方法

   ![](F:\截图\捕获37.PNG)

   ![](F:\截图\捕获38.PNG)

5. StringBuffer类

   - 基本介绍

     1. java.lang.StringBuffer代表可变的字符序列，一颗对字符串内容进行增删

     2. 很多方法与String相同，但StringBuffer是可变长度

     3. StringBuffer是一个容器
     4. StringBuffer的直接父类是AbstractStringBuilder
     5. StringBuffer实现了Serializable，即StringBuffer的对象可以串行化
     6. 在父类中AbstractStringBuffer有属性char[] value，不是final，该value数组存放字符串内容，引出存在在堆中
     7. StringBuffer是一个final类，不能被继承
     8. 因为StringBuffer字符内容是存放在char[] value中，所有变化（增加/删除）不能每次都更换地址（即不是每次都创建新对象）,所以效率高于String

   - String VS StringBuffer

     1. String保存的是字符串常量，里面的值不能更改，每次String类的更新实际上就是更改地址，效率较低//private final char value[];
     2. StringBuffer保存的是字符串常量，里面的值可以更改，每次StringBuffer的更新实际上可以更新内容，不用每次更新地址，效率较高//char[] value;这个放在堆里

   - StringBuffer构造器

     ![](F:\截图\捕获39.PNG)

   - String转换成StringBuffer

     ```java
     String str = "wgx";
     //方式一，使用构造器
     StringBuffer stringbuffer = new StringBuffer(str);//返回的才是StringBuffer对象，对str本身没有影响
     //方式二，使用的是append方法
     StringBuffer stringbuffer = new StringBuffer();//先创建一个StringBuffer对象
     stringbuffer = stringbuffer.append(str);//调用append方法
     ```

   - StringBuffer转成String

     ```java
     StringBuffer stringbuffer = new StringBuffer("wgx");
     //方式一，使用StringBuffer提供的toString方法
     String str = stringbuffer.toString();
     //方式二，使用构造器
     String str = new String(stringbuffer)
     ```

   - StringBuffer的常用方法

     append()追加

     delete()删除  delete(2, 4)->删除[2，4)，左闭右开

     replace()替换  同上，左闭右开

     indexOf()查找

     insert()插入

     length()返回长度

6. StringBuilder

   - 基本介绍

     1. 一个可变的字符序列，此类提供一个与StringBuffer兼容的API，但是不保证同步（StringBuilder不是线程安全）。该类被设计用作StringBuffer的一个简易替换，用在字符串缓冲区被单个线程使用的时候。如果可能，建议优先采用该类，因为大多数现实中，它比StringBuffer要快
     2. 在StringBuilder上的主要操作是append和insert方法，可重载这些方法，以接受任意类型的数据
     3. StringBuilder继承AbstractStringBuilder类
     4. 实现了Serializable，说明StringBuilder对象是可以串行化的
     5. StringBuilder是final类，不能被继承
     6. StringBuilder对象字符序列任然是存放在父类AbstractStringBuilder的char[] value;因此，字符序列是堆中
     7. StringBuilder的方法，没有做互斥处理，即没有synchronize关键字，因此在单线程的情况下使用StringBuilder

   - String，StringBuffer，StringBuilder的比较

     1. StringBuffer和StringBuilder非常类似，均代表可变字符序列，而且方法也一样
     2. String：不可变字符序列，效率低，但是复用率高
     3. StringBuffer：可变字符序列，效率较高（增删），线程安全
     4. StringBuilder：可变字符序列，效率最高，线程不安全

   - String使用注意

     ![](F:\截图\捕获40.PNG)

   - String，StringBuffer，StringBuilder的选择

     1. 如果字符串存在大量的修改操作，一般使用Stringbuffer或者StringBuilder
     2. 如果字符串存在大量的修改操作，并在单线程的情况使用StringBuilder
     3. 如果字符串存在大量的修改操作，并在多线程的情况使用StringBuffer
     4. 如果字符串很少修改，被多个对象引用，使用String，比如配置信息等

## 三，Math类

1. 基本介绍
   - Math类包含用于执行基本数学运算的方法

2. Math类常见方法

   ![](F:\截图\捕获41.PNG)

## 四，Arrays类

1. 基本介绍

   Arrays里面包含了一系列静态的方法，用于管理或操作数组（比如排序和搜索）

2. Arrays常见方法

   - toString返回数组的字符串系列  Arrays.toString(arr);

   - sort排序（自然排序和定制排序）  Integer arr[] = {1, -1, 7, 0, 89};  Arrays.sort(arr);

   - binarySearch通过二分搜索法进行查找，要求必须排好序 int index = Arrays.binarySearch(arr, 3);

     如果不存在则return -(low + 1);  *//-(low + 1);为该数应该存在的下标加一取负*

   - copyOf数组元素的复制  Integer[] newArr = Arrays.copyOf(arr, arr.length);

     如果拷贝的长度大于arr.length就在newArr后面加null

   - fill数组元素的填充  Integer[] num = new Integer[]{9, 3, 2};

     ​                                  Arrays.fill(num, 99);使用99填充num，即替换num中所有元素

   - equals比较两个数组元素内容是否完全一致  boolean equals = Arrays.equals(arr1, arr2);

   - asList将一组值，转换成list

     List<Integer> asList = Arrays.asList(2, 3, 4, 5, 6, 1);

     System.out.println("asList=" + asList);

   - ```java
     //定义一个Integer数组
             Integer[] i = {1, 4, -1, 8, 9};
             //调用sort方法排序
             Arrays.sort(i);
             //调用toString方法输出结果，升序排序[-1, 1, 4, 8, 9]
             System.out.println(Arrays.toString(i));
             //调用sort方法并载compare方法，new Comparator(){}为匿名内部类
             Arrays.sort(i, new Comparator(){
                 //重载compare方法
                 @Override
                 public int compare(Object o1, Object o2) {
                     Integer i1 = (Integer)o1;//向下转型
                     Integer i2 = (Integer)o2;//向下转型
                     return i2 - i1;//查看源码，compare方法的返回值大于0，或小于0，影响排序结果
                 }
             });
             System.out.println(Arrays.toString(i));
     ```

3. 定制排序

   ```java
   public class Arrays02 {
       public static void main(String[] args) {
           int[] arr = {5, -2, 3, 44, 51, 1};
           Bubble.bubble(arr, new Comparator() {
               @Override
               public int compare(Object o1, Object o2) {
                   int i1 = (Integer) o1;//自动拆箱
                   int i2 = (Integer) o2;
                   return i1 - i2;//返回类型影响排序
               }
           });
           System.out.println(Arrays.toString(arr));
       }
   }
   class Bubble{
       public static void bubble(int[] arr, Comparator c){
           for (int i = 0; i < arr.length - 1; i++) {
               for (int j = 0; j < arr.length - 1; j++) {
                   if (c.compare(arr[j], arr[j + 1]) > 0){//i1 - i2 > 0交换
                       int temp = arr[j];
                       arr[j] = arr[j + 1];
                       arr[j + 1] = temp;
                   }
               }
           }
       }
   }
   ```

## 五，System类

1. System常用方法

   - exit退出当前程序

   - arraycopy复制数组元素，比较适合底层调用，一般使用Arrays.copyOf完成复制数组

     int[] src = {1, 2, 3};

     int[] dest = new int[3];

     System.arraycopy(src, 0, dest, 0, 3)

     ==src==：源数组

     ==0==：从源数组那个索引位置开始拷贝

     ==dest==：目标数组

     ==0==：把源数组的数据拷贝到目标数组的按个索引

     ==3==：从源数组拷贝多少数据到目标数组

   - currentTimeMillens返回当前时间距离1970 - 1 - 1的毫秒数

   - gc运行垃圾回收机制

## 六， BigInteger和BigDecimal类

1. BigInteger和BigDecimal常见方法

   - add加

   - subtract减

   - multiply乘

   - divide除

   - 如果出现无限循环使用下面方法，保留分子的精度

     bigDecimal1.divide(bigDecimal2, BigDecimal.ROUND_CEILING);

## 七，日期类

1. 第一代日期类

   - Date：精确到毫秒，代表特定的瞬间

   - SimpleDateFormat：格式和解析日期的类

     SimpleDateFormat格式化和解析日期的具体类。

     它允许进行格式化（日期->文本）、解析（文本->日期）和规范化

     ![](F:\截图\捕获42.PNG)

     ![](F:\截图\捕获43.PNG)

2. 第二代日期类
   - 第二代日期类，主要就是Calendar类
   - Calendar类是一个抽象类，它为特定瞬间与一组如YEAR、MONTH、DAY_OF_MONTH、HOUR等日历字段之间的转换提供了一些方法，并为操作日历字段提供了一些方法
   - ![](F:\截图\捕获44.PNG)
   - 24小时制小时c.get(Calendar.HOUR_OF_DAY)

3. 第三代日期

   - LocalDate（日期/年月日）、LocalTime（时间/时分秒）、LocalDateTime（日期时间）

   - ```java
     //使用now()返回当前日期时间的对象
             LocalDateTime ldt = LocalDateTime.now();//LocalDate.now();//LocalTime.now();
             System.out.println(ldt);
             System.out.println("年=" + ldt.getYear());
             System.out.println("月=" + ldt.getMonth());
             System.out.println("月=" + ldt.getMonthValue());
             System.out.println("日=" + ldt.getDayOfMonth());
             System.out.println("时=" + ldt.getHour());
             System.out.println("分=" + ldt.getMinute());
             System.out.println("秒=" + ldt.getSecond());
             //创建DateTImeFoematter对象,创建格式
             DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss:ss E");
             //将ldt传进去，返回给format
             String format = dateTimeFormatter.format(ldt);
             System.out.println(format);
     ```

   - Instant时间戳

     类似于Date，提供了一系列和Date类转换的方式

     Instant->Date:Date date = Date.from(instant);

     Date->Instant:Instant instant = date.toInstant(); 

   - ![](F:\截图\捕获45.PNG)

# 第十二章 集合

## 一，集合

1. 基本介绍

   - 可以动态保存任意多个对象，使用比较方便
   - 提供了一系列方便的操作对象的方法：add、remove、set、get等
   - 使用集合添加，删除新元素

2. 集合框架体系

   - 集合主要是两组（单列集合，双列集合）

     Collection接口有两个重要的子接口List、Set，他们的实现子类都是单列集合

     Map接口的实现子类是双列集合，存放K-V

   - ![](F:\截图\捕获46.PNG)
   - ![](F:\截图\捕获47.PNG)

3. Collection接口常用方法public interface Collection<E> extendslterable<E>

   - Collection实现子类可以存放多个元素，每个元素可以是Object
   - 有些Collection的实现类，可以存放重复的元素，有些不可以
   - 有些Collection的实现类，是有序的（List）,有些不是有序的（Set）
   - Collection接口没有直接的实现子类，是通过它的子类接口Set和List来实现的
   - ![](F:\截图\捕获48.PNG)

4. Collection接口遍历元素方式一——使用Iterator（迭代器）

   - Iterator对象称为迭代器，主要用于遍历Collection集合中的元素

   - 所有实现了Collection接口的集合类都有一个iterator()方法，用于返回一个实现了Iterator接口的对象，即可以返回一个迭代器

   - Iterator仅用于遍历集合，Iterator本身不存放对象

   - ```java
     Collection collection = new ArrayList();//创建一个集合
             //添加元素到集合
             collection.add(new Book("三国演义", "罗贯中", 20.0));
             collection.add(new Book("红楼梦", "曹雪芹", 25.5));
             collection.add(new Book("三体", "刘世慈", 15.0));
             Iterator iterator = collection.iterator();//创建collection的Iterator迭代器
             //使用while循环遍历
             while (iterator.hasNext()) {//使用hasNext()判断是否还有元素
                 System.out.println(iterator.next());//使用next()返回下一个元素
             }
     ```

5. Collection接口遍历对象方式二——for循环增强

   - 增强for循环，可以代替Iterator迭代器，增强for循环是简化版Iterator，本质一样，只能用于遍历集合或数组

   - 基本语法

     for(元素类型 元素名:集合名或数组名){

     ​		访问元素

     }

   - ```java
     Collection collection = new ArrayList();//创建一个集合
             //添加元素到集合
             collection.add(new Book("三国演义", "罗贯中", 20.0));
             collection.add(new Book("红楼梦", "曹雪芹", 25.5));
             collection.add(new Book("三体", "刘世慈", 15.0));
             for(Object book : collection){//使用增强for遍历集合
                 System.out.println(book);
             }
     ```

6. List接口方法

   - 基本介绍

     List接口是Collection接口的子接口

     List集合类中元素有序（即添加顺序和取出顺序一致）、且可重复

     List集合中的每个元素都有其对应的顺序索引，即支持索引

     List容器中元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素

   - 常用方法

     ![](F:\截图\捕获49.PNG)

   - 三种遍历方式

     迭代器、增强for循环、普通for循环

7. ArrayList使用细节

   - ArrayList可以加入null，并且多个

   - ArrayList是由数组来实现数据存储的

   - ArrayList基本等同于Vector，除了ArrayList是线程不安全的（执行效率高）

8. ==ArrayList底层结构和源码分析==

   - ArrayList中维护了一个Object类型的数组elementDate

     transient Object[] elementDate;//transient 表示瞬间，短暂的，表示该属性不会被序列化

   - 当创建ArrayList对象时，如果使用无参构造器，则初始elementDate容量为0，第一次添加，则扩容elementDate为10，如需要再次扩容，则扩容elementDate为1.5倍

   - 如果使用的是指定大小的构造器，则初始elementDate容量指定大小，如果需要扩容，则直接扩容elementDate为1.5倍

   - [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=512)

9. Vector的基本介绍

   - Vector类的定义说明

     public class Vector<E> extends AbstractList<E> 

     implements List<E>,RandomAccess,Cloneable,Serializable

   - Vector底层也是一个对象数组，protected Object[] elementDate;

   - Vector是线程同步的，即线程安全，Vector类的操作方法带有synchronized

   - 在开发中，需要线程安全，考虑选用Vector

10. Vector底层结构和ArrayList的比较

    - ![](F:\截图\捕获50.PNG)

11. LinkedList底层结构

    - LinkedList底层实现了双向链表和双端队列特点
    - 可以添加任意元素（元素可以重复），包括null
    - 线程不安全，没有实现同步

12. LinkedList的底层操作机制

    - LinkedList底层维护了一个双向链表

      [双向链表](https://www.bilibili.com/video/BV1fh411y7R8?p=514)

    - LinkedList中维护了两个属性first和last分别指向首节点和尾结点

    - 每个节点（Node对象），里面又维护了prev、next、item三个属性，其中通过prev指向前一个，通过next指向后一个节点。最终实现双向链表

    - 所有LinkedList的元素的添加和删除，不是通过数组完成的，相对来说效率较高

    - ```java
      LinkedList linkedList = new LinkedList();//创建LinkedList对象
              linkedList.add(1);//添加元素
              linkedList.add(2);//添加元素
              linkedList.add(3);//添加元素
              System.out.println(linkedList);
              linkedList.remove();//默认删除第一个
              //linkedList.remove(1);//删除第二个
              System.out.println(linkedList);
              linkedList.set(1, 99);//修改元素
              System.out.println(linkedList);
              linkedList.get(1);//得到某个元素
              //LinkedList是实现了List接口，可以使用迭代器和增强for循环,也可以使用传统for循环
              //迭代器
              Iterator iterator = linkedList.iterator();
              while (iterator.hasNext()){
                  System.out.println(iterator.next());
              }
              //增强for循环
              for(Object o : linkedList){
                  System.out.println(o);
              }
              //for循环
              for (int i = 0; i < linkedList.size(); i++) {
                  System.out.println(linkedList.get(i));
              }
      ```

13. ArrayList和LinkedList比较

    - ![](F:\截图\捕获51.PNG)

## 二，Set接口

1. Set接口基本介绍
   - 无序（添加和取出顺序不一致），没有索引
   - 不允许重复元素，所以最多包含一个null
2. 常用方法
   - 和List接口一样，Set接口也是Collection的子接口，因此，常用方法和Collection接口一样
3. Set接口的遍历方式
   - 可以使用迭代器
   - 增强for循环
   - 不能使用索引
4. Set使用细节
   - Set接口的实现类的对象（Set接口对象），不能存放重复元素，可以添加一个null
   - Set接口对象存放数据是无序的（即添加的顺序和取出的顺序不一致）
   - 取出顺序的顺序虽然不是添加的顺序，但是他是固定的
   - Set接口对象，不能通过索引获取（不能使用普通for循环）
5. Set接口实现类—HashSet
   - HashSet实现了Set接口
   - HashSet实际上是HashMap
   - 可以存放null值，但是只能有一个null
   - HashSet不保证元素是有序的，取决于hash后，再确定索引的结果
   - 不能有重复的元素/对象
6. ==HashSet底层机制==
   - HashSet底层是HashMap
   - 添加一个元素时，先得到hash值-会转成索引值
   - 找到存储数据表table，看这个索引是否已经存放的有元素
   - 如果没有，直接加入
   - 如果有，调用equals比较，如果相同放弃添加，如果不同则添加到最后
   - 在java8中，如果一条链表的元素个数到TREEIFY_THRESHOLD(默认是8)，并且table的大小大于等于MIN_TREEIFY_CAPACITY(默认64)，就会进行树化(红黑树)
   - [视频链接1](https://www.bilibili.com/video/BV1fh411y7R8?p=522)
   - [视频链接2](https://www.bilibili.com/video/BV1fh411y7R8?p=523)
7. LinkedHashSet基本介绍
   - LinkedHashSet是HashSet的子类
   - LinkedHashSet底层是一个LinkedHashMap，底层维护了一个数组+双向链表
   - LinkedHashSet根据元素的hashCode值来决定元素的存储位置，同时使用链表维护元素的次序，这使得元素看起来是以插入顺序保存的
   - LinkedHashSet不允许添加重复元素

## 三，Map接口

1. Map接口实现类的特点（jdk8）

   - Map与Collection并列存在，用于保存具有映射关系的数据：Key-Value

   - Map中的key和value可以是任何引用类型的数据，会封装到HashMap$Node对象宗

   - Map中的key不允许重复，原因和HashSet一样

   - Map中的value可以重复

   - Map的key可以为null，value也可以为null，注意：key为null，只能有一个，value为null可以多个

   - 常用String类作为Map的key

   - key和value之间存在单向一对一关系，即通过指定的key总能找到对应的value

   - Map存放数据的key-value示意图，一对k-v是放在一个HashMap$Node中的，有因为Node实现了Entry接口

     ![](F:\截图\捕获52.PNG)

     [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=532)

2. Map的常见方法

   - put添加
   - remove根据key删除映射关系
   - get根据key获取值
   - size获取元素个数
   - ifEmpty判断个数是否为0
   - clear清空k-v
   - containsKey查找key是否存在

3. Map遍历方法

   ```java
   //Map的遍历方法
   Map map = new HashMap();
   map.put("wgx", "22");
   map.put("WGX", "22");
   map.put("Wgx", "22");
   //第一组:先取出所有的key，通过key,取出对应的value
   Set keySet = map.keySet();
   //增强for循环
   for (Object key : keySet) {
       System.out.println(key + "-" + map.get(key));
   }
   //迭代器
   Iterator iterator = keySet.iterator();
   while (iterator.hasNext()){
       Object key = iterator.next();
       System.out.println(key + "-" + map.get(key));
   }
   //第二组:把所有的value取出
   Collection values = map.values();
   //增强for循环
   for (Object value : values) {
       System.out.println(value);
   }
   //迭代器
   Iterator iterator1 = values.iterator();
   while (iterator1.hasNext()){
       System.out.println(iterator1.next());
   }
   //第三组:通过EntrySet获取k-v
   Set entrySet = map.entrySet();//EntrySet<Map.Entry<k,v>>
   //增强for循环
   for (Object entry : entrySet) {
       Map.Entry m = (Map.Entry)entry;
       System.out.println(m.getKey() + "-" + m.getValue());
   }
   //迭代器
   Iterator iterator2 = entrySet.iterator();
   while (iterator2.hasNext()) {
       Map.Entry m = (Map.Entry)iterator2.next();
       System.out.println(m.getKey() + "-" + m.getValue());
   }
   ```

4. HashMap基本介绍

   - Map接口的常见实现类：HashMap、Hashtable和Properties
   - HashMap是Map接口使用频率最高的实现类
   - HashMap是以key-val对的方式来存储数据
   - key不可以重复，但是值可以重复，允许使用null键和null值
   - 如果添加相同的key，则会覆盖原来的key-val，等同于修改（key不会替换，val会替换）
   - 与HashSet一样，不保证映射的顺序，因为底层是以hash表的方式来存储的（jdk8的HashMap底层 数组+链表+红黑树）
   - HashMap没有实现同步，因此是线程不安全的

5. ==HashMap底层机制及源码分析==

   - （k-v）是一个Node实现了Map.Entry<k,v>

   - 扩容机制

     ![](F:\截图\捕获53.PNG)

6. Hashtable基本介绍
   - 存放的元素是键值对：即k-v
   - hashtable的键和值都不能为null
   - hashTable使用方法基本上和HashMap一样
   - HashTable是线程安全的，HashMap是线程不安全的

7. Hashtable底层结构

   - 底层有数组Hashtable$Entry[] 初始化大小为11
   - 临界值threshold为11 * 0.75 = 8
   - 扩容机制大于等于临界值时，按2倍加一扩容

8. Hashtable和HashMap对比

   |           | 版本 | 线程安全(同步) | 效率 | 允许null键null值 |
   | --------- | ---- | -------------- | ---- | ---------------- |
   | HashMap   | 1.2  | 不安全         | 高   | 允许             |
   | Hashtable | 1.0  | 安全           | 较低 | 不允许           |

9. Properties基本介绍

   - Properties类继承自Hashtable类且实现了Map接口，也是使用一种键值对的形式来保存数据
   - Properties的使用特点和Hashtable类似
   - Properties还可以用于从xxx.properties文件中，加载数据到Properties类对象，并进行读取和修改
   - xxx.properties文件通常作为配置文件

10. ==开发中任何选择集合实现类==

    ![](F:\截图\捕获54.PNG)

11. TreeSet

    [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=544)

12. TreeMap

    [视频链接](https://www.bilibili.com/video/BV1fh411y7R8?p=545)

## 四，Collection工具类

1. Collections工具类介绍
   - Collection是一个操作Set、List和Map等集合的工具类
   - Collection中提供了一系列静态的方法对集合元素进行排序、查询和修改等操作
2. 排序操作（均为static方法）
   - reverse（List）：反转List中的元素的顺序
   - shuffle（List）：对List集合元素进行随机排序
   - sort（List）：根据元素的自然顺序对指定List集合元素按升序排序
   - sort（List, Comparator）：根据Comparator产生的顺序对List集合元素进行排序
   - swap（List，int，int）：将指定list集合中的i处元素进行交换
   - Object max(Collection)：根据元素的自然顺序，返回给定集合中的最大元素
   - Object max(Collection，Comparator)：根据Comparator指定的顺序，返回给定集合中的最大元素
   - Object min(Collection)：略
   - Object min(Collection)：略
   - int frequency（Collection，Object）：返回给定集合中指定元素的出现次数
   - copy（List dest, List src）：将src中的内容复制到dest中，需要先给dest赋值，大小和src.size()一样
   - boolean replaceAll(List list, Object oldVal, Object newVal):使用新值替换List对象的所有旧值

# 第十三章 泛型

## 一，泛型

- 泛型又称参数化类型，是jdk5.0出现的新特性，解决数据类型的安全性问题
- 在类声明或实例化时只要指定好需要的具体的类型即可
- java泛型可以保证如果程序在编译时没有发出警告，运行时就不会产生ClassCastException异常
- 泛型的作用是：可以在类声明时通过一个标识表示类中某个属性的类型，或者是某个方法的返回值的类型，或者是参数类型

## 二，泛型的语法

- 泛型的声明

  interface 接口<T>{} 和class 类<K,V>{}

  其中T，K，V不代表值，而是表示类型，任意字母都行

- 泛型的实例化

  在类名后面指定类型参数的值（类型），如

  List<String> list = new ArrayList<String>();

  Iterator<Customer> iterator = customers.iterator();

## 三，泛型的使用细节

- interface List<T>{}, public class HashSet<E>{};

  T，E只能是引用类型

- 在指定泛型具体类型后，可以传入该类型或者其子类型

- 泛型使用形式

  List<String> list = new ArrayList<String>();

  List<String> list = new ArrayLIst<>();//*推荐*

- 默认给定的泛型是Object

## 四，自定义泛型

- 基本语法

  class 类名<T, R...>{//...表示可以有多个泛型

    成员

  }

- 注意细节

  1. 普通成员可以使用泛型
  2. 使用泛型的数组，不能初始化
  3. 静态方法不能使用泛型
  4. 泛型类的类型，是在创建对象时确定的（因为创建对象时，需要指定确定类型）
  5. 如果在创建对象时，没有指定类型默认为Object

- 自定义泛型接口

  interface 接口名<T, R>{

  }

- 注意细节

  1. 接口中，静态成员也不能使用泛型
  2. 泛型接口的类型，在继承接口或者实现接口时确定
  3. 没有指定类型，默认为Object

- 自定义泛型方法

  修饰符 <T, R> 返回类型 方法名(参数列表){

  }

- 注意细节
  1. 泛型方法，可以定义在普通类中，也可以定义在泛型类中
  2. 当泛型方法被调用时，类型会确定
  3. public void eat(E e){}，修饰符后会没有<T, R...>eat方法不是泛型方法，而是使用了泛型

## 五，泛型的继承和通配符

- 泛型不具有继承性

  ~~List<Object> list = new ArrayList<String>();~~

- 通配符

  1. <?>:支持任意泛型类型
  2. <? extends A>:支持A类以及A类的子类，规定了泛型的上限
  3. <? super A>:支持A类以及A类的父类，不限于直接父类，规定了泛型的下限
  

# 第十四章 线程

## 一，线程相关概念

1. 进程
   - 进程是指运行中的程序，
   - 进程是程序一次执行过程，或是正在运行的一个程序，是动态过程：有它自身的产生、存在和消亡的过程
2. 线程
   - 线程是由进程创建的，是一个进程的一个实体
   - 一个进程可以拥有多个线程
3. 单线程
   - 同一个时刻，只允许执行一个线程
4. 多线程
   - 同一个时刻，可以执行多个线程
5. 并发
   - 同一个时刻，多个任务交替执行，造成一个“貌似同时”的错觉，简单来说，单核CPU实现的多任务就是并发
6. 并行
   - 同一个时刻，多个任务同时执行，多核CPU可以实现并行

## 二，线程的基本使用

1. 继承Thread实现线程

   - ![](F:\截图\捕获57.PNG)

   - ```java
     public class Threaduse01 {
         public static void main(String[] args) {
             Cat cat = new Cat();
             cat.start();
         }
     }
     class Cat extends Thread {//继承Thread类
         int i = 0;
         @Override
         public void run () {//重写run方法
             while(true) {
                 System.out.println("喵喵，我是一只小猫咪" + ++i);
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
                 if (i == 10){
                     break;
                 }
             }
         }
     }
     ```

2. 实现Runable接口

   - ```java
     public class Threaduse02 {
         public static void main(String[] args) {
             Dog dog = new Dog();
             Thread thread = new Thread(dog);//这里底层使用了设计模式
             thread.start();
         }
     }
     class Dog implements Runnable{
         int i;
         @Override
         public void run() {
             while (true) {
                 System.out.println("汪汪" + ++i);
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
                 if (i == 10){
                     break;
                 }
             }
         }
     }
     ```

3. 多线程使用

   - ```java
     public class Thread03 {
         public static void main(String[] args) {
             A a = new A();
             B b = new B();
             Thread thread1 = new Thread(a);
             Thread thread2 = new Thread(b);
             thread1.start();
             thread2.start();
         }
     }
     class A implements Runnable{
         int i;
         @Override
         public void run() {
             while (true) {
                 System.out.println("hi" + ++i);
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
                 if (i == 100){
                     break;
                 }
             }
         }
     }
     class B implements Runnable{
         int i;
         @Override
         public void run() {
             while(true){
                 System.out.println("hello" + ++i);
                 try {
                     Thread.sleep(1000);
                 } catch (InterruptedException e) {
                     e.printStackTrace();
                 }
                 if(i == 50){
                     break;
                 }
             }
         }
     }
     ```

4. 继承Thread和实现Runnable的区别

   ![](F:\截图\捕获58.PNG)

## 四，线程的终止

1. 基本介绍
   - 当线程完成任务后，会自动退出
   - 还可以通过使用变量来控制run方法退出的方式停止线程，即通知方式

## 五，线程常用方法

- ![](F:\截图\捕获59.PNG)

- 注意事项和细节

  1. start底层会创建新的线程，调用run，run就是一个简单的方法调用，不会启动新线程

  2. 线程优先级的范围

     <img src="F:\截图\捕获60.PNG" style="zoom:80%;" />

  3. interrupt,中断线程，但是没有真正的结束线程。所以一般用于中断正在休眠线程

  4. sleep线程的静态方法
  
  5. yield：线程的礼让，让出CPU，让其他线程执行，但礼让时间不确定，所以也不一定礼让成功
  
  6. join：线程的插队，插队的线程一旦插队成功，则肯定先执行完插入的线程所有任务

## 六，守护线程

1. 用户线程和守护线程
   - 用户线程：也叫工作线程，当线程的任务执行完成或通知方式结束
   - 守护线程：一般是为工作线程服务的，当所有的用户线程结束，守护线程自动结束
   - 常见的守护线程：垃圾回收机制

## 七，线程的七大状态

![](F:\截图\捕获61.PNG)

## 八，线程同步机制

1. 在多线程编程，一些敏感数据不允许被多个线程同时访问，此时就使用同步访问技术，保证数据在任何同一时刻，最多有一个线程访问，以保证数据的完整性

2. 线程同步，即当有一个线程对内存进行操作时，其他线程都不可以对这个内存地址进行操作，直到该线程完成操作，其他线程才能对该内存地址进行操作

3. 同步具体方法

   - 同步代码块

     synchronize (对象) {//得到对象的锁，才能操作同步代码

     ​      //需要被同步的代码；

     }

   - synchronize还可以放在方法声明中，表示整个方法为同步方法

     public synchronize void m (String name){

     ​       //需要被同步的代码

     }

4. 互斥锁

   - java语言中，引入了对象互斥锁的概念没来保证共享数据操作的完整性
   - 每个对象都对应于一个可称为“互斥锁”的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象
   - 关键字synchronized来与对象的互斥锁联系，当某个对象用synchronized修饰时，表明该对象再任一时刻只能由一个线程访问
   - 同步的局限性:导致程序的执行效率要降低
   - 同步方法（非静态的）的锁可以是this，也可以是其他对象（要求是同一个对象）
   - 同步方法（静态的）的锁为当前类本身

5. 线程死锁

   ```java
   public class DeadLock {
       public static void main(String[] args) {
           T1 A = new T1(true);
           T1 B = new T1(false);
           A.start();
           B.start();
       }
   }
   class T1 extends Thread{
       static Object o1 =  new Object();
       static Object o2 =  new Object();
       boolean flag;
       public T1(boolean flag){
           this.flag = flag;
       }
       @Override
       public void run() {
               if (flag){
                   synchronized (o1) {
                       System.out.println(Thread.currentThread().getName() + "进入1");
                       try {
                           Thread.sleep(1000);
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                       synchronized (o2) {
                           System.out.println(Thread.currentThread().getName() + "进入2");
                       }
                   }
               }else {
                   synchronized (o2) {
                       System.out.println(Thread.currentThread().getName() + "进入3");
                       try {
                           Thread.sleep(1000);
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                       synchronized (o1) {
                           System.out.println(Thread.currentThread().getName() + "进入4");
                       }
                   }
               }
           }
       }
   ```

6. 释放锁

   - 当前线程的同步方法、同步代码块执行结束，释放锁
   - 当前线程在同步代码块、同步方法中遇到break、return，释放锁
   - 当前线程在同步代码块同步方法中出现了未处理的Error或Exception，导致异常结束，释放锁
   - 当前线程在同步代码块、同步方法中执行了线程对象的wait()方法，当前线程暂停，并释放锁

   ---

   - 线程实现同步代码块或同步方法时，程序调用Tread.sleep()、Thread.yield()方法暂停当前线程的执行，不会释放锁
   - 线程执行同步代码快时，其他线程调用了该线程的suspend()方法将该线程挂起，该线程不会释放锁

# 第十五章 坦克大战

## 一，java绘图技术

1. 绘图原理

   Component类提供了两个和绘图相关的最重要的方法

   - paint(Graphics g)绘制组件的外观
   - repaint()刷新组件的外观

2. 当组件第一次在屏幕上显示的时候，程序会自动调用paint()方法来绘制组件

3. 在以下情况paint()将会被调用

   - 窗口最小化，再最大化
   - 窗口的大小发生变化
   - repaint方法被调用

4. Graphic类常用方法

   ![](F:\截图\捕获55.PNG)

## 二，java事件处理机制

- java事件处理是采取“委派事件模型”，当事件发生时，产生事件的对象，会把此“信息”传递给“事件监听者”处理，这里说的“信息”实际上就是java.awt.event事件类库里某个类所创建的对象，把它称为“事件的对象”

- 事件源

  事件源就是一个产生事件的对象，比如按钮，窗口等

- 事件

  事件就是承载事件源状态改变时的对象，比如当键盘事件、鼠标事件、窗口事件等等，会生成一个事件对象，该对象保存着当前事件很多信息。

- 事件类型

  ![](F:\截图\捕获56.PNG)

- 事件监听器
  1. 当事件源产生一个事件，可以传给事件监听者处理
  2. 事件监听者实际上就是一个类，该类实现了某个事件监听接口
  3. 事件监听器接口有很多种，不同的时间监听器接口可以监听不同的事件，一个类可以实现多个监听接口
  4. 这些接口在java.awt.event包和javax.swing.event包中

# 第十六章 IO流

## 一，文件

1. 什么是文件
   - 文件就是保存数据的地方
2. 文件流
   - ![](F:\截图\捕获62.PNG)

3. 常见的文件操作
   - ![](F:\截图\捕获63.PNG)
   - ![](F:\截图\捕获64.PNG)

4. 目录操作
   - mkdir创建一级目录、mkdirs创建多级目录、delete删除空目录或文件

## 二，IO流原理及流的分类

1. java IO流的原理

   - I/O是Input/OutPut的缩写，I/O技术是非常实用的技术，用于处理数据传输

   - java程序中，对于数据的输入/输出操作以“流(stream)”的方式进行
   - java.io包下面提供了各种“流”类和接口，用以获取不同种类的数据，并通过方法输入或输出数据
   - 输入input：读取外部数据（磁盘、光盘等储存设备的数据）到程序（内存）中
   - 输出output：将程序（内存）数据输出到磁盘。光盘等存储设备中

2. 流的分类

   - ![](F:\截图\捕获65.PNG)

## 三，字节流

1. InputStream

   ![](F:\截图\捕获66.PNG)

2. FileInputStream

   - 基本介绍

     ![](F:\截图\捕获67.PNG)

     ![](F:\截图\捕获68.PNG)

   - ```java
     //单个字节读取
         @Test
         public void readFile01() throws IOException {
             String filePath = "f:\\hello.txt";
             int read = 0;
             FileInputStream fileInputStream = null;
             try {
                 //创建FileInputStream对象，用于读取文件
                 fileInputStream = new FileInputStream(filePath);
                 //从该输入流读取一个字节数据，如果没有输入可用此方法将阻止
                 //如果返回-1，表示读取完毕
                 while ((read = fileInputStream.read()) != -1){
                     System.out.print((char)read);
                 }
             } catch (IOException e) {
                 e.printStackTrace();
             }finally {
                 //关闭文件，释放资源
                 fileInputStream.close();
             }
         }
     ```

   - ```java
     //使用public int read(byte[] b)读取
         @Test
         public void readFile02() throws IOException {
             String filePath = "f:\\hello.txt";
             int read = 0;
             byte[] buf = new byte[8];//一个读取8个字节
             FileInputStream fileInputStream = null;
             try {
                 //创建FileInputStream对象，用于读取文件
                 fileInputStream = new FileInputStream(filePath);
                 //从该输入流读取最多b.length字节的数据到字节数组。 此方法将阻塞，直到某些输入可用。
                 //如果返回-1，表示读取完毕
                 //如果读取正常，返回实际读取的字节数
                 while ((read = fileInputStream.read(buf)) != -1){
                     System.out.println(new String(buf, 0, read));//显示
                 }
             } catch (IOException e) {
                 e.printStackTrace();
             }finally {
                 //关闭文件，释放资源
                 fileInputStream.close();
             }
         }
     ```

3. FileOutputStream

   - ![](F:\截图\捕获69.PNG)

   - ![](F:\截图\捕获70.PNG)

   - ![](F:\截图\捕获71.PNG)

   - ```java
     @Test
     public void writeFile(){
         //创建FileOutputStream对象
         FileOutputStream fileOutputStream = null;
         String filePath = "f:\\a.txt";
         try {
             //new FileOutputStream(filePath);是写入内容会覆盖原来内容
             //new FileOutputStream(filePath, true);是写入内容追加到文件后面
             fileOutputStream = new FileOutputStream(filePath,true);
             //写入一个字节
             fileOutputStream.write('w');
             //写入一个字符串
             String str = "\nhello,world\n";
             fileOutputStream.write(str.getBytes());
             //	write(byte[] b, int off, int len)
             //将 len字节从位于偏移量 off的指定字节数组写入此文件输出流。
             fileOutputStream.write(str.getBytes(), 7, 5);
         } catch (IOException e) {
             e.printStackTrace();
         }finally{
             try {
                 fileOutputStream.close();
             } catch (IOException e) {
                 e.printStackTrace();
             }
         }
     }
     ```

4. 文件拷贝

   - ```java
     public void fileCopy(){
         //将hello拷贝到e:\\
         String srcFilePath = "f:\\111.jpg";
         String destFilePath = "e:\\111.jpg";
         FileInputStream fileInputStream = null;
         FileOutputStream fileOutputStream = null;
         //定义字节数组，提高效率
         byte[] buf = new byte[1024];
         int readLen = 0;
         try {
             fileInputStream = new FileInputStream(srcFilePath);
             fileOutputStream = new FileOutputStream(destFilePath, true);
             //读取
             while ((readLen = fileInputStream.read(buf)) != -1){
                 fileOutputStream.write(buf, 0, readLen);//写入
             }
             System.out.println("拷贝成功");
         } catch (IOException e) {
             e.printStackTrace();
         }finally {
             try {
                 if (fileInputStream != null){
                     fileInputStream.close();
                 }
                 if (fileOutputStream != null){
                     fileOutputStream.close();
                 }
             } catch (IOException e) {
                 e.printStackTrace();
             }
         }
     }
     ```

## 四，字符流

1. FileReader介绍

   - FileReader和FileWriter是字符流，即按照字符来操作io

   - ![](F:\截图\捕获75.PNG)

   - FileReader相关方法

     ![](F:\截图\捕获73.PNG)

     ![](F:\截图\捕获72.PNG)

2. FileWriter介绍

   - ![](F:\截图\捕获74.PNG)

   - FileWriter常用方法

     ![](F:\截图\捕获76.PNG)

3. FileReader

   ```java
   public class FileReader_ {
       public static void main(String[] args) {
           String filePath = "f:\\hello.txt";
           FileReader fileReader = null;
           int date = 0;
           char[] buf = new char[8];
           try {
               fileReader = new FileReader(filePath);
               while ((date = fileReader.read(buf)) != -1){
                   System.out.print(new String(buf, 0, date));
               }
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   fileReader.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
       }
   }
   ```

4. FileWriter

   ```jav
   public class FileWriter_ {
       public static void main(String[] args) {
           String filePath = "f:\\hello.txt";
           FileWriter fileWriter = null;
           char[] chars = {'w', 'g', 'x'};
           String str = "wgx";
           try {
               fileWriter = new FileWriter(filePath, true);
               //写入单个字符
               fileWriter.write('W');
               //写入指定数组
               fileWriter.write(chars);
               //写入指定数组的指定部分
               fileWriter.write(chars, 0, 2);
               //写入指定字符串
               fileWriter.write(str);
               //写入指定字符串的指定部分
               fileWriter.write(str, 0, 2);
           } catch (IOException e) {
               e.printStackTrace();
           }finally {
               try {
                   fileWriter.close();
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
       }
   }
   ```

## 五，节点流和处理流

1. 节点流可以从特定的数据读写数据，如FileReader、FileWriter
2. 处理流（也叫包装流）是“连接”在已存在的流（节点流或处理流）之上，为程序提供更为强大的读写功能，如BufferedReader、BufferedWriter

3. 节点流和处理流的区别和联系
   - 节点流是底层流/低级流，直接跟数据源相接
   - 处理流（包装流）包装节点流，既可以消除不同的节点流的实现差异，也可以提供更方便的方法来完成输入输出
   - 处理流对节点流进行包装，使用了修饰器设计模式，不会直接与数据源相连
4. 处理流的功能主要体现在以下两个方面
   - 性能的提高：主要以增加缓冲的方式来提高输出的效率
   - 操作的便捷：处理流可能提供一系列的便捷的方法来一次输入输出大批量的数据，使用更加灵活方便

## 六，BufferedReader和BufferedWriter

1. BufferedReader

   ```java
   public static void main(String[] args) throws IOException {
           //文件路径
           String filePath = "f:\\hello.txt";
           //创建BufferedReader对象
           BufferedReader bufferedReader = new BufferedReader(new FileReader(filePath));
           //读取
           String line;//按行读取，效率高
           //bufferedReader.readLine()按行读取，当返回null时表示读取完毕
           while((line = bufferedReader.readLine()) != null){
               System.out.println(line);
           }
           //关闭流，只需要关闭BufferedReader,底层会自动关闭节点流
           bufferedReader.close();
           /**public void close() throws IOException {
               synchronized (lock) {
                   if (in == null)
                       return;
                   try {
                       in.close();
                   } finally {
                       in = null;
                       cb = null;
                   }
               }
           }*/
       }
   ```

2. BufferedWriter

   ```java
   public static void main(String[] args) throws IOException {
           String filePath = "f:\\hello.txt";
           //创建BufferedWriter对象
           BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(filePath, true));
           bufferedWriter.write("\nhello");
           bufferedWriter.newLine();//换行
           bufferedWriter.write('W');
           bufferedWriter.newLine();//换行
           bufferedWriter.write("hello,你好", 6, 2);
           //关闭流
           bufferedWriter.close();
       }
   ```

3. 拷贝文件

   ```java
   public static void main(String[] args) throws IOException {
           String srcFilePath = "f:\\hello.txt";
           String destFilePath = "e:\\hello.txt";
           String line;
           BufferedReader bufferedReader = new BufferedReader(new FileReader(srcFilePath));
           BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(destFilePath, true));
           while ((line = bufferedReader.readLine()) != null){
               bufferedWriter.write(line);
           }
           if (bufferedReader != null) {
               bufferedReader.close();
           }
           if (bufferedWriter != null) {
               bufferedWriter.close();
           }
       }
   ```

## 七，BufferedInputStream和BufferedOutputStream

1. BufferedInputStream

   ![](F:\截图\捕获77.PNG)

2. BufferedOutputStream

   ![](F:\截图\捕获78.PNG)

3. 文件拷贝

   ```java
   public static void main(String[] args) throws IOException {
           String srcFilePath = "f:\\111.jpg";
           String destFilePath = "e:\\111.jpg";
           byte[] buf = new byte[1024];
           int read;
           BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream(srcFilePath));
           BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(new FileOutputStream(destFilePath, true));
           while ((read = bufferedInputStream.read(buf)) != -1){
               bufferedOutputStream.write(buf, 0, read);
           }
           if (bufferedInputStream == null){
               bufferedInputStream.close();
           }
           if (bufferedOutputStream == null){
               bufferedOutputStream.close();
           }
       }
   ```

## 八，对象流

1. 序列化和反序列化

   - 序列化就是在保存数据时，保存数据的值和数据类型

   - 反序列化就是恢复数据时，恢复数据的值和数据类型

   - 需要让某个对象支持序列化机制，则必须让其类是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一:

     Serializable //这是一个标记接口，没有方法

     Externalizable //该接口有方法需要实现，因此一般实现上面的Serializable接口

   - ![](F:\截图\捕获79.PNG)

   - ![](F:\截图\捕获80.PNG)

2. ObjectOutputStream

   ```java
   public class ObjectOutputStream_ {
       public static void main(String[] args) throws IOException {
           String filePath = "f:\\date.dat";
           ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(filePath));
           objectOutputStream.write(100);//保存Integer
           objectOutputStream.writeBoolean(true);//保存Boolea
           objectOutputStream.writeChar('A');//保存Character
           objectOutputStream.writeDouble(1.1);//保存Double
           objectOutputStream.writeUTF("吴功勋");//保存String
           //保存一个Dog对象
           objectOutputStream.writeObject(new Dog("大黄", 5));
           objectOutputStream.close();
       }
   }
   class Dog implements Serializable {
       private String name;
       private int age;
   
       public Dog(String name, int age) {
           this.name = name;
           this.age = age;
       }
   }
   ```

3. ObjectInputStream

   ```java
   public class ObjectInputStream_ {
       public static void main(String[] args) throws IOException, ClassNotFoundException {
           String filePath = "f:\\date.dat";
           ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(filePath));
           //读取(反序列化)的顺序需要和保存的数据(序列化)顺序一致
           //否则会出现异常
           System.out.println(objectInputStream.readInt());
           System.out.println(objectInputStream.readBoolean());
           System.out.println(objectInputStream.readChar());
           System.out.println(objectInputStream.readDouble());
           System.out.println(objectInputStream.readUTF());
           Object dog = objectInputStream.readObject();
           dog = (Dog)dog;
           System.out.println(((Dog) dog).getName() + ((Dog) dog).getAge());
           objectInputStream.close();
       }
   ```

4. 注意事项

   - 要求序列化或反序列化对象需要实现Serializable
   - 序列化的类中建议添加SerializableUID为了提高版本兼容性
   - 序列化对象时，默认将里面的所有属性都进行序列化，但除了static或transient修饰的成员
   - 序列化对象时，要求里面属性的类型也需要实现序列化接口
   - 序列化具备可继承性，也就是如果某类已经实现了序列化，则它的所有子类也已经默认实现了序列化

5. 标准输入输出流
   - System.in标准输入，编译类型为InputStream，运行类型为BufferedInputStream
   - System.out标准输出，编译类型为PrintStream，运行类型为PrintStream

## 九，转换流

1. InputStreamReader

   ```java
   public static void main(String[] args) throws IOException {
           String filePath = "f:\\a.txt";
           //把FileInputStream转成inputStreamReader
           //指定编码gbk
           InputStreamReader inputStreamReader = new InputStreamReader(new FileInputStream(filePath), "gbk");
           //把InputStreamReader传入BufferedReader
           BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
           //读取
           String read;
           while ((read = bufferedReader.readLine()) != null){
               System.out.println(read);
           }
           bufferedReader.close();
       }
   ```

   

2. OutputStreamWriter

   ```java
   public static void main(String[] args) throws IOException {
           String filePath = "f:\\hello.txt";
           OutputStreamWriter outputStreamWriter = new OutputStreamWriter(new FileOutputStream(filePath), "utf-8");
           outputStreamWriter.write("hello,你好");
           outputStreamWriter.close();
       }
   ```

## 十，打印流

1. PrintStream

   - ![](F:\截图\捕获81.PNG)

   ```java
   public static void main(String[] args) throws FileNotFoundException {
           PrintStream printStream = System.out;
           //默认情况下PrintStream输出数据的位置是标准输出，即显示器
           //PrintStream printStream = new PrintStream(System.out);
           printStream.println("hello");
           /*
             public void println(String x) {
                     synchronized (this) {
                         print(x);
                         newLine();
                    }
                 }
            */
           printStream.close();
           //修改打印流的输出位置
           System.setOut(new PrintStream("f:\\new1.txt"));
           printStream.println("hello,hello");
           System.out.println("wgx");
           printStream.close();
       }
   ```

2. PrintWriter

   ![](F:\截图\捕获82.PNG)

   ```java
   public static void main(String[] args) throws FileNotFoundException {
           PrintWriter printWriter = new PrintWriter(System.out);
           printWriter.println("hello,hi");
           printWriter.close();
           //修改打印流输出位置
           printWriter = new PrintWriter("f:\\new2.txt");
           printWriter.println("wgx");
           printWriter.close();
       }
   ```

## 十一，properties类

1. 使用传统方法

   ```java
   public static void main(String[] args) throws IOException {
           //读取mysql.properties文件，并得到ip,user和pwd
           BufferedReader bufferedReader = new BufferedReader(new FileReader("src\\mysql.properties"));
           String line;
           while ((line = bufferedReader.readLine()) != null){//循环读取
               String[] split = line.split("=");
               System.out.println(split[0] + "值是:" + split[1]);
           }
           bufferedReader.close();
       }
   ```

2. properties基本介绍

   - 专门用于读取配置文件的集合类

     配置文件格式：键=值

   - 注：键值对不需要有空格，值不需要用引号一起来，默认类型是String

3. Properties的常见方法

   - load：加载配置文件的键值对到Properties对象
   - list：将数据显示到指定设备
   - getProperty(key)：根据键获取值
   - setProperty(key, value)：设置键值对到Properties对象
   - store：将Properties中的键值对储存到配置文件，在IDEA中，保存信息到配置文件，如果含有中文，会储存为Unicode码

   ```java
   public static void main(String[] args) throws IOException {
           //使用Properties类来读取mysql.Properties文件
           //创建Properties对象
           Properties properties = new Properties();
           //加载配置文件
           properties.load(new FileInputStream("src\\mysql.properties"));
           //把k-v显示到屏幕
           properties.list(new PrintStream(System.out));
           //根据key获取值
           System.out.println(properties.getProperty("user"));
           //设置键值对到Properties对象，如果key存在则为修改
           properties.setProperty("charset", "utf-8");
           properties.list(new PrintStream(System.out));
           //将k-v储存到文件中,comments为注释
           properties.store(new FileOutputStream("src\\mysql2.properties"), null);
       }
   ```


# 第十六章 网络编程

## 一，网络相关概念

1. 网络通信
   - 概念：两台设备之间通过网络实现数据传输
   - 网络通信：将数据通过网络从一台设备传输到另一台设备
   - java.net包下提供了乙烯类的类或者接口，供程序员使用，完成网络通信

2. 网络
   - 概念：两台或多台设备通过一定物理设备连接起来构成了网络
   - 局域网：覆盖范围最小，仅仅覆盖一个教室或者一个机房
   - 城域网：覆盖范围较大，可以覆盖一个城市
   - 广域网：覆盖范围最大，可以覆盖全国，甚至全球，万维网就是广域网的代表
3. ip地址
   - 概念：用于唯一标识网络中的每台计算机/主机
   - 查看ip地址：ipconfig
   - ip地址的表示形式：点分十进制 xx.xx.xx.xx
   - 每一个十进制的范围：0-255
   - ip地址的组成=网络+主机地址，比如192.168.67.173
   - IPv6是互联网工程任务组设计的用于替代IPv4的下一代IP协议，其地址数量号称可以为全世界每一粒沙子编上一个地址
   - 由于IPv4最大的问题在于网络地址资源有限，严重制约了互联网的引用和发展IPv6的使用，不仅能解决网络地址资源数量的问题，而且也解决了多种接入设备连入互联网的障碍

4. 域名

   - 好处：为了方便记忆，解决ip的困难
   - 概念：将ip地址映射成域名

5. 端口号

   - 概念：用于标识计算机上某个特定的网络程序

   - 表示形式：以整数形式，范围0-65535

   - 0~1024已经被占用，比如：ssh 22,ftp 21,smtp 25,http 80

   - 常见的网络程序端口号：

     tomcat:8080

     mysql:3306

     oracle:1521

     sqlserver:1433

6. 网络通信协议

   协议(tcp/ip)TCP/IP(Transmission Control Protocol/Internet Protocol)，中文译名为==传输控制协议/因特网互联协议==，又叫网络通讯协议，这个协议是Internet最基本协议，就是由网络层的IP协议和传输层TCP协议组成的

   ![](F:\截图\捕获83.PNG)

   ![](F:\截图\捕获84.PNG)

7. TCP和UDP
   - ![](F:\截图\捕获85.PNG)

8. InetAddress

   ```java
   public static void main(String[] args) throws UnknownHostException {
           //获取本机的InetAddress对象
           InetAddress localHost = InetAddress.getLocalHost();
           System.out.println(localHost);//DESKTOP-1CL7FO0/10.130.96.31
           //根据主机名，获取InetAdderss
           InetAddress host1 = InetAddress.getByName("DESKTOP-1CL7FO0");
           System.out.println(host1);
           //根据域名获取InetAddress对象
           InetAddress host2 = InetAddress.getByName("www.baidu.com");
           System.out.println(host2);//www.baidu.com/14.215.177.39
           //通过InetAddress对象获取对应地址
           String hostAddress = host2.getHostAddress();
           System.out.println(hostAddress);
           //通过InetAddress对象获取主机名/或者域名
           String hostName = host2.getHostName();
           System.out.println(hostName);
       }
   ```

9. Socket
   - 套接字(Socket)开发网络引用程序被广泛采用，以至于称为事实上的标准
   - 通信的两端都要有Socket，是两台机器间通信的端点
   - 网络通信其实就是Socket间的通信
   - Socket允许程序把网络连接当成一个流，数据在两个Socket间通过IO传输
   - 一般主动发起通信的引用程序属于客户端，等待通信请求的为服务器

## 二，TCP网络通信编程

1. 服务器（字节流）

   ```java
   public static void main(String[] args) throws IOException {
       //1.在本机的9999端口监听，等待连接
       //要求在本机没有其他服务在监听9999端口
       ServerSocket serverSocket = new ServerSocket(9999);
       System.out.println("等待连接...");
       //2.当没有客户端连接9999端口时，程序会阻塞，等待连接
       //如果有客户端连接，则会返回Socket对象，程序继续
       Socket socket = serverSocket.accept();
       //这个serverSocket.accept()返回多个server[多并发]
       System.out.println("服务器" + socket.getClass());
       //3.通过socket.getInputStream()读取客户端写入到数据通道的数据，并显示
       InputStream inputStream = socket.getInputStream();
       byte[] buf = new byte[1024];
       int readLen;
       while ((readLen = inputStream.read(buf)) != -1){
           System.out.println(new String(buf, 0, readLen));
       }
       //4.获取socket相关的输出流
       OutputStream outputStream = socket.getOutputStream();
       outputStream.write("hello".getBytes());
       socket.shutdownOutput();//结束标记
       //5.关闭流和socket
       inputStream.close();
       outputStream.close();
       socket.close();
       serverSocket.close();
   }
   ```

2. 客户端（字节流）

   ```java
   public static void main(String[] args) throws IOException {
       //1.连接服务器
       Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
       System.out.println("客户端" + socket.getClass());
       //2.连接上后，生成socket，用过socket.getOutputStream()得到和socket对象关联的输出流对象
       OutputStream outputStream = socket.getOutputStream();
       //3.通过输出流，写入数据到数据通道
       outputStream.write("hello".getBytes());
       socket.shutdownOutput();//输出结束标记
       //4.获取输入流，读取信息
       InputStream inputStream = socket.getInputStream();
       byte[] buf = new byte[1024];
       int readLen;
       while ((readLen = inputStream.read(buf)) != -1){
           System.out.println(new String(buf, 0, readLen));
       }
       //5.关闭流和socket
       outputStream.close();
       inputStream.close();
       socket.close();
   }
   ```

3. 服务器（字符流）

   ```java
   public static void main(String[] args) throws IOException {
       ServerSocket serverSocket = new ServerSocket(9999);
       System.out.println("服务器" + serverSocket.getClass() + "等待连接");
       Socket socket = serverSocket.accept();
       InputStream inputStream = socket.getInputStream();
       InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
       BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
       String line;
       while ((line = bufferedReader.readLine()) != null){
           System.out.println(line);
       }
       OutputStream outputStream = socket.getOutputStream();
       OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream, "utf-8");
       BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
       bufferedWriter.write("hello,client");
       bufferedWriter.flush();
       socket.shutdownOutput();
       bufferedWriter.close();
       bufferedReader.close();
       socket.close();
       serverSocket.close();
   }
   ```

4. 客户端（字符流）

   ```java
   public static void main(String[] args) throws IOException {
       Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
       System.out.println("客户端" + socket.getClass());
       OutputStream outputStream = socket.getOutputStream();
       OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream, "utf-8");
       BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);
       bufferedWriter.write("hello,server");
       bufferedWriter.flush();//使用字符流必须刷新，否则数据不会写入数据通道
       socket.shutdownOutput();
       InputStream inputStream = socket.getInputStream();
       InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
       BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
       String line;
       while ((line = bufferedReader.readLine()) != null){
           System.out.println(line);
       }
       inputStream.close();
       outputStream.close();
       socket.close();
   }
   ```

5. 网络上传文件

   - 服务器

     ```java
     public static void main(String[] args) throws IOException {
         FileOutputStream fileOutputStream = new FileOutputStream("src\\111.jpg");
         ServerSocket serverSocket = new ServerSocket(8888);
         Socket socket = serverSocket.accept();
         InputStream inputStream = socket.getInputStream();
         OutputStream outputStream = socket.getOutputStream();
         byte[] buf = new byte[1024];
         int read;
         while ((read = inputStream.read(buf)) != -1){
             fileOutputStream.write(buf, 0, read);
         }
         outputStream.write("传输完成".getBytes());
         outputStream.flush();
         socket.shutdownOutput();
         outputStream.close();
         inputStream.close();
         fileOutputStream.close();
         socket.close();
         serverSocket.close();
     }
     ```

   - 客户端

     ```java
     public static void main(String[] args) throws IOException {
         FileInputStream fileInputStream = new FileInputStream("f:\\111.jpg");
         Socket socket = new Socket(InetAddress.getLocalHost(), 8888);
         OutputStream outputStream = socket.getOutputStream();
         InputStream inputStream = socket.getInputStream();
         byte[] buf = new byte[1024];
         int read;
         while ((read = fileInputStream.read(buf)) != -1){
             outputStream.write(buf, 0, read);
         }
         outputStream.flush();
         socket.shutdownOutput();
         while ((read = inputStream.read(buf)) != -1){
             System.out.println(new String(buf, 0, read));
         }
         inputStream.close();
         outputStream.close();
         fileInputStream.close();
         socket.close();
     }
     ```

6. netstat指令

   - netstat -an 可以查看当前主机网络情况，包括端口监听情况和网络连接情况
   - netstat -an | more可以分页显示
   - 要求在DOS控制台下执行

## 三，UDP网络编程

1. 基本介绍
   - 类DatagramSocket和DatagramPacket(数据包/数据报)实现了基于UDP协议网络程序
   - UDP数据报通过数据报套接字DatagramSocket发送和接受，系统不保证UDP数据报一定能安全送到目的地，也不能确定什么时候能送达
   - DatagramPacket对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号以及接受端的IP地址和端口号
   - UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接受方的连接
2. 基本流程
   - 核心的两个类/对象 DatagramSocket和DatagramPacket
   - 建立发送端，接受端
   - 建立数据包
   - 调用DatagramSocket的发送，接受方法
   - 关闭DatagramSocket

3. ```java
   //UDP接受端
   public static void main(String[] args) throws IOException {
       //1.创建一个DatagramSocket对象，准备在9999接受数据
       DatagramSocket socket = new DatagramSocket(9999);
       //2.构建一个DatagramPacket对象，准备接受数据
       byte[] buf = new byte[1024 * 64];//UDP最大为64k
       DatagramPacket packet = new DatagramPacket(buf, buf.length);
       //3.调用接受方法,将网络传输的DatagramPacket对象填充到packet对象
       //当有数据包发送到9999端口，就会接受到数据包，若没有数据发送到9999端口，就会阻塞等待
       socket.receive(packet);
       //4.把packet进行拆包，取出数据，并显示
       int length = packet.getLength();//实际接受到的长度
       byte[] data = packet.getData();//接受到的数据
       String s = new String(data, 0, length);
       System.out.println(s);
       data = "hello".getBytes();
       packet = new DatagramPacket(data, data.length, InetAddress.getLocalHost(), 9990);
       socket.send(packet);
       //4.关闭资源
       socket.close();
   }
   ```

4. ```java
   //UDP发送端
   public static void main(String[] args) throws IOException {
       //1.创建DatagramSocket对象，准备发送和接受数据
       DatagramSocket socket = new DatagramSocket(9990);
       //2.将需要发送的数据封装到DatagramPacket对象
       byte[] data = "hello,你好".getBytes();
       //封装的DatagramPacket对象data内容字节数组，data.length,主机IP，端口
       DatagramPacket packet = new DatagramPacket(data, data.length, InetAddress.getLocalHost(), 9999);
       //3.发送
       socket.send(packet);
       byte[] buf = new byte[1024 * 64];//UDP最大为64k
       packet = new DatagramPacket(buf, buf.length);
       socket.receive(packet);
       String s = new String(packet.getData(), 0, packet.getLength());
       System.out.println(s);
       //4.关闭资源
       socket.close();
   }
   ```


# 第十七章 QQ项目

# 第十八章 反射

## 一，反射机制

1. 反射机制允许程序在执行期借助于ReflectionAPI取得任何类的内部信息（比如成员变量，构造器，成员方法等等），并能操作对象的属性及方法。
2. 加载完类之后，在堆中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象包含了类的完整结构信息。通过这个对象得到类的结构

3. 反射原理图

   ![](F:\截图\捕获86.PNG)

4. 反射机制可以完成
   - 在运行时判断任意一个对象所属的类
   - 在运行时构造任意一个类的对象
   - 在运行时得到任意一个类所属的成员变量和方法
   - 在运行时调用任意一个对象的成员变量和方法
   - 生成动态代理

5. 反射调用优化-关闭访问检查
   - Method和Field、Constructor都有setAccessible()方法
   - setAccessible作用是启动和禁用访问安全检查的开关
   - 参数值为true表示反射的对象再使用时取消访问检查，提高反射效率。参数值为false则表示反射对象执行访问检查

## 二，Class类

1. 基本介绍

   - Class也是类，因此继承Object类

     ![](F:\截图\捕获87.PNG)

   - Class类对象不是new出来的，而是系统创建的

   - 对于某个类的Class对象，在内存中只有一份，因此类只加载一次

   - 每个类的实例都会记得自己是有那个Class实例所生成的

   - 通过Class可以完整的得到一个类的完整结构，通过一系列API

   - Class对象是存放在堆中的

   - 类的字节码数据，是放在方法区的，有些地方称为元数据（包括方法代码，变量名，方法名，访问权限等等）

2. Class类的常用方法

   ![](F:\截图\捕获88.PNG)

   ```java
   String classAllPath = "com.wgx.reflection.Cat";
   //获取Cat类对应的Class
   //<?>表示不确定的java类型
   Class<?> cls = Class.forName(classAllPath);
   System.out.println(cls);
   //输出运行类型
   System.out.println(cls.getClass());//java.lang.Class
   //得到包名
   System.out.println(cls.getPackage().getName());
   //得到全类名
   System.out.println(cls.getName());
   //生成对象实例
   Cat cat = (Cat)cls.newInstance();
   System.out.println(cat);
   //通过反射得到属性
   Field name = cls.getField("name");
   System.out.println(name.get(cat));//小猫
   //通过反射给属性设置值
   name.set(cat, "小白");
   System.out.println(name.get(cat));//小白
   //通过反射得到所有属性
   Field[] fields = cls.getFields();
   for (Field field : fields) {
       System.out.println(field.getName());
   }
   ```

3. 获取Class类对象的方式

   - 已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法forName()获取，例：Class cls = Class.forName("java.lang.Cat");

     多用于配置文件，读取全路径，加载类

   - 已知具体的类，通过类的class获取，该方式最为安全可靠，程序性能最高，例：Class cls =         Cat.class;

     多用于参数传递，比如通过反射得到对应构造器对象

   - 已知某个类的实例，调用该实例的getClass()方法获取Class对象，例：Class cls =              对象.getClass();

     通过创建好的对象，获取Class对象

   - 其他方式

     ClassLoader classLoader = 对象.getClass().getClassLoader();

     Class cls = classLoader.loadClass("类的全类名")

   - 基本数据类型(int, char, boolean, float, double, byte, long, short)得到Class类

     Class cls = 基本数据类型.class

   - 基本数据类型的包装类，可以通过.type得到Class类对象

     Class cls = 包装类.TYPE

   - ```java
     //1. Class.forName()
     String classAllPath = "com.wgx.reflection.Cat";
     Class cls1 = Class.forName(classAllPath);
     //2. 类名.class
     Class<Cat> cls2 = Cat.class;
     //3. 对象.getClass()
     Cat cat = new Cat();
     Class cls3 = cat.getClass();
     //4. 通过类加载器[4种]来获取到类的Class对象
     //先得到类加载器car
     ClassLoader classLoader = cat.getClass().getClassLoader();
     //通过类加载器得到Class对象
     Class cls4 = classLoader.loadClass(classAllPath);
     //基本数据类型(int, char, boolean, float, double, byte, long, short)得到Class类
     Class cls5 = int.class;
     //基本数据类型的包装类，可以通过.type得到Class类对象
     Class cls6 = Integer.TYPE;
     ```

4. 那些类型有Class对象

   - 外部类，成员内部类，静态内部类，局部内部类，匿名内部类

   - interface：接口

   - 数组

   - enum：枚举

   - annotation：注解

   - 基本数据类型

   - void

   - ```java
     Class<String> cls1 = String.class;//外部类
             Class<Serializable> cls2 = Serializable.class;//接口
             Class<Integer[]> cls3 = Integer[].class;//数组
             Class<float[][]> cls4 = float[][].class;//二维数组
             Class<Deprecated> cls5 = Deprecated.class;//注解
             Class<Thread.State> cls6 = Thread.State.class;//枚举
             Class<Long> cls7 = long.class;//基本数据类型
             Class<Void> cls8 = void.class;//void数据类型
             Class<Class> cls9 = Class.class;
     ```

5. 类加载

   - 静态加载：编译时加载相关的类，如果没有则报错，依懒性强

   - 动态加载：运行时加载需要的类，如果运行时不用该类，则不报错，降低了依懒性

   - 类加载时机：

     当创建对象时（new）//静态加载

     当子类被调用时，父类也加载//静态加载

     调用类中的静态成员时//静态加载

     通过反射//动态加载

6. 类加载流程

   ![](F:\截图\捕获89.PNG)

   - 加载阶段

     JVM在该阶段的主要目的是将字节码从不同的数据源（可能是class文件、也可能是jar包，甚至网络）转化为二进制字节流加载到内存中，并生成一个代表该类的java.lang.Class对象

   - 连接阶段-验证

     目的是为了确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全

     包括：文件格式验证（是否以魔数oxcafebabe开头）、元数据验证、字节码验证和符号引用验证

     可以考虑使用-Xverify:none参数来关闭大部分的类验证措施，缩短虚拟机类加载的时间

   - 连接阶段-准备

     JVM会在该阶段对静态变量，分配内存并默认初始化（对应数据类型的默认初始值，如0、0L、null、false等）。这些变量所使用的内存都将在方法区中进行分配

   - 连接阶段-解析

     虚拟机将常量池内的符号引用替换为直接引用的过程

   - 初始化

     到初始化阶段，才真正开始执行类中的定义的java程序代码，此阶段是执行<clinit>()方法的过程

     <clinit>()方法是由编译器按语句在源文件中出现的顺序，依次自动收集类中的所有静态变量的赋值动作和静态代码块中的语句，并进行合并

     虚拟机会保证一个类的<clinit>()方法在多线程环境中被正确地加锁、同步，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类<clinit>()方法，其他线程都需要阻塞等待，直到活动线程执行<clinit>()方法完毕

7. 通过反射获取类的结构信息(java.lang.Class类)

   ```java
   public class ReflectionUtils {
       public static void main(String[] args) throws ClassNotFoundException {
           //得到对象
           Class<?> personClass = Class.forName("com.wgx.reflection.Person");
           //获取全类名
           System.out.println(personClass.getName());//com.wgx.reflection.ReflectionUtils
           //得到简单类名
           System.out.println(personClass.getSimpleName());//Person
           //获取所有public修饰的属性，包括本类以及父类的
           Field[] fields = personClass.getFields();
           for (Field field : fields) {
               System.out.println(field.getName());
           }
           //获取本类所有属性
           Field[] declaredFields = personClass.getDeclaredFields();
           for (Field field : declaredFields) {
               System.out.println(field.getName());
           }
           //获取所有public修饰方法，包括本类以及父类的
           Method[] methods = personClass.getMethods();
           for (Method method : methods) {
               System.out.println(method.getName());
           }
           //获得本类所有方法
           Method[] declaredMethods = personClass.getDeclaredMethods();
           for (Method method : declaredMethods) {
               System.out.println(method.getName());
           }
           //获取所有本类public修饰的构造器
           Constructor<?>[] constructors = personClass.getConstructors();
           for (Constructor constructor : constructors) {
               System.out.println(constructor.getName());
           }
           //获取本类所有的构造器
           Constructor<?>[] declaredConstructors = personClass.getDeclaredConstructors();
           for (Constructor constructor : declaredConstructors) {
               System.out.println(constructor.getName());
           }
           //以Package形式返回包信息
           System.out.println(personClass.getPackage());
           //以Class形式返回父类信息
           System.out.println(personClass.getSuperclass());
           //以Class[]形式返回接口信息
           Class<?>[] interfaces = personClass.getInterfaces();
           for (Class anInterface : interfaces) {
               System.out.println(anInterface);
           }
           //以Annotation[]形式返回注解信息
           Annotation[] annotations = personClass.getAnnotations();
           for (Annotation annotation : annotations) {
               System.out.println(annotation);
           }
   
       }
   }
   class A{
       public String hobby;
       public void m0(){}
       public A(){}
   }
   interface IA{}
   interface IB{}
   @Deprecated
   class Person extends A implements IA, IB{
       //属性
       public String name;
       protected int age;
       String job;
       private double sal;
       //方法
       public void m1(){}
       protected void m2(){}
       void m3(){}
       private void m4(){}
       //构造器
       public Person(){}
       private Person(String name){}
   }
   ```

8. 通过反射获取类的结构信息(Field类，Method类，Constructor类)

   ```java
   public class ReflectionUtils2 {
       public static void main(String[] args) throws ClassNotFoundException {
           Class<?> personClass = Class.forName("com.wgx.reflection.Person");
           Field[] declaredFields = personClass.getDeclaredFields();
           for (Field field : declaredFields) {
               //getName:返回属性名
               //getModifiers:以int形式返回修饰符[默认修饰符是0，public是1，private是2，protected是4，static是8，final是16，public+static是1+8=9]
               //getType:以Class形式返回类型
               System.out.println("本类中的所有属性" + field.getName() + ":" + field.getModifiers() + "  类型为" + field.getType());
           }
           Method[] declaredMethods = personClass.getDeclaredMethods();
           for (Method method : declaredMethods) {
               //getReturnType:返回方法的返回类型
               //getParameterTypes:以数组形式返回方法的所有形参类型
               System.out.println("本类中所有方法" + method.getName() + ":" + method.getModifiers() + "  返回类型为" + method.getReturnType());
               Class<?>[] parameterTypes = method.getParameterTypes();
               for (Class<?> parameterType : parameterTypes) {
                   System.out.println("该方法的形参类型为" + parameterType);
               }
           }
           Constructor<?>[] declaredConstructors = personClass.getDeclaredConstructors();
           for (Constructor<?> constructor : declaredConstructors) {
               //getParameterTypes:以数组形式返回方法的所有形参类型
               Class<?>[] parameterTypes = constructor.getParameterTypes();
               for (Class<?> parameterType : parameterTypes) {
                   System.out.println("构造器的形参类型为" + parameterType);
               }
           }
       }
   }
   class A{
       public String hobby;
       public void m0(){}
       public A(){}
   }
   interface IA{}
   interface IB{}
   @Deprecated
   class Person extends A implements IA, IB{
       //属性
       public String name;
       protected final int age = 22;
       static String job;
       private double sal;
       //方法
       public void m1(String name){}
       protected void m2(int age, String job){}
       void m3(){}
       private void m4(){}
       //构造器
       public Person(){}
       private Person(String name){}
   }
   ```

9. 通过反射创建对象

   - 方式一：调用类中的public修饰的无参构造器

   - 方式二：调用类中的指定构造器

   - Class类相关方法

     newInstance：调用类中的无参构造器，获取对应类的对象

     getConstructor(Class...class)：根据参数列表，获取对应的public构造器对象

     getDecalaredConstructor(Class...class)：根据参数列表，获取对应的所有构造器对象

   - Constructor类相关方法

     setAccessible：爆破

     newInstance(Object...obj)：调用构造器

   - ```java
     public class ReflecCreateInstance {
         public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
             //1. 先获取User类的Class对象
             Class<?> userClass = Class.forName("com.wgx.reflection.User");
             //2. 通过public的无参构造器创建实例
             Object o = userClass.newInstance();
             System.out.println(o);
             //3. 通过public的有参构造器创建实例
             Constructor<?> constructor = userClass.getConstructor(String.class);//constructor为有参public构造器
             Object o1 = constructor.newInstance("WGX");
             System.out.println(o1);
             //4. 通过非public的有参构造器创建实例
             Constructor<?> declaredConstructor = userClass.getDeclaredConstructor(String.class, int.class);
             declaredConstructor.setAccessible(true);//爆破，使用反射可以访问private构造器
             Object o2 = declaredConstructor.newInstance("WGX", 10);
             System.out.println(o2);
         }
     }
     class User{
         private String name = "wgx";
         private int age = 22;
     
         public User() {//无参public
         }
     
         public User(String name) {//有参public
             this.name = name;
         }
         private User(String name, int age){//有参private
             this.name = name;
             this.age = age;
         }
     
         @Override
         public String toString() {
             return "User{" +
                     "name='" + name + '\'' +
                     ", age=" + age +
                     '}';
         }
     }
     ```

10. 通过反射访问类中的属性

    - 根据属性名获取Field对象

      Field field = class对象.getDeclaredField(属性名)；

    - 爆破：field.setAccessible(true);

    - 访问

      field.set(o, 值);

      syso(field.get(o));

    - 如果是静态属性，则set和get中的参数o，可以写作null

    - ```java
      public class ReflecAccessProperty {
          public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchFieldException {
              //1. 得到Student类的Class对象
              Class<?> studentClass = Class.forName("com.wgx.reflection.Student");
              //2. 创建对象
              Object o = studentClass.newInstance();//o的运行类型就是Student
              System.out.println(o);
              //3. 使用反射得到age属性
              Field age = studentClass.getField("age");
              age.set(o, 22);//通过反射设置属性
              System.out.println(o);
              //4. 使用反射操作name属性
              Field name = studentClass.getDeclaredField("name");
              name.setAccessible(true);//爆破
              name.set(o, "wgx");
              System.out.println(o);
              name.set(null, "WGX");//因为name是static属性，因此o也可以写null
              System.out.println(o);
          }
      }
      class Student {
          public int age;
          private static String name;
      
          public Student() {
          }
      
          @Override
          public String toString() {
              return "Student{" +
                      "age=" + age +
                      ", name=" + name +
                      '}';
          }
      }
      ```

11. 通过反射访问类中的方法

    - 根据方法名和参数列表获取Method方法对象：Method method = class.getDeclaredMethod(方法名， XX.class);//获取本类所有方法

    - 获取对象：Object o = class.newInstance();

    - 爆破：method.setAccessible(true);

    - 访问：Object returnValue = method.invoke(o, 实参列表)；//o就是对象

    - 注意：如果是静态方法，则invoke的参数o，也可以写成null

    - ```java
      public class ReflecAccessMethod {
          public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException {
              //1. 得到Boss类对应的Class对象
              Class<?> bossClass = Class.forName("com.wgx.reflection.Boss");
              //2. 创建对象
              Object o = bossClass.newInstance();
              //3. 调用普通的hi方法
              Method hi = bossClass.getMethod("hi", String.class);
              hi.invoke(o, "wgx");//调用
              //4. 调用private static方法
              Method say = bossClass.getDeclaredMethod("say", int.class, String.class, char.class);
              say.setAccessible(true);
              System.out.println(say.invoke(o, 22, "wgx", '男'));
              System.out.println(say.invoke(null, 22, "wgx", '男'));//因为say方法时static，o可以写成null
              //5. 在反射中，如果有返回值，统一返回Object
              Object reValue = say.invoke(o, 22, "wgx", '男');
              System.out.println("reValue的返回类型为=" + reValue.getClass());//String
          }
      }
      class Boss {
          public int age;
          private String name;
      
          public Boss() {
          }
          public void hi(String s){//普通方法
              System.out.println("hi " + s);
          }
          private static String say(int n, String s, char c){//静态方法
              return n + " " + s + " " + c;
          }
      }
      ```


# 第十九章 MySQL数据库

## 一，安装和使用

1. 使用命令行窗口连接MySQL数据库
   - mysql -h 主机名 -P 端口 -u 用户名 -p密码
   - ![](F:\截图\捕获90.PNG)

## 二，数据库的三层结构

1. 所谓安装MySQL数据库，就是在主机安装一个数据库管理系统（DBMS，database manage system），这个管理程序可以管理多个数据库

2. 一个数据库中可以创建多个表，以保存数据（信息）

3. 数据库管理系统、数据库和表的关系

   ![](F:\截图\捕获91.PNG)

4. SQL语句分类
   - DDL：数据定义语句[create 表，库...]
   - DML：数据操作语句[增加 insert，修改 update，删除 delete]
   - DQL：数据查询语句[select]
   - DCL：数据控制语句[管理数据库：比如用户权限grant授予 revoke撤销]

## 三，语句操作Mysql

1. 创建数据库

   ![](F:\截图\捕获92.PNG)

   ```sql
   #演示数据库的操作
   #创建一个名称为wgx_db01的数据库
   CREATE DATABASE wgx_db01;
   #删除数据库
   DROP DATABASE wgx_db01;
   #创建一个使用utf8字符集的wgx_db02数据库
   CREATE DATABASE wgx_db02 CHARACTER SET utf8;
   #创建一个使用utf8字符集，并带小队规则的wgx_db03数据库
   CREATE DATABASE wgx_db03 CHARACTER SET utf8 COLLATE utf8_bin
   #校对规则 utf8_bin区分大小写 默认utf8_general_ci不区分大小写
   #查询的sql，select 查询 * 表示所有字段 from 从哪个表
   #where 从哪个字段查询 name = 'wgx' 查询名字是wgx
   SELECT * FROM t1 WHERE NAME = 'wgx'
   ```

   

2. 查看、删除数据库

   ![](F:\截图\捕获93.PNG)

   ```sql
   #演示删除和查询数据库
   #查看当前数据库服务器中的所有数据库
   SHOW DATABASES
   #查看前面创建的wgx_db01数据库的定义信息
   SHOW CREATE DATABASE wgx_db01
   #在创建数据库、表示的时候，为了规避关键字，可以使用反引号
   #删除前面创建的wgx_db01数据库
   DROP DATABASE wgx_db01
   ```

3. 备份和恢复数据库

   ![](F:\截图\捕获94.PNG)

   备份和恢复数据库的表

   mysqldump -u 用户名 -p 数据库 表1 表2 表n > 文件名.sql

   ```sql
   #练习：database03.sql备份wgx_db02和wgx_db03库中的数据，并恢复
   
   #备份，在DOS下执行mysqldump指令
   #这个备份文件就是对应的sql语句
   mysqldump -u root -p -B wgx_db02 wgx_db03 > f:\\bak.sql
   DROP DATABASE wgx_db02
   DROP DATABASE wgx_db03
   #恢复数据库(注意：进入Mysql命令再执行)
   source f:\\bak.sql
   #第二个恢复方法，直接将bak.sql的内容放在查询编辑器中，运行
   
   #备份表
   mysqldump -u root -p wgx_db02 t1 > f:\\bak02.sql
   DROP TABLE t1
   #恢复表
   source f:\\bak02.sql
   ```

4. 创建表

   ![](F:\截图\捕获95.PNG)

   ```sql
   #指令创建表
   CREATE TABLE `user` (
   	id INT,
   	`name` VARCHAR(255),
   	`password` VARCHAR(255),
   	birthday DATE
   )CHARACTER SET utf8 COLLATE utf_bin ENGINE INNODB
   ```

5. Mysql常用的数据类型（列类型）

   ![](F:\截图\捕获96.PNG)

6. 列类型使用细节

   - char(size):固定长度字符串最大255字符

   - varchar(size):0~65535字节

     可变长度字符串，最大65532字节（utf8编码最大21844字符，1-3个字节用于记录大小）

     utf8 varchar(size) size = (65535 - 3) / 3 = 21844

     gbk varchar(size) size = (65535 - 3) / 2 = 32766

   - char(4)//这个4表示字符数（最大255），不是字节数，不管中文还是字母都是放四个，按字符计算

   - varchar(4)//这个4表示字符数，不管是字母还是中文都以定义好的表的编码来存放数据

   - char(4)是定长，即使插入'aa'，也会占用分配的4个字符空间

   - varchar(4)是变长，实际占用空间大小并不是4个字符，而是按照实际占用空间来分配

   - 如果数据是定长，推荐使用char，比如邮编，手机号，身份证号码等

   - 如果一个字段的长度是不确定的，使用varchar

   - 在存放文本时可以使用Text数据类型，可以将TEXT列视为VARCHAR列，注意Text不能有默认值，大小0-2^16^字节，MEDIUMTEXT为0-2^24^，LONGTEXT为0-2^32^

   - 如果希望TIMESTAMP自动更新，需要配置

     TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

7. ==修改表==

   - ![](F:\截图\捕获97.PNG)

   - ```sql
     CREATE TABLE t2 (
     	id INT,
     	`name` VARCHAR(4),
     	sex CHAR(1),
     	brithday DATE,
     	entry_date DATETIME,
     	job VARCHAR(10),
     	salary DOUBLE,
     	`resume` TEXT
     )CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
     INSERT INTO t2 VALUES(1, 'wgx', '男', '1999-11-17', '2021-10-7 16:30:30', 'java', 10000.0, 'java实习');
     #添加一个image列，varchar类型(要求在resume后面)
     ALTER TABLE t2 ADD image VARCHAR(10) NOT NULL DEFAULT '' AFTER `resume`;
     #修改job列，使其长度为20
     ALTER TABLE t2 MODIFY job VARCHAR(20) NOT NULL DEFAULT '';
     #删除sex列
     ALTER TABLE t2 DROP sex;
     #修改表名为employee
     RENAME TABLE t2 TO employee;
     #修改表的字符集为utf8
     ALTER TABLE employee CHARACTER SET utf8;
     #列名name修改为user_name
     ALTER TABLE employee CHANGE `name` uer_name VARCHAR(4) NOT NULL DEFAULT '';
     ```

## 四，数据库CRUD语句

1. Insert语句

   - 使用Insert语句向表中插入数据

   - INSERT INTO table_name [(column [, column...])]

     VALUES                                 (value [, value...]);

   - ```sql
     #练习insert语句
     CREATE TABLE goods (
     	id INT,
     	goods_name VARCHAR(10),
     	price DOUBLE);
     INSERT INTO goods (id, goods_name, price)
     	VALUES (1, 'PS5', 5000);
     INSERT INTO goods (id, goods_name, price)
     	VALUES (2, 'iPhone', 8000);
     SELECT * FROM goods;
     ```

   - 注意事项

     插入的数据应与字段的数据类型相同

     数据的长度应在列的规定范围内

     在value中列出数据位置必须与被加入的列的排列位置相对应

     字符和日期型数据应包含在单引号中

     列可以插入空值（前提是该字段允许为空）

     insert into table_name (列名...)   values (),(),()   添加多条记录

     如果是给表中的所有字段添加数据，可以不写前面的字段名称

     默认值的使用，当不给某个字段值时，如果有默认值就会添加

2. update语句

   - 使用update语句修改表中数据

     UPDATE table_name

     ​				SET col_name1=expr1 [, col_name2=expr2...]

     ​				[WHERE where_definition]

   - ```sql
     #练习update语句
     CREATE TABLE employee (
     	id INT,
     	`name` VARCHAR(4),
     	sex CHAR(1),
     	brithday DATE,
     	entry_date DATETIME,
     	job VARCHAR(10),
     	salary DOUBLE,
     	`resume` TEXT
     )CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
     INSERT INTO employee VALUES(1, 'wgx', '男', '1999-11-17', '2021-10-7 16:30:30', 'java', 10000.0, 'java实习');
     INSERT INTO employee VALUES(2, 'WGX', '男', '1999-11-17', '2021-10-7 16:30:30', 'java', 10000.0, 'java实习');
     #将所有员工薪水修改为5000
     UPDATE employee SET salary = 5000;
     #姓名为wgx的员工薪水修改为3000
     UPDATE employee SET salary = 3000 WHERE `name` = 'wgx';
     #将WGX的薪水在原有的基础上增加1000
     UPDATE employee SET salary = salary + 1000 WHERE `name` = 'WGX';
     ```

   - 使用细节

     UPDATE语句可以用新值更新原有表行中的各列

     SET子句指示要修改哪些列和要给予哪些值

     WHERE子句指定应更新哪些行，如果没有WHERE子句，则更新所有的行

     如果需要修改多个字段可以通过set 字段1=值1，字段2=值2...

3. delete语句

   - 使用delete语句删除表中数据

     DELETE FROM table_name

     ​					[WHERE where_definition]

   - ```sql
     #练习delete语句
     #删除表中WGX的记录
     DELETE FROM employee WHERE `name` = 'WGX';
     #删除表中所有记录
     DELETE FROM employee;
     ```

   - 使用细节

     如果不使用where语句，将删除表中所有数据

     delete语句不能删除某一列的值（可使用update设为null或者''）

     使用delete语句仅删除记录，不能删除表本身。如果要删除表，使用drop table 表名;语句
   
4. select语句

   - 基本语法

     SELECT [==DISTINCT==] *| {column1, column2, column3...}

     ​							FROM table_name;

     select指定查询哪些列的数据

     column指定列名

     *号代表查询所有列

     from指定查询那张表

     distinct可选，指显示结果，是否去掉重复数据

   - select语句可以使用表达式对查询的列进行运算

     SELECT *| {column1 | expression, column2 | expression, ...}

     ​							FROM table_name;

   - 在select语句中可使用as语句

     SELECT column_name as 别名 from 表名；

   - ![](F:\截图\捕获98.PNG)

   - order by子句排序查询结果

     SELECT column1, column2, column3...

     ​					FROM table_name;

     ​					ORDER BY column ASC|DESC, ...

     order by指定排序的列，排序的列既可以是表中的列名，也可以是select语句后指定的列名

     ASC升序[默认]、DESC降序

     ORDER BY子句应位于select语句的结尾

   - ```sql
     #select语句
     CREATE TABLE student (
     	id INT NOT NULL DEFAULT 1,
     	`name` VARCHAR(20) NOT NULL DEFAULT '',
     	chinese FLOAT NOT NULL DEFAULT 0.0,
     	english FLOAT NOT NULL DEFAULT 0.0,
     	math FLOAT NOT NULL DEFAULT 0.0
     );
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(1, 'aaa', 88, 66, 77);
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(2, 'bbb', 55, 65, 75);
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(3, 'ccc', 85, 78, 64);
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(4, 'ddd', 91, 75, 56);
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(5, 'eee', 84, 68, 72);
     INSERT INTO student(id, `name`, chinese, english, math) 
     	VALUES(6, 'fff', 81, 63, 75);
     #查询表中所学生的信息
     SELECT * FROM student;
     #查询表中所有学生的姓名和对应的英语成绩
     SELECT `name`, english FROM student;
     #过滤表中重复数据distinct
     SELECT DISTINCT math FROM student;
     #统计每个学生的总分
     SELECT `name`, (chinese + english + math) FROM student;
     #在所有学生的总分加十分
     SELECT `name`, (chinese + english + math + 10) FROM student;
     #使用别名表示学生总分
     SELECT `name`, (chinese + english + math) AS total_score FROM student;
     #查询姓名为bbb的学生成绩
     SELECT * FROM student WHERE `name` = 'bbb';
     #查询数学成绩大于70分的学生
     SELECT * FROM student WHERE math > 70;
     #查询总分大于200的学生
     SELECT * FROM student WHERE (chinese + english + math) > 200;
     #查询数学成绩大于70且语文成绩大于80的学生
     SELECT * FROM student WHERE math > 70 AND chinese > 80;
     #查询英语成绩大于数学成绩的学生
     SELECT * FROM student WHERE english > math;
     #查询总分大于200分且数学成绩小于语文成绩的a开头的学生
     SELECT * FROM student WHERE (chinese + english + math) > 200 AND math < chinese AND `name` LIKE 'a%';
     #查询语文分数在80~90之间的学生
     SELECT * FROM student WHERE chinese BETWEEN 80 AND 90;
     #查询总分为231,195,227
     SELECT * FROM student WHERE (chinese + english + math) IN (231, 195, 227);
     #查询所有a开头或者b开头的学生
     SELECT * FROM student WHERE `name` LIKE 'a%' OR `name` LIKE 'b%';
     #查询数学比语文多20分的学生
     SELECT * FROM student WHERE (chinese - math) > 20;
     #对数学成绩排序后输出[升序]
     SELECT * FROM student ORDER BY math;
     #按总分按降序输出
     SELECT `name`, (chinese + english + math) AS total_score FROM student ORDER BY total_score DESC;
     #对a开头的学生成绩排序输出
     SELECT `name`, (chinese + english + math) AS total_score FROM student WHERE `name` LIKE 'a%' ORDER BY total_score;
     ```

5. 合计/统计函数

   - COUNT返回行的总数

     SELECT COUNT(*) | COUNT(列名) FROM table_name

     ​							[WHERE where_definition]

     ```sql
     #练习mysql的统计函数的使用
     #统计一个班级共有多少学生
     SELECT COUNT(*) FROM student;
     #统计数学成绩大于70的学生有多少个
     SELECT COUNT(*) FROM student WHERE math > 70;
     #统计总分大于200的人数有多少
     SELECT COUNT(*) FROM student WHERE (chinese + math + english) > 200;
     #count(*)和count(列)的区别
     #count(*)返回满足条件的行数
     #count(列)统计满足条件的某列有多少个，但是会排除为null
     ```

   - SUM函数返回满足WHERE条件的行的和

     SELECT SUM(列名) {, SUM(列名)...} FROM table_name

     ​							[WHERE where_definition]

     ```sql
     #练习sum函数的使用
     #统计一个班级数学总成绩
     SELECT SUM(math) FROM student;
     #统计一个班级语文、英语、数学各科的总成绩
     SELECT SUM(chinese), SUM(english), SUM(math) FROM student;
     #统计一个班级语文、英语、数学的成绩总和
     SELECT SUM(chinese + english + math) FROM student;
     #统计一个班级语文平均分
     SELECT SUM(chinese) / COUNT(*) FROM student;
     ```

     注意：sum仅对数值起作用，否则会报错

     ​			对多列求和，“，“不能少

   - AVG函数返回满足where条件的一列平均值

     SELECT AVG(列名) {, AVG(列名)...} FROM table_name

     ​							[WHERE where_definition]

     ```sql
     #练习AVG的使用
     #求一个班级的数学平均分
     SELECT AVG(math) FROM student;
     #求一个班级的总分平均分
     SELECT AVG(chinese + english + math) FROM student;
     ```

   - MAX/MIN的函数返回满足where条件的一列的最大值/最小值

     SELECT MAX(列名) FROM table_name

     ​							[WHERE where_definition]

     ```sql
     #练习MAX/MIN的使用
     #求班级最高分和最低分
     SELECT MAX(chinese + english + math), MIN(chinese + english + math) FROM student;
     ```

   - group by子句对列进行分组
   
     SELECT column1, column2, column3... FROM table
   
     ​						GROUP BY column
   
   - having子句对分组后的结果进行过滤
   
     SELECT column1, column2, column3...
   
     ​						FROM table_name
   
     ​						GROUP BY column HAVING...
     
     ```sql
     #练习group by和having使用
     CREATE TABLE dept(
     	deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
     	dname VARCHAR(20) NOT NULL DEFAULT '',
     	loc VARCHAR(13) NOT NULL DEFAULT ''
     );
     INSERT INTO dept VALUES(10, 'ACCOUNTING', 'NEW YORK'), 
     			(20, 'RESEARCH', 'DALLAS'), 
     			(30, 'SALES', 'CHICAGO'), 
     			(40, 'OPERATIONS', 'BOSTON');
     CREATE TABLE emp (
     	empno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
     	ename VARCHAR(20) NOT NULL DEFAULT '',
     	job VARCHAR(9) NOT NULL DEFAULT '',
     	mgr MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
     	hiredate DATE NOT NULL,
     	sal DECIMAL(7, 2) NOT NULL,
     	comm DECIMAL(7, 2),
     	deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0
     );
     INSERT INTO emp VALUES (7369, 'SMITH', 'CLERK' , 7902, '1990-12-17', 800.00, NULL , 20),
     	(7499,'ALLEN' ,'SALESMAN' , 7698, '1991-2-20', 1600.00, 300.00, 30),
     	(7521, 'WARD','SALESMAN' ,7698,'1991-2-22', 1250.00, 500.00, 30) ,
     	(7566,'JONES','MANAGER',7839,'1991-4-2', 2975.00, NULL, 20) ,
     	(7654,'VARTIN' , 'SALESMAN', 7698,'1991-9-28',1250.00, 1400.00 ,30),
     	(7698, 'BLAKE' , 'MANAGER' , 7839,'1991-5-1', 2850.00, NULL ,30);
     #显示每个部门的平均工资和最高工资
     SELECT AVG(sal), MAX(sal), deptno FROM emp GROUP BY deptno;
     #显示每个部门的每种岗位的平均工资和最低工资
     SELECT AVG(sal), MIN(sal), deptno, job FROM emp GROUP BY deptno, job;
     #显示平均工资低于2000的部门号和它的平均工资
     SELECT AVG(sal) AS avg_sal, deptno FROM emp GROUP BY deptno HAVING avg_sal < 2000;
     ```
   
6. 字符串函数

   - ![](F:\截图\捕获99.PNG)

   - ```sql
     #练习字符串相关函数的使用
     #charset(str)返回字串字符集
     SELECT CHARSET(ename) FROM emp;
     #concat(string2 [, ...]) 连接字符串
     SELECT CONCAT (ename, 'job is ', job) FROM emp;
     #instr(string, substring)返回substring在string中出现的位置，没有返回0
     #dual亚元表，系统表，可以作为测试表使用
     SELECT INSTR('wugongxun', 'xun') FROM DUAL;
     #ucase(string)转成大写
     SELECT UCASE(ename) FROM emp;
     #lcase(string)转成小写
     SELECT LCASE(ename) FROM emp;
     #left(string, length)从string中的左边起取length个字符
     SELECT LEFT(ename, 2) FROM emp;
     #right(string, length)从string中的右边起取length个字符
     SELECT RIGHT(ename, 2) FROM emp;
     #length(string) string长度（按照字节）
     SELECT LENGTH(ename) FROM emp;
     #replace(str, search_str, replace_str)
     SELECT ename, REPLACE(job, 'MANAGER', '经理') FROM emp;
     #strcmp(string1, string2)逐字符比较两字符串大小
     SELECT STRCMP('a', 'b');
     #substring(str, position[, length])从str的position开始（从1开始计算），取length个字符
     SELECT ename, SUBSTRING(ename, 1, 2) FROM emp;
     #ltrim(string)取出前端空格 rtrim(string)取出后端空格 trim(string)去除左右两边空格
     SELECT LTRIM(' wgx') FROM DUAL;
     SELECT RTRIM('wgx ') FROM DUAL;
     SELECT TRIM(' wgx ') FROM DUAL;
     #以首字母大写方式显示所有员工姓名
     SELECT ename, CONCAT(UCASE(LEFT(ename, 1)), LCASE(SUBSTRING(ename, 2))) FROM emp;
     ```

7. 数学函数

   - ![](F:\截图\捕获100.PNG)

   - ```sql
     #练习数学相关函数
     #abs(num) 绝对值
     SELECT ABS(-10) FROM DUAL;
     #bin(decimal_number)十进制转二进制
     SELECT BIN(10) FROM DUAL;
     #ceiling(number)向上取整，得到比number大的最小的整数
     SELECT CEILING(1.1) FROM DUAL;
     #conv(number, from_base, to_base)进制转换
     SELECT CONV(8, 10, 2) FROM DUAL;#十进制8转成二进制
     #floor(number)向下取整，得到比number小最大的整数
     SELECT FLOOR(-1.1) FROM DUAL;
     #format(number, decimal_places)保留小数位数（四舍五入）
     SELECT FORMAT(1.23456789, 2) FROM DUAL;
     #hex(decimal_number)转成十六进制
     SELECT HEX(11) FROM DUAL;
     #least(number1, number [, ...])求最小值
     SELECT LEAST(1, 2, 3) FROM DUAL;
     #mod(number, denominator)取余
     SELECT MOD(10, 3) FROM DUAL;
     #rand([seed])返回一个随机数0到1之间
     #如果使用seed返回随机数，如果seed不变，随机数不变
     SELECT RAND() FROM DUAL;
     ```

8. 时间和日期相关函数

   - ![](F:\截图\捕获101.PNG)

   - 使用细节

     DATE_ADD()中的interval后面可以是year minute second day等

     DATE_SUB()中的interval后面可以是year minute second hour day等

     DATEDIFF(date1, date2)得到的是天数，而且是date1-date2的天数，因此可以取负数

     这四个函数的日期类型可以是date，datetime或者timestamp

   - ```sql
     #练习日期时间相关函数
     #current_date()当前日期
     SELECT CURRENT_DATE() FROM DUAL;
     #current_time()当前时间
     SELECT CURRENT_TIME() FROM DUAL;
     #current_timestamp()当前时间戳
     SELECT CURRENT_TIMESTAMP() FROM DUAL;
     #创建测试表
     CREATE TABLE message(
     	id INT,
     	content VARCHAR(30),
     	send_time DATETIME
     );
     INSERT INTO message VALUES(1, '北京时间', CURRENT_TIMESTAMP()),
     			(2, '纽约时间', NOW()),
     			(3, '太平洋时间', NOW());			
     #显示所有时间信息，只显示日期，不用显示时间
     SELECT id, content, DATE(send_time) FROM message;
     #请查询10分钟内发布的新闻
     SELECT * FROM message WHERE DATE_ADD(send_time, INTERVAL 10 MINUTE) >= NOW();
     #请在mysql的sql语句中求出2011-11-11和1990-1-1相差多少
     SELECT DATEDIFF('2011-11-11', '1990-1-1') FROM DUAL;
     #请用mysql的sql语句求出你活了多少天
     SELECT DATEDIFF(CURRENT_DATE(), '1999-11-17') FROM DUAL;
     #如果你能活到80岁，求出你还能活多少天
     SELECT DATEDIFF(DATE_ADD('1999-11-17', INTERVAL 80 YEAR), CURRENT_DATE()) FROM DUAL;
     #year|month|day| date(datetime)
     SELECT YEAR(NOW()) FROM DUAL;
     SELECT MONTH(NOW()) FROM DUAL;
     SELECT DAY(NOW()) FROM DUAL;
     SELECT YEAR('1999-11-17') FROM DUAL;
     #unix_timestamp()返回的是1970-1-1到现在的毫秒数
     SELECT UNIX_TIMESTAMP() FROM DUAL;
     #from_unixtime()可以把一个unix_timestamp秒数，转成指定格式日期
     SELECT FROM_UNIXTIME(UNIX_TIMESTAMP(), '%Y-%m-%d') FROM DUAL;#当前日期
     ```

9. 加密和系统函数

   - ![](F:\截图\捕获102.PNG)

   - ```sql
     #练习加密函数和系统函数
     #user()查询用户
     SELECT USER() FROM DUAL;
     #database()查询当前使用数据库名称
     SELECT DATABASE() FROM DUAL;
     #md5(str)为字符串算出一个MD5 32的字符串，常用（用户密码）加密
     SELECT MD5('wgx') FROM DUAL;
     #password(str)加密函数
     SELECT PASSWORD('wgx') FROM DUAL;
     SELECT * FROM mysql.user;
     ```

10. 流程控制函数

    - ![](F:\截图\捕获103.PNG)

    - ```sql
      #练习流程控制语句
      #if(expr1, expr2, expr3)如果expr1为true，则返回expr2否则返回expr3
      SELECT IF(TRUE, '上海', '深圳') FROM DUAL;
      #ifnull(expr1, expr2)如果expr1不为null返回expr1，否则返回expr2
      SELECT IFNULL(NULL, 'wgx') FROM DUAL;
      #select case 
      #	when expr1 then expr2 
      #	when expr3 then expr4
      #	else expr5 end
      SELECT CASE
      	WHEN FALSE THEN '1'
      	WHEN TRUE THEN '2'
      	ELSE '3'
      	END
      #查询emp表，如果comm是null，则显示0.0
      SELECT ename, IF(comm IS NULL, 0.0, comm) FROM emp;
      SELECT ename, IFNULL(comm, 0.0) FROM emp;
      #如果emp表的job是CLERK则显示职员，如果是MANAGER则显示经理，如果是SALESMAN则显示销售人员
      SELECT ename, (SELECT CASE
      		WHEN job = 'CLERK' THEN '职员'
      		WHEN job = 'MANAGER' THEN '经理'
      		WHEN job = 'SALESMAN' THEN '销售人员'
      		END) AS '岗位' 
      		FROM emp;
      ```

## 五，多表查询

1. 查询加强

   ```sql
   #查询加强
   #查询1992.1.1后入职的员工
   SELECT * FROM emp WHERE hiredate > '1991-01-01';
   #使用like操作符
   #%表示0到多个字符，_表示单个字符
   #显示首字母为s的员工姓名和工资
   SELECT ename, sal FROM emp WHERE ename LIKE 'S%';
   #显示第三个字母为R的所有员工姓名和工资
   SELECT ename, sal FROM emp WHERE ename LIKE '__R%';
   #显示没有上级的雇员的情况
   SELECT * FROM emp WHERE mgr IS NULL;
   #查询表结构
   DESC emp;
   #使用order by子句
   #按照工资从低到高顺序，显示雇员信息
   SELECT * FROM emp ORDER BY sal;
   #按照部门号升序而雇员的工资降序排列，显示雇员信息
   SELECT * FROM emp ORDER BY deptno, sal DESC;
   ```

2. 分页查询

   - select ... limit start, rows

     从start+1行开始取，取出rows行，start从0开始计算

   - ```sql
     #练习分页查询
     #按雇员的empno号升序取出，每页显示3条记录，请别显示第一页，第二页
     SELECT * FROM emp ORDER BY empno LIMIT 0, 3;#第一页
     SELECT * FROM emp ORDER BY empno LIMIT 3, 3;#第二页
     #select * from table_name limit 每页显示的记录数*(第几页-1), 每页显示记录数
     ```

3. 分组查询

   - ```sql
     #练习分组查询
     #显示每种岗位的雇员总数，平均工资
     SELECT job, AVG(sal), COUNT(*) FROM emp GROUP BY job;
     #显示雇员总数，以及获取补助的雇员总数
     SELECT COUNT(*), COUNT(comm) FROM emp;
     #统计没有获得补助的雇员数
     SELECT COUNT(IF(comm IS NULL, 1, NULL))FROM emp;
     SELECT COUNT(*) - COUNT(comm) FROM emp;
     #显示管理者的总人数
     SELECT COUNT(DISTINCT mgr) FROM emp;
     #显示雇员工资的最大差值
     SELECT MAX(sal) - MIN(sal) FROM emp;
     ```

4. 数据分组总结

   - 如果select语句同时包含有group by，having，limit，order by，那么他们的顺序是group by，having，order by，limit

   - SELECT column1, column2, column3... FROM table_name

     ​						GROUP BY column

     ​						HAVING condition

     ​						ORDER BY column

     ​						LIMIT start, rows;

   - ```sql
     #请统计各个部门的平均工资，并且是大于1000的，并且按照平均工资从高到底排序
     #取出前两行记录
     SELECT deptno, AVG(sal) AS avg_sal FROM emp
     	GROUP BY deptno
     	HAVING avg_sal > 1000
     	ORDER BY avg_sal DESC
     	LIMIT 0, 2;
     ```

5. 多表查询

   - 多表查询是指基于两个和两个以上的表的查询

   - 多表查询的条件不能少于表的个数减一，否则会出现笛卡尔集

   - ```sql
     #多表查询
     #显示雇员名，雇员工资以及所在的部门的名字
     SELECT ename, sal, dname 
     	FROM emp, dept 
     	WHERE emp.deptno = dept.deptno;
     #显示部门号为20的部门名、员工名和工资
     SELECT dept.dname, ename, sal 
     	FROM emp, dept
     	WHERE emp.deptno = dept.deptno AND emp.deptno = 20;
     #显示各个员工的姓名，工资，以及其工资的级别
     SELECT ename, sal, grade
     	FROM emp, salgrade
     	WHERE sal BETWEEN losal AND hisal;
     #显示雇员名，雇员工资以及所在部门的名字，并按照部门排序
     SELECT ename, sal, dname
     	FROM emp, dept
     	WHERE emp.deptno = dept.deptno
     	ORDER BY emp.deptno;
     ```

6. 自连接

   - 自连接把同一张表当做两张表来使用

   - 需要给表取别名

   - 列名不明确，可以指定列的别名

   - ```sql
     #多表查询的自连接
     #显示公司员工名字和他的上级名字
     SELECT worker.ename AS 'worker', boss.ename AS 'boss'
     	FROM emp AS worker, emp AS boss
     	WHERE worker.mgr = boss.empno;
     ```

7. 子查询

   - 子查询是指嵌入在其他sql语句中的select语句，也叫嵌套查询

   - 单行子查询：单行子查询是指值返回一行数据的子查询

   - 多行子查询：多行子查询是指返回多行数据的查询，使用关键字in

   - ```sql
     #练习子查询
     #显示与SMITH同一部门的所有员工
     SELECT * FROM emp
     	WHERE deptno = (
     		SELECT deptno
     		FROM emp
     		WHERE ename = 'SMITH'
     	);
     #查询和部门20的工作相同的雇员的
     #名字、岗位、工资、部门号，但是不包含20号部门自己的雇员
     SELECT ename, job, sal, deptno 
     	FROM emp 
     	WHERE job IN (
     		SELECT DISTINCT job
     		FROM emp
     		WHERE deptno = 20
     	) AND deptno != 20
     ```

   - 子查询临时表：子查询结果可以当做临时表使用

     ```sql
     #查询各个部门最高工资的员工信息
     SELECT empno, ename, job, mgr, hiredate, emp.sal, comm, emp.deptno
     	FROM (SELECT deptno, MAX(sal) AS max_sal
     			FROM emp
     			GROUP BY deptno) AS temp, emp
     			WHERE temp.deptno = emp.deptno AND temp.max_sal = emp.sal
     ```

   - 在多行子查询中使用all和any操作符

     ```sql
     #all和any的使用
     #显示工资比部门30的所有员工的工资高的员工的姓名、工资和部门号
     SELECT ename, sal, deptno
     	FROM emp
     	WHERE sal > ALL(
     		SELECT sal
     		FROM emp
     		WHERE deptno = 30);
     #显示工资比部门30的一个员工的工资高的员工的姓名、工资和部门号
     SELECT ename, sal, deptno
     	FROM emp
     	WHERE sal > ANY(
     		SELECT sal
     		FROM emp
     		WHERE deptno = 30);
     ```

   - 多列子查询：多列子查询是指查询返回多个数据的子查询语句

     ```sql
     #查询与ALLEN的部门和岗位完全相同的所有雇员（并且不包含SMITH本人）
     SELECT *
     	FROM emp
     	WHERE (deptno, job) = (
     		SELECT deptno, job
     		FROM emp
     		WHERE ename = 'ALLEN'
     	) AND ename != 'ALLEN';
     ```

8. 表复制和去重

   - ```sql
     #表的复制
     #为了对某个sql语句进行效率测试我们需要海量数据时，可以使用表复制创建海量数据
     CREATE TABLE my_table01(
     	id INT,
     	`name` VARCHAR(32),
     	sal DOUBLE,
     	job VARCHAR(32),
     	deptno INT);
     #把emp表的记录复制到my_table01
     INSERT INTO my_table01
     	(id, `name`, sal, job, deptno)
     	SELECT empno, ename, sal, job, deptno FROM emp;
     #自我复制
     INSERT INTO my_table01
     	SELECT * FROM my_table01;
     #删除掉一张表中重复的记录
     CREATE TABLE my_table02 LIKE emp;
     INSERT INTO my_table02
     	SELECT * FROM emp;
     #去重
     #1. 先创建一张临时表temp，该表的结构和my_table02一样
     #2. 把my_table02的记录通过distinct关键字处理后把记录复制到temp
     #3. 清除掉my_table02所有记录
     #4. 把temp表记录复制到my_table02
     #5. drop掉临时表temp
     CREATE TABLE temp LIKE my_table02;
     INSERT INTO temp
     	SELECT DISTINCT * FROM my_table02
     DELETE FROM my_table02;
     INSERT INTO my_table02
     	SELECT * FROM temp;
     DROP TABLE temp;
     ```

9. 合并查询

   - 在实际应用中，为了合并多个select语句的结果，可以使用集合操作符号union，union all

   - ```sql
     #合并查询
     SELECT ename, sal, job FROM emp WHERE sal > 2500;
     SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';
     #使用union all将两个查询结果合并，不会去重
     SELECT ename, sal, job FROM emp WHERE sal > 2500
     UNION ALL
     SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';
     #使用union将两个查询结果合并，会去重
     SELECT ename, sal, job FROM emp WHERE sal > 2500
     UNION
     SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';
     ```

10. 外连接

    - 左外连接：如果左侧的表完全显示我们就叫左外连接

      select .. from 表1 left join 表2 on 条件

    - 右外连接：如果右侧的表完全显示我们就叫右外连接

      select .. from 表1 right join 表2 on 条件

    - ```sql
      #外连接
      #列出部门名称和这些部门的员工信息（名字和工作），同时列出那些没有员工的部门名
      #左外连接
      SELECT dname, ename, job
      	FROM dept LEFT JOIN emp
      	ON emp.deptno = dept.deptno;
      #右外连接
      SELECT dname, ename, job
      	FROM emp RIGHT JOIN dept
      	ON dept.deptno = emp.deptno
      ```

## 六，mysql约束

1. primary key(主键)

   - 字段名 字段类型 primary key

     用于唯一的标示表行的数据，当定义主键约束后，该列不能重复

   - 主键的使用细节

     primary key不能重复而且不能为null

     一张表最多只能有一个主键，但是可以是复合主键

     主键的指定方式有两种：

     ​										直接在字段后指定：字段名 primary key

     ​										在表定义最后写primary key(列名)

     使用desc 表名，可以看到primary key的情况

   - ```sql
     #主键的使用
     CREATE TABLE t3(
     	id INT PRIMARY KEY,-- 表示ID列是主键
     	`name` VARCHAR(32),
     	email VARCHAR(32)
     	);
     INSERT INTO t3
     	VALUES(1, 'jack', 'jack@qq.com');
     INSERT INTO t3
     	VALUES(2, 'tom', 'tom@qq.com');
     INSERT INTO t3
     	VALUES(3, 'wgx', 'wgx@qq.com');
     #复合主键的使用
     CREATE TABLE t4(
     	id INT,
     	`name` VARCHAR(32),
     	email VARCHAR(32),
     	PRIMARY KEY (id, `name`)-- 复合主键
     	);
     ```

2. unique

   - 使用细节

     如果没有指定not null，则unique字段可以有多个null

     一张表可以有多个unique字段

   - ```sql
     #unique的使用
     CREATE TABLE t5(
     	id INT UNIQUE,-- 表示id列不可以重复的
     	`name` VARCHAR(32),
     	email VARCHAR(32)
     	);
     INSERT INTO t5
     	VALUES(1, 'jack', 'jack@qq.com');
     INSERT INTO t5
     	VALUES(2, 'tom', 'tom@qq.com');
     INSERT INTO t5
     	VALUES(3, 'wgx', 'wgx@qq.com');
     INSERT INTO t5
     	VALUES(NULL, 'wgx', 'wgx@qq.com');
     #一张表可以有多个unique字段
     CREATE TABLE t6(
     	id INT UNIQUE,-- 表示id列不可以重复的
     	`name` VARCHAR(32) UNIQUE,-- 表示name列也不可以重复
     	email VARCHAR(32)
     	);
     ```

3. foreign key(外键)

   - 用于定义主表和从表的关系：外键约束要定义在从表上，主表则必须具有主键约束或是unique约束，当定义外键约束后，要求外键数据必须在主表的主键列存在或是为null

   - foreign key (本表字段名) references 主表名(主键名或者unique名)

   - 使用细节

     外键指向的表的字段，要求是primary key或者是unique

     表的类型是innodb才支持外键

     外键字段的类型要和主键字段的类型一致（长度可以不同）

     外键字段的值，必须在主键字段中出现过，或者为null（==前提是外键字段允许为null==）

     一旦建立外键的关系，数据不能随意删除了

   - ```sql
     #外键使用
     CREATE TABLE my_class(
     	id INT PRIMARY KEY,
     	`name` VARCHAR(32) NOT NULL DEFAULT ''
     	);
     CREATE TABLE my_stu(
     	id INT PRIMARY KEY,
     	`name` VARCHAR(32) NOT NULL DEFAULT '',
     	class_id INT,
     	#指定外键
     	FOREIGN KEY (class_id) REFERENCES my_class(id)
     	);
     INSERT INTO my_class
     	VALUES(100, 'java'), (200, 'web');
     INSERT INTO my_stu
     	VALUES(1, 'tom', 100);
     INSERT INTO my_stu
     	VALUES(2, 'jack', 200);
     INSERT INTO my_stu
     	VALUES(3, 'wgx', NULL);
     ```

4. check

   - 用于强制行数据必须满足的条件，例如在sal列上定义check约束，并要求sal列值在1000~2000之间如果不在1000~2000之间就会报错

   - mysql5.7目前还不支持check，只做语法的校验，但不会生效

   - 列名 类型 check (check条件)

   - ```sql
     #check的使用
     CREATE TABLE t7(
     	id INT PRIMARY KEY,
     	`name` VARCHAR(32),
     	sex VARCHAR(6) CHECK (sex IN('man', 'woman')),
     	sal DOUBLE CHECK (sal > 1000 AND sal < 2000)
     	);
     ```

5. 自增长

   - 字段名 整型 primary key auto_increment

   - 使用细节

     一般来说自增长是和primary key配合使用

     自增长也可以单独使用但是要配合unique

     自增长修饰的字段为整数型的（小数也可以，但是使用非常少）

     自增长默认从1开始，也可以通过 alter table 表名 auto_increment = xxx;修改

     如果添加数据时给自增长字段指定有值，则以指定的值为准

   - ```sql
     #自增长的使用
     CREATE TABLE t8 (
     	id INT PRIMARY KEY AUTO_INCREMENT,
     	`name` VARCHAR(32) NOT NULL DEFAULT '',
     	email VARCHAR(32) NOT NULL DEFAULT ''
     	);
     INSERT INTO t8
     	VALUES(NULL, 'jack', 'jack@qq.com');
     INSERT INTO t8 (`name`, email)
     	VALUES('wgx', 'wgx@qq.com');
     #修改自增长默认值
     ALTER TABLE t8 AUTO_INCREMENT = 100;
     INSERT INTO t8 (`name`, email)
     	VALUES('tom', 'tom@qq.com');
     ```

## 七，索引

1. 索引的原理

   - 没有索引为什么会慢？因为全表扫描

   - 使用索引为什么会快？形成一个索引的数据结构，比如二叉树

   - 索引的代价

     磁盘的占用

     对dml(update，delete，insert)语句的效率影响

2. 索引的类型

   - 主键索引，主键自动的为主索引(类型为primary key)
   - 唯一索引(UNIQUE)
   - 普通索引(INDEX)
   - 全文索引(FULLTEXT)[适用于MyISAM]

3. 创建索引

   - 添加索引

     create [UNIQUE] index index_name on table_name (col_name [(length)] [ASC | DESC], ...)

     alter table table_name ADD INDEX [index_name] (index_col_name, ...)

   - 添加主键索引

     ALTER TABLE 表名 ADD PRIMARY KEY(列名...)

   - ```sql
     #练习mysql的索引使用
     CREATE TABLE t9 (
     	id INT,
     	`name` VARCHAR(32)
     	);
     #查询表是否有索引
     SHOW INDEXES FROM t9;
     #添加索引
     #添加唯一索引
     CREATE UNIQUE INDEX id_index ON t9 (id);
     #添加普通索引
     ALTER TABLE t9 ADD INDEX id_index (id);
     #如果某列的值是不会重复的，则优先考虑unique索引，否则使用普通索引
     #添加主键索引
     #1. 直接在创建表时添加primary key
     #2. 
     ALTER TABLE t9 ADD PRIMARY KEY (id);
     ```

4. 删除索引

   - 删除索引

     DROP INDEX index_name ON table_name;

     ALTER TABLE table_name DROP INDEX index_name;

   - 删除主键索引

     ALTER TABLE table_name DROP PRIMARY KEY;

   - ```sql
     #删除索引
     DROP INDEX id_index ON t9;
     #删除主键索引
     ALTER TABLE t9 DROP PRIMARY KEY;
     #修改索引，先删除在添加新的索引
     #查询索引
     #方式1
     SHOW INDEX FROM t9;
     #方式2
     SHOW INDEXES FROM t9;
     #方式3
     SHOW KEYS FROM t9;
     #方式4
     DESC t9;
     ```

5. 创建索引的规则

   - 较为频繁的作为查询条件的字段应该创建索引
   - 唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件
   - 更新非常频繁的字段不适合创建索引
   - 不会出现在WHERE子句中字段不该创建索引

## 八，事务

1. 基本概念
   - 事务用于保存数据的一致性，它由一组相关的dml语句组成，该组的dml语句要么全部成功，要么全部失败
   
   - 事务和锁
   
     当执行事务时（dml语句），MySQL会在表上加锁，防止其他用户修改表的数据
   
2. 事务操作

   - start transaction——开始一个事务

   - savepoint 保存点名——设置保存点

   - rollback to 保存点名——回退事务

   - rollback ——回退全部事务

   - commit ——提交事务，所有操作生效，不能回退

   - ```sql
     #事务的一个重要的概念和具体操作
     #1. 创建一张测试表
     CREATE TABLE t10 (
     	id INT,
     	`name` VARCHAR(32)
     	);
     #2. 开始事务
     START TRANSACTION
     #3. 设置保存点
     SAVEPOINT a
     #4. 执行dml操作
     INSERT INTO t10 VALUES(100, 'tom')
     SELECT * FROM t10;
     #5. 设置保存点
     SAVEPOINT b
     #6. 执行dml操作
     INSERT INTO t10 VALUES(200, 'jack')
     #7. 回退到b
     ROLLBACK TO b
     #8. 继续回退到a
     ROLLBACK TO a
     #9. 回退到事务开始的状态
     ROLLBACK
     ```

   - 保存点是事务中的点，用于取消部分事务，当结束事务时（commit），会自动删除该事务所定义的所有保存点，当执行回退事务时，通过指定保存点可以回退到指定的点

   - 使用commit语句可以提交事务，当执行了commit语句后会确认事务的变化、结束事务、删除保存点、释放锁，数据生效，当使用commit语句结束事务后，其他会话将可以查看到事务变化后的新数据

3. 事务使用细节

   ![](F:\截图\捕获104.PNG)

4. 隔离级别

   - 基本介绍

     多个连接开始各自事务操作数据库中数据时，数据库系统要负责隔离操作，以保证各个连接在获取数据时的准确性

   - 如果不考虑隔离性，可能会引发

     1. 脏读(dirty read)：当一个事务读取另一个事务未提交的修改时，产生脏读
     2. 不可重复读(nonrepeatable read)：同一查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读
     3. 幻读(phantom read)：同一查询在同一事务中多次进行，由于其他提交事务所做的插入操作，每次返回不同的结果集，此时发生幻读

   - ![](F:\截图\捕获105.PNG)

   - 设置隔离级别

     1. 查看当前会话隔离级别

        select @@tx_isolation;

     2. 查看当前系统隔离级别

        select @@global.tx_isolation;

     3. 设置当前会话隔离级别

        set session transaction isolation level repeatable read;

     4. 设置当前系统隔离级别

        set global transaction isolation level repeatable read;

     5. mysql默认的事务隔离级别是repeatable read，一般情况下，没有特殊要求没有必要修改

5. 事务的acid特性

   ![](F:\截图\捕获106.PNG)

## 九，表类型和存储引擎

1. 基本介绍

   - MySQL的表类型有存储引擎（Storage Engines）决定，包括MyISAM、InnoDB、Memory等
   - MySQL数据表主要支持六种类型，分别是CSV、Memory、ARCHIVE、MRG_MYISAM、MYISAM、InnoDB
   - 这六种又分为两类，一类是“事务安全型”（transaction-safe），比如InnoDB；其余都属于第二类，称为“非事务安全型”（non-transaction-safe）[mysiam和memory]

2. 主要的存储引擎/表类型特点

   - ![](F:\截图\捕获107.PNG)
   - ![](F:\截图\捕获108.PNG)

3. 如何选择表的存储引擎

   ![](F:\截图\捕获109.PNG)

4. 修改存储引擎

   ALTER TABLE table_name ENGINE = 存储引擎；

## 十，视图

1. 基本概念
   - 视图是一个虚拟表，其内容由查询定义，同真实的表一样，视图包含列，其数据来自对应的真实表（基表）
   - 视图是根据基表来创建的
   - 视图也有列，数据来自基表
   - 通过视图可以修改基表的数据
   - 基表的改变，也会影响到视图的数据
   
2. 视图的基本使用

   - create view 视图名 as select语句

   - alter view 视图名 as select语句

   - show create view 视图名

   - drop view 视图名1，视图名2..

   - ```sql
     #练习视图使用
     #创建一个视图emp_view01,只能查询emp表的(empno, ename, job和deptno)信息
     CREATE VIEW emp_view01
     	AS 
     	SELECT empno, ename, job, deptno FROM emp;
     #查看视图
     DESC emp_view01;
     SELECT * FROM emp_view01;
     #查看创建视图的指令
     SHOW CREATE VIEW emp_view01;
     #删除视图
     DROP VIEW emp_view01;
     ```

3. 使用细节

   - 创建视图后，到数据库去看，对应视图只有一个视图结构文件（形式：视图名.frm）
   - 视图的数据变化会影响到基表，基表的数据变化也会影响到视图[insert，update，delete]
   - 视图中可以再使用视图，数据任然来自基表

## 十一，MySQL管理

1. MySQL用户

   - MySQL中的用户，都存储在系统数据库mysql中user表中
   - host：允许登录的“位置”，localhost表示该用户只允许本机登录，也可以指定ip地址
   - user：用户名
   - authentication_string：密码，是通过mysql的password()函数加密之后的密码

2. 创建用户

   create user ‘用户名’ @‘允许登录的位置’ identified by ‘密码’

3. 删除用户

   drop user ‘用户名’ @‘允许登录的位置’

   ```sql
   #mysql用户管理
   #创建用户
   CREATE USER 'wgx' @'localhost' IDENTIFIED BY 'wgx';
   #删除用户
   DROP USER 'wgx' @'localhost'
   ```

4. 用户修改密码

   - 修改自己密码

     set password = password('密码')；

   - 修改别人的密码（需要有修改密码权限）

     set password for '用户名' @'登录位置' = password('密码')；

5. mysql中的权限

   - 给用户授权

     ![](F:\截图\捕获110.PNG)

   - 回收用户权限

     revoke 权限列表 on 库.对象名 from ‘用户名’ @‘登录位置’；

   - 权限生效指令

     如果权限没有生效，可以执行下面指令

     FLUSH PRIVILEGES；

6. MySQL管理细节

      - 在创建用户的时候，如果不指定Host则为%，%表示所有的IP都有连接权限

        create user xxx;

      - 也可以create user 'xxx' @'192.168.1.%' 表示xxx用户在192.168.1.*的IP都可以登录MySQL

      - 在删除用户的时候，如果host不是%，需要明确指定 ‘用户’ @‘host值’

        drop user 'xxx' 默认为 drop user 'xxx' @'%'

# 第二十章 JDBC和数据库连接池

## 一，基本介绍

1. JDBC为访问不同的数据库提供了统一的接口，为使用者屏蔽了细节问题

2. java程序员使用JDBC，可以连接任何提供了JDBC驱动的数据库系统，从而完成对数据库的各种操作

3. JDBC原理示意图

   ![](F:\截图\捕获111.PNG)

4. JDBC是java提供的一套用于数据库操作的接口API，java程序员只需要面向这套接口编程即可，不同的数据库厂商需要针对这套接口提供不通的实现

5. JDBC API是一系列接口，它统一和规范了应用程序与数据库的连接，执行SQL语句，并得到返回结果等各种操作，相关类和接口在java.sql与javax.sql包中

6. JDBC快速入门

   - 注册驱动 - 加载Driver类

   - 获取连接 - 得到Connection

   - 执行增删改查 - 发送SQL给MySQL执行

   - 释放资源 - 关闭相关连接

   - ```java
     public static void main(String[] args) throws SQLException {
             //前置工作
             //在项目下创建一个文件夹
             //将mysql-connector-java-8.0.26.jar文件加入到项目
             //1. 注册驱动
             Driver driver = new Driver();//创建Driver对象
             //2. 得到连接
             //jdbc:mysql:// 规定好的协议,通过jdbc方式连接MySQL
             //localhost 主机，可以是ip地址
             //3306 表示MySQL监听的端口
             //wgx_db02 表示连接到那个数据库
             //MySQL连接本质就是socket连接
             String url = "jdbc:mysql://localhost:3306/wgx_db02";
             //将用户名和密码放入到Properties对象中
             Properties properties = new Properties();
             properties.setProperty("user", "root");//用户
             properties.setProperty("password", "wgx");//密码
             //获取连接
             Connection connect = driver.connect(url, properties);
             //3. 执行SQL语句
             String sql = "delete from actor where id = 1";
             //statement用于执行静态SQL语句并返回其生成结果的对象
             Statement statement = connect.createStatement();
             int rows = statement.executeUpdate(sql);//如果是dml语句返回的就是影响行数
             System.out.println(rows > 0? "成功" : "失败");
             //4. 关闭连接资源
             statement.close();
             connect.close();
         }
     ```

## 二，连接数据库

1. 获取Driver实现类对象

   会直接使用com.mysql.jdbc.Driver()，属于静态加载，灵活性差，依懒强

2. 使用反射

   Class cls = Class.forName("com.mysql.jdbc.Driver");

   Driver driver = (Driver) cls.newInstance();

3. 使用DriverManager替换Driver

   DriverManager.registerDriver(driver);

4. 使用Class.forName自动完成注册驱动

   Class.forName("com.mysql.jdbc.Driver");

   Connection connection = DriverManager.getConnection(url, user, password);

5. 使用配置文件，连接数据库更灵活

   jdbc.properties

   user=root

   password=wgx

   url=jdbc:mysql://localhost:3306/wgx_db02

   driver=com.mysql.jdbc.Driver

## 三，ResultSet（结果集）

1. 基本介绍
   - 表示数据库结果集的数据表，通常通过执行查询数据库的语句生成
   - ResultSet对象保持一个光标指向其当前的数据行。最初，光标位于第一行之前
   - next方法将光标移动到下一行，并且由于在ResultSet对象中没有更多行时返回false，因此可以在while循环中使用来遍历结果集

## 四，SQL注入

1. 基本介绍

   - Statement对象用于执行静态SQL语句并返回其生成的结果对象

   - 在连接建立后，需要对数据进行访问，执行命令或是SQL语句，可以通过

     Statement[存在SQL注入]

     PreparedStatement[预处理]

     CallableStatement[存储过程]

   - Statement对象执行SQL语句，存在注入风险

   - SQL注入是利用某些系统没有对用户输入的数据继续充分的检查，而在用户输入数据中注入非法的SQL语句段或命令，恶意攻击数据库

   - 要防范SQL注入，只要用PreparedStatement（从Statement扩展而来）取代Statement就可以了

2. PreparedStatement

   String sql = "SELECT COUNT(*) FROM admin WHERE username = ? AND password = ?"

   - PreparedStatement执行的SQL语句中的参数用问号来表示，调用PreparedStatement对象的setXxx()方法来设置这些参数，setXxx()方法有两个参数，第一个参数是要设置的SQL语句中的参数的索引（从1开始），第二个是设置SQL语句中的参数的值

   - 调用executeQuery()，返回ResultSet对象
   - 调用executeUpdate()，执行更新，包括增、删、修改

3. 预处理的好处

   - 不再使用 + 拼接SQL语句，减少语法错误
   - 有效的解决了SQL注入问题
   - 大大减少了编译次数，效率较高

## 五，事务

1. 基本介绍
   - JDBC程序中当一个Connection对象创建时，默认情况下是自动提交事务的：每次执行一个SQL语句时，如果指执行成功就会向数据库提交，而不能回滚
   - JDBC程序中为了让多个SQL语句作为一个整体执行，需要使用事务
   - 调用Connection的setAutoCommit(false)可以取消自动提交事务
   - 在所有的SQL语句都成功执行后，调用commit()方法提交事务
   - 在其中某个操作失败或出现异常时，调用rollback()方法回滚事务

## 六，批处理

1. 基本介绍

   - 当需要成批插入或者更新记录时，可以采用java的批量更新机制，这一机制允许多条语句一次性提交给数据库批量处理，通常情况下比单独提交处理更效率

   - JDBC的批量处理语句包括下面方法

     addBatch():添加需要批量处理的SQL语句或者参数

     executeBatch():清空批处理包的语句

     clearBatch():清空批处理包的语句

   - JDBC连接MySQL时，如果要使用批处理功能，在url中加参数

     ?rewriteBatchedStatements = true

   - 批处理往往和PreparedStatement一起搭配使用，可以既减少编译次数，又减少运行次数，效率大大提高

## 七，数据库连接池原理

1. 传统获取Connection问题

   - 传统的JDBC数据库连接使用DriverManager来获取，每次想数据库建立连接的时候都要讲Connection加载到内存中，在验证IP地址，用户名和密码。需要数据库连接的时候，就向数据库要求一个，频繁的进行数据库连接操作将占用很多的系统资源，容易造成服务器崩溃
   - 每一次数据库连接，使用后都得断开，如果程序出现异常而未能关闭，将导致数据库内存泄漏，最终将导致重启数据库
   - 传统获取连接的方式，不能控制创建的连接数量，如连接过多，也可能导致内存泄漏，MySQL崩溃

2. 数据库连接池基本介绍

   - 预先在缓冲池中放入一定数量的连接，当需要建立数据库连接时，只需要从“缓冲池”中取出一个，使用完毕后在放回去
   - 数据库连接池负责分配、管理和释放数据库连接，他允许应用程序重复使用一个现有的数据库连接，而不是重新建立一个
   - 当应用程序向连接池请求的连接数超过最大连接数量时，这些请求将被加入到等待队列中

3. 数据库连接池种类

   - JDBC的数据库连接池使用javax.sql.DateSource来表示，DataSource只是一个接口，该接口通常由第三方提供实现
   - C3P0数据库连接池，速度相对较慢，稳定性不错
   - DBCP数据库连接池，速度相对C3P0较快，但是不稳定
   - Proxool数据库连接池，有监控连接池状态功能，稳定性较C3P0差一点
   - BoneCP数据库连接池，速度快
   - Druid(德鲁伊)是阿里提供的数据库连接池，集DBCP、C3P0、Proxool优点与一身的数据库连接池

4. 使用C3P0数据库连接池

   ```java
   public static void main(String[] args) throws IOException, PropertyVetoException, SQLException {
       //1. 创建一个数据源对象
       ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
       //2. 通过配置文件mysql.properties获取相关连接的信息
       Properties properties = new Properties();
       properties.load(new FileInputStream("src\\mysql.properties"));
       String user = properties.getProperty("user");
       String password = properties.getProperty("password");
       String url = properties.getProperty("url");
       String driver = properties.getProperty("driver");
       //3. 给数据源设置相关的参数
       comboPooledDataSource.setDriverClass(driver);
       comboPooledDataSource.setJdbcUrl(url);
       comboPooledDataSource.setUser(user);
       comboPooledDataSource.setPassword(password);
       //设置初始化连接数
       comboPooledDataSource.setInitialPoolSize(10);
       comboPooledDataSource.setMaxPoolSize(50);
       //4. 得到连接
       Connection connection = comboPooledDataSource.getConnection();
       System.out.println("连接成功");
       connection.close();
   }
   //第二种方式: 使用配置文件模板来完成
   //1. 将C3P0提供的c3p0_config.xml文件拷贝到src目录下
   //   该文件指定了连接数据库和连接池的相关参数
   @Test
   public void testC3P0() throws SQLException {
       ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("wgx");
       Connection connection = comboPooledDataSource.getConnection();
       System.out.println("连接成功");
       connection.close();
   }
   ```

5. 德鲁伊数据库连接池

   ```java
   //1. 加入Druid的jar包
   //2. 将druid.properties 文件放到src下面
   public static void main(String[] args) throws Exception {
       //3. 创建Properties对象，读取配置文件
       Properties properties = new Properties();
       properties.load(new FileInputStream("src\\druid.properties"));
       //4. 创建一个指定参数的数据库连接池,Druid的连接池
       DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
       Connection connection = dataSource.getConnection();
       System.out.println("连接成功");
       connection.close();
   }
   ```

## 八，ApDBUtils

1. 基本介绍

   - commons-dbutils是Apache组织提供的一个开源的JDBC工具类库，它对JDBC的封装，使用dbutils能极大简化jdbc编码的工作量

   - QueryRunner类：该类封装了SQL的执行，是线程安全的，可以实现增删改查、批处理

   - 使用QueryRunner类实现查询

   - ResultHandler接口：该接口用于处理java.sql.ResultSet，将数据按要求转换成另一种形式

   - ![](F:\截图\捕获112.PNG)

2. 测试

   ```java
   //查询多行结果
   public static void main(String[] args) throws SQLException {
       //1. 得到连接，使用Druid
       Connection connection = JDBCUtilsByDruid.getConnection();
       //2. 先引入DbUtils相关的jar包，加入到Project
       //3. 创建QueryRunner
       QueryRunner queryRunner = new QueryRunner();
       //4. 就可以执行相关的方法，返回ArrayList
       //   query就是执行SQL语句，得到resultSet -> 封装到ArrayList集合中
       //   返回集合
       //   connection:连接
       //   sql:执行的SQL语句
       //   new BeanListHandler<>(Actor.class):将resultset ->Actor对象 -> 封装到ArrayList
       String sql = "select * from actor where id = ?";
       List<Actor> list = queryRunner.query(connection, sql, new BeanListHandler<>(Actor.class), 1);
       for (Actor actor : list) {
           System.out.println(actor);
       }
       //5. 关闭资源
       JDBCUtilsByDruid.close(null, null, connection);
   }
   @Test
   //查询单行结果
   public void testQuerySingle() throws SQLException {
       Connection connection = JDBCUtilsByDruid.getConnection();
       QueryRunner queryRunner = new QueryRunner();
       String sql = "select * from actor where id = ?";
       Actor actor = queryRunner.query(connection, sql, new BeanHandler<>(Actor.class), 1);
       System.out.println(actor);
       JDBCUtilsByDruid.close(null, null, connection);
   }
   @Test
   //查询单行单列
   public void testScalar() throws SQLException {
       Connection connection = JDBCUtilsByDruid.getConnection();
       QueryRunner queryRunner = new QueryRunner();
       String sql = "select name from actor where id = ?";
       Object query = queryRunner.query(connection, sql, new ScalarHandler(), 1);
       System.out.println(query);
       JDBCUtilsByDruid.close(null, null, connection);
   }
   @Test
   //测试dml语句
   public void testInsert() throws SQLException {
       Connection connection = JDBCUtilsByDruid.getConnection();
       QueryRunner queryRunner = new QueryRunner();
       String sql = "insert into actor values(null, 'wugongxun', '男', '1999-11-17', '15555664494')";
       int affectedRow = queryRunner.update(connection, sql);//受影响的行数
       System.out.println("共" + affectedRow + "行收到影响");
   }
   @Test
   public void testUpdate() throws SQLException {
       Connection connection = JDBCUtilsByDruid.getConnection();
       QueryRunner queryRunner = new QueryRunner();
       String sql = "update actor set name = 'WGX' where id = ?";
       int affectedRow = queryRunner.update(connection, sql, 2);//受影响的行数
       System.out.println("共" + affectedRow + "行收到影响");
   }
   @Test
   public void testDelete() throws SQLException {
       Connection connection = JDBCUtilsByDruid.getConnection();
       QueryRunner queryRunner = new QueryRunner();
       String sql = "delete from actor where id = ?";
       int affectedRow = queryRunner.update(connection, sql, 2);//受影响的行数
       System.out.println("共" + affectedRow + "行收到影响");
   }
   ```

## 九，BasicDao

1. 基本介绍
   - DAO：data access object数据访问对象
   - 这样的通用类称为BasicDao，是专门和数据库交互的，即完成对数据库（表）的crud操作
   - 在BasicDao的基础上，实现一张表对应一个Dao，更好的完成功能，比如Customer表-Customer.java类(javabean)-CustomerDao.java

# 第二十一章 正则表达式

## 一，基本介绍

1. 一个正则表达式，就是某种模式去匹配字符串的一个公式

## 二，正则表达式语法

1. 元字符(Metacharacter)-转义号\\\

   - \\\符号说明：在我们使用正则表达式去检索某些特殊字符的时候，需要用到转义符号，否则检索不到结果，甚至会报错

   - 需要使用到转义符号的字符：.*+()$/\?[]^{}

2. 元字符-字符匹配符

   - ![](F:\截图\捕获113.PNG)

   - ![](F:\截图\捕获114.PNG)

   - java正则表达式默认区分大小写，可以使用如下方法实现不区分大小写

     (?!)abc:表示abc都不区分大小写

     a(?!)bc:表示bc不区分大小写

     a((?!)b)c:表示只有b不区分大小写

     Pattern pattern = new Pattern.compile(regStr, ==Pattern.CASE_INSENSITIVE==);

   - \\\w:匹配单个数字、大小写字母字符、下划线，相当于[0-9a-zA-Z_]

   - \\\W:匹配单个非数字、大小写字母字符、下划线，相当于[ ^0-9a-zA-Z_]

   - \\\s:匹配任何空白字符（空格，制表位等）

   - \\\S:匹配任何非空白字符，和\\\s相反

   - .:匹配出\n之外的所有字符，如果需要匹配.本身需要使用\\\\.

3. 元字符-选择匹配符

   - 在匹配某个字符串的时候是选择性的，即：既可以匹配这个，又可以匹配那个，使用|

4. 元字符-限定符

   - 用于指定其前面的字符和组合项连续出现多少次
   - ![](F:\截图\捕获115.PNG)
   - ![](F:\截图\捕获116.PNG)
   - java匹配默认贪婪匹配，即尽可能匹配多的
   - 非贪婪匹配：在限定符后加？

5. 元字符-定位符

   - 定位符，规定要匹配的字符串出现的位置，比如在字符串的开始还是在结束的位置
   - ![](F:\截图\捕获117.PNG)
   
6. 分组

   - ![](F:\截图\捕获118.PNG)
   - ![](F:\截图\捕获119.PNG)

## 三，正则表达式常用类

1. Pattern类

   - pattern对象是一个正则表达式对象。Pattern类没有公共的构造方法，要创建一个Pattern对象，调用其公共的静态方法，它返回一个Pattern对象。该方法接受一个正则表达式作为它的第一个参数，Pattern pattern = Pattern.compile(pattern)；
   - Pattern类的matches方法：整体匹配。pattern.matches(pattern, content);

2. Matcher类

   - Matcher对象是对输入的字符串进行解释和匹配的引擎。与Pattern类一样，Matcher也没有公共构造方法。需要调用Pattern对象的matcher方法来获取一个Matcher对象
   - ![](F:\截图\捕获120.PNG)
   - ![](F:\截图\捕获121.PNG)

3. PatternSyntaxException

   PatternSyntaxException是一个非强制异常类，它表示一个正则表达式模式中的语法错误

## 四，反向引用

1. 基本介绍

   - 分组

     我们可以用圆括号组成一个比较复杂的匹配模式，那么一个圆括号的部分我们可以看做是一个子表达式/一个分组

   - 捕获

     把正则表达式中子表达式/分组匹配的内容，保存到内容中以数字编号或者显示命名的组里，方便后面引用，从左到右，以分组的左括号为标志，第一个出现的分组的组号为1，第二个为2，以此类推。组0代表的是整个正则式

   - 反向引用

     圆括号的内容被捕获过后，可以在这个括号后被使用，从而写出一个比较实用的匹配模式，这个我们就称为反向引用，这种引用既可以是在正则表达式内部，也可以是在正则表达式外部，==内部反向引用\\\分组号，外部反向引用$分组号==

   - 例

     匹配两个连续的相同数字：(\\\\d)\\\1

     匹配五个连续的相同数字：(\\\d)\\\\1{4}

     匹配个位与千位相同，十位与百位相同的数：(\\\d)(\\\\d)\\\2\\\1

## 五，String类中的使用正则表达式

1. public String replaceAll(String regex, String replacement)
2. public boolean matcher(String regex)//整体匹配
3. public String[] split(String regex)

# 第二十二章 java8新特性

## 一，lambda表达式

1. 基本介绍

   - lambda是一个匿名函数，我们可以吧lambda表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。使用它可以写出更简洁、更灵活的代码

   - ```java
     @Test
     public void test1(){
         //不使用lambda表达式
         Runnable runnable1 = new Runnable() {
             @Override
             public void run() {
                 System.out.println("wgx");
             }
         };
         runnable1.run();
         //使用lambda表达式
         Runnable runnable2 = () -> System.out.println("WGX");
         runnable2.run();
     }
     @Test
     public void test2() {
         //不使用lambda表达式
         Comparator<Integer> comparator1 = new Comparator<Integer>() {
     
             @Override
             public int compare(Integer o1, Integer o2) {
                 return Integer.compare(o1, o2);
             }
         };
         System.out.println(comparator1.compare(1, 2));
         //使用lambda表达式
         Comparator<Integer> comparator2 = (o1, o2) -> Integer.compare(o1, o2);
         System.out.println(comparator2.compare(2, 1));
         //使用方法引用
         Comparator<Integer> comparator3 = Integer::compare;
         System.out.println(comparator3.compare(1, 1));
     }
     ```

   - ->：lambda操作符或者箭头操作符

     ->左边：lambda形参列表（就是接口中的抽象方法的形参列表）

     ->右边：lambda体（就是重写的抽象方法的方法体）

2. lambda表达式的使用

   - 无参，无返回值

     Runnable runnable = () -> {System.out.println("wgx");};

   - 需要一个参数，但是没有返回值

     Consumer<String> consumer = (String s) -> {System.out.println(s);};

   - 数据类型可以省略，因为可由编译器推断得出，称为“类型推断”

     Consumer<String> consumer = (s) -> {System.out.println(s);};

   - 若只需要一个参数，参数的小括号可以省略

     Consumer<String> consumer = s -> {System.out.println(s);};

   - 需要两个或两个以上的参数，多条执行语句，并且可以有返回值

     Comparator<Integer> comparator = (o1, o2) -> {

     ​		System.out.println(o1);

     ​		System.out.println(o2);

     ​		return o1.compareTo(o2);

     };

   - 当lambda体只有一条语句的时，return与大括号都可以省略

     Comparator<Integer> comparator = (o1, o2) -> o1.compareTo(o2);

3. 函数式接口

   - ==只包含一个抽象方法的接口，称为函数式接口==
   - 你可以通过lambda表达式来创建该方法的对象。（若lambda表达式抛出一个受检异常[即非运行时异常]，那么该异常需要在目标接口的抽象方法上进行声明）
   - 我们可以在一个接口上使用@FunctionalInterface注解，这样做可以检查它是否是一个函数式接口。同时javadoc也会包含一条声明，说明这个接口是一个函数式接口

4. java内置四大核心函数式接口

   ![](F:\截图\捕获122.PNG)

5. 方法引用和构造器引用

   - 当要传递给lambda体的操作，已经有实现的方法了，可以使用方法引用

   - 方法引用可以看做是lambda表达式深层次的表达，也就是说，方法引用就是lambda表达式，也就是函数式接口的一个实例，通过方法的名字来指向一个方法，可以认为是lambda表达式的一个语法

   - 要求：实现接口的抽象方法的参数列表和返回值的类型，必须与方法引用的方法的参数列表和返回值保持一致

   - 格式：使用操作符“: :”将类（或者对象）与方法名分隔开来

   - 如下三种主要使用情况

     对象 : : 实例方法名

     类 : : 静态方法名

     类 : : 实例方法名

## 二，Stream API

1. 基本介绍

   - Stream是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列“集合讲的是数据，Stream讲的是计算！”

   - 注意

     Stream自己不会存储元素

     Stream不会改变源对象，相反，他们会返回一个持有结果的新Stream

     Stream操作是延迟执行的，这意味着他们会等到需要结果的时候才执行

2. Stream操作的三个步骤

   - 创建Stream

     一个数据源（如：集合、数组）,获取一个流

   - 中间操作

     一个中间操作链，对数据源的数据进行处理

   - 终止操作（终端操作）

     一旦执行终端操作，就执行中间操作链，并产生结果，之后不会再被使用

3. 创建Stream

   ```java
   //创建Stream的方式一:通过集合
   List<Employee> employees = new ArrayList<Employee>();
   //default Stream<E> stream();返回一个顺序流
   Stream<Employee> stream1 = employees.stream();
   //default Stream<E> parallelStream();返回一个并行流
   Stream<Employee> parallelStream = employees.parallelStream();
   //创建Stream的方式二:通过数组
   Employee[] arr = new Employee[5];
   Stream<Employee> stream2 = Arrays.stream(arr);
   //创建Steam的方式三:通过Stream的of()
   Stream<Integer> stream3 = Stream.of(1, 2, 3, 4, 5);
   //创建Steam的方式四:创建无限流
   //迭代
   //public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)
   //遍历前10个偶数
   Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out :: println);
   //生成
   //public static<T> Stream<T> generate(Supplier<T> s)
   Stream.generate(Math :: random).limit(10).forEach(System.out :: println);
   ```

4. Stream的中间操作

   - 筛选与切片

     ```java
     //筛选与切片
     List<Employee> employees = EmployeeData.getEmployee();
     //filter(Predicate p)——接受lambda，从流中排除某些元素
     Stream<Employee> stream = employees.stream();
     stream.filter(e -> e.getSalary() > 7000).forEach(System.out :: println);
     //limit(n)——截断流，使其元素不超过给定数量
     employees.stream().limit(3).forEach(System.out :: println);
     //skip(n)——跳过元素，返回一个扔掉了前n个元素的流，若流中不足n个元素，则返回一个空流
     employees.stream().skip(3).forEach(System.out :: println);
     //distinct()——筛选，通过流所生成的元素的hashCode()和equals()去除重复元素
     employees.stream().distinct().forEach(System.out :: println);
     ```

   - 映射

     ```java
         //映射
         List<Employee> employees = EmployeeData.getEmployee();
         //map(Function f)——接受一个函数作为参数，将元素转换成其他形式或提取信息，该函数会应用到每一个元素上，并将其映射成一个新的元素
         List<String> list = Arrays.asList("aa", "bb", "cc");
         list.stream().map(str -> str.toUpperCase()).forEach(System.out :: println);
         //flatMap(Function f)——接受一个函数作为参数，将流中的每个值都换成另一个流，然后将所有的流连成一个流
         list.stream().flatMap(StreamAPI02 :: fromStringToStream).forEach(System.out :: println);
     }
     //将字符串中的多个字符构成的集合转换为对应的Stream的实例
     public static Stream<Character> fromStringToStream(String str) {
         ArrayList<Character> list = new ArrayList<Character>();
         for (Character character : str.toCharArray()) {
             list.add(character);
         }
         return list.stream();
     }
     ```

   - 排序

     ```java
     //排序
     //sorted()——自然排序
     List<Integer> list = Arrays.asList(12, 32, 45, -65, -10, 99);
     list.stream().sorted().forEach(System.out :: println);
     //sorted(Comparator comparator)——定制排序
     List<Employee> employees = EmployeeData.getEmployee();
     employees.stream().sorted((e1, e2) -> e1.getAge() - e2.getAge()).forEach(System.out :: println);
     ```

5. Stream的终止操作

   - 匹配与查找

     ```java
     List<Employee> employees = EmployeeData.getEmployee();
     //allMatch(Predicate p)——检查是否匹配所有元素
     //检查是否所有员工年龄大于18
     boolean allMatch = employees.stream().allMatch(e -> e.getAge() > 18);
     System.out.println(allMatch);
     //anyMatch(Predicate p)——检查是否至少匹配一个元素
     //检查是否有员工工资大于10000
     boolean anyMatch = employees.stream().anyMatch(e -> e.getSalary() > 10000);
     System.out.println(anyMatch);
     //noneMatch(Predicate p)——检查是否没有匹配元素
     //检查是否有员工姓“雷”
     boolean noneMatch = employees.stream().noneMatch(e -> e.getName().startsWith("雷"));
     System.out.println(noneMatch);
     //findFirst()——返回一个元素
     Optional<Employee> employee1 = employees.stream().findFirst();
     System.out.println(employee1);
     //findAny——返回当前流中任意元素
     Optional<Employee> employee2 = employees.parallelStream().findAny();
     System.out.println(employee2);
     //count——返回流中元素的总个数
     long count = employees.stream().count();
     System.out.println(count);
     //max(Comparator c)——返回流中最大值
     //返回最高工资
     Optional<Double> max = employees.stream().map(e -> e.getSalary()).max(Double::compare);
     System.out.println(max);
     //min(Comparator c)——返回流中最小值
     //返回最低工资的员工
     Optional<Employee> min = employees.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
     System.out.println(min);
     //forEach(Consumer c)——内部迭代
     employees.stream().forEach(System.out :: println);
     //外部迭代
     employees.forEach(System.out :: println);
     ```

   - 归约

     ```java
     //reduce(T identity, BinaryOperator)——可以将流中的元素反复结合起来，得到一个值，返回T
     //求1-10的自然数的和
     List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
     Integer sum = list.stream().reduce(0, Integer::sum);
     System.out.println(sum);
     //reduce(BinaryOperator)——可以将流中的元素反复结合起来，得到一个值，返回Operator<T>
     //计算所有员工的工资总和
     List<Employee> employees = EmployeeData.getEmployee();
     Optional<Double> sumSalary = employees.stream().map(Employee::getSalary).reduce(Double::sum);
     System.out.println(sumSalary);
     ```

   - 收集

     ```java
     //collect(Collect c)——将流转化成其他形式，接受一个collect接口的实现，用于给Stream中元素做汇总的方法
     //查找工资大于6000的员工，结果返回一个List或者Set
     List<Employee> employees = EmployeeData.getEmployee();
     List<Employee> list = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toList());
     list.forEach(System.out :: println);
     ```

## 三，Optional类

1. 基本介绍

   - Optional<T>类(java.util.Optional)是一个容器类，它可以保存类型T的值，代表这个值存在。或者仅仅保存null，表示这个值不存在，原来null表示一个值不存在，现在Optional类可以更好的表达这个概念，并且避免空指针异常
   - Optional类的javadoc描述如下：这是一个可以为null的容器对象。如果存在isPresent()方法会返回true，调用get()方法会返回该对象

2. Optional类的方法

   - 创建Optional类对象的方法

     Optional.of(T t):创建一个Optional实例，t必须非空

     Optional.empty():创建一个空的Optional实例

     Optional.ofNullable(T t):t可以为null

   - 判断Optional容器中是否包含对象

     boolean isPresent():判断是否包含对象

     void ifPresent(Consumer<? super T> consumer):如果有值，就执行Consumer接口的实现代码，并且该值会作为参数传给它

   - 获取Optional容器的对象

     T get():如果调用对象包含值，返回值，否则抛异常

     T orElse(T other):如果有值则将其返回，否则返回指定的other对象

     T orElseGet(Supplier<? extends T> other):如果有值则将其返回，否则返回由Supplier接口实现提供对象

     T orElseThrow(Supplier<? extends X> exceptionSupplier):如果有值则将其返回，否则抛出由Supplier接口实现提供的异常



