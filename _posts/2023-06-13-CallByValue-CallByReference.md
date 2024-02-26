---

layout: post
title: "[Java] Call By Value vs Call By Reference"
tags: [study, cs, java]

---

## 들어가기 전에

Call By Value와 Call By Reference는 함수에서 다른 함수로 값을 넘겨줄 때 중요한 이론이다.

C언어 같은 경우에는 포인터를 사용하기 때문에 Value를 넘겨줄 수도, Reference를 넘겨줄 수도 있는데 Java는 **무조건** Value를 넘긴다고 한다. 이게 무슨 뜻인지에 대해 알아볼 것이다.



## Call By Value vs Call By Reference

C언어는 앞서 말했듯이 포인터를 사용하기 때문에 Call By Reference를 구현할 수 있다. 간단한 예시로 a와 b가 있을 때 서로의 값을 바꾸는 swap 함수를 구현한다고 해보자.

```c
#include<stdio.h>

void swap1(int a, int b){
	int tmp;
	
	tmp = a;
	a = b;
	b = tmp;
}

void swap2(int *a, int *b){
	int tmp;
	
	tmp = *a;
	*a = *b;
	*b = tmp;
}

int main(){
	int a = 3, b = 5;
	
	swap1(a, b); // Call By Value
	printf("a: %d, b: %d\n", a, b);
	
	swap2(&a, &b); // Call By Reference
	printf("a: %d, b: %d\n", a, b);
	
	return 0;
}
```

swap1 함수는 Call By Value

swap2 함수는 토인터를 사용하여 Call By Reference로 구현하였다.

```
a: 3, b: 5 // Call By Value
a: 5, b: 3 // Call By Reference
```

결과는 위처럼 나오게 됐다. 둘 다 서로의 값을 바꾸는 건데 방식에 따라 결과도 다르게 나올 수 있다.

Value로 보낸 것은 값을 복사해서 넘기기 때문에 swap 함수 내에서 서로의 값을 바꾸더라도 main함수에서는 바뀌지 않았다. 이는 a, b값 자체만을 넘기기 때문에 스택메모리에 변수에 대한 값이 복사가 되어 원본과는 별개가 되기 때문이다.

Reference로 주소값만 보낼 때는 메모리에 저장되어 있는 a, b의 메모리 주소 값을 가져가는 것이기 때문에 swap 함수에서 a, b의 내용을 바꾸게 되면 원본 값도 바뀌게 되는 것이다.

그림으로 표현하자면 아래와 같다.

![image](https://github.com/piacu/piacu.github.io/assets/26267376/c385bb5f-8406-4640-b038-248e91937732)



## Java의 Call By Reference?

Java는 포인터 개념을 없애버렸다. 하지만 원본 데이터에 대한 접근은 필요하기에 참조라는 개념을 사용한다. 포인터, 참조 둘 다 원본 데이터에 접근하는 기능을 가진다. 다만, **포인터는 메모리를 직접 다룰 수 있지만 참조는 직접 다룰 수 없다**는 차이점이 있다.

그렇다면 힙(heap) 메모리에 올라가는 객체나 배열 등을 다른 함수의 매개변수로 사용하면 해당 객체의 주소값이 넘어갈 테니 포인터 비스무리하게 쓸 수는 없을까? 하는 의문이 생긴다.

아래의 예제를 보면서 확인해보자.

```java
class Num {
	int num1;
	int num2;
	
	public Num(int num1, int num2) {
		this.num1 = num1;
		this.num2 = num2;
	}	
}

public class Test {
    public static void main(String[] args) throws IOException {
    	Num a = new Num(3, 5);
    	Num b = new Num(3, 5);
        
        change(a, b);
        
        System.out.println("Num a [num1=" + a.num1 + ", num2=" + a.num2 + "]");
        System.out.println("Num b [num1=" + b.num1 + ", num2=" + b.num2 + "]");
    }

	private static void change(Num a, Num b) {
		a = new Num(99, 88);
		
		b.num1 = 99;
		b.num2 = 88;
	}
}
```

```
Num a [num1=3, num2=5]
Num b [num1=99, num2=88]
```

Num a를 출력했을 때 바뀌지 않은 이유는 결론적으로 change() 메소드로 넘어가게 될 때 **주소값을 복사**해서 넘겨주게 되기에 의 Num a는 a의 내용이 아닌 a의 주소값을 가지고 있는 것이 된다. 여기서 a = new Num(99, 88)를 실행하면 a에 새로운 Num을 할당하는 게 아니라, new Num(99, 88)으로 새로운 메모리 공간을 할당하고 할당된 공간의 주소를 a에 대입(연결)하게 되는 것이다.

b같은 경우에는 b의 주소값에서 객체 프로퍼티를 직접 참조해서 내용을 바꾸는 것이기 때문에 값의 변경이 정상적으로 가능하다.



이처럼 자바와 C의 변수 할당 방식은 다르다.

C/C++에서는 생성한 변수마다 새로운 메모리 공간을 할당하고 이에 값을 덮어씌우는 형식으로 값을 할당한다. (포인터를 사용한다면, 같은 주소값을 가리킬 수 있도록 할 수 있다.)

Java에서 또한 생성한 변수마다 새로운 메모리 공간을 갖는 것은 마찬가지지만, 그 메모리 공간에 값 자체를 저장하는 것이 아니라 값을 다른 메모리 공간에 할당하고 이 주소값을 저장하는 것이다.

**C**

```c
#include<stdio.h>

int main(){
	int a = 10, b = a;
	
	printf("a: %d, b: %d\n", &a, &b);
	
	a = 11;
	b = 10;
	
	printf("a: %d, b: %d\n", &a, &b);
	
	return 0;
}
```

결과

```
a: 6487580, b: 6487576
a: 6487580, b: 6487576
```

**Java**

```java
public class Test {
    public static void main(String[] args) throws IOException {
        int a = 10;
        int b = a;
        
        System.out.println("a: "+System.identityHashCode(a)+", b: "+System.identityHashCode(b));
        
        a = 11;
        b = 10;

        System.out.println("a: "+System.identityHashCode(a)+", b: "+System.identityHashCode(b));
    }
}

```

결과

```
a: 32374789, b: 32374789
a: 1694819250, b: 32374789
```



## 정리

Call By Value와 Call By Reference는 각자의 특성이 있다.

*Call By Value* : 값을 복사하기 때문에 속도가 Call By Reference 대비 느림. 직접 참조이기 때문에 원본 값이 바뀌지 않음

*Call By Reference* : 복사하지 않고 주소값을 넘겨 참조를 하기 때문에 Call By Value 대비 속도가 빠름. 원본값이 바뀔 수 있다는 위험이 있음



이는 C와 같은 포인터가 있는 언어에서 활용되는 것이고, 자바의 경우에는 Call By Value이기 때문에 변수 할당 방식 정도만 기억하고 있으면 프로그램을 만들 때 도움이 될 듯 하다.



## 참고자료

https://sorjfkrh5078.tistory.com/278

https://github.com/gyoogle/tech-interview-for-developer/blob/master/Language/%5Bjava%5D%20Call%20by%20value%EC%99%80%20Call%20by%20reference.md

https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EB%8A%94-Call-by-reference-%EA%B0%9C%EB%85%90%EC%9D%B4-%EC%97%86%EB%8B%A4-%E2%9D%93

https://mangkyu.tistory.com/106
