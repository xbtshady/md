## Java 反射

定义：JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法，这种动态获取、调用对象方法的功能称为java语言的反射机制。

反射是通过Class对象(字节码文件)，来知道某个类的所有属性和方法。也就是说通过反射我们可以获取构造器，对象，属性，方法（原本不知道）

### Class对象

![img](https://img2020.cnblogs.com/blog/1971784/202003/1971784-20200318213539251-923984642.png)

想使用反射，必须先得到代表的字节码的Class对象，Class类用于表示.class文件（字节码）

- 通过该类的对象去获取到对应的Class对象

  ```java
  //第一种方式: student--->Class对象  通过getClass()方法
  //Student是一个空类
  Student student = new Student();
  //这里我们就省去泛型了，Class<?>
  Class stuClass = student.getClass();  //获取到了对应的Class对象
  
  System.out.println(stuClass);
  System.out.println(stuClass.getName());  //获取Class对象的名字
  ```

  这种基本不用，这里显然和反射机制相悖（你有类对象 student 还去用反射获取Class类对象干嘛，多此一举）

- 通过类名.class静态属性获取

  ```java
  //第二种方式:  每个类创建后 都会有一个默认的静态的class属性 用于返回该类的class对象
  //需要注意的是:  任何数据类型（包括基本数据类型）都有“静态”的class属性
  Class stuClass2 = Student.class;
  System.out.println("是否为同一个class对象?"+(stuClass==stuClass2));
  ```

- 通过Class类中的静态方法 forName()方法获取

  ```java
  //第三种方式:  Class.forName("fanshe.Student");  注意参数一定为该类的全限定类名
  try {
  	Class stuClass3 = Class.forName("fanshe.Student");
  	//System.out.println(stuClass3); 输出仍然是class fanshe.Student
  	System.out.println("是否为同一个class对象?"+(stuClass3==stuClass2));
  } catch (ClassNotFoundException e) {
  	e.printStackTrace();
  }
  ```

### setAccessible

​	setAccessible(true);//忽略访问修饰符,解除私有限定

### 使用

- 获取到该类的构造器

  - Constructor getConstructor()
    - 返回的是构造方法对象
    - 可传入参数，根据构造器入参筛选构造器
  - Constructor[] getDeclaredConstructors() 
    - 返回 Constructor 对象的一个数组，这些对象反映此 Class 对象表示的类声明的所有构造方法
    - 可传入参数，根据构造器入参筛选构造器
  - Constructor[] getConstructors() 
    - 返回一个包含某些 Constructor 对象的数组，这些对象反映此 Class 对象所表示的类的所有公共（public）构造方法。
    - 可传入参数，根据构造器入参筛选构造器
  - Constructor#newInstance()
    - 获取到Constructor之后调用newInstance方法传入参数可创建对象

- 获取成员变量

  - Field[]  getDeclaredFields()
    - 返回 Field 对象的一个数组，这些对象反映此 Class 对象的所有字段
    - 可传入String(参数的名字)来获取特定字段
  - Field[]  getField()
    - 返回 Field 对象的一个数组，这些对象反映此 Class 对象的所有（public）字段
    - 可传入String(参数的名字)来获取特定（public）字段
  - Field#getType()
    - 返回这个变量的类型
  - Field#getGenericType()
    - 如果当前属性有签名属性类型就返回，否则就返回 `Field.getType()`
  - Field#set(Object obj, Object value)
    - 给字段赋值
    - 第一个入参是进行赋值的对象

- 获取方法

  - Method[] getDeclaredMethods()
    - 获取所有的方法
  - Method getDeclaredMethod(String name, Class<?>... parameterTypes)
    - 根据方法名和入参类型获取方法
  - Method[] getMethods()
    - 获取所有的(public)方法
  - Method getMethod(String name, Class<?>... parameterTypes)
    - 根据方法名和入参类型获取(public)方法
  - Method#invoke(Object obj, Object... args)
    - 调用方法

  

