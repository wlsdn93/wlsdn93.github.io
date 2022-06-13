---
title : "변수의 자료형이 갖는 의미"
excerpt : "변수를 정의하는 자료형과, 변수가 참조하고 있는 실제 객체를 구분하자"
author_profile: true
sidebar:
  nav: "sidebar"
category: Java
---
  
아래와 같은 코드를 작성하면 **`Intellij`**는 빨간 줄을 표시하며, **`Integer`** 타입의 값이 들어와야 하는데 **`String`**을 대입하려고 하는것은 문제가 있다고 친절히 알려준다.
  
```java
Integer[] intArr = new Integer[]{1, 2, 3, 4};
intArr[2] = "hello";
```  
  
에러를 해결해보자. **`intArr`**을 **`Object[]`** 변수에 옮겨 담은 뒤, 다시 문자열을 대입해보면 아무 문제가 없어보인다.  

```java
Integer[] intArr = new Integer[]{1, 2, 3, 4};
Object[] oArr = intArr;
oArr[2] = "hello";
System.out.println(Arrays.toString(oArr));
```
위 코드를 실행하면 어떤 결과가 나올까? [1, 2, hello, 4] 가 출력될까?  

정답은 "예외가 발생한다" 이다. 정확히 얘기 하자면 **`ArrayStoreException`**이 발생한다.  

위 코드에 문제 없다고 예상한 사람은 **`변수의 자료형`**과 변수가 참조하고 있는 **`객체의 자료형`** 을 동일시 하고 있기 때문이다.     

우리는 다형성에 의해 **`부모 타입 변수`**가 **`자녀 타입 객체`**를 참조 할 수 있다는 것을 알고 있다.   

```java
public class InheritanceExample {
    public static void main(String[] args) {
        Parent p = new Child();
        p.childMethod(); // impossible
    }
}
```
   
변수 **`p`**에 담겨있는 것은 분명 **`Child`** 타입의 객체 인데, 왜 자녀 클래스에 정의된 기능을 수행할 수 없을까?  

`Object[]` 타입 변수로 `Integer[]` 타입 객체를 참조했는데, 왜 `Object` 의 서브 클래스 타입들을 배열에 담을 수 없었을까?  
  
```java
Parent p = new Child();
```
  
위 코드를 그림으로 나타낸다면 아래와 같이 생각 할 수 있다.   

![image.png](/assets/images/java/inheritance.png)  
  
**변수의 자료형을 선언 하는 것은 `변수가 참조하고 있는 객체`가 수행 할 수 있는 `기능`과 `상태`를 `컴파일러`에게 알려주는 것이다.**  
  
컴파일러는 해당 변수가 실제로 어떤 값을 참조하고 있는지는 관심이 없다. 그저 우리가 작성한 코드를 **`JVM`**이 실행할 수 있는 바이트 코드인 **`class`** 파일로 변환해줄 뿐이다.    
  
그렇다면 **`overriding`** 된 메서드는 어떻게 실행 되는 걸까?    

> The Java virtual machine (JVM) calls the appropriate method for the object that is referred to in each variable. 
> It does not call the method that is defined by the variable's type. 
> This behavior is referred to as **[`virtual method invocation`](https://blogs.oracle.com/javamagazine/post/mastering-the-mechanics-of-java-method-invocation)** and demonstrates an aspect of the important polymorphism features in the Java language.
  
**`JVM`**은 **`overriding`** 된 메서드를 호출할 때, 변수가 참조하고 있는 **`객체의 자료형이 정의한 메서드`**를 **`변수의 자료형이 정의한 메서드`**보다 우선적으로 실행한다.  
  