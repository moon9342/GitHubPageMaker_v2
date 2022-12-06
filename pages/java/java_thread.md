---
title: Java Thread
sidebar: java_sidebar
summary: "Java Thread에 대한 설명입니다."
permalink: java_thread.html
folder: java
---

## Java Thread 개요

Java의 `Thread`에 대해서 살펴보겠습니다. Thread가 무엇이고 
중요한 용어는 무엇인지 알아 본 후 Java Thread class를 정의하는 방법부터 시작해서
Thread의 다양한 method가 어떠한 역할을 하는지를 중점적으로 살펴볼 것입니다.

---

## 기본용어

우리가 많이 들어본 `Process`, `Thread`, `Multitasking`, `Multithreading` 이 4가지 기본적인 용어부터 알아봐야 할 듯 합니다.

### Process & Thread

`Process`(프로세스)란 간단히 말해 실행중인 프로그램을 의미합니다. 프로그램을 실행하면
OS로부터 실행에 필요한 `resource`를 할당받아 프로세스가 동작하게 됩니다.
<br><br>

여기에서 말하는 resource는 다음과 같은 4가지를 의미합니다.
<br>
* `Code` : 실행 명령어 코드
* `Data` : global variable 혹은 static variable
* `Heap` : 동적할당 메모리 영역
* `Stack` : 함수 혹은 메소드 실행 시 필요한 지역변수를 위한 영역

<br>

이렇게 process는 필요한 resource와 이런 resource를 이용해서 실제로 작업을 수행하는
`thread`로 구성됩니다.
<br><br>

다시말하면 모든 process에는 최소한 하나 이상의 thread가 존재하며, 둘 이상의 thread를
가진 process를 `multi-thread process`라고 합니다.
<br><br>

아주 쉬운예로 process는 공장이라고 표현할 수 있고 thread는 공장에서 일하는 일꾼으로
표현할 수 있습니다.

{% include image.html
file='java-thread-process-thread-concept.png'
%}
<br>

그렇다면 하나의 process가 가지는 thread의 수는 제한이 있을까요? 사실 thread의 수에는 제한이 없지만
실제적으로 thread가 실행되려면 개별적인 메모리 공간(`call stack`)이 필요하기 때문에 process가
가지는 메모리 한계에 따라 생성할 수 있는 thread의 수는 제한됩니다.

### Multitasking & Multithreading

우리가 사용하는 Windows 혹은 Linux OS는 여러 개의 process를 동시에 실행시킬 수 있습니다.
하지만 가만히 생각해보면 실제 일을 수행하는 `CPU`의 `core`는 한 순간에 단 한개의 작업만 수행할
수 있습니다. 이 core가 상대적으로 훨씬 많은 여러개의 process를 시간적으로 동시에 수행할 수는
없습니다. 그럼 어떻게 마치 동시에 실행되는 것처럼 할 수 있을 까요?
<br><br>

시분할(`time-slicing`)기법을 이용해서 아주 짧은 시간동안 여러 process를 번갈아 가면서
수행시키면 마치 동시에 실행되는 것처럼 여겨지게 됩니다. 이 방식을 우리는 `Multitasking`(다중작업)이라고
합니다.
<br><br>

이와 비슷하지만 서로 다른 core가 서로 다른 process를 시간적으로 동시에 실행시키는 것을 우리는
`Multiprocessing`이라고 부릅니다.
<br><br>

이와 비슷한 개념으로 하나의 process안에 여러개의 thread가 동시에 작업할 수 있는데 이를
`Multithreading`이라고 부릅니다. 결국 시분할기법을 이용해 core가 하나의 process안에서 여러개의
thread를 번갈아 가면서 실행시켜 마치 동시에 실행되는 것처럼 여겨지는 것이죠.
<br><br>

위에서 설명한 `cpu`, `process`, `thread`의 개념을 정리해서 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='java-thread-cpu-process-thread-concept.png'
%}
<br>

기본적인 설명은 위와 같습니다. 결국 Core하나당 Thread 한개를 담당할 수 있다는 말이네요. 하지만 요즘
나온 CPU의 Spec을 보면 `6 Core / 12 Thread` 이런 식으로 표기가 되어 있습니다. 컴퓨존 같은 사이트에서
CPU를 검색해서 CPU사양을 보시면 됩니다. 
<br><br>

코어와 쓰레드에 대한 개념을 조금 더 정확히 이해 하려면 `HyperThreading`(하이퍼쓰레딩)이라는 기능을 알아야 합니다.
<br><br>

이 기술은 원래 한개의 코어에서 일을 하는 동안 남는 잉여자원을 활용하는 기술입니다.
즉 core를 효율적으로 사용하기 위한 기술로 이 기능의 유무에 따라 CPU의 종류가 달라지고 가격차도 당연히 많이 나게 됩니다.
<br><br>

쉽게 말하면 원래는 한개의 core는 1 thread 로써 한가지 일만 할 수 있었지만 하이퍼쓰레딩 기술을 적용하여 
한개의 코어를 2개의 쓰레드로 나눔으로써 두가지 작업을 동시에 할 수 있게 됬다는 것이죠.
<br><br>

따라서 하이퍼쓰레딩이 적용된 모델인 경우 한개의 코어가 2쓰레드로 동작하기 때문에 듀얼코어는 2코어 4쓰레드가 되는것이고
쿼드코어는 4코어 8쓰레드가 되게 됩니다. 
<br><br>

당연한 말이지만 하이퍼쓰레딩이 적용된 2코어4쓰레드와 일반 4코어4쓰레드의 경우 같은 동클럭이라면 
실제 4코어 4쓰레드가 더 좋은 CPU입니다. 
<br><br>

내 CPU가 많은 쓰레드를 지원한다고 해도 그것을 활용하지 못하면 싱글코어나 마찬가지 입니다.
즉, 내 컴퓨터에서 사용하는 소프트웨어가 다중코어를 지원해야만 많은 쓰레드의 CPU의 의미가 있습니다.
<br><br>

만약 소프트웨어에서 다중쓰레드를 지원한다면 쓰레드가 많은 CPU가 유리하다고 생각하면 됩니다.
<br><br>

예를 들어 스타크래프트2 같은 경우는 아직도 다중쓰레드 지원이 되질 않습니다. 옛날 게임에 그래픽만 변경한 것이기 때문이죠.
그래서 클럭이 높은 저렴한 듀얼코어만 사용해도 쿼드코어 이상의 비싼 CPU들에 비해 게임성능이 별반 차이가 없게 되는것이죠.
<br><br>

일반적으로 core 갯수가 많을수록 CPU클럭은 낮게 적용되어 나오기 때문에 다중코어에 저클럭보다는 코어 갯수가 적은 고클럭이 더 좋을수가 있습니다.
대표적으로 발열때문에 그러하죠.

---

## Multithreading의 장단점

`Multithreading`은 일반적으로 다음과 같은 장점을 가집니다.
<br><br>

* `CPU`와 `resource`의 효율적인 사용(thread는 resource를 공유)
* 사용자에 대한 응답성 향상

<br>

하지만 프로그램을 정교하게 작성하지 않으면 다음과 같은 점이 문제가 됩니다.
<br><br>

* process내에서 resource를 공유하기 때문에 발생하는 동기화 문제(`synchronization`)
* 두 개의 thread가 resource를 점유한 상태에서 서로 상대편이 점유한 resource를
  사용하려고 기다리느라 수행이 멈춰있는 교착상태(`deadlock`)

<br><br>
교착상태는 현재 화물연대 파업상황으로 예를 들어볼 수 있습니다. 노동계는 대화를 통해 요구사항을 관철하려하고 있고
대화가 될 때 까지 파업을 진행한다는 입장이죠. 반대로 정부는 파업을 풀고 업무로 복귀해야 대화를 진행한다는 입장입니다. 
<br><br>

즉, 서로 끝이 나지 않는 기다림이 유지되는 현상이 발생하는 것입니다. 이 상황이 Process에서 일어나는 것입니다. 다음그림처럼
말이죠.

{% include image.html
file='java-process-deadlock.png'
%}
<br>

조금 더 진행하자면 이런 교착상태를 해결하는 방안은 크게 4가지가 있습니다.
<br><br>

교착상태 예방, 교착상태 회피, 교착상태 탐지, 교착상태 복구 이렇게 말이죠. 이 중 가장 대표적인 교착상태 회피에 대해서 잠깐
언급하고 넘어가죠. 교착상태 회피는 데드락에 빠질 가능성이 있는지 없는지를 운영체제가 검사하고 빠질 가능성이 없는 경우에만
자원을 할당함으로 문제 발생을 피하는 방법입니다. 
<br><br>

대표적인 알고리즘으로 은행원 알고리즘(Banker's Algorithm)이 있습니다. 
<br><br>

간단하게 예를 들어보죠.
<br><br>

이 예에서는 안전상태(Safe State)와 불안정상태(UnSafe State)라는 개념이 등장합니다. 
<br><br>

아래의 그림을 보죠.

{% include image.html
file='java-safe-state-banker-algorithm.png'
%}
<br>

위의 그림을 잘 이해해보면 모든 고객에게 돈을 빌려주고 은행이 다시 돈을 돌려받을 수 있는
안전 순서열이 존재합니다. 이런 안전 순서열이 존재하는 상태를 우리는 `Safe State`라고 합니다. 좋은거죠.
<br><br>

반면 아래 그림처럼 첫번째 사람에게 $20가 아닌 $35를 빌려줬다고 가정하면 안전 순서열이 존재하지 않게 됩니다.
이런 경우를 불안전상태(Unsafe State)라고 합니다. 즉, 안전순서열이 존재하지 않는 상태를 의미합니다. 
불안전상태는 `deadlock`이 되기 위한 필요조건입니다. 즉, 교착상태는 불안전상태에서만 발생한다는 말입니다.
(즉, Unsafe State라고 무조건 deadlock이 발생한다는 의미는 아닙니다.)
<br><br>

{% include image.html
file='java-safe-unstate-banker-algorithm.png'
%}
<br>

위의 예제를 OS와 메모리에 비유하면 어떤 상황인지 이해하실 수 있을겁니다. 
<br><br>

Banker's Algorithm은 이런 불안정상태에 빠지지 않을려면 어떻게 자원을 배분해야하는 가를 정해놓은 프로그래밍
규칙(순서)입니다. 즉, 불안정상태를 만들지 않도록 알고리즘을 고안해서 데드락을 회피하자는 것이죠. 해당 알고리즘의
실제 내용은 여기서 언급하지는 않겠습니다. 
<br><br>

이 얘기를 언급한 이유는 OS를 공부한다는 것이 어떤것을 공부하는 것인지(정보처리기사시험의 운영체제) 그리고 알고리즘이
어떤 것인지를 간접적으로 알려드리기 위함입니다. 
<br><br>

자 이제 다시 우리 내용으로 돌아가기로 하죠.

---

## Thread의 구현과 실행

`Thread`를 구현하는 방법은 크게 2가지 입니다. Thread class를 상속하는 방법과 `Runnable` interface를
구현하는 방법입니다. Thread class를 상속하는 방법은 다른 class를 상속할 수 없기 때문에(단일 상속)
이 방식보다는 재사용성과 class의 결합을 느슨하게 만들 수 있는 Runnable interface를 구현하는 방법이
조금 더 객체지향적이라고 할 수 있습니다.

~~~java

class MyThread extends Thread {
	
    // Thread class의 run() overriding
	
    public void run() {
        /* Thread의 수행 내용 */
    }

}

~~~

~~~java

class MyThread implements Runnable {
	
    // Runnable interface의 run() overriding
	
    public void run() {
        /* Thread의 수행 내용 */
    }

} 

~~~

Thread를 구현한다는 것은 어느 방법을 사용하던지 그저 Thread를 통해 작업하고자 하는 내용을
`run()` method를 통해 구현하는 것입니다.
<br><br>

간단한 예제를 실행시켜보죠.

~~~java

package sampleProj;

class ThreadEx_01_1 extends Thread {
    
    @Override
    public void run() {
        for(int i=0; i<5; i++) {
            // Thread class의 getName() method 호출
            System.out.println(getName());
        }
    }
}

class ThreadEx_01_2 implements Runnable {
    
    @Override
    public void run() {
        for(int i=0; i<5; i++) {
            // 현재 실행중인 Thread를 return받아서 getName() method 호출
            System.out.println(Thread.currentThread().getName());
        }
    }
}


public class ThreadExam01 {

    public static void main(String[] args) {

        ThreadEx_01_1 t1 = new ThreadEx_01_1();
        
        Runnable r = new ThreadEx_01_2();
        Thread t2 = new Thread(r);
        
        t1.start();
        t2.start();

    }

}

// 실행결과. 항상 이렇게 수행되는건 아니라는 것에 주의.

//Thread-0
//Thread-0
//Thread-0
//Thread-0
//Thread-0
//Thread-1
//Thread-1
//Thread-1
//Thread-1
//Thread-1

~~~

위의 코드에서 Thread의 생성과 실행에 대해 주의해야 합니다. 코드에서 볼 수 있듯이
Thread를 생성했다고 해서 자동으로 실행되지 않습니다. `start()` method를 호출해야지만
Thread가 실행됩니다.
<br><br>

정확히 말하면 `start()`가 호출되었다고 해서 바로 Thread가 실행되는 것은 아닙니다.
일단 `Runnable`(실행대기 상태)에 있다가 `Thread scheduler`에 의해서 순서에 따라 실행됩니다.
일반적으로 `Runnable` 상태의 Thread들은 `Thread Queue`안에 순차적으로 들어있다가
실행될때 `Thread scheduler`에 의해서 실행됩니다.
<br><br>

추가적으로 한번 Thread의 실행이 종료되면 해당 Thread는 `dead` 상태가 되어 다시
실행할 수 없습니다. 즉 하나의 Thread에 대해서는 start()를 단 1번만 호출 할 수
있습니다.
<br><br>

Thread는 실행 과정에 따라 그 상태가 계속 변하게 되는데 현재 까지의 상태전이를
그림으로 표현하면 다음과 같습니다.

{% include image.html
file='java-thread-thread-state-1.png'
%}

---

## start()와 run()

위에서 설명했다시피 Thread를 실행시키기 위해서는 `run()`이 아닌 `start()`를
호출합니다.
<br><br>

만약 `main()` 메소드에서 run()을 직접 호출하게 되면 이는 생성된 Thread를
실행시키는 것이 아니라 단순히 class에 선언된 method를 호출하는 것입니다.

~~~java

package sampleProj;

class ThreadEx_02 extends Thread {
    
    // Thread의 이름을 설정
    public ThreadEx_02(String name) {
        this.setName(name);
    }
    
    @Override
    public void run() {
        for(int i=0; i<5; i++) {
            // Thread class의 getName() method 호출
            System.out.println(getName());
        }
    }
}

public class ThreadExam02 {

    public static void main(String[] args) {

        ThreadEx_02 t1 = new ThreadEx_02("My-Thread");
        
        t1.run();   // run()을 직접 호출

    }
}

~~~

그림으로 표현하면 아래의 그림과 같습니다. `call stack`을 살펴보면 좀 더 명확합니다.

{% include image.html
file='java-thread-main-call-run.png'
%}
<br>

반면에 `start()` 메소드는 새로운 Thread가 작업을 실행하는데 필요한 call stack을
생성한 다음 run()을 호출해서 생성된 call stack에 run()이 첫번째로 올라가게 합니다.
<br><br>

모든 Thread는 독립적인 작업을 수행하기 위해 자신만의 call stack을 필요로 하기 때문에
Thread을 생성하고 실행시킬 때마다 새로운 call stack이 생성되고 Thread가 종료되면
해당 call stack은 소멸되게 됩니다. 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='java-thread-main-call-start.png'
%}
<br>

위의 그림에서 알 수 있듯이 `main()` 메소드의 작업을 수행하는 것도 하나의 Thread입니다.
이를 `main thread`라고 합니다. 사실 지금까지 우리는 계속 Thread를 사용해 오고 있던 셈이죠.
<br><br>

즉, 우리의 자바 프로그램을 실행하면 기본적으로 하나의 Thread를 생성하고 해당 Thread가
main() 메소드를 호출해서 작업이 수행되도록 하는 것입니다.
<br><br>

지금까지는 main() method의 수행이 종료되면 프로그램이 종료되었지만 이제는 조금 달리 생각해야 합니다.
main() method가 수행을 마쳤다 하더라도 다른 Thread가 아직 작업을 마치지 않은 상태라면 프로그램은
종료되지 않습니다.

> 실행 중인 user thread(사용자 Thread)가 하나도 없을 때 프로그램은 종료됩니다.

참고로 Thread는 크게 2가지 종류가 있는데 `user thread`(사용자 Thread, 혹은 non-daemon thread)와
`daemon thread`로 구분할 수 있습니다. 각 thread의 특징은 뒤쪽에서 다시 알아보도록 하겠습니다.

---

## Single Thread vs. Multi Thread

이번에는 single thread process와 multi thread process의 차이를 알아보도록 하겠습니다.
<br><br>

두 개의 작업(A,B)을 하나의 thread로 처리하는 경우와 두 개의 thread로 처리하는 경우를 가정해보죠.
<br><br>

하나의 Thread로  두 작업을 처리하는 경우는 한 작업을 마친 후에 다른 작업을 시작하지만,
두 개의 Thread로 작업 하는 경우에는 짧은 시간동안 2개의 Thread가 번갈아 가면서 작업을
수행해 동시에 두 작업이 처리되는 것처럼 느껴지게 됩니다.
<br><br>

아래의 그림은 `single core`일 때 single thread process와 multi thread process의
비교 그림입니다.

{% include image.html
file='java-thread-single-core-thread-execution.png'
%}
<br>

위의 그림에서 알 수 있듯이 single core에서는 하나의 Thread로 두 개의 작업을 수행한
시간과 두개의 Thread로 두 개의 작업을 수행한 시간이 거의 같습니다. 오히려 두 개의
Thread로 작업한 시간이 하나의 Thread로 작업한 시간보다 더 걸리게 되는데 그 이유는
Thread간의 `Context Switching`(작업 전환)이 발생하기 때문입니다.
<br><br>

하지만 `multi core`에서는 조금 달라집니다. multi core에서 multi thread로 작업을
수행하면 시간상 동 시간에 두 개의 Thread가 실행될 수 있기 때문에 아래 그림과 같이
두 작업이 겹치는 부분이 발생할 수 있기 때문에 수행시간이 단축됩니다.

{% include image.html
file='java-thread-multi-core-thread-execution.png'
%}

---

## Thread Priority(우선순위)

Thread는 priority(우선순위)라는 field를 가지고 있습니다. 이 값에 따라 Thread가
얻는 실행시간이 달라지게 됩니다. 따라서 Thread가 수행하는 작업의 중요도에 따라
Thread의 우선순위를 서로 다르게 지정하면 특정 Thread가 더 많은 작업시간을 갖도록
할 수 있습니다.

~~~java 

    // Thread의 우선순위를 지정한 값으로 변경
    void setPriority(int newPriority)
    
    // Thread의 우선순위를 반환
    int getPriority() 
    
    public static final int MAX_PRIORITY = 10  // 최대우선순위
    public static final int MIN_PRIORITY = 1   // 최소우선순위
    public static final int NORM_PRIORITY = 5  // 보통우선순위

~~~

Thread가 가질 수 있는 우선범위는 1~10이며 순서가 높을 수록 우선순위가 높습니다.
또 한가지 기억해야 할 점은 Thread의 우선순위는 Thread를 생성한 Thread로부터
상속됩니다. main thread의 우선순위가 5이므로 main thread에서 생성하는 thread의
우선순위는 자동적으로 5가 됩니다.
<br><br>

또한 Thread의 우선순위는 Thread가 실행되기 전에만 변경할 수 있습니다.
<br><br>

그럼 우선순위를 변경해서 Thread를 실행시켜 보죠.

~~~java

package sampleProj;

class ThreadEx_03 extends Thread {
    
    // Thread의 이름을 설정
    public ThreadEx_03(String name) {
        this.setName(name);
    }
    
    @Override
    public void run() {
        for(int i=0; i<10; i++) {
            // Thread class의 getName() method 호출
            System.out.println(getName());
        }
    }
}

public class ThreadExam03 {

    public static void main(String[] args) {

        ThreadEx_03 t1 = new ThreadEx_03("Thread-01");
        ThreadEx_03 t2 = new ThreadEx_03("Thread-02");
        
        t1.setPriority(1);
        t2.setPriority(9);
        
        t1.start();
        t2.start();

    }
}

~~~

여러번 반복해서 수행시켜보면 결과가 좀 이상하게 나오는 걸 느끼실 수 있을겁니다.
<br><br>

single core에서는 이 우선순위가 영향을 미치지만 multi core에서는 이 우선순위가
거의 영향을 미치지 않습니다. 실제로 전혀 영향이 없다고 봐도 무방합니다.
<br><br>

그래서 우리는 이 우선순위를 그다지 중요하게 여기지 않습니다.
<br><br>

Thread에 우선순위를 부여하는 방법은 크게 의미가 없고 `PriorityQueue`와
같은 것을 이용해 우선순위가 높은 작업이 먼저 처리되도록 로직을 구현하는 방법을
사용합니다.

---

## Daemon Thread(데몬 쓰레드)

`Daemon Thread`는 다른 일반 Thread의 작업을 돕는 보조적인 역할을 하는 Thread를
의미합니다. Daemon Thread가 아닌 일반 Thread가 모두 종료되면 Daemon Thread는
강제적으로 자동종료되는데 그 이유는 이 Thread의 역할이 보조역할을 수행하기 때문입니다.
<br><br>

즉, 일반 Thread가 모두 종료되면 Daemon Thread의 존재의미가 없어지기 때문이죠.
<br><br>

대표적인 Damon Thread로는 `Garbage Collector`, 워드 프로세서의 `auto save`등이
있습니다.
<br><br>

이런 Daemon Thread는 일반적으로 무한 loop와 조건문을 이용해서 실행 후 대기하고 있다가 특정
조건이 만족되면 작업을 수행하고 다시 대기하도록 작성합니다.
<br><br>

그럼 이런 Daemon Thread는 어떻게 만들까요? 만드는 방법은 일반 Thread와 동일합니다. 단,
실행하기전에 `setDaemon(true)` 메소드를 호출하기만 하면 됩니다. 또한 Daemon Thread가
생성한 Thread 역시 Daemon Thread가 됩니다.
<br><br>

아래의 코드를 보죠. daemon thread로 지정하는 부분을 빼고 수행하면 우리 프로그램은
종료되지 않게 됩니다. main thread가 종료된다 하더라도 별도의 thread가 무한 loop를
돌고 있기 때문입니다. 하지만 daemon thread로 지정하면 main thread가 종료되는 순간
별도의 Thread 역시 자동으로 종료되게 됩니다.

~~~java

package sampleProj;


public class ThreadExam04 implements Runnable {

    static boolean autoSave = false;
    
    public static void main(String[] args) {

        Thread t = new Thread(new ThreadExam04());
        t.setDaemon(true);   // daemon thread로 지정
                             // 만약 daemon thread로 지정하지 않으면 프로그램이
                             // 종료되지 않는다.
        t.start();
        
        for(int i=0; i<10; i++) {
            try {
                Thread.sleep(1000);
            } catch(InterruptedException e) {}
            System.out.println(i);
            
            if(i == 5) {
                autoSave = true;
            }
        }

    }
    
    @Override
    public void run() {
        
        while(true) {
            try {
                Thread.sleep(3000);
            } catch (Exception e) {

            }
            if (autoSave) {
                System.out.println("자동저장되었습니다.");
            }
        }        
    }
}


~~~

## Thread의 실행제어

지금까지 Thread의 기본적인 내용을 살펴봤습니다. 이런 Thread는 프로그램을 굉장히
효율적으로 동작시킬 수 있지만 여러가지 이유로 프로그래밍이 상당히 어렵습니다.
<br><br>

대표적인 이유가 `Synchronization`(동기화)과 `Scheduling`(스케쥴링) 입니다.
<br><br>

효율적인 multi thread programming을 하기 위해 보다 정교한 scheduling을 통해
프로세스에게 주어진 자원과 시간을 여러 thread가 낭비없이 잘 사용하도록 프로그래밍
해야 하는점이 어렵습니다.
<br><br>

자 그러면 Thread의 scheduling을 잘 하려면 어떻게 해야 할까요? 기본적으로 Thread의
상태전이와 관련된 method를 이해해야 합니다. 하나하나 method를 살펴보면서 Thread의
상태전이가 어떻게 되는지를 알아보도록 하겠습니다.
<br><br>

참고로 thread의 method 중 `stop()`, `suspend()`, `resume()` method는
Thread의 `dead-lock`(교착상태)을 발생시키기 쉽기 때문에 `deprecated`되었습니다.
이 method는 사용하지 않는것이 좋습니다.

---

## sleep(long millis)

`sleep()`은 지정된 시간동안 Thread를 멈추게 합니다.
<br><br>

sleep()에 의해서 일시 정지 상태가 된 Thread는 지정된 시간이 다 되거나 interrupt()가
호출되면(`InterruptedException` 발생) sleep에서 깨어나 실행대기 상태가 됩니다.
<br><br>

따라서 sleep()은 항상 `try~catch`를 이용해 예외처리를 해 줘야 합니다.
<br><br>

Thread의 상태전이를 보면 다음과 같습니다.

{% include image.html
file='java-thread-thread-sleep-method-state.png'
%}
<br>

그럼 코드를 한번 보죠.

~~~java

package sampleProj;

class ThreadEx_05_1 extends Thread {

    @Override
    public void run() {

        for(int i=0; i<300; i++) {
            System.out.print("-");
        }
        System.out.print("<<thread 1 종료>>");
    }
}

class ThreadEx_05_2 extends Thread {

    @Override
    public void run() {
        for(int i=0; i<300; i++) {
            System.out.print("|");
        }
        System.out.print("<<thread 2 종료>>");
    }
}


public class ThreadExam05 {

    public static void main(String[] args) {
        Thread t1 = new ThreadEx_05_1();
        Thread t2 = new ThreadEx_05_2();
        
        t1.start();
        t2.start();
        
        try {            
            t1.sleep(2000);
        } catch (Exception e) {}
        
        System.out.println("<<main thread 종료>>");
    }

}

~~~

코드를 실행시켜보면 조금 이상하다라는 생각이 듭니다. 왜냐하면 Thread를 2개 시작한다음
`t1`을 sleep()시켜 2초간 정지시켰으니 아마도 `t2`가 먼저 종료될 것이라 예상하는데
실행결과를 보면 거의 `t1`이 먼저 종료됩니다.
<br><br>

그 이유는 이 `sleep()`이라는 method가 `static`으로 지정되어 있으며 항상 현재 실행중인
Thread에 대해서 동작하기 때문입니다. 즉, 코드에서 `t1.sleep(2000)`이라고 해도 실제로 영향을
받는것은 main method를 수행하는 `main thread`입니다. 따라서 main thread가 가장 늦게
종료되는 것이죠.
<br><br>

결국 `sleep()` method는 참조변수를 이용해서 사용하기 보다는 `Thread.sleep(2000)`과
같은 형태로 사용해야 합니다.
<br><br>

그럼 우리의 의도대로 Thread `t1`을 2초간 `sleep` 시키려면 코드를
어떻게 바꾸어야 할까요?

~~~java

package sampleProj;

class ThreadEx_05_1 extends Thread {

    @Override
    public void run() {
        try {
            Thread.sleep(2000);
        } catch (Exception e) {}
        
        for(int i=0; i<300; i++) {
            System.out.print("-");
        }
        System.out.print("<<thread 1 종료>>");
    }
}

class ThreadEx_05_2 extends Thread {

    @Override
    public void run() {
        for(int i=0; i<300; i++) {
            System.out.print("|");
        }
        System.out.print("<<thread 2 종료>>");
    }
}


public class ThreadExam05 {

    public static void main(String[] args) {
        Thread t1 = new ThreadEx_05_1();
        Thread t2 = new ThreadEx_05_2();
        
        t1.start();
        t2.start();
        
        System.out.println("<<main thread 종료>>");
    }
}

~~~

---

## interrupt()와 interrupted()

진행중인 Thread의 작업이 끝나기 전에 실행을 중지시켜야 할 때 도 있습니다. 이럴때 사용하는
method가 `interrupt()` 입니다. 기존에 제공됬던 `stop()`은 deprecated되어서 사용되지
않습니다.
<br><br>

그런데 여기서 조금 주의해야 할 점이 있습니다. 이 `interrupt()` 메소드는 Thread에게
작업을 중지하라고 요청하는 것이지 Thread를 강제로 종료시키지 못합니다. 이 `interrupt()`
메소드는 Thread의 `interrupted state`를 변경할 뿐입니다.
<br><br>

또한 `interrupted()` 혹은 `isInterrupted()` method는 Thread에 대해
`interrupt()`가 호출되었는지를 알려줍니다.
만약 interrupt()가 호출되었다면 `true`를 반환하며 그렇지 않으면 `false`를 반환합니다.
<br><br>

여기서 한가지 조심해야 하는데 `interrupted()` method는 `static`이며 현재
수행중인 Thread의 interrupted상태를 반환한 후 그 값을 `false`로 변경합니다.
<br><br>

이와 비슷한 역할을 수행하는 `isInterrupted()` 메소드는 `instance method`로
특정 thread의 interrupted상태를 반환만 해 주고 상태를 변경시키지 않습니다.
<br><br>

만약 Thread가 `sleep()`, `wait()`, `join()`에 의해 일시 정지 상태에 있을 때 해당 Thread에
대해 interrupt()를 호출하면 `sleep()`, `wait()`, `join()`에서 Interrupt Exception이
발생하고 Thread는 `Runnable` 상태로 바뀌게 됩니다. 즉, 멈춰있던 Thread를 깨워서 실행 가능한
상태로 만든다는 말이죠.
<br><br>

그럼 어떻게 interrupt()를 이용해서 Thread를 안전하게 종료할 수 있는지를 예제를 통해서
알아보겠습니다. 예제의 실행을 위해 `Swing`을 잠깐 이용하도록 하죠.

~~~java

package sampleProj;

import javax.swing.JOptionPane;

class ThreadEx_06 extends Thread {

    @Override
    public void run() {

        int i = 10;
        
        while(i != 0 && !isInterrupted()) {
            System.out.println(i--);
            for(long k=0; k<2500000000L; k++); // 시간지연
        }
        System.out.print("카운트가 종료되었습니다.");
    }
}


public class ThreadExam06 {

    public static void main(String[] args) {
        Thread t1 = new ThreadEx_06();
        
        t1.start();

        String input = JOptionPane.showInputDialog("값을 입력하세요");
        System.out.println("입력한 값은 : " + input);
        
        t1.interrupt();
        // t1 thread의 interrupted상태가 true가 된다.
        
        System.out.println("t1 interrupted : " + t1.isInterrupted());
        
    }
}

~~~

자 이번에는 아래의 코드를 한번 보죠. 우리의 생각과 약간 다르게 동작할 거 같습니다.

~~~java

package sampleProj;

import javax.swing.JOptionPane;

class ThreadEx_07 extends Thread {

    @Override
    public void run() {

        int i = 10;
        while(i != 0 && !isInterrupted()) {
            System.out.println(i--);
            // 시간지연
            try {
                Thread.sleep(1000);
            } catch (Exception e) {}
        }
        System.out.print("카운트가 종료되었습니다.");
    }
}


public class ThreadExam07 {

    public static void main(String[] args) {
        Thread t1 = new ThreadEx_07();
        
        t1.start();

        String input = JOptionPane.showInputDialog("값을 입력하세요");
        System.out.println("입력한 값은 : " + input);
        t1.interrupt();
        
        // t1 thread의 interrupted상태가 true가 된다.
        System.out.println("t1 interrupted : " + t1.isInterrupted());
        
    }
}

~~~

시간지연을 위해 `Thread.sleep()`을 이용했습니다. 그런데 카운트가 종료되지 않고 계속 이어집니다.
왜 그럴까요?
<br><br>

`Thread.sleep(1000)`이 수행되고 있는 중에 `interrupt()`가 호출되면 InterruptedException이
발생된다는건 위에서 언급했었습니다. 중요한건 이 Exception이 발생한 후 해당 Thread의 interrupted상태값이
`false`로 자동 초기화된다는 것입니다.
<br><br>

그럼 어떻게 해야 할까요? 이런 경우 catch 구문에서 다시 `interrupt()`를 걸어서 Thread의 interrupted상태를
true로 변경해줘야 합니다.

아래의 코드처럼 말이죠.

~~~java

    @Override
    public void run() {

        int i = 10;
        while(i != 0 && !isInterrupted()) {
            System.out.println(i--);
            // 시간지연
            try {
                Thread.sleep(1000);
            } catch (Exception e) {
                // sleep 상태에서 interrupt()가 호출되면
                // exception이 발생하고 interrupted state가 false가 된다.
                // 따라서 interrupted state를 변경해주기 위해
                // interrupt() 호출이 필요! 
                interrupt();
            }
        }
        System.out.print("카운트가 종료되었습니다.");
    }

~~~

---

## yield()

`yield()` method는 Thread 자신에게 주어진 실행시간을 다음 차례의 Thread에게 양보하는
메소드 입니다. 예를 들면, scheduler에 의해 1초의 실행시간을 할당받은 Thread가 0.5초의
시간동안 작업한 상태에서 yield()를 호출하면 나머지 0.5는 포기하고 다시 `runnable`상태가 됩니다.
<br><br>

이 `yield()`를 사용하는 이유는 프로그램의 응답성을 높여 보다 효율적인 실행을 하기 위해서
입니다.
<br><br>

이를 이해하기 위해서 간단한 예제를 작성해야 하는데 이 예제를 위해 위에서 잠깐 언급한
`stop()`, `suspend()`, `resume()` 메소드부터 알아보도록 하죠.
<br><br>

`stop()`, `suspend()`, `resume()` 이 3개의 메소드는 Thread의 `dead-lock`을 유발시키기 쉽다는
이유로 `deprecated`되었습니다. 당연히 이를 사용하는건 좋지 않습니다. 그래도 한번 사용해 보기로 하죠.

~~~java 

package sampleProj;

class ThreadEx_08 implements Runnable {

    @Override
    public void run() {

        while(true) {
            System.out.println(Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (Exception e) {

            }
        }
    }
}


public class ThreadExam08 {

    public static void main(String[] args) {
        ThreadEx_08 r = new ThreadEx_08();
        Thread t1 = new Thread(r,"*");    // thread의 이름 설정
        Thread t2 = new Thread(r,"**");
        Thread t3 = new Thread(r,"***");
        
        t1.start();
        t2.start();
        t3.start();

        try {
            Thread.sleep(2000);
            t1.suspend();   // thread t1을 일시중지.
            Thread.sleep(2000);
            t2.suspend();   // thread t2를 일시중지.
            Thread.sleep(3000);
            t1.resume();    // thread t1이 다시동작하도록 설정
            Thread.sleep(3000);
            t1.stop();
            t2.stop();
            Thread.sleep(2000);
            t3.stop();
            
        } catch (Exception e) {
            // TODO: handle exception
        }
    }
}

// 출력결과 - 실행될 때 마다 다르다.
//    *
//    ***
//    **
//    ***
//    **
//    *
//    ***
//    **
//    ***
//    **
//    ***
//    ***
//    ***
//    *
//    ***
//    *
//    ***
//    *
//    ***
//    ***
//    ***


~~~

실행결과는 매번 달라지겠지만 어쨋든 `stop()`, `suspend()`, `resume()` 메소드가 어떻게
동작하는지 볼 수 있습니다. 이 코드를 deprecated 메소드를 사용하지 않고 어떻게 바꿀 수 있을까요?
<br><br>

변수를 이용한 로직처리로 변경할 수 있을듯 합니다. 아래의 코드를 살펴보죠.

~~~java

package sampleProj;

class ThreadEx_09 implements Runnable {

    volatile boolean suspended = false;
    volatile boolean stopped = false;
    
    @Override
    public void run() {

        while(!stopped) {
            if(!suspended) {
                System.out.println(Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (Exception e) {}                
            }
        }
    }
    public void suspend() { suspended = true; }
    public void resume() { suspended = false; }
    public void stop() { stopped = true; }
}



public class ThreadExam09 {

    public static void main(String[] args) {
        ThreadEx_09 r1 = new ThreadEx_09();
        ThreadEx_09 r2 = new ThreadEx_09();
        ThreadEx_09 r3 = new ThreadEx_09();
        
        Thread t1 = new Thread(r1,"*");    // thread의 이름 설정
        Thread t2 = new Thread(r2,"**");
        Thread t3 = new Thread(r3,"***");
                
        t1.start();
        t2.start();
        t3.start();

        try {
            Thread.sleep(2000);
            r1.suspend();   // thread를 제어하는게 아니다.
            Thread.sleep(2000);
            r2.suspend();    
            Thread.sleep(3000);
            r1.resume();     
            Thread.sleep(3000);
            r1.stop();
            r2.stop();
            Thread.sleep(2000);
            r3.stop();
            
        } catch (Exception e) {
            // TODO: handle exception
        }
    }
}

~~~

코드가 점점 더 어려워지는군요. 여하간 이렇게 객체의 field와 제어문을 이용해서 처리할
수 있습니다. 한가지 특이한 키워드가 나오는데 `volatile`(välədl)입니다.
<br><br>

위의 예제는 반드시 `volatile` 키워드를 사용해야 정상적으로 출력이 될텐데 그 이유는 다음과
같습니다.
<br><br>

우리 예제는 아마도 multi core 프로세서에서 실행될 것이고 multi core 프로세서는 각
core마다 별도의 `cache`(캐시)를 가지고 있습니다.
<br><br>

core는 메모리에서 읽어온 값을 캐시에 저장하고 `cache`(캐시)에서 값을 읽어서 작업합니다.
다시 같은 값을 읽어올 때는 먼저 캐시에 있는지 확인하고 없을 때만 메모리에서 읽어옵니다.
<br><br>

그러다보니 메모리에 저장된 변수의 값이 변경되었는데도 캐시에 저장된 값이 갱신되지 않아서
오류가 발생하는 경우가 있습니다. 우리가 사용한 `volatile` 키워드는 이 변수의 값을
읽어올 때 캐시에서 읽어오는 것이 아니라 메모리에서 읽어오도록 지시하는 지시어입니다.
이 키워드를 이용하면 메모리와 캐시의 불일치를 해결 할 수 있습니다.
<br><br>

그림으로 보면 다음과 같습니다.

{% include image.html
file='java-thread-multi-core-volatile.png'
%}
<br>

자 이제 우리 `yield()`로 넘어와 보죠. 위의 코드를 `yield()`를 이용해 다음과 같이
수정하면 조금 더 응답을 높일 수 있습니다. 여기서 응답을 높이겠다는 의미는
만약 `suspended` 변수가 true이면 while문이 아무런 일도 하지 않고 루프를
반복해서 돌게 됩니다. 이를 `busy-waiting`이라고 하는데 이 부분을 수정하겠다는
의미입니다.
<br><br>

`yield()`를 이용해 무의미한 while 반복을 하는 대신 다른 Thread에게 양보해서
효율적으로 만들겠다는 의미입니다.

~~~java

package sampleProj;

class ThreadEx_10 implements Runnable {

    volatile boolean suspended = false;
    volatile boolean stopped = false;
    
    Thread t;
    
    void setThread(Thread t) {
        this.t = t;
    }
    
    @Override
    public void run() {

        while(!stopped) {
            if(!suspended) {
                System.out.println(Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (Exception e) {}                
            } else {
                Thread.yield();
            }
        }
    }
    public void suspend() { 
        suspended = true;
        t.interrupt();
        System.out.println(t.getName() + " => interrupted by suspend()");
        
    }
    public void resume() { suspended = false; }
    public void stop() { 
        stopped = true;
        t.interrupt();
        System.out.println(t.getName() + " => interrupted by stop()");        
    }
}


public class ThreadExam10 {

    public static void main(String[] args) {
        ThreadEx_10 r1 = new ThreadEx_10();
        ThreadEx_10 r2 = new ThreadEx_10();
        ThreadEx_10 r3 = new ThreadEx_10();
        
        Thread t1 = new Thread(r1,"*");    // thread의 이름 설정
        Thread t2 = new Thread(r2,"**");
        Thread t3 = new Thread(r3,"***");
        
        r1.setThread(t1);
        r2.setThread(t2);
        r3.setThread(t3);

        t1.start();
        t2.start();
        t3.start();

        try {
            Thread.sleep(2000);
            r1.suspend();   // thread를 제어하는게 아니다.
            Thread.sleep(2000);
            r2.suspend();    
            Thread.sleep(3000);
            r1.resume();     
            Thread.sleep(3000);
            r1.stop();
            r2.stop();
            Thread.sleep(2000);
            r3.stop();
            
        } catch (Exception e) {
            // TODO: handle exception
        }
    }
}

~~~

`yield()`의 의한 상태전이를 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='java-thread-thread-yield-method-state.png'
%}

---

## join()

Thread가 자신이 하던 작업을 잠시 멈추고 다른 Thread가 지정된 시간동안 작업을 수행하도록 할 때
`join()`을 사용합니다.

~~~java

void join()
void join(long millis)

~~~

시간을 지정하지 않으면, 해당 Thread가 작업을 모두 마칠 때까지 기다리게 됩니다. 작업 중 다른
Thread의 작업이 먼저 수행되어야 할 필요가 있을 때 사용합니다.
<br><br>

이런 `join()`도 `sleep()`과 마찬가지로 `interrupt()`에 의해서 대기상태에서 벗어날 수 있습니다.
따라서 `join()`이 호출되는 부분은 `try~catch`로 예외 처리를 해 주어야 합니다.
<br><br>

이전에 `sleep()` 메소드에 대해서 알아봤는데 이 sleep()은 특정 Thread가 아니라 현재 Thread를
대상으로 합니다. 그래서 `static`으로 지정되어 있는 것이었죠. 하지만 join()은 특정 Thread에
대해서 동작하기 때문에 `static`이 아닙니다.
<br><br>

간단한 예를 보죠. `join()`이 어떻게 동작하는지를 일단 알아보죠.

~~~java

package sampleProj;

class ThreadEx_11_1 extends Thread {

    @Override
    public void run() {
        for(int i=0; i<300; i++) {
            System.out.print(new String("-"));
        }
    }    
}

class ThreadEx_11_2 extends Thread {

    @Override
    public void run() {
        for(int i=0; i<300; i++) {
            System.out.print(new String("|"));
        }
    }    
}


public class ThreadExam11 {
    
    static long startTime = 0;
    
    public static void main(String[] args) {
        ThreadEx_11_1 t1 = new ThreadEx_11_1();
        ThreadEx_11_2 t2 = new ThreadEx_11_2();
        
        t1.start();
        t2.start();
        
        startTime = System.currentTimeMillis();

        try {
            t1.join();
            t2.join();
        } catch (Exception e) {
            // TODO: handle exception
        }
        System.out.println("소요시간 : " + 
                (System.currentTimeMillis() - startTime));
    }
}

~~~

`join()`의 의미는 자신의 작업 중간에 다른 Thread의 작업을 참여(join)시킨다는 의미로
이름이 지어진 것입니다. 위의 코드에서 만약 `join()`을 사용하지 않았다면 main thread는
바로 종료되었겠지만 join()으로 Thread t1과 t2의 작업이 모두 종료될 때 까지
main thread를 기다리도록 만든 것입니다. 사용하는 방법을 잘 봐 두시길 바랍니다.
<br><br>

그런데 `join()`의 의미는 이해했는데 이런 join()을 도대체 언제 사용하는 것일까요?
<br><br>

아래의 코드를 보면 이해를 할 수 있습니다. 아래의 예제는 JVM의 `gabage collector`를
흉내내어 간단히 구현해 본 것입니다.

~~~java

package sampleProj;

class ThreadEx_12 extends Thread {

    final static int MAX_MEMORY = 1000;
    int usedMemory = 0;
    
    @Override
    public void run() {
        while(true) {
            try {
                Thread.sleep(10000);  // 10초간 기다린다.
            } catch (Exception e) {
                System.out.println("Awaken by interrupt()");
            }
            gc();
            System.out.println("Garbage Collected. Free Memory : " 
                + freeMemory());
        }
    }    
    public void gc() {
        usedMemory -= 300;
        if (usedMemory < 0) {
            usedMemory = 0;
        }
    }
    
    public int totalMemory() { return MAX_MEMORY; }
    public int freeMemory() { return MAX_MEMORY - usedMemory; }
    
}


public class ThreadExam12 {
    

    public static void main(String[] args) {
        ThreadEx_12 gc = new ThreadEx_12();
        gc.setDaemon(true);
        gc.start();        

        int requiredMemory = 0;
        
        for(int i=0; i<20; i++) {
            requiredMemory = (int)(Math.random() * 10) * 20;
            
            // 필요한 memory가 사용할 수 있는 양보다 크거나
            // 전체 메모리의 60% 이상을 사용했을 경우 gc를 깨운다.
            
            if(gc.freeMemory() < requiredMemory || 
                    gc.freeMemory() < gc.totalMemory() * 0.4) {
                gc.interrupt();
            }
            gc.usedMemory += requiredMemory;
            System.out.println("usedMemory : " + gc.usedMemory);
        }
    }
}

//	usedMemory : 180
//	usedMemory : 180
//	usedMemory : 360
//	usedMemory : 400
//	usedMemory : 560
//	usedMemory : 600
//	usedMemory : 760
//	usedMemory : 880
//	usedMemory : 980
//	usedMemory : 1160
//	usedMemory : 1280
//	usedMemory : 1300
//	usedMemory : 1400
//	usedMemory : 1400
//	usedMemory : 1540
//	Awaken by interrupt()
//	Garbage Collected. Free Memory : -300
//	usedMemory : 1600
//	usedMemory : 1420
//	usedMemory : 1480
//	usedMemory : 1640
//	Awaken by interrupt()
//	usedMemory : 1640
//	Garbage Collected. Free Memory : -340
//	Awaken by interrupt()
//	Garbage Collected. Free Memory : -40

~~~

위의 예제를 여러번 시행시켜보면 이상한 점을 발견할 수 있습니다. 코드를
분석할 때는 눈에 띄지 않았지만 실제 수행해보면 사용된 메모리가 1000을
넘어가는 경우를 볼 수 있습니다.
<br><br>

왜 그럴까요?
<br><br>

그 이유는 Thread `gc`가 `interrupt()`에 의해서 깨어나 `gc()`를
실행시키기 이전에 main thread의 작업이 계속 수행되어 메모리를 사용하기
때문입니다.
<br><br>

따라서 Thread `gc`를 깨우는 것만 아니라 `join()`을 통해서 Thread `gc()`가
작업할 시간을 어느정도 보장해주고 main thread가 실행될 수 있도록 처리해야
할 듯 합니다.
<br><br>

다음의 코드로 변경하면 될 듯 합니다.

~~~java 

if(gc.freeMemory() < requiredMemory || 
        gc.freeMemory() < gc.totalMemory() * 0.4) {
    gc.interrupt();
    try {
        gc.join(100);    // 이 부분이 추가되어야 한다.
    } catch (Exception e) {}
}

~~~

---

## Thread 동기화(Synchronization)

멀티쓰레드 프로세서의 경우 여러 Thread가 같은 자원을 공유해서 작업하기 때문에
서로의 작업에 영향을 주게 됩니다. 영화표를 예매하는 다음의 예를 보시죠.

{% include image.html
file='java-thread-thread-movie-reservation.png'
%}
<br>

위의 예처럼 Thread A가 작업하던 도중에 다른 Thread B에게 제어권이 넘어갔을 때,
Thread A가 작업하던 공유데이터를 Thread B가 임의로 변경하였다면, 다시 Thread A
가 제어권을 받아서 나머지 작업을 마쳤을 때 원래 의도했던 것과는 다른 결과를
얻을 수 있습니다.
<br><br>

이러한 현상이 발생하지 않도록 한 Thread가 특정 작업을 마치기 전까지 다른 Thread에
의해 방해받지 않도록 하는 것이 필요합니다. 그래서 도입한 개념이 바로
`Critical Section`(임계 영역)과 `Lock`(락, 잠금)입니다.
<br><br>

`Lock`은 다른말로 `Monitor`라고 하기도 하는데 아래의 그림처럼 생각하시면 됩니다.

{% include image.html
file='java-thread-thread-movie-reservation-monitor.png'
%}
<br>

공유자원에 Thread가 접근하는 것을 제어하기 위해서 `Monitor`라는 것을 제공합니다. 그림에서
보듯이 모든 공유자원은 각자의 Monitor를 가지고 있습니다. 일단 Monitor를 한 Thread가 가지고
있으면 다른 Thread는 해당 공유자원을 이용하는 동기화 method를 실행 시킬 수 없게 됩니다.
<br><br>

그러면 이런 공유자원의 Monitor를 획득하려면 어떻게 해야 할까요? Java에서는 `synchronized` keyword를
제공합니다.
<br><br>

다시말하면, 공유 데이터를 사용하는 코드 영역을 `Critical Section`으로 지정해 놓고 공유 데이터가 가지고
있는 Lock(Monitor)를 획득한 단 한개의 Thread만 이 영역 내의 코드를 수행할 수 있게 합니다. 그리고
해당 Thread가 Critical Section내에 모든 코드를 수행하고 벗어나서 Lock(Monitor)를 반납해야만
다른 Thread가 반납된 Lock(Monitor)를 획득하여 Critical Section의 코드를 수행할 수 있게 됩니다.
<br><br>

이처럼 한 Thread가 진행 중인 작업을 다른 Thread가 간섭하지 못하도록 막는 것을 Thread의
`Synchronization`(동기화)라고 합니다.
<br><br>

가장 간단한 방법은 위에서 언급했듯이 `synchronized` keyword를 이용하는 것입니다. 메소드 자체를
동기화 시킬 수도 있고 메소드 내의 코드 일부를 synchronized block으로 설정해서 사용할 수 있습니다.
<br><br>

`Lock`의 획득과 반납은 자동으로 이루어지기 때문에 그 부분에 대한 코드는 필요가 없습니다. 우리는 단지
`Critical Section`만 설정해주면 됩니다.
<br><br>

아래의 예를 작성해서 실행해보죠. 기본적으로 예금 계좌에 잔액이 1000원이 있는데 두 개의 Thread가
경쟁적으로 출금을 진행하는 예제입니다.

~~~java

package sampleProj;

class Account {
    
    private int balance = 1000;   // 계좌 잔액
    
    public int getBalance() {
        return balance;
    }
    
    public void withdraw(int money) {
        if(balance >= money) {
            try {
                Thread.sleep(1000);                
            } catch (Exception e) {}
            balance -= money;
        }
    }
}

class ThreadEx_13 implements Runnable {
    
    Account acc = new Account();
    
    @Override
    public void run() {
        while(acc.getBalance() > 0) {
            int money = (int)(Math.random() * 3 + 1) * 100;
            acc.withdraw(money);
            System.out.println("balance : " + acc.getBalance());
        }
    }    
        
}


public class ThreadExam13 {
    
    public static void main(String[] args) {
        ThreadEx_13 r = new ThreadEx_13();
        
        Thread t1 = new Thread(r);
        Thread t2 = new Thread(r);
        
        t1.start();
        t2.start();

    }
}

//  실행결과 예시
//    balance : 600
//    balance : 600
//    balance : 400
//    balance : 200
//    balance : 0
//    balance : -100


~~~

여러번 실행해보면 위와 같은 결과를 얻을 수 있습니다. 원래 프로그램은 잔고를 확인하고
임의의 금액을 출금하는데 잔고가 출금액보다 큰 경우에만 출금하도록 되어 있습니다.
<br><br>

하지만 실행결과를 보면 잔고가 음수로 되어 있는것을 볼 수 있습니다. 왜 그럴까요?
<br><br>

그 이유는 한 Thread가 if문의 조건을 통과하고 출금하기 바로 직전에 다른 Thread가
끼어들어서 출금을 먼저 했기 때문입니다.
<br><br>

이 예제를 실행하기 위해 일부러 출금하는 코드 직전에 `sleep()`을 이용하여 다른 Thread에게
제어권을 넘겼습니다. 그래서 위와 같은 현상이 발생하였지만 일반적으로 이런 경우는
항상 발생할 수 있는 경우입니다.
<br><br>

이런 문제를 해결하기 위해 우리는 `synchronized` 키워드를 사용할 수 있습니다.
<br><br>

두가지 형태로 사용할 수 있습니다.

~~~java

public synchronized void withdraw(int money) {
    if(balance >= money) {
        try {
            Thread.sleep(1000);                
        } catch (Exception e) {}
        balance -= money;
    }
}

~~~

또는 다음과 같이 synchronized block을 사용할 수 있습니다.

~~~java

public void withdraw(int money) {
    synchronized (this) {
        if(balance >= money) {
            try {
                Thread.sleep(1000);                
            } catch (Exception e) {}
            balance -= money;
        }            
    }
}

~~~

이를 Thread의 상태전이도로 나타내면 다음 그림과 같습니다.

{% include image.html
file='java-thread-thread-synchronized.png'
%}

---

## wait()과 notify()

`synchronized` keyword를 이용해서 공유 데이터를 보호하는것 까지는 좋은데,
특정 Thread가 공유 자원의 Lock을 가진 상태로 오랜 시간을 보내지 않도록 처리하는것도
중요합니다. 해당 자원을 필요로 하는 다른 Thread가 아무런 일도 하지 못한 채 오랬동안
그냥 기다리는 현상이 발생하기 때문입니다.
<br><br>

간단한 예로, 빵집에서 빵을 사려고 줄을 서있는 것을 상상해 보죠. 자신의 차례가 되었는데
원하는 빵이 나오지 않으면 다음 사람에게 일단 순서를 양보하고 기다리다가 자신이 원하는
빵이 나오면 통보를 받고 빵을 사가면 됩니다. 하지만 그렇지 않고 계속 자신의 빵이 나오길
기다리면 뒤의 사람은 원하는 빵이 있지만 그냥 계속 기다리는 현상이 발생하는 것이죠.
<br><br>

이런 상황을 개선하기 위해 나온 메소드가 바로 `wait()`과 `notify()`입니다.
<br><br>

동기화된 `Critical Section`의 코드를 수행하다가 작업을 더 이상 진행할 수 없으면
일단 `wait()`을 호출하여 Thread가 Lock을 반납하고 기다리게 합니다. 다른 Thread가
Lock을 얻어 해당 객체에 대한 작업을 수행하고 나중에 작업을 진행할 수 있는 상황이 되면
`notify()`를 호출해서 작업을 중단했던 Thread가 다시 Lock을 얻어 작업을 진행할 수 있도록
해 주는 개념입니다.
<br><br>

이때 조심해야 하는 것이 있는데 오래 기다린 Thread가 Lock을 얻는 다는 보장이 없다는
것입니다. 즉, `wait()`이 호출되면, 실행 중이던 Thread는 해당 공유 자원의 `waiting pool`에서
통지를 기다리게 됩니다. `notify()`가 호출되면 해당 공유 자원의 waiting pool 중에서
임의의 Thread만 통지를 받게 됩니다. `notifyAll()`은 기다리고 있는 모든 Thread에게 통지를
하지만 그래도 Lock을 얻는 Thread는 당연히 하나의 Thread입니다. Lock을 얻지 못하면
계속해서 Lock을 얻기 위해 대기해야 하는 것이죠.
<br><br>

정리하자면

* `wait()` : 현재 Thread를 wait 상태로 전환. notify()에 의해서 다시 runnable상태로 전환.
* `notify()` : wait()에 의해 일시 정지된 Thread 중 한 개를 runnable상태로 전환.
* `notifyAll()` : wait()에 의해서 일시 정지된 모든 Thread들을 runnable상태로 전환.

<br>

위에서 살짝 언급했지만 이 waiting pool은 공유 자원마다 존재합니다. 따라서 `notifyAll()`이
호출된다고 해서 모든 공유 자원의 waiting pool에 있는 Thread가 통지를 받는 것은 아닙니다.
<br><br>

이를 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='java-thread-thread-wait-notify.png'
%}
<br>

자 그럼 간단한 예제를 이용해서 `wait()`과 `notify()`에 대해서 알아보죠.
그런다음 조금 복잡한 예제를 생각해 보면 될 듯 합니다.
<br><br>

먼저 간단한 예제를 보죠.
<br><br>

1초마다 자신의 이름을 출력하는 Thread를 2개 생성하여 실행합니다.
당연히 두 Thread간의 실행 순서를 제어할 수 없습니다. 만약 이 두 개의 Thread가
교대로 번갈아 가면서 실행하는걸 보장하려면 어떻게 해야 할까요?

~~~java

package sampleProj;

class Shared {

    public synchronized void printNum() {    
        
        try {
            for(int i=0; i < 10; i++) {
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName() + " : " + i);
                notify();
                wait();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
}

class Exam03_MyRunnable implements Runnable {

    private Shared shared;
    
    public Exam03_MyRunnable(Shared shared) {
        this.shared = shared;
    }
    
    @Override
    public void run() {            
        shared.printNum();
    }        
}

public class ThreadExam15 {

    public static void main(String[] args) {

        Shared obj = new Shared();
        
        Thread t1 = new Thread(new Exam03_MyRunnable(obj));
        Thread t2 = new Thread(new Exam03_MyRunnable(obj));
        
        t1.setName("첫번째 Thread!!");
        t2.setName("두번째 Thread!!");

        t1.start();
        t2.start();
    }
}

~~~

이번에는 조금 더 복잡한 예제를 살펴보죠.
<br><br>

일반적으로 이 두 개의 method를 테스트할때는 `Producer-Consumer Pattern`을
사용합니다. 우리나라 말로는 생성자-소비자 패턴이라고 합니다.
<br><br>

식당에서 음식(Dish)을 만들어서 테이블(Table)에 추가하는 요리사(Cook)와
테이블의 음식을 소비하는 손님(Customer)을 Thread로 구현해 보죠.

~~~java

package sampleProj;

import java.util.ArrayList;

class Table {
    
    String[] dishNames = {"donut", "donut", "burger"};
    final int MAX_FOOD = 6;  // 테이블에 놓을 수 있는 최대 음식 수
    
    private ArrayList<String> dishes = new ArrayList<String>();
    
    public void add(String dish) {
        // 테이블에 음식이 가득 찼으면, 테이블에 음식을 추가하지 않는다.
        if(dishes.size() >= MAX_FOOD) {
            return;
        } else {
            dishes.add(dish);
            System.out.println("Dishes : " + dishes.toString());
        }        
    }
    
    public boolean remove(String dishName) {
        // 지정된 요리와 일치하는 요리를 테이블에서 제거합니다.
        for(int i=0; i<dishes.size(); i++) {
            if( dishName.equals(dishes.get(i))) {
                dishes.remove(i);
                return true;
            }
        }
        return false;
    }
    
    public int dishNum() {
        return dishNames.length;
    }
}

class Cook implements Runnable {
    
    private Table table;
    
    Cook(Table table) {
        this.table = table;
    }
    
    @Override
    public void run() {
        while(true) {
            // 임의의 요리를 하나 선택해서 table에 추가
            int idx = (int)(Math.random() * table.dishNum());
            table.add(table.dishNames[idx]);
            try {
                Thread.sleep(1);
            } catch (Exception e) {}
        }        
    }
}

class Customer implements Runnable {
    
    private Table table;
    private String food;
    
    Customer(Table table, String food) {
        this.table = table;
        this.food = food;
    }
    
    @Override
    public void run() {
        while(true) {
            try {
                Thread.sleep(10);
                String name = Thread.currentThread().getName();
                if( eatFood() ) {
                    System.out.println(name + " ate a " + food);
                } else {
                    System.out.println(name + " failed to eat");
                }
            } catch (Exception e) {}
        }        
    }
    
    boolean eatFood() {
        return table.remove(food);
    }
    
}


public class ThreadExam14 {
    
    public static void main(String[] args) throws Exception {
        
        Table table = new Table();
        
        Thread cook = new Thread(new Cook(table), "COOK");
        Thread customer1 = new Thread(new Customer(table, "donut"), 
                "CUSTOMER1");
        Thread customer2 = new Thread(new Customer(table, "burger"), 
                "CUSTOMER2");
        
        cook.start();
        customer1.start();
        customer2.start();
        
        Thread.sleep(200);
        System.exit(0);
    }
}

~~~

위의 예제는 문제가 없는 듯 보이지만 실제로 여러번 실행을 시켜보면 2가지 종류의 오류가 발생할
여지가 있습니다.
<br><br>

첫번째는 요리사(COOK) Thread가 테이블에 음식을 놓는 도중에, 손님(CUSTOMER) Thread가 음식을
가져가려 했기 때문에 발생하는 `ConcurrentModificationException`이고 다른 하나는 손님 Thread가
테이블의 마지막 남은 음식을 가져가려는 도중에 다른 손님 Thread가 먼저 음식을 가져가서 있지도 않은
음식을 테이블에서 제거하려 했기 때문에 발생하는 `IndexOutOfBoundsException`입니다.
<br><br>

이런 예외가 발생하는 이유는 당연히 공유 자원인 Table에 대한 동기화를 하지 않았기 때문입니다.
<br><br>

동기화를 추가하고 코드를 약간 수정해 예외가 발생하지 않도록 처리해보죠.

~~~java

package sampleProj;

import java.util.ArrayList;

class Table {
    
    String[] dishNames = {"donut", "donut", "burger"};
    final int MAX_FOOD = 6;  // 테이블에 놓을 수 있는 최대 음식 수
    
    private ArrayList<String> dishes = new ArrayList<String>();
    
    public synchronized void add(String dish) {
        // 테이블에 음식이 가득 찼으면, 테이블에 음식을 추가하지 않는다.
        if(dishes.size() >= MAX_FOOD) {
            return;
        } else {
            dishes.add(dish);
            System.out.println("Dishes : " + dishes.toString());
        }        
    }
    
    public boolean remove(String dishName) {
        synchronized (this) {
            while(dishes.size() == 0) {
                String name = Thread.currentThread().getName();
                System.out.println(name + " is waiting");
                try {
                    Thread.sleep(500);
                } catch (Exception e) {}
            }
            // 지정된 요리와 일치하는 요리를 테이블에서 제거합니다.
            for(int i=0; i<dishes.size(); i++) {
                if( dishName.equals(dishes.get(i))) {
                    dishes.remove(i);
                    return true;
                }
            }

        }
        return false;
    }
    
    public int dishNum() {
        return dishNames.length;
    }
}

class Cook implements Runnable {
    
    private Table table;
    
    Cook(Table table) {
        this.table = table;
    }
    
    @Override
    public void run() {
        while(true) {
            // 임의의 요리를 하나 선택해서 table에 추가
            int idx = (int)(Math.random() * table.dishNum());
            table.add(table.dishNames[idx]);
            try {
                Thread.sleep(100);
            } catch (Exception e) {}
        }        
    }
}

class Customer implements Runnable {
    
    private Table table;
    private String food;
    
    Customer(Table table, String food) {
        this.table = table;
        this.food = food;
    }
    
    @Override
    public void run() {
        while(true) {
            try {
                Thread.sleep(10);
                String name = Thread.currentThread().getName();
                if( eatFood() ) {
                    System.out.println(name + " ate a " + food);
                } else {
                    System.out.println(name + " failed to eat");
                }
            } catch (Exception e) {}
        }        
    }
    
    boolean eatFood() {
        return table.remove(food);
    }
    
}


public class ThreadExam14 {
    
    public static void main(String[] args) throws Exception {
        
        Table table = new Table();
        
        Thread cook = new Thread(new Cook(table), "COOK");
        Thread customer1 = new Thread(new Customer(table, "donut"), 
                "CUSTOMER1");
        Thread customer2 = new Thread(new Customer(table, "burger"), 
                "CUSTOMER2");
        
        cook.start();
        customer1.start();
        customer2.start();
        
        Thread.sleep(5000);
        System.exit(0);
    }
}

~~~

공유 객체인 Table의 `add()`와 `remove()`를 동기화 처리했습니다. 더이상 예외는 발생하지
않지만 잘 동작하고 있는지 확신이 들지 않습니다.
<br><br>

코드를 가만히 살펴보면 손님 Thread가 원하는 음식이 Table에 존재하지 않으면 `failted to eat`을
출력하고 Table에 음식이 하나도 없으면 0.5초마다 음식이 추가되었는지를 확인하면서 기다리도록
작성되어 있습니다.
<br><br>

그런데 요리사(COOK) Thread는 왜 음식을 추가하지 않고 손님 Thread를 계속 기다리게 하는 걸까요?
<br><br>

그 이유는 손님 Thread가 Table 객체의 Lock을 쥐고 기다리기 때문입니다. 음식을 추가하려고
해도 요리사 Thread가 Table 객체의 Lock을 얻을 수 없기 때문에 음식이 추가되지 못하는 것이죠.
<br><br>

이럴때 사용하는 것이 바로 `wait()`과 `notify()`입니다.
<br><br>

손님 Thread가 Lock을 쥐고 기다리는게 아니라 `wait()`으로 Lock을 풀고 기다리다가 음식이 추가되면
`notify()`로 통보를 받고 다시 Lock을 얻어서 작업을 진행하게 할 수 있습니다.
<br><br>

아래의 코드를 보시죠.

~~~java

package sampleProj;

import java.util.ArrayList;

class Table {
    
    String[] dishNames = {"donut", "donut", "burger"};
    final int MAX_FOOD = 6;  // 테이블에 놓을 수 있는 최대 음식 수
    
    private ArrayList<String> dishes = new ArrayList<String>();
    
    public synchronized void add(String dish) {
        
        while(dishes.size() >= MAX_FOOD) {
            String name = Thread.currentThread().getName();
            System.out.println(name + " is waiting");
            try {
                wait();     // Cook Thread를 기다리게 한다.
                Thread.sleep(500);
            } catch (Exception e) {
                // TODO: handle exception
            }
        } 
            
        dishes.add(dish);
        notify();    // 기다리고 있는 CUSTOMER를 깨운다.
        System.out.println("Dishes : " + dishes.toString());
                
    }
    
    public void remove(String dishName) {
        
        synchronized (this) {
            String name = Thread.currentThread().getName();
            
            while(dishes.size() == 0) {
                
                System.out.println(name + " is waiting");
                try {
                    wait();  // CUSTOMER Thread를 기다리게 한다.
                    Thread.sleep(500);
                } catch (Exception e) {}
            }
            
            while(true) {
                for(int i=0; i<dishes.size(); i++) {
                    if( dishName.equals(dishes.get(i))) {
                        dishes.remove(i);
                        notify();   // 잠자고 있는 COOK을 깨운다.
                        return;
                    }
                }
                
                try {
                    System.out.println(name + " is waiting");
                    wait();   // 원하는 음식이 없는 CUSTOMER를 기다리게 한다.
                    Thread.sleep(500);
                } catch (Exception e) {
                    // TODO: handle exception
                }
                
            }
            // 지정된 요리와 일치하는 요리를 테이블에서 제거합니다.
        }
    }
    
    public int dishNum() {
        return dishNames.length;
    }
}

class Cook implements Runnable {
    
    private Table table;
    
    Cook(Table table) {
        this.table = table;
    }
    
    @Override
    public void run() {
        while(true) {
            // 임의의 요리를 하나 선택해서 table에 추가
            int idx = (int)(Math.random() * table.dishNum());
            table.add(table.dishNames[idx]);
            try {
                Thread.sleep(10);
            } catch (Exception e) {}
        }        
    }
}

class Customer implements Runnable {
    
    private Table table;
    private String food;
    
    Customer(Table table, String food) {
        this.table = table;
        this.food = food;
    }
    
    @Override
    public void run() {
        while(true) {
            try {
                Thread.sleep(100);
            } catch (Exception e) {}

            String name = Thread.currentThread().getName();
                
            table.remove(food);
            System.out.println(name + " ate a " + food);            
        }        
    }    
}


public class ThreadExam14 {
    
    public static void main(String[] args) throws Exception {
        
        Table table = new Table();
        
        Thread cook = new Thread(new Cook(table), "COOK");
        Thread customer1 = new Thread(new Customer(table, "donut"), 
                "CUSTOMER1");
        Thread customer2 = new Thread(new Customer(table, "burger"), 
                "CUSTOMER2");
        
        cook.start();
        customer1.start();
        customer2.start();
        
        Thread.sleep(5000);
        System.exit(0);
    }
}

~~~

마지막으로 지금까지 설명한 Thread의 최종 상태 전이도는 다음 그림과 같습니다.

{% include image.html
file='java-thread-thread-state-transition.png'
%}


End.

{% include links.html %}
