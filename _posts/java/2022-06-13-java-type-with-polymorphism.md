---
title : "다형성 : 변수의 자료형이 갖는 의미"
excerpt : "변수의 속성을 정의하는 자료형과, 변수가 실제로 갖고 있는 값을 구분하자"
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
  
에러를 해결해보자. `intArr`을 `Object[]` 변수에 옮겨 담은 뒤, 다시 문자열을 대입해보면 아무 문제가 없어보인다.  

```java
Integer[] intArr = new Integer[]{1, 2, 3, 4};
Object[] oArr = intArr;
oArr[2] = "hello";
System.out.println(Arrays.toString(oArr));
```
위 코드를 실행하면 어떤 결과가 나올 까? [1, 2, hello, 4] 가 출력될 까?  

정답은 "예외가 발생한다" 이다. 정확히 얘기 하자면 **`ArrayStoreException`**이 발생한다.  

위 예시에서 예외가 발생하는 이유를 이해할 수 없다면 변수의 자료형과, 변수가 참조하고 있는 객체의 자료형을 동일시 하고 있기 때문이다.     

잠깐 다형성에 대해 간단히 짚고 넘어가자  
   
> The dictionary definition of polymorphism refers to a principle in biology in which an organism or species can have many different forms or stages.
> This principle can also be applied to object-oriented programming and languages like the Java language.
  
다형성이란 **"다양한 형태를 갖을 수 있는 성질"** 그 이상도 이하도 아니지만, 참 온갖 곳에서 다형성 이라는 단어를 사용한다.  
  
매개변수의 종류와 갯수가 다르고, 접근 제한자와 이름이 같은 메서드를 여러개 정의하는 **`overloading`** 은 메서드 레벨에서의 다형성이다.
  
부모 클래스를 상속받거나 혹은 인터페이스를 구현한 클래스가, 기존에 정의된 메서드의 수행방식을 재정의 하여 사용하는 **`overriding`** 은 클래스 레벨에서의 다형성이다.  
   
우리는 다형성에 의해 **`자녀 타입 객체`**를 **`부모 타입 변수`**에 할당 할 수 있다는 것을 알고 있다.   

```java
public class InheritanceExample {
    public static void main(String[] args) {
        Parent p = new Child();
        p.parentMethod(); // possible
        p.childMethod(); // impossible
    }
}
```
   
변수 **`p`**에 담겨있는 것은 분명 **`Child`** 타입의 객체 인데, 왜 자녀 클래스에 정의된 기능을 수행할 수 없을까?     
  
```java
Parent p = new Child();
```
  
위 코드를 그림으로 나타낸다면 아래와 같이 생각 할 수 있다.   

![image.png](/assets/images/java/inheritance.png) 

**즉 자료형을 선언 한다는 것은 해당 변수에 담겨있는 값이 수행 할 수 있는 '기능'과 '상태'를 JVM에게 알려주는 것이다.**  

그렇다면 **`overriding`** 된 메서드는 어떻게 실행 될 까?  

> The Java virtual machine (JVM) calls the appropriate method for the object that is referred to in each variable. 
> It does not call the method that is defined by the variable's type. 
> This behavior is referred to as virtual method invocation and demonstrates an aspect of the important polymorphism features in the Java language.
  
**`JVM`**은 오버라이딩된 동일한 이름의 메서드가 충돌했을 때, **'런타임 시점의 메서드(변수에 담겨있는 실제 객체의 메서드)'**를 **'컴파일 타임 시점의 메서드(변수의 자료형에 정의된 메서드)'**보다 우선적으로 실행한다.  
  