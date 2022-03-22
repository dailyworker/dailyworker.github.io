---
title: Java Thread - 자바 쓰레드
date: 2018-08-21 19:00:00 +0900
tags: 
- Java
- Thread
redirect_to: "https://brewagebear.github.io/"
---

# 개요 
해당 장에서는 멀티 스레드 프로그래밍의 개념을 잡기 위해서 프로세스와 쓰레드에 대한 내용을 학습한다. 블로그의 내용은 "자바의 정석 개정 3판"을 정리하였다.

# Java Programming - Process And Thread
+ STEP 1. 프로세스와 멀티 쓰레드란?
	+ STEP 1.1 멀티태스킹과 멀티쓰레딩
	+ STEP 1.2 멀티쓰레드의 장단점
+ STEP 2. 쓰레드의 구현과 실행
	+ STEP 2.1 start()와 run()
	+ STEP 2.2 싱글쓰레드와 멀티쓰레드
	+ STEP 2.3 쓰레드의 우선순위 
	+ STEP 2.4 쓰레드 그룹
	+ STEP 2.5 데몬 쓰레드 
	+ STEP 2.6 쓰레드 실행제어 
		+ STEP 2.6.1 sleep()
		+ STEP 2.6.2 interrupt()와 interrupted()
		+ STEP 2.6.3 suspend(), resume(), stop()
		+ STEP 2.6.4 yield()
		+ STEP 2.6.5 join()
	+ STEP 2.7 쓰레드의 동기화 
		+ STEP 2.7.1 synchronized를 이용한 동기화
		+ STEP 2.7.2 wait()와 notify()
+ STEP 3. 캐시와 메모리간의 값의 불일치 해결
	+ STEP 3.1 volatile

## STEP 1. 프로세스와 멀티 쓰레드란? 
**프로세스** 란? 실행 중인 하나의 어플리케이션 (ex : 크롬을 새 창으로 2개를 띄웠다면, 2개의 프로세스가 실행 중이라 말할 수가 있다.)

**멀티 쓰레드** 란?  하나의 프로세스가 두가지 이상의 작업을 처리할 수 있도록 하는 것이다.

그렇다면? **쓰레드** 는 무엇일까?  바로, 한 프로세스에서 동작되는 여러 실행 흐름이라고 볼 수가 있다.

따라서 다시 정의를 내려보자면 아래와 같다고 볼 수가 있다.

> 프로세스는 운영체제로부터 작업을 할당받는 작업의 단위이고, 스레드는 프로세스가 할당 받은 자원을 이용하는 실행의 단위이다.  

그렇다면, **멀티 쓰레드** 는 정확히 무엇을 뜻할까? 두가지 이상의 작업을 처리 할 수 있다고 했다. 즉, 작업의 단위는 쓰레드이므로, **적어도 2개 이상의 쓰레드가 한 프로세스 내에서 처리되는 것** 이 멀티 쓰레드라고 볼 수가 있다.

### STEP 1.1 멀티태스킹과 멀티쓰레딩

위에서 쓰레드와 프로세스 그리고 멀티 쓰레드의 개념을 간략히 나마 배웠다.
그렇다면 멀티태스킹은 무엇이고? 멀티쓰레딩과 무슨 차이일까?

멀티태스킹은 여러 개의 프로세스를 동시에 실행 하는 것이다.
그렇다면, 왜 굳이 멀티태스킹(멀티프로세스)로 처리하면 될 것을 또 다시 쓰레드까지 쪼개서 처리해야될까? 

문제는 바로 프로세스를 호출 시 발생하는 Context switch(문맥교환) [^1]에 있다.
프로세스는 호출할 때마다 문맥교환이라는 오버헤드가 발생하는데 스레드로 처리를 하면 프로세스끼리 통신하는 비용보다 통신 비용이 적고, 문맥교환이 적게 발생하기 때문에 보다 효율적인 작업이 가능하기 때문이다.


### STEP 1.2 멀티쓰레드의 장단점 

위에서 말한 바와 같이 멀티쓰레드는 장점이 매우 많아보인다. 
장점을 간략히 정리하자면 아래와 같다.

1. CPU의 사용률을 향상시킨다.
2. 자원을 보다 효율적으로 사용할 수 있다.
3. 사용자에 대한 응답성이 향상된다.
4. 작업이 분리되어 코드가 간결해진다.

멀티쓰레드의 예시로는 카카오톡을 볼 수 있다. 카카오톡은 채팅을 하면서 파일을 다운로드 받거나 음성대화를 나눌 수도 있다. 이러한 이유가 다른 작업이 가능한 멀티쓰레드 환경이기 때문이다. 

그러나, 멀티쓰레드가 장점만 있는 것이 아니다. 
대부분의 문제는 멀티쓰레드 프로세스는 여러 쓰레드가 같은 프로세스 내에서 자원을 공유하면서 작업을 하기 때문에 **동기화(Synchronization)** , **교착상태(Deadlock)** 와 같은 문제들이 발생할 수가 있다.

## STEP 2. 쓰레드의 구현과 실행
자바에서 쓰레드를 구현하는 방법은 2가지가 있다.

1. Thread Class를 상속받는 방법
2. Runnable 인터페이스를 구현하는 방법 

Thread Class를 상속받으면 다른 클래스를 상속 받을 수 없기때문에 Runnable 인터페이스를 구현하는게 일반적인 방법이다. 

```java
	// 1. Thread Class를 상속받는 방법
	class MyThread extends Thread {
		@Override
		public void run() { //작업할 내용 }
	}

	// 2. Runnable 인터페이스를 구현하는 방법
	class MyThread implements Runnable {
		@Override
		public void run() { //작업할 내용 }
}
```

Runnable 인터페이스는 run()만 정의되어 있는 간단한 인터페이스이다.

아래의 예시를 통해서 쓰레드를 구현하는 2가지 방법의 차이를 확인하자!

```java
public class ThreadEx1 {
    public static void main(String args[]){
        //Thread 클래스 상속
        ThreadEx1_1 t1 = new ThreadEx1_1();


        //Runnable 구현
        Runnable r = new ThreadEx1_2();
        Thread t2 = new Thread(r); // 생성자 Thread(Runnable target)

        t1.start(); //쓰레드 실행
        t2.start(); //쓰레드 실행
    }
}

class ThreadEx1_1 extends Thread {
    @Override
    public void run(){
        for(int i = 0; i < 5; i ++){
            System.out.println(getName());
        }
    }
}

class ThreadEx1_2 implements Runnable {
    @Override
    public void run() {
        for(int i = 0; i < 5; i ++){
            // Thread.currentThread() -> 현재 실행중인 쓰레드 반환
			System.out.println(Thread.currentThread().getName());
        }
    }
}
```


결과값은 Thread-0이 5번 호출되고, Thread-1이 5번 호출될 것이다.
getName()은 쓰레드 이름을 지정안 할시에 ‘Thread-번호’의 형식으로 이름이 정해지기 때문이다.

또한 여기서 start() 함수 사용시에 주의해야되는데 하나의 쓰레드에 대해 start()가 한 번만 호출될 수 있다. 
위 소스의 main()에서 t1.start()를 다시 한번 입력하면 **IllegalThreadStateException** 에러가 발생하는데 이게 한 쓰레드에 대해서 start()는 한번만 호출 할 수 있기 때문이다. 다시 호출하기 위해서는 생성자를 이용해서 다시 생성해줘야한다. 

### STEP 2.1 start()와 run()

JAVA Call Stack에 의해서 main은 start를 호출하게 되며, 각 쓰레드마다 run()이 호출되면서 독립적인 호출스택이 생성되고, 쓰레드가 종료되면 작업에 사용된 호출스택은 소멸된다.

![](&&&SFLOCALFILEPATH&&&thread_callstack.png)

위의 그림을 통해서 Call Stack이 어떻게 바뀌는지 볼 수가 있다. 

### STEP 2.2 싱글쓰레드와 멀티쓰레드

1. 싱글코어에서 하나의 쓰레드로 두개의 작업 VS 싱글코어에서 두개의 쓰레드로 두개의 작업
2. 멀티코어에서 하나의 쓰레드로 두개의 작업 VS 멀티코어에서 두개의 쓰레드로 두개의 작업

결론부터 말하자면, 1의 경우에는 전자가 빠르며, 2의 경우에는 후자가 빠르다.
1의 경우를 설명해보자면 위에서도 간략히 설명했던 문맥 교환(Context Switch)때문에 하나의 쓰레드로 두개의 작업을 하는 것이 빠르다는 결과가 나온다.

프로세스간의 문맥 교환보다 쓰레드간의 문맥 교환이 비용이 낮다고하지만, 하나의 쓰레드로 두개의 작업을 하는 것보다 문맥교환이 발생하는 두개의 쓰레드로 두개의 작업을 하는 것이 느리다는 것이다.

```java
class ThreadEx4 {
	public static void main(String args[]){
		long startTime = System.currentTimeMillis();
		
		for(int i=0; i < 300; i++)
				// -를 찍는 작업
			System.out.printf("%s", new String("-"));
		System.out.print("소요시간1:" + (Systen,currentTimeMillis() - startTime));
		
		for(int i=0; i < 300; i++)
				// |를 찍는 작업
			System.out.printf("%s", new String("|"));
		System.out.print("소요시간2:" + (System.currentTimeMillis() - startTime));
}

```

위의 예시는 1개의 쓰레드(Main)에서 '-'와 '|'를 찍는 2개의 작업을 한다.

```java
class ThreadEx5{
	static long startTime = 0;

	public static void main(String args[]){
		ThreadEx5_1 th1 = new ThreadEx5_1();
		th1.start();
		startTime = System.currentTimeMillis();
		
		for(int i =0; i < 300; i++)
			System.out.printf("%s", new String("-"));
		System.out.print("소요시간 1:" + (System.currentTimeMillis() - ThreadEx5.startTime));
	}
}
class ThreadEx5_1 extends Thread {
	public void run(){
		for(int i=0; i < 300; i++)
			System.out.printf("%s", new String("|"));
		System.out.print("소요시간 1:" + (System.currentTimeMillis() - ThreadEx5.startTime));

	}
}
```

위의 예시는 2개의 쓰레드(Main과 ThreadEx5_1.run())에서 2개의 작업을 한다.

후자가 느린 이유는 바로, 2개의 쓰레드가 번갈아 작업을 처리하면서 쓰레드간의 문맥교환이 발생하기 때문이고, 또다른 이유는 화면에 출력하는 동안 다른 쓰레드는 출력이 끝나기를 기다려야하는데, 이때 발생하는 대기시간 때문이다.

또한, 싱글코어와 멀티코어 환경과 비교를 하자면 싱글코어일 경우에는 멀티쓰레드라도 하나의 코어가 번갈아가면서 작업을 수행하는 것이므로 두 작업이 겹치지가 않는다. 
그러나, 멀티코어 환경에서는 **동시에 두 쓰레드가 수행될 수 있으므로 두 작업이 겹치는 부분** 이 발생한다.

이 부분은 OS의 프로세스 스케줄러의 영향을 받는다. 따라서 매 순간 상황에 따라 프로세스에게 할당되는 실행시간이 일정하지 않고 쓰레드에게 할당되는 시간 역시 일정하지 않게 된다. 그래서 쓰레드는 이러한 **불확실성** 을 가지고 있다는 것을 염두에 두어야 한다.

그렇다면? 싱글쓰레드와 멀티쓰레드같은 경우에는 어떻게 선택해야될까? 
두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 **멀티쓰레드**가 효율적이다.
예를 들면, 프린터로 파일을 출력하는 작업과 같이 외부기기와 입출력을 필요로 하는 경우가 이에 해당한다.

아래 예시코드를 보자.

```java
import javax.swing.JOptionPane;

class ThreadEx6{
	public static void main(String[] args) throws Exception {
		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요.");
		System.out.println("입력하신 값은" + input + "입니다.");
		for(int i=10; i > 0; i--){
			System.out.println(i);
			try{
				Thread.sleep(1000);
			} catch(Exception e){}
		}
	}
}
```

위의 예제는 싱글쓰레드로 동작하기 때문에 사용자가 입력을 마치기 전까지 화면에 숫자가 출력이 되지 않는다.

그렇다면, 멀티쓰레드의 경우에는 어떨까? 

```java
import javax.swing.JOptionPane;

class ThreadEx7 {
	public static void main(String[] args) throws Exception {
		ThreadEx7_1 th1 = new ThreadEx7_1();
		th1.start();
		
		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요.");
		System.out.println("입력하신 값은 " + input + "입니다.");
	}
}

class ThreadEx7_1 extends Thread {
	public void run(){
		for(int i = 10; i > 0; i--){
			System.out.println(i);
			try{
				sleep(1000);
			} catch(Exception e) {}
		}
	}
}
```

싱글쓰레드 예제와 달리 사용자가 입력을 마치지 않았더라도 화면에 숫자가 출력되는 것을 알 수 있다.

### STEP 2.3 쓰레드의 우선순위 

쓰레드는 우선순위라는 속성을 가지고 있는데, 이 우선순위의 값에 따라 쓰레드가 얻는 실행시간이 달라진다. 

이러한 우선순위 속성을 이용해서 우리는 쓰레드가 수행하는 작업의 중요도에 따라 쓰레드의 우선순위를 서로 다르게 지정하여 특정 쓰레드가 더 많은 작업시간을 갖도록 할 수 있다.

**쓰레드의 우선순위 지정하기**
```java

void setPriority(int newPriority) //쓰레드의 우선순위를 지정한 값으로 변경
int getPriority() //쓰레드의 우선순위 반환

//우선순위 지정 예시
public static final int MAX_PRI = 10 //최대우선순위
public static final int MIN_PRI = 1  //최소우선순위
public static final int AVG_PRI = 5  //보통우선순위
```

위에 예시의 주석을 보면 알겠지만, 쓰레드의 우선순위는 **int값 1~10** 을 지정할 수가 있다. 숫자가 높을수록 우선순위가 높다.

그리고 쓰레드의 우선순위는 쓰레드를 생성한 쓰레드로부터 상속받는다는 것이다.
쉽게 설명하자면, main메소드를 수행하는 쓰레드는 우선순위가 5이므로 main메소드 내에서 생성하는 쓰레드의 우선순위는 자동적으로 5가 된다.

아래 코드를 보자.

```java
class ThreadEx8 {
	public static void main(String args[]){
		ThreadEx8_1 th1 = new ThreadEx8_1();
		ThreadEx8_2 th2 = new ThreadEx8_2();
		
		th2.setPriority(7);
		
		System.out.println("Priority of th1(-) : " + th1.getPriority());
		System.out.println("Priority of th2(|) : " + th2.getPriority());
		th1.start();
		th2.start();
	}
}
class ThreadEx8_1 extends Thread{
	public void run(){
		for(int i=0; i < 300; i++){
			System.out.print("-");
			for(int x=0; x < 10000000; x++);
		}
	}
}
class ThreadEx8_2 extends Thread{
	public void run(){
		for(int i=0; i < 300; i++){
			System.out.print("|");
			for(int x=0; x < 10000000; x++);
		}
	}
}
```

th1와 th2 모두 main 메소드를 실행하는 우선순위인 5를 상속받았다.
여기서 th2의 우선순위를 7로 변경한 후에 start()를 호출하여 쓰레드를 실행하였다.
**여기서 중요한 것은 쓰레드를 실행하기 전에 우선순위를 변경할 수 있다는 점이다.**

위의 코드에서 아무 것도 안하는 for문은 쓰레드의 작업을 지연시키기위한 코드이다.
이전의 예제와 달리 th2의 작업 실행시간이 길어졌다는 것을 확인 할 수가 있다.

### STEP 2.4 쓰레드 그룹
쓰레드 그룹은 서로 관련된 쓰레드를 그룹으로 다루기 위한 것이다. 

이러한 쓰레드 그룹을 사용하는 이유는 자신이 속한 쓰레드 그룹이나 하위 쓰레드 그룹은 변경할 수 있지만 다른 쓰레드 그룹의 쓰레드는 변경하지 못하게 하기 위함이다. (보안성)

쓰레드 그룹의 주요 생성자와 메소드는 아래와 같다.

| 생성자 / 메소드                                                                                                                                                 	| 설 명                                                                                                                                                                                                  	|
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| ThreadGroup(String name)                                                                                                                                        	| 지정된 이름의 새로운 쓰레드 그룹을 생성                                                                                                                                                                	|
| ThreadGroup(ThreadGroup parent, String name)                                                                                                                    	| 지정된 쓰레드 그룹에 포함되는 새로운 쓰레드 그룹 생성                                                                                                                                                  	|
| int activeCount()                                                                                                                                               	| 쓰레드 그룹에 포함된 활성상태에 있는 쓰레드의 수를 반환                                                                                                                                                	|
| int activeGroupCount()                                                                                                                                          	| 쓰레드 그룹에 포함된 활성상태에 있는 쓰레드 그룹의 수를 반환                                                                                                                                           	|
| void checkAccess()                                                                                                                                              	| 현재 실행중인 쓰레드가 쓰레드 그룹을 변경할 권한이 있는지 체크                                                                                                                                         	|
| void destroy()                                                                                                                                                  	| 쓰레드 그룹과 하위 쓰레드 그룹까지 모두 삭제한다.                                                                                                                                                      	|
| int enumerate(Thread[] list) int enumerate(Thread[] list, boolean recurse) int enumerate(ThreadGroup[] list) int enumerate(ThreadGroup[] list, boolean recurse) 	| 쓰레드 그룹에 속한 쓰레드 또는 하위 쓰레드 그룹의 목록을 지정된 배열에 담고 그 개수를 반환 두 번째 매개변수인 recurse의 값을 true로 하면 하위 쓰레드 그룹에 쓰레드 또는 쓰레드 그룹까지 배열에 담는다. 	|
| int getMaxPriority()                                                                                                                                            	| 쓰레드 그룹의 최대우선순위를 반환                                                                                                                                                                      	|
| String getName()                                                                                                                                                	| 쓰레드 그룹의 이름을 반환                                                                                                                                                                              	|
| ThreadGroup getParent()                                                                                                                                         	| 쓰레드 그룹의 상위 쓰레드그룹을 반환                                                                                                                                                                   	|
| void interrupt()                                                                                                                                                	| 쓰레드 그룹에 속한 모든 쓰레드를 interrupt                                                                                                                                                             	|
| boolean isDaemon()                                                                                                                                              	| 쓰레드 그룹이 데몬 쓰레드그룹인지 확인                                                                                                                                                                 	|
| boolean isDestroyed()                                                                                                                                           	| 쓰레드 그룹이 삭제되었는지 확인                                                                                                                                                                        	|
| void list()                                                                                                                                                     	| 쓰레드 그룹에 속한 쓰레드와 하위 쓰레드그룹에 대한 정보를 출력                                                                                                                                         	|
| boolean parentOf(ThreadGroup g)                                                                                                                                 	| 지정된 쓰레드 그룹의 상위 쓰레드그룹인지 확인                                                                                                                                                          	|
| void setDeamon(boolean daemon)                                                                                                                                  	| 쓰레드 그룹을 데몬 쓰레드그룹으로 설정, 해제                                                                                                                                                           	|
| void setMaxPriority(int pri)                                                                                                                                    	| 쓰레드 그룹의 최대우선순위를 설정                                                                                                                                                                      	|

쓰레드를 쓰레드 그룹에 포함시키려면 Thread의 생성자를 이용해야한다.

> Thread(ThreadGroup group, String name)  
> Thread(ThreadGroup group, Runnable target)  
> Thread(ThreadGroup group, Runnable target, String name)  
> Thread(ThreadGroup group, Runnable target, String name, long stackSize)  

모든 쓰레드는 반드시 쓰레드 그룹에  포함되어야 하기 때문에, 쓰레드 그룹을 지정하는 **생성자를 사용하지 않은 쓰레드는 기본적으로 자신을 생성한 쓰레드와 같은 쓰레드 그룹에 속하게 된다.** 

아래 예시는 쓰레드 그룹과 쓰레드를 생성하고 main.list()를 호출해서  main쓰레드 그룹의 정보를 출력하는 예제이다.

```java
class ThreadEx9 {
	public static void main(String args[]) throws Exception{
		ThreadGroup main = Thread.currentThread().getThreadGroup();
		ThreadGroup grp1 = new ThreadGroup("Group1");
		ThreadGroup grp2 = new ThreadGroup("Group2");
		
		// ThreadGroup(ThreadGroup parent, String name);
		ThreadGroup subGrp1 = n ew ThreadGroup(grp1, "SubGroup1");
		
		grp1.setMaxPriority(3);
		
		Runnable r = new Runnable(){
			public void run(){
				try{
					Thread.sleep(1000);
				} catch(InterruptedException e) {}
			}
		};
		//Thread(ThreadGroup tg, Runnable r, String name)
		new Thread(grp1, r, "th1").start();
		new Thread(subGrp1, r, "th2").start();
		new Thread(grp2, r, "th3").start();
		System.out.println(">> List of TheadGroup : " + main.getName() + ", Active ThreadGroup: " + main.activeGroupCount() + ", Active Thread: " + main.activeCount());
		main.list();
	}
}
```

결과 창을 보면, 쓰레드 그룹에 포함된 하위 쓰레드 그룹이나 쓰레드는 들여쓰기를 이용해서 구별하였다. 새로 생성한 모든 쓰레드 그룹은 main쓰레드 그룹의 하위 쓰레드 그룹으로 포함되어 있다는 것과 setMaxPriority()는 쓰레드가 쓰레드 그룹에 추가되기 전에호출되어야 하며, 쓰레드 그룹 grp1의 최대우선순위를 3으로 했기 때문에, 후에 여기에 속하게 된 쓰레드 그룹과 쓰레드가 영향을 받았다는 것을 알 수가 있다.

### STEP 2.5 데몬 쓰레드 

데몬 쓰레드는 **다른 일반 쓰레드의 작업을 돕는 보조적인 역할을 수행하는 쓰레드이다.**

일반 쓰레드가 모두 종료되면 데몬 쓰레드는 강제로 종료되는데 이러한 이유는 데몬 쓰레드는 일반 쓰레드를 돕는 쓰레드이기 때문에 일반 쓰레드가 끝나면 의미가 없어지기 때문이다.

이 점을 제외하고는 데몬 쓰레드와 일반 쓰레드의 차이는 없다. 데몬 쓰레드의 예로는 가비지 컬렉터, 화면자동갱신 등이 있다.

데몬 쓰레드는 무한루프와 조건문을 이용해서 실행 후 대기하고 있다가 특정 조건이 만족되면 작업을 수행하고 다시 대기하도록 작성한다.

데몬 쓰레드의 작성법과 실행법은 일반 쓰레드와 같으며 _(start() & run() 그리고 Runnable 구현 혹은 Thread 상속)_ 다만 쓰레드를 생성한 다음 실행 전에 **setDaemon(ture)** 를 호출하면 된다. 그리고 데몬 쓰레드가 생성한 쓰레드는 자동적으로 데몬 쓰레드가 된다는 점도 알아두자.

> boolean isDeamon() //쓰레드가 데몬 쓰레드인지 확인 참이면 true  
> void setDeamon(boolean on) //쓰레드를 데몬 쓰레드 또는 사용자 쓰레드로 변경  ture면 데몬쓰레드 false면 사용자 쓰레드  

아래 예시로 데몬 쓰레드를 사용하는 것을 해보자.

```java
class ThreadEx10 implements Runnable {
	static boolean autosave = false;

	public static void main(String args[]) {
		Thread t = new Thread(new ThreadEx10());
		//아래 코드가 없으면 코드가 무한루프에 빠져 종료되지않는다. 
		t.setDaemon(true);
		t.start();
		
		for(int i=1; i <= 10; i++) {
			try{
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
			System.out.println(i);

			if(i==5)
				autoSave = true;
		}
	}
	System.out.println("프로그램을 종료합니다.");

	public void run(){
		while(true){
			try{
				//3초
				Thread.sleep(3 * 1000);
			} catch(InterruptedException e) {}
			if(autoSave){
				autoSave();
			}
		}
	}
	public void autoSave(){
		System.out.println("작업파일이 자동저장되었습니다.");
	}
}

```

위의 코드는 3초마다 변수 autoSave 값을 확인하여 그 값이 true면, autoSave()를 호출하는 일을 무한히 반복하는 쓰레드이다. 만약에 이 쓰레드가 데몬 쓰레드가 아니라면 이 프로그램을 강제종료하지 않는 한 영원히 종료되지 않는다.  

왜냐하면 main 쓰레드가 끝날때 데몬 쓰레드로 설정된 thread t가 종료되는데 이것이 그냥 쓰레드면 계속해서 돌아가기 때문이다.

### STEP 2.6 쓰레드 실행제어 

쓰레드 프로그래밍이 어려운 이유는 동기화와 스케줄링 때문이다. 
우리는 이전 챕터에서 우선순위를 주어 쓰레드간의 스케줄링을 하는 방법을 배우긴 했지만 **이것만으로 쓰레드를 다루기엔 턱 없이 부족하다.** 따라서 아래와 같은 스케줄링과 관련된 메소드가 존재하며, 숙지하여 스케줄링을 잘 설정해야 된다.

_쓰레드 스케줄링 관련 메소드_

| 메소드                                                                   	| 설명                                                                                                                              	|
|--------------------------------------------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------------	|
| static void sleep(long millis) static void sleep(long millis, int nanos) 	| 지정된 시간동안 쓰레드를 일시정지시킨다. 지정한 시간이 지나고 나면, 자동으로 실행대기상태가 된다.                                 	|
| void join() void join(long millis) void join(long millis, int nanos)     	| 지정된 시간동안 쓰레드가 실행되도록 한다. 지정된 시간이 지나거나 작업이 종료되면 join()을 호출한 쓰레드로 돌아와 실행을 계속한다. 	|
| void interrupt()                                                         	| sleep()이나 join()에 의해 일시정지된 쓰레드를 깨워서 실행대기상태로 만든다.                                                       	|
| void suspend()                                                           	| 쓰레드를 일시정지시킨다. resume()이 호출되어야 실행대기상태가 된다.                                                               	|
| void resume()                                                            	| suspend()에 의해 정지된 쓰레드를 다시 실행대기상태로 복구한다.                                                                    	|
| static void yield()                                                      	| 실행 중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보하고 자신은 실행대기상태가 된다.                                         	|

_쓰레드 상태_

| 상태                   	| 설명                                                                  	|
|------------------------	|-----------------------------------------------------------------------	|
| NEW                    	| 쓰레드가 생성되고 아직 start()가 호출되지 않은 상태                   	|
| RUNNABLE               	| 실행 중 또는 실행 가능한 상태                                         	|
| BLOCKED                	| 동기화블럭에 의해서 일시정지된 상태(lock이 풀릴 때까지 기다리는 상태) 	|
| WAITING, TIMED_WAITING 	| 쓰레드의 작업이 종료되지는 않았지만, 실행가능하지 않은 일시정지 상태  	|
| TERMINATED             	| 쓰레드의 작업이 종료된 상태                                           	|

좀 더 정리하면 쓰레드의 생명주기(Life-Cycle)은 아래와 같다고 볼 수가 있다.

![](&&&SFLOCALFILEPATH&&&%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202018-08-21%205.07.54%20PM.png)

1. 쓰레드를 생성하고 start()를 호출하면 바로 실행되는 것이 아니라 실행대기열에 저장되어 자신의 차례가 될 때까지 기다린다. (RUNNABLE인 상태의 쓰레드가 여러개 존재 가능)
2. 실행대기상태에 있다가 자신의 차례가 되면 실행상태가 된다.
3. 주어진 실행시간이 다되거나 yield()를 만나면 다시 실행대기상태가 되고 다음 차례의 쓰레드가 실행상태가 된다.
4. 실행 중 suspend(), sleep(), wait(), join(), I/O block에 의해 일시정지상태가 될 수 있다.
5. 지정된 일시정지시간이 다되거나(time-out), notify(), resume(), interrupt()가 호출되면 일시정지상태를 벗어나 다시 실행대기열에 저장되어 자신의 차례를 기다린다.
6. 실행을 모두 마치거나 stop()이 호출되면 쓰레드는 소멸된다.

번호 순서대로 쓰레드가 수행되는 것이 아니라 케이스를 나열하기 위해서 번호를 붙였으니 위의 그림을 참고하자.

#### STEP 2.6.1 sleep()
sleep()은 일정시간동안 쓰레드를 멈추게 한다.
밀리세컨즈와 나노세컨즈의 단위로 시간 지정이 가능하다. 

키포인트 - **sleep()에 의해 일시정지 상태가 된 쓰레드는 지정된 시간이 다 되거나 intterupt()가 호출되면, InterruptedException이 발생되어 실행대기 상태가 된다는 것이다.**

이러한 익셉션 발생때문에 항상 sleep 사용시에는 try-catch 블록을 사용하여 예외처리를 해야된다.

다음과 같은 예시코드를 보자.

```java
class ThreadEx12{
	public static void main(String args[]){
		ThreadEx12_1 th1 = new ThreadEx12_1();
		ThreadEx12_2 th2 = new ThreadEx12_2();
		th1.start();
		th2.start();

		try{
			th1.sleep(2000);
		} catch(InterruptedException e) {}
		System.out.print("<<main 종료>>");
	}
}

class ThreadEx12_1 extends Thread{
	public void run(){
		for(int i=0; i < 300; i++)
			System.out.print("-");
			System.out.print("<<th1 종료>>");
	}
}

class ThreadEx12_2 extends Thread{
	public void run(){
		for(int i=0; i < 300; i++)
			System.out.print("|");
			System.out.print("<<th2 종료>>");
	}
}
```

코드를 실행시켜 결과를 보면, th1의 작업이 가장 먼저 종료되고, th2, main 순서임을 알 수가 있다. 하지만 우리는 **th1.sleep(2000);** 주어서, th2 -> th1 -> main 순서로 예상하고 있었다.

이런 예상치 못한 경우가 발생한 이유는 sleep() 메소드는 인스턴스 메소드가 아니라 **클래스 메소드(static)** 이기 때문이다. sleep() 메소드가 동작하는 포인트는 **현재 실행중인 Thread**이다. 따라서 th1.sleep()을 했을 시에 main메소드를 실행하는 main 쓰레드에 적용된 것이다. 그러므로 참조변수가 아닌 **Thread.sleep()**과 같이 작성해야된다.

그렇게해야 대기열에 먼저 올라간 th1 쓰레드에 영향을 받기때문이다.

#### STEP 2.6.2 interrupt()와 interrupted()
interrupt()와 interrpupted()는 쓰레드의 작업을 취소하는 역할을 한다.
진행 중인 쓰레드의 작업이 끝나기 전에 취소할 때 사용하는 메소드이다.

interrupt()는 쓰레드의 interrupted 상태를 바꾸는 것이다.
그리고 interrupted()는 쓰레드에 대해 interrupt()가 호출되었는지 알려준다.

> void interrupt() //쓰레드의 interrupted상태를 false -> ture로 변경  
> boolean isInterrputed() //쓰레드의 interrupted상태를 반환  
> statice boolean interrupted() 현재 쓰레드의 interrupted상태를 반환 후, false로 변경  

쓰레드가 sleep(), wait(), join()에 의해 '일시정지 상태'에 있을 떄, 해당 쓰레드에 대해 interrupt()를 호출하면, sleep(), wait(), join()에서 InterruptedException이 발생하고 쓰레드는 '실행대기 상태'로 바뀐다. 즉, 멈춰있던 쓰레드를 깨워서 실행가능한 상태로 만드는 것이다.

```java
import javax.swing.JOptionPane;

class ThreadEx13 {
	public static void main(String[] args) throws Exception {
		ThreadEx13_1 th1 = new ThreadEx13_1();
		th1.start();
		
		String input = JOptionPane.showInputDialog("아무 값이나 입력하세요.");
		System.out.println("입력하신 값은 " + input + "입니다.");
		th1.interrupt();
		System.out.println("isInterrupted() : " + th1.isInterrupted());
	}
}

class Thread13_1 extends Thread {
	public void run() {
		int i = 10;
		while(i!=0 && !isInterrrupted()) {
			System.out.println(i--);
			for(long x=0; x<2500000000L; x++);
		}
		System.out.println("카운터가 종료되었습니다.");
	}
}
```

이전 예제와 달리 사용자의 입력이 끝나자 interrupt()에 의해 카운트다운이 중간에 멈추었다.

```java
import javax.swing.JOptionPane;

class ThreadEx14 {
	public static void main(String[] args) throws Exception {
		ThreadEx14_1 th1 = new ThreadEx14_1();
		th1.start();
		
		String input = JOptionpane.showInputDialog("아무 값이나 입력하세요.");
		System.out.println("입력하신 값은 " + input + "입니다.");
		th1.interrupt(); //interrupted상태가 true로 된다.
		System.out.println("isInterrupted():" + th1.isInterruptd());
	}
}

class ThreadEx14_1 extends Thread {
	public void run(){
		int i = 10;
		while(i!=0 && !isInterrupted()){
			System.out.println(i--);	
			try{
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
		}
		System.out.println("카운트가 종료되었습니다.");
	}
}
```

이전 예제와 다르게 for문을 Thread.sleep(1000)로 바꿨는데 카운트가 종료되지 않았고, isInterrupted()의 결과를 보니 false이다. 

그 이유는 Thread.sleep()에서 InterruptedException이 발생했기 때문이다. 
sleep()에 의해 쓰레드가 잠시 멈춰있을 때, interrupt()를 호출하면 InterruptedException이 발생되고 쓰레드의 interrupted상태는 false로 자동 초기화 된다.

해결 법은 
```java
	//이전 코드
		try{
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
	//다음 코드
		try{
				Thread.sleep(1000);
			} catch(InterruptedException e) {
				interrupt(); //이런식으로 추가해야 해결할 수가 있다.
			}

```


#### STEP 2.6.3 suspend(), resume(), stop()
suspend()는 sleep()처럼 쓰레드를 멈추게 한다. suspend()에 의해 정지된 쓰레드는 resume()을 호출해야 다시 실행대기 상태가 된다. stop()은 호출되는 즉시 쓰레드가 종료된다. 

suspend(), resume(), stop()은 쓰레드의 실행을 제어하는 가장 손쉬운 방법이지만, suspend()와 stop()이 **교착상태(Deadlock)를 일으키기 쉽게 작성되있으므로 사용이 권장되지 않는다.** 

```java
class ThreadEx15 {
	public static void main(String args[]) {
		RunImplEx15 r = new RunImplEx15();
		Thread th1 = new Thread(r, "*");
		Thread th2 = new Thread(r, "**");
		Thread th3 = new Thread(r, "***");
		th1.start();
		th2.start();
		th3.start();
		
		try { 
			Thread.sleep(2000);
			th1.suspend();
			Thread.sleep(2000);
			th2.suspend();
			Thread.sleep(3000);
			th1.resume();
			Thread.sleep(3000);
			th1.stop();
			th2.stop();
			Thread.sleep(2000);
			th3.stop();
		} catch(InterruptedException e) {}
	}
}

class RunImplEx15 implements Runnable {
	public void run(){
		while(true) {
				System.out.println(Thread.currentThread().getName());
try{
	Thread.sleep(1000);
} catch(InterruptedException e) {}
		}
	}
}
```
 
이러한 예제는 교착상태가 일어날 일이 없으므로 suspend()와 stop()을 사용해도 아무런 문제가 없지만, 더 복잡할 경우에는 사용하지 않는 것이 좋다.

#### STEP 2.6.4 yield()
yield() : 다른 쓰레드에게 양보한다.

예를 들면, 스케쥴러에 의해 1초의 실행시간을 할당받은 쓰레드가 0.5초의 시간동안 작업한 상태에서 yield()가 호출되면, 나머지 0.5초는 다른 쓰레드에게 양보한다.

yield()와 interrupt()를 적절하게 사용하면, 프로그램의 응답성을 높이고 보다 효율적인 실행이 가능하게 할 수 있다.

다음과 같이 사용할 수가 있다.

```java
class ThreadEx18 {
	public static void main(String args[]) {
		ThreadEx18_1 th1 = new ThreadEx18_1("*");
		ThreadEx18_1 th2 = new ThreadEx18_1("**");
		ThreadEx18_1 th3 = new ThreadEx18_1("***");
		th1.start();
		th2.start();
		th3.start();
		
		try{
			Thread.sleep(2000);
			th1.suspend();
			Thread.sleep(2000);
			th2.suspend();
			Thread.sleep(3000);
			th1.resume();
			Thread.sleep(3000);
			th1.stop();
			th2.stop();
			Thread.sleep(2000);
			th3.stop();
		} catch (InterruptedException e) {}
	}
}

class Thread18_1 implements Runnable {
	boolean suspended = false;
	boolean stopped = false;
	
	Thread th;
	
	ThreadEx18_1 (String name){
		th = new Thread(this, name);
	}
	public void run() {
		String name = th.getName();
		while(!stopped) {
			if(!suspended) {
				System.out.println(name);
				try {
					Thread.sleep(1000);
				} catch(InterruptedException e) {
					System.out.println(name + " - interrupted");
				}
			} else {
				Thread.yield();
			}
		}
		System.out.println(name + " - stopped");
}

public void suspend() {
	suspended = true;
	th.interrupt();
	System.out.println(th.getName() + " - interrupt() by suspend()");
}

public void stop() {
	stopped = true;
	th.interrupt();
	System.out.println(th.getName() + " - interrupt() by stop()");
}

public void resume(){ suspended = false;}
public void start() { th.start(); }
```

이 전의 예제에 yield()와 intererupt()를 추가한 예제이다.

#### STEP 2.6.5 join()
**join()** 메소드는 **다른 쓰레드의 종료를 기다리는 것** 이다. 

이후에 살펴볼 _synchronized_ 사용법에서 wait()와 notify()를 사용하는 것과 흡사하다고 볼 수 있다.

아래 예제를 보자
```java
    public class ThreadEx19 extends Thread{
        public void run(){
            for(int i = 0; i < 5; i++){
                System.out.println("MyThread5 : "+ i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        } // run
    }
```

+ 위의 코드는 1초씩 쉬면서 숫자를 출력하는 부분이다.

```java
public class JoinExam { 
        public static void main(String[] args) {
            MyThread5 thread = new MyThread5();
            // Thread 시작 
            thread.start(); 
            System.out.println("Thread가 종료될때까지 기다립니다.");
            try {
                // 해당 쓰레드가 멈출때까지 멈춤
                thread.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Thread가 종료되었습니다."); 
        }   
    }
```

+ 위의 코드는 Join()문을 활용하여 쓰레드가 종료할 때까지 대기하는 코드이다. 

자세한 내용은 바로 뒤에 _synchronized_ 을 정리하면서 wait()와 notify()와 비교하기로 해보겠다.

### STEP 2.7 쓰레드의 동기화 
싱글쓰레드 환경에서는 한 개의 쓰레드가 객체를 독차지해서 사용하면 되지만, 멀티 쓰레드 환경에서는 **쓰레드들이 객체를 공유해서 작업** 해야하는 경우가 있으며, 이렇게 공유 자원으로 쓰이는 영역을 유식하게 **임계영역(Critical Section) [^2]** 이라한다.
이를 해결하기 위해서 나온 개념이 **세마포어(Semaphore) [^3], 상호배제(Mutex) [^4]** 등이 있다.

공유 자원을 사용하게 될 때 무슨 문제가 나오길래 이런 해법들이 나타나게 된 것일까?
그것을 알기위해서 아래 예제를 돌려보자.

+ MultiThreadCollisionEx.java

```java
public class MultiThreadCollisionEx {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
 
        User1 user1 = new User1();
        user1.setCalculator(calc);
        user1.start();
 
        User2 user2 = new User2();
        user2.setCalculator(calc);
        user2.start();
    }
}
```

+ Calculator.java

```java
public class Calculator {
    private int memory;
 
    public int getMemory() {
        return memory;
    }
 
    public void setMemory(int memory) {
        this.memory = memory;
        try {
            Thread.sleep(2000); // 2초간 sleep
        } catch (Exception e) {
        }
 
        System.out.println(Thread.currentThread().getName() + ": " + this.memory);
    }
}
```

+ User1.java

```java
public class User1 extends Thread {
    private Calculator calc;
 
    public void setCalculator(Calculator calc) {
        this.setName("calcUser1");
        this.calc = calc;
    }
 
    public void run() {
        calc.setMemory(100);
    }
}
```

+ User2.java

```java
public class User2 extends Thread {
    private Calculator calc;
 
    public void setCalculator(Calculator calc) {
        this.setName("calcUser2");
        this.calc = calc;
    }
 
    public void run() {
        calc.setMemory(50);
    }
 
}
```


실행해보면 분명 User1에서 setMemory(100)을 실행했으나 User2에서 객체에 접근하면서 _(setMemory(50))_객체 값이 바뀌면서 User1이 사용하던 객체의 값도 바뀌는 것을 확인할 수가 있다.

**어떻게 해결할 수가 있을까?**
바로 **동기화 메소드 및 동기화 블록** 을 사용해서 해결할 수가 있다.  동기화 메소드 및 동기화 블록은 **현재 데이터를 사용하고 있는 해당 쓰레드를 제외하고, 나머지 쓰레드들은 데이터에 접근할 수 없도록 막기위한 것** 라고 볼 수가 있다. 
이런 것을 **thread-safe** 환경을 만든다고 한다.

#### STEP 2.7.1 synchronized를 이용한 동기화

위에서 말한 동기화 메소드 및 동기화 블록을 Java에서 어떻게 제공할까? 
바로 **동기화 키워드(Synchronized)** 를 이용하여 제공한다.

하지만 명심해야할 점은 **동기화 키워드를 남용할 경우에는 오히려 프로그램 성능이 저하된다는 것이다.**

그렇다면 자바코드에서는 어떻게 사용할까? 아래를 참고하자.

```java
 //동기화 메소드 사용
 public synchronized void method() {
		//코드
 }
```

```java
 //객체 변수에 사용하는 경우
private Object sharedObj = new Object();
public void method(){
	synchronized(sharedObj){
		//코드
	}
}
```

이것을 응용하여, 위의 공유자원 문제를 해결한 소스코드를 보자. 

+ 수정한 Calculator.java 
```java
public class Calculator {
    private int memory;
 
    public int getMemory() {
        return memory;
    }
 	  //메소드 동기화처리
    public synchronized void setMemory(int memory) {
        this.memory = memory;
        try {
            Thread.sleep(2000); // 2초간 sleep
        } catch (Exception e) {
        }
 
        System.out.println(Thread.currentThread().getName() + ": " + this.memory);
    }
}
```

#### STEP 2.7.2 wait()와 notify(), notifyAll()

wait()와 notify()는 동기화블록 안에서 사용되는 상태제어 메소드이다.
위에서 봤던 join()과 같은 역할을 한다고 볼 수가 있다. 

일단, 먼저 함수의 원형부터 보자.

+ public final void wait(long timeout) throws interruptedException
+ public final void wait(long timeout, int nanos) throws interruptedException
+ public final void wait() throws interruptedException
+ public final void notifyAll()
+ public final void notify() 

차이는 "final이라는 키워드와 wait에는 Exception이 전부 붙어 있구나"정도만 봐도 된다.

**wait()** 는 기다리는 뜻이며, **notify()** 는 알리다라는 뜻이다.
즉, wait()는 동기화된 공유자원에 접근할 때 동기화된 공유자원을 다른 쓰레드가 사용하고 있다면 기다리라는 신호이며, 해당 쓰레드가 공유자원을 다 사용하고 나면 이 자원을 wait하고 있는 쓰레드가 있다면 사용하라고 알리는 용도(notify)이다.

이 부분은 운영체제에서 Dead Lock에 관한 선행지식이 없다면 이해하기가 좀 힘들 수가 있는데 일단 위에서 말한대로 하나의 공유자원에 여러 개의 쓰레드가 접근하게 되면 문제가 발생할 수 있다는 것을 기억한다면 아래를 이해해보도록 한다. 

+ 하나의 공유자원에 여러 개의 쓰레드가 몰리게되는 현상 -> 병목현상(Bottle Neck) [^5] 발생 -> 여기서 문제가 생기면 교착상태(Dead Lock) [^6] 발생 
+ 이때 synchronized를 사용하여 만일 한 쓰레드가 공유자원을 사용한다면, 그 자원을 잠궈서 다른 쓰레드가 접근하지 못하도록 함.
+ 그러나 이 키워드만으로는 부족 -> wait()와 notify() 메소드를 이용하여 미세한 제어 수행 

중요한 점은 한 쓰레드가 공유자원을 사용시 시에 다른 쓰레드에게 나중에 쓰라고 말하는 것이 wait()이고, 대기 상태인 쓰레드가 실행상태가 되는 메소드는 notify()와  notifyall()이라고 볼 수가 있다.

아래 간단한 예시를 동작시켜보자

```java
import java.util.*;

class SyncStack{
	private Vector buffer = new Vector();
	public synchronized char pop(){
		char c;
		while(buffer.size()==0){
		try{
			System.out.println("stack대기:");
			this.wait();
		}catch(Exception e){}
	}
	Character cr = ((Character)buffer.remove(buffer.size()-1));
	c = cr.charValue();
	System.out.println("stack삭제:" + c);
	return c;
}

public synchronized void push(char c){
	this.notify();
	Character charObj = new Character(c);
	buffer.addElement(charObj);
	System.out.println("stack삽입:" + c);
	}
}

class PopRunnable extends Thread{
	public void run(){
		SyncTest.ss.pop();
	}
}

class PushRunnable extends Thread{
	private char c;
	
	public PushRunnable(char c){
	this.c =c;
	}

	public void run(){
		SyncTest.ss.push(c);
	}
}

public class SyncTest {
	public static SyncStack ss =new SyncStack();
	public static void main(String[] args){

	//SyncStack에 5데이터삽입

	new PushRunnable('J').start();
	new PushRunnable('A').start();
	new PushRunnable('B').start();
	new PushRunnable('O').start();
	new PushRunnable('O').start();

	new PopRunnable().start();//O
	new PopRunnable().start();//O
	new PopRunnable().start();//B
	new PopRunnable().start();//A
	new PopRunnable().start();//J

	new PopRunnable().start();//대기상태

	try{
		Thread.sleep(5000);
	}catch(Exception e){}
		System.out.println("===== passed 5 seconds======");
	new PushRunnable('K').start();
	}
}
```

위의 예시는 5개 값을 집어 넣고, 6개를 추출하는 것이다.
따라서 마지막 pop 연산은 push를 기다리는 wait상태가 되고, 임의의 시점에서 문자가 삽입되서 notify해주면 wait에서 깨어나서 다시 pop작업을 하는 예제이다.

## STEP 3. 캐시와 메모리간의 값의 불일치 해결
먼저 volatile은 멀티쓰레딩 환경에서 동기화 해주는 것이다.
즉, 읽기 쓰기시에 어떤 스레드가 값을 변경하든 항상 최신값을 읽게 해준다. 
이 부분은 syncronized키워드와 같아보이지만 아래 설명하면서 차이점을 서술해보도록 하겠다.

**캐시와 메모리간의 값의 불일치 해결** 어려운 말이지만 생각을 잘하면 쉽다.

> int count = 10;   

이라는 변수를 선언하고, 쓰레드 A와 B가 사용한다고 가정한다.
A쓰레드가 count를 읽어 조작하고, B쓰레드도 같이 count를 읽어 조작한다.
이 경우에는 두 쓰레드간에 count의 값은 서로 동일한 값(서로 다른 메모리 주소)을 가르키지 않는다.

**쓰레드가 자신만의 저장 영역에 원본의 값을 복사하여 조작하기 때문이다.**
그래서 쓰레드마다 count는 다른 값을 가지고 있을 수 있다.
따라서 **A에서 조작한 값을 B에서는 읽을 수 없는 현상이 생길 수가 있다.**

그리고 쓰레드 작업이 끝난 후에 조작한 값을 원래 값으로 돌리는데 그 작업이 끝나지 않거나 **배치 최적화(reordering)** 등으로 그러지 않을 수가 있다. 

이런 경우들 때문에 변수 count는 쓰레드간 값 불일치가 발생한다.


### STEP 3.1 volatile
위에서 말한 쓰레드간 값 불일치 이유중에서도 배치 최적화를 회피하는 방법이 있는데 
그것이 바로 **volatile** 키워드이다.

프로그래머가 코드를 작성하고 컴파일을 하면 컴파일 과정에서 좀 더 빠르게 실행될 수 있도록 최적화하는 것을 리오더링이라 한다. 

이것은 위에 말한 것과 같이 멀티쓰레드 환경에서는 읽기와 쓰기의 순서가 바뀌면서, 문제가 발생할 수가 있다. volatile 변수를 사용하면 리오더링에서 제외되며, 항상 프로그래머가 지정한 순서로 읽기 및 쓰기를 수행한다.

그렇다면? syncronized와 차이점은 무엇일까? 
바로 원자성에 있다. 

int보다 큰 경우에 (JVM 환경에 따라 다르지만) long과 double을 선언할 때 예시를 통해서 설명해보겠다. 

> long stat = 324L;  

이러한 코드를 선언했을 때, long은 데이터형이 8바이트 즉, 64비트이다. 이때 해당 JVM이 32비트 단위로 끊어서 할당한다고 가정한다면 첫 32비트 할당 + 32비트 할당으로 두번의 작업으로 선언이 되는 것이다. 

**하지만 첫 32비트 할당시에 다른 쓰레드가 값을 읽어간다면?**

바로 문제가 발생한다.

해결방법은 바로 volatile 키워드를 stat에 선언하면 할당이 원자성을 갖게되어 문제가 발생하지 않는다.

그렇다면? syncronized와 차이점은 무엇인가?

> int val = stat + 10;  

이러한 변수를 선언했다고 가정한다면 volatile 선언을 해도 thread-safe가 아니다.
쓰레드의 접근 순서에 따라 어떤 쓰레드는 10을 더한 값을 가져가기도, 안하기도 한 값을 읽기도 한다. 

위 작업은 원자성이 깨지기때문이다.

즉, **stat를 int로 캐스팅 + 10을 더하고 + val**에 대입이 세가지 연산이 돌아가기때문인데 이때, syncronized 키워드는 작업의 원자성을 주어 이러한 문제를 해결한다.


# REFERENCE 
1. [프로세스와 스레드의 차이](https://brunch.co.kr/@kd4/3)
2. [Java - (멀티쓰레딩 4) 쓰레드에서 값 반환](http://hochulshin.com/java-multithreading-returning-values-from-task/)
3. [Java - (멀티쓰레딩 9) 쓰레드 Join하기](http://hochulshin.com/java-multithreading-thread-join/)
4. [Java 멀티 스레드 - 우선순위, 동기화 메소드](http://palpit.tistory.com/728)
5. [JAVA - wait(), notify(), notifyAll()원형](http://ayonc.tistory.com/33) 
6. [자바에서의 volatile 키워드](http://blog.javarouka.me/2012/04/volatile-keyword-in-java.html)

---

[^1]:[Context switch - Wikipedia](https://en.wikipedia.org/wiki/Context_switch)
[^2]:[Critical section - Wikipedia](https://en.wikipedia.org/wiki/Critical_section)
[^3]:[Semaphore (programming) - Wikipedia](https://en.wikipedia.org/wiki/Semaphore_(programming))
[^4]:[Lock (computer science) - Wikipedia](https://en.wikipedia.org/wiki/Lock_(computer_science))
[^5]:[Bottleneck (software) - Wikipedia](https://en.wikipedia.org/wiki/Bottleneck_(software))
[^6]:[Deadlock - Wikipedia](https://en.wikipedia.org/wiki/Deadlock)