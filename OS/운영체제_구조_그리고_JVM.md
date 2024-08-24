### **목차**
- [운영체제(OS) 구조](#운영체제(os)-구조)
    - [플랫폼이란](#플랫폼이란)
    - [Native Code와 Managed Code](#native-code와-managed-code)
        - [Native Code](#native-code)
        - [Managed Code](#managed-code)
- [JVM](#jvm)
    - [1. Class Loader (클래스 로더)](#1.-class-loader-(클래스-로더))
    - [2. Interpreter (인터프리터)](#2.-interpreter-(인터프리터))
    - [3. JIT 컴파일러](#3.-jit-컴파일러)
    - [4. JNI (Java Native Interface)](#4.-jni-(java-native-interface))
    - [실제 하드웨어와의 통신 절차](#실제-하드웨어와의-통신-절차)

# 운영체제(OS) 구조

컴퓨터는 크게 세 가지 영역으로 나뉜다.
1. HW (CPU, RAM, SSD/HDD 등)
2. SW (OS)
	- Kernel Mode : OS가 하드웨어 리소스를 사용하기 위해 동작하는 모드
	- User Mode : 사용자가 실행하는 프로세스를 실행하는 모드

이처럼 OS는 하드웨어와 사용자가 실행한 소프트웨어(프로세스)의 중개자 역할을 하며,
프로세스가 함부로 하드웨어 자원을 사용하지 못하도록 한다. 하드웨어 제조사에서 제공하는 **드라이버라는 소프트웨어를 통해 OS는 하드웨어와 통신할 수 있다.**
(드라이버에 관한 자세한 설명은 https://learn.microsoft.com/ko-kr/windows-hardware/drivers/gettingstarted/what-is-a-driver- 에서 그림과 함께 확인해볼 수 있다)

저장장치(디스크), 네트워크 제어와 같이 시스템의 중요한 부분을 차지하는 요소들을 제어할 때는 주로 Kernel Mode에서 관련된 드라이버를 실행한다. 반면, 상대적으로 시스템에서 미치는 영향이 적은 프린터, USB 드라이버 등은 User Mode에서 실행한다.

![](https://i.imgur.com/ZX6ovWJ.png)

한 대의 컴퓨터의 리소스를 활용하여 여러 프로세스를 실행한다면, 이들은 각각 번갈아가며 실행될 것이다. (동시성) 이 때, 각 프로세스마다 컴퓨터의 리소스를 얼마만큼 사용하며, 언제까지 실행할 것인지를 OS가 결정해줘야 한다.

## 플랫폼이란

컴퓨터 구조에서 플랫폼이란 **하드웨어와 소프트웨어의 결합으로 응용 프로그램이 컴퓨터에서 실행될 수 있는 환경을 제공하는 것이 주된 목적이다.**

1. 하드웨어 아키텍처
	1. CPU (x86, ARM - 이들은 명령어 구조가 다르며, 각각 장단점을 갖고 있다)
	2. 메모리
	3. 입출력 장치
	4. 네트워크 장치 등
2. 운영 체제(OS) : 응용 프로그램이 하드웨어와 상호작용 할 수 있도록 하며, 하드웨어 리소스 관리
3. 런타임 환경 : 응용 프로그램이 실행될 수 있는 라이브러리, 드라이버 등을 운영체제에서 제공

![](https://i.imgur.com/ixAfo4f.png)
refs) https://m.cafe.daum.net/hwayoungs/4Yi2/5

이는 안드로이드의 구성요소인데, 위에 파란색으로 된 두 레이어(Applications, Application Framework)를 제외한 나머지 부분이 플랫폼(물론 밑에 하드웨어 장치까지 포함되어야 한다)이라고 보면 이해하기 쉬울 것이다. **이들이 주목하는 것은 응용 프로그램의 실행이다.** 따라서 응용 프로그램이 실행될 수 있는 다양한 소프트웨어(드라이버)들로 구성되어 있음을 알 수 있다.

## Native Code와 Managed Code

### Native Code
앞서 사진에서 알 수 있듯이, 드라이버라는 소프트웨어를 통해 운영체제가 하드웨어와 통신할 수 있게 되었다. 그렇다면 이 드라이버라는 소프트웨어는 어떠한 코드로 작성되었을까? 우리가 확실히 알 수 있는건 이들은 특정 하드웨어에 최적화된 소프트웨어라는 것이다. 즉, 플랫폼 환경에 의존적인 코드로 주로 C/C++이며, 이들은 특정 플랫폼(하드웨어 아키텍처, OS)의 컴파일러에 따라 기계어로 변환되어 실행된다.

드라이버는 운영체제와 하드웨어 사이에 존재하기 때문에 그 무엇보다도 운영체제와 하드웨어에 대한 의존성이 높을 수 밖에 없다. 따라서 이들은 실행될 수 있는 환경이 한정적이다. 우리는 프로그램을 개발함에 있어서 이처럼 플랫폼에 의존적인 프로그램을 마주하는 일이 많다. 때로는 특정 환경에서 내가 개발한 프로그램 혹은 특정 기능이 작동되지 않아 곤혹을 치를 수도 있다. 그럴 때 우리는 이러한 내용을 떠올리면 좀 더 납득이 될 것이다. **"우리의 프로그램을 실행할 수 있도록 저 밑에는 하드웨어와 드라이버 그리고 OS가 존재하는데 이러한 플랫폼의 환경에 따라 확인해야 될 부분이 있구나"라고 말이다.**

![](https://i.imgur.com/Gey2fQ2.png)
refs) https://thinkobjectoriented.hashnode.dev/introduction-to-computer-programs-native-code-managed-code-and-the-net-framework

위 그림에서 알 수 있듯이, 각각의 하드웨어 특징에 따라 각기 다른 Native 기반 프로그램이 존재함을 알 수 있다. 추후 살펴볼 JVM도 플랫폼 환경에 맞추어 각각 다르게 구현되어 있다.

### Managed Code
Managed Code는 플랫폼 환경에 구애받지 않으며, 런타임 환경에서 실행되어 관리되는 코드를 말한다. 대표적으로 Java가 있으며, JVM이라는 가상 머신을 제공한다.

![](https://i.imgur.com/CekAggk.png)
refs) https://thinkobjectoriented.hashnode.dev/introduction-to-computer-programs-native-code-managed-code-and-the-net-framework

![](https://i.imgur.com/azJmZxw.png)

refs) https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973

해당 그림에서 알 수 있듯이, 우리가 작성한 자바 파일(.java)은 컴파일러에 의해 바이트 코드(.class)로 변환되어 JVM이라는 가상 머신에서 동작한다.

![](https://i.imgur.com/fku7eS1.png)

모든 자바 프로그램은 JVM 위에서 동작할 수 있다. 따라서 특정 플랫폼 환경(Mac, Window)에 의존성이 있지 않아 보다 유연하다. 한 번 프로그램을 만들면 여러 환경에서 사용할 수 있기 때문이다.

하지만 JVM 자체는 Native Code로 작성된 프로그램이다. 즉, 특정 플랫폼 환경에 맞추어져 개발된 소프트웨어라는 것이다. 따라서 JVM을 설치할 때는 플랫폼 환경에 맞추어 설치를 해야 한다.

# JVM

![](https://i.imgur.com/CItmykM.png)
refs) https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973

해당 그림에서 알 수 있듯이, JVM은 여러가지 기능들을 갖고 있다. **명심해야할 점은 JVM에서 구성된 메모리 영역 (Heap, Method Area, Stack)은 OS로부터 할당받은 자원이라는 것이다.**

## 1. Class Loader (클래스 로더)

클래스 로더는 자바 바이트 코드(.class)를 메모리 영역에 올리는 역할을 한다. 우리는 프로그램이 실행되면 메모리에 로드되어 프로세스 단위로 관리된다고 하였다. 마찬가지로 자바 코드가 실행되기 위해선 메모리에 로드되어야 하는데, JVM이 OS에 할당받은 메모리에 바이트 코드를 로드하는 역할을 한다.

## 2. Interpreter (인터프리터)

인터프리터는 코드를 한 줄씩 읽어 바로바로 실행한다. 따라서 컴파일러에 비해 실행속도가 빠르다는 장점이 있다. JVM의 인터프리터는 자바 바이트 코드를 한 줄씩 해석하여 바로 실행하는데, 이 때 실행되는 과정은 다음과 같다.

![](https://i.imgur.com/RDyAYN6.png)

결국, 코드를 실행하는 것은 CPU(코어)이며, 실행 단위는 스레드이다. 따라서 인터프리터도 플랫폼에 맞는 기계어(네이티브 명령어)로 해석해야 한다. 이는 JVM이 특정 플랫폼 환경에 맞게 Native Code로 작성될 수 밖에 없는 이유이기도 하다.

## 3. JIT 컴파일러

인터프리터는 코드를 해석한 시점에 바로 실행된다는 이점이 있다. 하지만 매번 해석을 하기 때문에 반복되는 코드가 많을수록 이전에 했던 해석을 계속 해야한다. 이러한 성능 이슈를 최적화하기 위해 등장한 JIT 컴파일러는 자주 실행되는 바이트 코드를 모아서 사전에 미리 컴파일하여 네이티브 명령어로 변환한다. (CPU가 실행할 수 있는 기계어로 변환하는 건 인터프리터와 동일)
**JIT 컴파일러가 생성한 네이티브 명령어는 메모리의 특정 영역에 캐시되며, 반복적으로 호출될 경우 재사용된다.**

## 4. JNI (Java Native Interface)

자바 프로그램이 JVM의 리소스(OS에게 할당받은 CPU, 메모리 등) 뿐만 아니라 하드웨어의 자원을 사용해야 하는 경우가 있다. 예를 들어, 네트워크 통신이나 데이터베이스 호출 등이 있을 것이다.
즉, JVM은 JNI라는 인터페이스를 제공하며, Java 단에서 C/C++로 작성된 네이티브 메서드를 호출할 수 있다.

![ps52cED.png|426](https://i.imgur.com/ps52cED.png)

## 실제 하드웨어와의 통신 절차

JVM -> 네이티브 메서드 -> 운영체제 -> 하드웨어

따라서 I/O 빈도가 많은 작업들을 비동기로 처리함으로써 사용자 응답 속도를 높이는 이유가 필요한 것이다. 결국, I/O라는 것은 하드웨어의 리소스가 필요할 수 밖에 없다. 블록킹 I/O의 경우 위 절차가 실행되는 동안 다음 코드를 실행할 수 없다. 하지만 논블록킹 I/O는 위 절차가 실행되는 동안 다음 코드를 실행할 수 있다. I/O가 실행되는 작업을 하드웨어 측에 위임함으로써 다른 코드를 실행할 수 있다는 장점이 있다.

![](https://i.imgur.com/xn3aefA.png)

이처럼 소켓을 통해 (파일 시스템의 경우, File) 프로세스는 네트워크 통신을 위한 데이터를 보내거나(send) 받을 수 있다(recv). 이 때, 동기 I/O라면 send 후 recv가 끝날 때까지 기다려야 한다.



# 참고 자료
1. [면접 전에 알고 가면 좋을 것들](https://www.inflearn.com/course/%EB%A9%B4%EC%A0%91-%EC%8B%A0%EC%9E%85-java-%EB%B0%B1%EC%95%A4%EB%93%9C-%EA%B0%9C%EB%B0%9C%EC%9E%90/dashboard)
2. **Chat GPT**