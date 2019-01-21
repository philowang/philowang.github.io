# Java Generics

## 使用范型的目的

Java范型是JDK 1.5开始推出的一个新特性，主要用于限制集合类中元素的类型，确保集合中类型正确；此外，还可以增加代码的可维护性和健壮性。

在引入范型之前，集合的使用：

```java
List myIntList = new LinkedList();
myIntList.add(new Integer(10));
//强制类型转换，开发人员认为正确的转换，但实际并不一定正确
Integer n = (Integer)myIntList.itertor().next()；
```

使用范型的代码：

```java
List<Integer> myIntList = new LinkedList<Integer>();
myIntList.add(new Integer(10));
Integer n = myIntList.itertor().next();	//没有Integer的强制类型转换
```

使用范型的代码在变量的声明时，通过`<Integer>`表明了一个元素类型是`Integer`的集合，与之前的代码不同的是，`myIntList`是一个任意元素类型的集合。在取出集合中的元素时，也省去了繁琐的强制类型转换。范型的优势在于，与传统方法相比，编译器在编译期会检查集合类型，确保集合中元素类型的正确性；同时，省去了繁琐的强制类型转换（且该转换并不一定安全）。



## 范型的声明

### 接口/类

以下代码片段是从`java.util`包中选取出来的。

```java
//Itertor接口
public interface Itertor<E>{
    E next();
    boolean hasNext();
}

//List接口
public interface List<E>{
    public void add(E e);
    public Intertor<E> itertor();
}

```

与传统的接口相比，在接口声明处有一个以`<>`包围起来的**形式类型参数**`E`，在接口或类的声明内部，类型参数`E`可以像正常类型一样使用。

在调用范型类/接口时，形式类型参数（formal type parameter）都会被实际类型参数（actual type parameter），比如`List<Integer>`中，`E`出现的位置都会替换为`Integer`。但是这个替换又与传统的C++的模板不同，C++中的模板会根据类型参数扩展出多份类，而Java中范型对所有的调用只编译一次，也就是只有一份`class`字节码文件。Java中的范型可以与方法类比，方法只有一份，方法的形参相当于Java范型中的形式类型参数。

范型的形式类型参数一般选取单个大写字母，用于与其它类型作区分，集合中经常使用`E`代表Element，`T`代表Type，表中使用`K`, `V`，分别代表Key和Value。此外使用常用字母相邻的字母，比如`S`等。

### 方法

使用范型的方法可以使同一个方法作用于不同的数据类型，增加代码的复用性。



## 范型和子类型（Subtyping）

* 集合元素间有父子类型的关系，但元素通过范型构造出的集合之间并没有父子类型的关系，这种变化称为*不变性*。比如：

  ```java
  List<Object> lo = new LinkedList<Object>;
  List<Integer> li = new LinkedList<Integer>;
  ```

  虽然`Integer`是`Object`的子类，但是`List<Integer>`并不是`List<Object>`的子类。该结论的一个简单推理过程：如果`List<Integer>`不是`List<Object>`的子类，那么`List<String>`也是`List<Object>`的子类，这时就可以向`List<Object> lo`中加入一个`String`类型的元素，结果`li`集合中同时有`Integer`和`String`类型的元素，这显然不能通过编译器的类型检查。

## 通配符（Wildcard）

* `Object`是Java中所有其它类的父类，但是`List<Object>`并不是`List`集合的父类，为了表达接受多种元素是多种类型的`List`集合，通过在形式类型参数的位置处用`?`表示未知类型，`List<?>`用于接受多种类型的范型，这种类型称为wildcard type。
* ray type：没有指定范型的类型参数，作用与wildcard type相似，区别在于编译器不会对raw type进行严格的类型检查，用于支持范型代码调用JDK 1.5以下的不支持范型的代码。
* wildcard type的限制：因为`List<?>`表示元素是未知类型的集合，所以不能像该引用中增加元素，即调用该引用的`add(E e)`方法（`add(null)`例外），但可以正常调用`get()`方法。

## 约束（Bound）

通配符`?`用于表达任意类型，有时需要进一步限制元素类型，约束可以表达该需求。约束分上边界和下边界。

### `extends`

`extends`关键字表达上边界，用法可以用一下例子表示：

```java
public abstract Shape{
    public abstract void draw(Canvas c);
}

public class Circle extends Shape{
    public void draw(Canvas c){
        //drawing a circle on canvas
    }
}

public class Rectangle extends Shape{
    public void draw(Canvas c){
        //drawing a rectangle on canvas
    }
}

public class Canvas{
    public void draw(Shape s){
        s.draw(this);
    }
    
    //通过extends表达类型的上边界，接受元素类型是Shape子类的List集合
    //public void drawAll(List<Shape> ls)，只能接受元素类型为Shape的List集合
    //public void drawAll(List<?> ls)，不能确保集合元素有draw方法
    public void drawAll(List<? extends Shape> ls){
        for(Shape s : ls){
            s.draw(this);
        }
    }
}
```

以上定义中，`List<Circle>`， `List<Rectangle>`等其它元素是`Shape`子类的List集合均可作为实参传入`drawAll(List<? extends Shape>)`方法中。

### `super`

```java
public static <T extends Comparable<T>> T max(Collection<T> c);
public static <T extends Comparable<? super T>> T max(Collection<T> c);
public static <T extends Object & Comparable<? super T>> T max(Collection<? extends T> c);
```





