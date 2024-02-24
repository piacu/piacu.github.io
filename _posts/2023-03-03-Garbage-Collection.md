---

layout: post
title: "[Java] Garbage Collection"
tags: [study, cs, web, java]

---

## 가비지 컬렉션이란?

`C/C++` 언어에서는 객체를 생성(메모리 할당)한 후 사용하지 않는(사용이 끝난) 객체를 개발자가 free()라는 함수로 직접 메모리 해제를 해줘야 했다. 그렇지 않으면 메모리 누수가 발생하고 이는 다른 프로그램에 영향을 끼칠 수도 있었다.

이 같은 까다로운 개발환경을 벗어나고자 `Java`에서는 JRE(Java Runtime Environment, 자바 실행 환경)에서 Garbage Collector(GC)가 등장하였는데, 이 GC는 더 이상 사용하지 않는 메모리 영역을 알아서 해제시켜 주는 **`쓰레기 수집`**과 같은 편리한 기능을 제공해 주게 된다.



### 쓰레기 수집 대상

GC의 메모리 해제 대상은 다음과 같다.

* 객체가 NULL인 경우 (ex. String str = null)

* 블럭 실행 종료 후, 블럭 안에서 생성된 객체

* 부모 객체가 NULL인 경우, 포함하는 자식 객체



### GC의 구조

JVM의 메모리는 크게 Class, Heap, Stack, PC, Native Method 5개로 구분되는데 Garbage Collector는 Heap 영역에 위치한다.

<img src="https://velog.velcdn.com/images%2Frecordsbeat%2Fpost%2F682408fc-f29e-42e9-b980-3d6f1d6c4989%2Fimage.png" alt="img" style="zoom: 67%;" />

Heap 영역에서 GC는 Young, Old, Permanent Generation을 가지고 있다.

1. Young 영역(Yong Generation)

   새롭게 생성된 객체의 대부분이 여기에 위치하며 대부분의 객체가 금방 접근 불가능 상태(일회성)가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라지게 된다. 이 영역에서 객체가 사라질 때 **Minor GC** 가 발생한다고 말한다.

   * Eden : Young 영역 중 가장 최근에 생성된 객체들이 모여있는 곳
   * Survivor1,2 : Eden 영역에서 생존한 객체들이 Old에 가기 전에 모여있는 곳

2. Old 영역(Old Generation)

   접근 불가능 상태로 되지 않아 Young 영역에서 특정 횟수 이상을 살아남은 객체가 여기로 복사된다. 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생하게 된다. 이 영역에서 객체가 사라질 때 **Major GC(혹은 Full GC)** 가 발생한다고 말한다.

3. Permanent 영역(=Method Area)

   JVM이 class들과 method들을 설명하기 위해 필요한 metadata들을 포함하고 있다.(Java8부터는 제거됨)



### GC의 메모리 해제 과정



![img](https://blog.kakaocdn.net/dn/Cyho2/btqURvZRql6/4a7u6mMGofkpuURKQz0RT1/img.png)



1. 새로 생성된 객체가 Eden 영역에 할당된다.
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행된다.
   1. Eden 영역에서 사용되지 않는 객체의 메모리가 해제된다.
   2. Eden 영역에서 살아남은 객체는 1개의 Survivor 영역으로 이동된다.
3. 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor(ex. Survivor1 -> Survivor2) 영역으로 이동시킨다.(한 개의 Survivor 영역은 반드시 빈 상태가 된다.)
4. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동된다.



### GC의 장단점

#### 장점

- 유효하지 않은 포인터 접근: 이미 해제된 메모리에 접근하는 버그를 가리킨다. 만약 이 포인터가 해제되고 새로운 값이 할당되었다면, 잘못된 값을 읽어오게 된다.
- 이중 해제: 이미 해제된 메모리를 또다시 해제하는 버그를 가리킨다. 일부 메모리 할당 알고리즘에서는, 해제된 메모리를 다시 해제하려고 시도하는 것은 오류를 일으킬 수 있다.
- 메모리 누수: 더 이상 필요하지 않은 메모리가 해제되지 않고 남아있는 버그를 가리킨다. 메모리 누수가 반복되면 메모리 고갈로 프로그램이 중단될 수 있다. (접근 가능한 메모리가 증가하여 메모리가 고갈되는 문제는 쓰레기 수집으로도 막을 수 없다)

#### 단점

- 어떤 메모리를 해제할지 결정하는 데 비용이 든다. 객체가 필요 없어지는 시점을 프로그래머가 미리 알고 있는 경우에도 쓰레기 수집 알고리즘이 메모리 해제 시점을 추적해야 하므로, 이 작업은 오버헤드가 된다.
- 쓰레기 수집이 일어나는 타이밍이나 점유 시간을 미리 예측하기 어렵다. 때문에 프로그램이 예측 불가능하게 일시적으로 정지할 수 있다. 이런 특성은 특히 실시간 시스템에는 적합하지 않다.
- 할당된 메모리가 해제되는 시점을 알 수 없다. 자원 할당과 변수 초기화를 일치하는 RAII(Resource Acquisition is Initialization) 스타일의 프로그래밍에서는, 이것은 자원 해제 시점을 알 수 없다는 것을 의미한다.
