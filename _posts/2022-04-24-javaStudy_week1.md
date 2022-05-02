---
title: "JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가."
excerpt: 자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.
excerpt_separator: "<!--more-->"
categories:
  - Java Study
tags:
  - JVM
  - java

toc: true
toc_sticky: true
date: 2022-04-24
---

## 목표

자바 소스 파일(.java)을 JVM으로 실행하는 과정 이해하기.

## 학습할 것

- JVM이란 무엇인가
- 컴파일 하는 방법
- 실행하는 방법
- 바이트코드란 무엇인가
- JIT 컴파일러란 무엇이며 어떻게 동작하는지
- JVM 구성 요소
- JDK와 JRE의 차이

---

## JVM이란 무엇인가

`Java Virtual Machine`  

>Write once, run anywhere  

Java는 OS에 종속적이지 않다는 특징을 가지고 있습니다. OS에 종속받지 않고 실행되기 위해서 필요한 것이 JVM인데,

![imgage](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F0kg24%2Fbtq4YOOQH4J%2FEF2ISOpkYA36a1flwtLEmK%2Fimg.png)

자바 소스코드 `*.java` 를 컴파일하여 `*.class` 바이트코드로 만들면 이 코드가 JVM 환경에서 실행됩니다.

## 컴파일 하는 방법, 실행하는 방법

Java 소스코드는 컴파일 과정을 통해 JVM에서 실행할 수 있는 코드로 변환됩니다.. 이 컴파일을 통해 생성된 코드를 `byte code`라고 합니다. 터미널에서 `javac *.java`와 같은 명령어로 수행 가능하며, 보통은 IDE를 통해 수행하게 됩니다.

`java <package>.<classname>` 로 실행. `javac`는 파일을 지정하여 컴파일 하지만, `java`는 패키지에 존재하는 클래스를 가리켜서 실행합니다.

## 바이트코드란 무엇인가

특정 하드웨어가 아닌 가상 컴퓨터(JVM)에서 돌아가는 실행 프로그램을 위한 이진 표현법.

사람이 읽기 쉽도록 쓰인 소스코드와 비교하면, 바이트코드는 덜 추상적이며, 더 간결하고, 더 컴퓨터 중심적입니다. 자바에서는 바이트코드 형태로 컴파일되어 저장되며, 실행 전에 JIT 컴파일러에 의해 기계 코드로 번역됩니다. 이 번역으로 인해 프로그램 실행 전에 지연시간이 발생하게 되지만, 보통 인터프리터보다는 훨씬 좋은 성능을 보여줍니다.

## JIT 컴파일러란 무엇이며 어떻게 동작하는지

`Just In Time` 컴파일러. 기존의 인터프리터 방식으로 동작하는 Java의 한계를 보완한 기술.

기존 JVM은 다른 인터프리터 언어와 동일하게 코드를 한 줄 한 줄 읽어서 기계어로 번역하면서 실행했습니다. 아무리 사전에 컴파일 과정을 거쳤다 한들, 이런 번역과정의 반복은 실행속도를 늦추는 원인이 됩니다.

JVM은 가장 자주 실행되는 코드 블록, 메서드 또는 메서드의 일부, 특히 반복문을 모니터링해서 네이티브 코드로 컴파일 시킵니다. 이렇게 하면 불필요한 번역과정을 생략할 수 있기 때문에 더 빠른 실행속도를 보일 수 있습니다.

JIT 컴파일 과정은 별도의 스레드에서 실행되고, JVM 스레드는 JIT 컴파일 스레드의 영향을 받지 않기 때문에 애플리케이션의 실행에 영향을 주지 않습니다. 컴파일이 진행중일 때에는 인터프리터 방식으로 동작하지만, 컴파일이 완료되면 컴파일 된 버전을 사용하게 됩니다. 이를 `on-stack replacement(OSR)` 이라고 합니다.

JIT 컴파일 과정은 컴파일 과정에서는 분명 느려질 것입니다. 하지만, 네이티브 코드로 컴파일 되었을 때의 장점이 더 크므로 이런 성능저하는 무시할 수 있습니다.

JIT 컴파일러는 C1 (클라이언트 컴파일러), C2(서버 컴파일러)로 구분됩니다. C1과 C2는 동작이 다릅니다. C1은 빠르게 실행되지만 덜 최적화 된 코드를 생성하며, C2는 실행 시간이 좀 더 걸리지만 더 최적화된 코드를 생성합니다.

오늘날의 JVM은 두 가지를 모두 사용하며, 처음엔 C1을 사용하지만, 호출의 수가 증가하게 되면, C2를 사용합니다. 이를 tiered compilation이라고 합니다.

## JVM 구성 요소

JVM은 다음의 요소로 구분할 수 있습니다.

- 클래스로더 (Class Loader)
- 런타임 데이터 영역 (Runtime Data Area)
- 실행 엔진 (Execution Engine)

![image](https://user-images.githubusercontent.com/37537207/98539438-aebb0980-22cf-11eb-97a6-7349143c98a9.png)

### 1. 클래스 로더  

클래스 파일을 읽어들이는 부분입니다. 자바는 동적 로딩을 사용하기 때문에 실제 애플리케이션에서 필요한 부분만 로딩해서 사용하게 됩니다.

`로딩` > `링크` > `초기화` 순서로 진행 됩니다.

클래스가 있다면 파일을 읽고 메모리의 메서드 스택 영역에 저장됩니다.

### 2. 런타임 데이터 영역  

JVM의 메모리 부분이라고 볼 수 있습니다.  

아래 다섯가지 주요 영역으로 구분할 수 있습니다.  

1) `스택`  
LIFO 으로 동작하는 자료구조로써 쓰레드마다 런타임 스택을 만들고 JVM 스택에는 프레임에 저장된다. 메서드 하나가 호출될 때마다 새 프레임이 생성되어 스택에 쌓이고, 메서드 호출이 정상 완료되거나 예외가 던져지면 프레임은 스택에서 빠지면서 소멸된다. 쓰레드가 종료되면 스택도 제거된다.

2) `PC 레지스터`  
현재 실행중인 메서드가 네이티브가 아니면, 현재 JVM 명령어 위치에 저장되고, 네이티브면 PC 레지스터에 저장되는 값은 정의되지 않는다.

3) `네이티브 메소드 스택`  
자바가 아닌 다른 언어로 작성된 네이티브 메서드를 지원하기 위해 사용되는 스택. 스레드 단위의 자료구조

4) `힙`  
인스턴스화 된 모든 클래스 인스턴스와 배열, 객체를 저장하게 되는데 모든 JVM 스레드에 공유된다. 힙에 저장된 할당된 메모리 회수 권한은 가비지 컬렉터에 의해서만 회수 가능하다.

5) `메소드`  
런타임 상수풀, 필드와 메소드 데이터 내용 - 클래스 수준의 정보를 저장하게 된다.

### 3. 실행 엔진  

실행엔진은 런타임 데이터 영역에 할당된 Java 바이트코드를 읽어서 기계어로 번역해 실행하는 영역입니다.  

다음의 세 가지 요소를 가집니다.  

1) `인터프리터` : JVM은 자바 바이트코드를 한라인 씩 읽어서 실행  
2) `JIT 컴파일러` : 인터프리터 방식의 단점을 보완  
3) `가비지 컬렉터` : 힙 영역에서 사용하지 않는 개체들을 관리

## JDK와 JRE의 차이

`JDK` : 자바 개발 환경  
컴파일러, 역어셈블러, 디버거, 의존관계분석 등 개발에 필요한 도구 제공

`JRE` : 자바 실행 환경  
자바 실행 명령, 클래스로더와 바이트코드의 실행에 필요한 기본 라이브러리 제공

`JVM` : 자바 가상 머신  
바이트코드 인터프리터, JIT 컴파일러, 링커, 명령어 세트, 가비지 컬렉터, 런타임 데이터 영역(메모리) 등 OS에 독립적으로 실행될 수 있는 추상층 제공

## Deep Dive

[Java 컴파일에서 실행까지 - (1)](https://homoefficio.github.io/2019/01/31/Back-to-the-Essence-Java-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EC%97%90%EC%84%9C-%EC%8B%A4%ED%96%89%EA%B9%8C%EC%A7%80-1/)

[Java 컴파일에서 실행까지 - (2)](https://homoefficio.github.io/2019/01/31/Back-to-the-Essence-Java-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EC%97%90%EC%84%9C-%EC%8B%A4%ED%96%89%EA%B9%8C%EC%A7%80-2/)