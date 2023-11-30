# Annotation 与 反射

## 内置注解

### Override
> 重写

### Deprecated
> 不建议使用

### SuppressWarnings
> 镇压警告

## 元注解
注解其它注解的注解

### Target
> 注解的使用范围

### Retention
> 描述注解的生命周期
+ SOURCE
+ CLASS
+ RUNTIME

### Documented
> 包含到文档javadoc中

### Inherited
> 说明子类可以继承父类中的该注解

## 示例
[完整注释](https://www.bilibili.com/video/BV1p4411P7V3?p=16)

```java
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

public class Test
{
   public static void main(String[] args)
   {

   }

   /**
   * Test
   * 测试一下注解
   *
   * @return void
   */
   @MyAnnotation(name="小昊子")
   public void test () {

   }
}

/**
* 定义一个注解
*/

// Target 表示我们的注解可以用在哪些地方
@Target(value={ ElementType.METHOD, ElementType.TYPE })

// Retention 表示我们的注解在哪些地方还有效
@Retention(value=RetentionPolicy.RUNTIME)

// Documented 表示是否将我们的注解生成到JavaDoc中
@Documented

// 子类可以继承父类的注解
@Inherited
@interface MyAnnotation {
   // 注解的参数:参数类型 + 参数名
   String name() default "";
}

```

## 理解
> 方法的设计我觉不是反人类，handle然后invoke这个是经典的观察者模式，乍看反人类的，但是从代码设计的角度是很好的选择。
> 而且对于任何注解，它不应该仅仅是属性的提取，而是对属性的目的进行抽象。
> 所以具体要做什么才是重点，这个时候选择方法加观察者模式能非常好的解决这一问题。
> 个人也可以定义自己的注解满足某种场景
