## Java 注解（Annotation）

#### 修饰符

和class、interface一样是一种类的类型，使用的修饰符为

@interface

#### 元注解

用于注解的注解

- @Retention

  - Retention英文意思有保留、保持的意思，它表示注解存在阶段是保留在源码（编译期），字节码（类加载）或者运行期（JVM中运行）。在@Retention注解中使用枚举RetentionPolicy来表示注解保留时期
  - @Retention(RetentionPolicy.SOURCE) 注解仅存在于源码中，在class字节码文件中不包含
  - @Retention(RetentionPolicy.CLASS)， 默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得
  - @Retention(RetentionPolicy.RUNTIME)， 注解会在class字节码文件中存在，在运行时可以通过反射获取到

- @Target

  - Target的英文意思是目标，这也很容易理解，使用@Target元注解表示我们的注解作用的范围就比较具体了，可以是类，方法，方法参数变量等，同样也是通过枚举类ElementType表达作用类型
  - @Target(ElementType.TYPE) 作用接口、类、枚举、注解
  - @Target(ElementType.FIELD) 作用属性字段、枚举的常量
  - @Target(ElementType.METHOD) 作用方法
  - @Target(ElementType.PARAMETER) 作用方法参数
  - @Target(ElementType.CONSTRUCTOR) 作用构造函数
  - @Target(ElementType.LOCAL_VARIABLE)作用局部变量
  - @Target(ElementType.ANNOTATION_TYPE)作用于注解（@Retention注解中就使用该属性）
  - @Target(ElementType.PACKAGE) 作用于包
  - @Target(ElementType.TYPE_PARAMETER) 作用于类型泛型，即泛型方法、泛型类、泛型接口 （jdk1.8加入）
  - @Target(ElementType.TYPE_USE) 类型使用.可以用于标注任意类型除了 class （jdk1.8加入）

-  @Document、 

  - Document的英文意思是文档。它的作用是能够将注解中的元素包含到 Javadoc 中去。

- @Inherited

  - Inherited的英文意思是继承，但是这个继承和我们平时理解的继承大同小异，一个被@Inherited注解了的注解修饰了一个父类，如果他的子类没有被其他注解修饰，则它的子类也继承了父类的注解。

- @Repeatable

  - Repeatable的英文意思是可重复的。顾名思义说明被这个元注解修饰的注解可以同时作用一个对象多次，但是每次作用注解又可以代表不同的含义。

  - @Repeatable的值是另一个注解，其可以通过这个另一个注解的值来包含这个可重复的注解。

  - @Repeatable 所声明的注解，其元注解@Target的使用范围要比@Repeatable的值声明的注解中的@Target的范围要大或相同，否则编译器错误，显示@Repeatable值所声明的注解的元注解@Target不是@Repeatable声明的注解的@Target的子集

  - @Repeatable注解声明的注解的元注解@Retention的周期要比@Repeatable的值指向的注解的@Retention得周期要小或相同。

    周期长度为 SOURCE(源码) < CLASS (字节码) < RUNTIME(运行)

  - 例子：

    ```java
    @Target(ElementType.METHOD)
    @Retention(RetentionPolicy.RUNTIME)
    @Repeatable(Values.class)
    public @interface Value {
        String value() default "value";
    }
    
    @Target(ElementType.METHOD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface Values {
        Value[] value();
    }
    
    @Value("hello")
    @Value("world")
    public void test(String var1, String var2) {
        System.out.println(var1 + " " + var2);
    }
    
    // 获取使用`@Value`注解的`test`方法，并打印这个方法上的注解长度和信息
    @Test
    public void testValue() {
        Method[] methods = AnnotationClass.class.getMethods();
        for (Method method : methods){
            if (method.getName().equals("test")) {
                Annotation[] annotations = method.getDeclaredAnnotations();
                System.out.println(annotations.length);
                System.out.println(method.getName() + " = " + Arrays.toString(annotations));
            }
        }
    }
    ```

    输出的结果为

    ```
    1
    test = [@com.example.annotations.Values(value=[@com.example.annotations.Value(value=hello), @com.example.annotations.Value(value=world)])]
    ```

    可以知道多个Value注解通过反射获取到的是一个Values注解

#### 注解的属性

- 注解的属性其实和类中定义的变量有异曲同工之处，只是注解中的变量都是成员变量（属性）

- 注解中是没有方法的，只有成员变量，

- 变量名就是使用注解括号中对应的参数名，变量返回值注解括号中对应参数类型。

- 例：

  ```java
  /**注解Repeatable源码*/
  @Documented
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.ANNOTATION_TYPE)
  public @interface Repeatable {
      /**
       * Indicates the <em>containing annotation type</em> for the
       * repeatable annotation type.
       * @return the containing annotation type
       */
      Class<? extends Annotation> value();
  }
  ```

#### 注解的本质

- 注解的本质就是一个Annotation接口

```java
/**Annotation接口源码*/
public interface Annotation {

    boolean equals(Object obj);

    int hashCode();

    Class<? extends Annotation> annotationType();
}
```

- 注解本身就是Annotation接口的子接口，**也就是说注解中其实是可以有属性和方法，但是接口中的属性都是static final的，对于注解来说没什么意义，而我们定义接口的方法就相当于注解的属性，也就对应了前面说的为什么注解只有属性成员变量，其实他就是接口的方法，这就是为什么成员变量会有括号**，不同于接口我们可以在注解的括号中给成员变量赋值。

### 注解属性类型

- 1.基本数据类型
- 2.String
- 3.枚举类型
- 4.注解类型
- 5.Class类型
- 6.以上类型的一维数组类型

### 获取注解属性

使用反射，主要有三个基本的方法

```java
    /**是否存在对应 Annotation 对象*/
    public boolean isAnnotationPresent(Class<? extends Annotation> annotationClass) {
        return GenericDeclaration.super.isAnnotationPresent(annotationClass);
    }

    /**获取 Annotation 对象*/
    public <A extends Annotation> A getAnnotation(Class<A> annotationClass) {
        Objects.requireNonNull(annotationClass);

        return (A) annotationData().annotations.get(annotationClass);
    }
    /**获取所有 Annotation 对象数组*/   
    public Annotation[] getAnnotations() {
        return AnnotationParser.toArray(annotationData().annotations);
    }    
```