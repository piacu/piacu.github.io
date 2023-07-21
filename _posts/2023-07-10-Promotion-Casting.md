---

title: "[Java] Promotion & Casting"
author: piacu
date: 2023-06-13 18:00:00 +0900
categories: [Study, Computer Science]
tags: [study, cs, java]

---

## 들어가기 전에

Java는 데이터 타입을 지정하면 그에 맞는 값을 대입해야 합니다. 예로 다음과 같은 코드가 있습니다.

```java
int a = 1; // 정상
int b = 1.5; // 오류
        
System.out.println(1 + 1.5); // ?
```

b는 오류가 발생했습니다. 이유는 Type mismatch로 정수형에 실수 값을 넣으려고 했기 때문입니다.

하지만 프로그램을 짜다 보면 1 + 1.5 같은 정수와 실수 값을 더해야 될 때도 있을 것입니다. 소숫점 아래의 수를 버릴 수 없을 때, 항상 1을 1.0으로 변환해야 할까요?

위의 코드를 실행해보면 `2.5`가 출력됩니다. 이는 Java에서 자동으로 데이터 타입을 적절히 변경해주는 행위이며 **형변환**이라고 합니다.

이러한 형변환(타입변환)에는 크게 **자동 형변환(Promotion)** 과 **강제 형변환(Casting)** 이 있습니다. 또 다른말로 자동 형변환은 묵시적 타입 변환, 강제 형변환은 명시적 타입 변환이라고도 합니다.



## Promotion (자동 형변환, 묵시적 형변환)

> **묵시적**이란 '말이나 행동으로 직접 드러내지 않고 은연중에 뜻을 나타내보이는 것' 을 뜻한다.

묵시적 형변환은 개발자가 모르게 Java 자체적으로 형변환을 해준다는 뜻이다. 묵시적 형변환은 아래 그림처럼 표현 범위가 좁은 데이터 타입에서 넓은 타입으로(ex. int -> double)만 사용할 수 있다.![implicit_conversion](C:\Users\jszna\Desktop\implicit_conversion.png)

반대로 표현 범위가 넓은 타입에서 좁은 타입으로의 묵시적 변환은 지원하지 않는데, 이 이유는 데이터 타입의 크기에 있다.

`int`(4Byte) 형에서 `char`(2Byte) 형으로 변환한다고 할 때 4바이트에서 2바이트로 줄어드니 표현할 수 있는 범위가 좁아지게 된다.

예시는 편하게 unsigned로 계산하였다.

* `int` : 11111111 11111111 11111111 11111111 = 2147483647

* `char` : 11111111 11111111 = 256

만약에 Java에서 int를 char로 묵시적 형변환을 해줬다면 값이 엄청나게 변형되며 프로그램에서 심각한 문제를 초래할 수 있어 막아둔 것이다.

한 가지 의문점이 드는 건 long에서 float로 갈 때 8바이트 -> 4바이트로 가는 것이라 값이 깨질 것 같아서 테스트해봤는데 역시나였다. 왜 이렇게 만들었는지는 모르겠지만 구글에서 찾아봤더니 사용하지 말라는 것 같다..

```java
public class Test {
    public static void main(String[] args) throws IOException {
    	
    	long a = 987654321987654l;
    	float b = a;
    	double c = b;

    	System.out.println(a); // 987654321987654
    	System.out.println(b); // 9.876543E14
    	System.out.println(c); // 9.87654304825344E14
    }
}
```



## Casting (강제 형변환, 명시적 형변환)

>  **명시적**이란 '내용이나 뜻을 분명하게 드러내 보이는 것' 을 뜻한다.

이는 조건을 갖추지는 못했지만 형변환이 하고 싶을 때 쓰는 것으로 강제로 형변환을 적용하는 방식이다. 소괄호를 붙여서 표현한다.

```java
double a = 1.0;
int b = (int) a;
```

1.0이라는 실수 값을 정수형으로 변환할 때 8바이트에서 4바이트로 줄어드니 표현할 수 있는 범위가 좁아지게 되는 것으로 묵시적 형변환에서의 예시처럼 값이 깨질 수 있어 Java에서 자동적으로 해주지 않는다. 만약 2번째 줄에서 `(int)` 를 붙이지 않는다면 Type mismatch 오류가 발생한다.



## 형변환 연산

+, -, *, / 등 사칙연산을 할 때는 두 피연산자 중 표현범위가 넓은 데이터 타입으로 변환되어 연산이 진행된다.

1.0 이랑 3을 더하면 크기가 큰 double 타입으로 묵시적 형변환 되어 결과가 출력된다.

```
double a = 1.0;
int b = 3;
    	
System.out.println(a+b); // 4.0
```

하지만 내가 정수 값을 얻고 싶다면 두 가지 방법을 사용할 수 있겠다.

1. a를 형변환 - `System.out.println((int)a + b)`

2. 결과값을 형변환 - `System.out.println((int) (a + b))`



## 정리

**Promotion(자동 형변환, 묵시적 형변환)**

* 표현 범위가 좁은 데이터에서 넓은 데이터로 사용 가능
* Java에서 자동적으로 지원

**Casting(강제 형변환, 명시적 형변환)**

* 표현 범위가 넓은 데이터에서 좁은 데이터로 강제 사용
* 값이 깨질 위험성이 있어 Java에서 지원하지 않으며 개발자가 직접 소괄호를 사용해 지정해 줘야 함



## 참고자료

https://github.com/GimunLee/tech-refrigerator/blob/master/Language/JAVA/Promotion%20%26%20Casting.md#promotion--casting

https://okky.kr/articles/247028

https://20plus3.tistory.com/13
