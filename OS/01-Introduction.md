본 내용은 책 운영체제(Operating System Concepts, 교보문고)를 정리한 내용입니다.

# 1.0 서론 요약
- 운영체제는 하드웨어를 관리하는 프로그램이다.
- 어떤 운영체제들은 일반이이 사용하기에 *편리하도록* 설계되었고, 일부는 *효율성*에 주안을 두고 설계되었다.

# 1.1 운영체제가 할 일
- 컴퓨터 시스템은 대개 네 가지 구성요소인 하드웨어, 운영체제, 응용프로그램 및 사용자로 구분할 수 있다.

## 1.1.1 사용자 관점(User View)
- 컴퓨터에 대한 사용자의 관점에 따라, 사용의 용이성에 초점을 맞추면, 자원의 이용에 비교적 덜 신경 쓰는 운영체제가 있다.

## 1.1.2 시스템 관점
- 컴퓨터의 관점에선 운영체제는 하드웨어와 가장 밀접하게 연결된 프로그램
- 따라서 운영체제를 자원 할당자로 볼 수 있다.
- 효율적인 자원 분배를 많이 신경쓴다.
- 운영체제는 특히 입출력 장치의 제어와 작동에 깊이 관여

## 1.1.3 운영체제의 정의
- 일반적으로 운영체제에 대한 완벽한 정의는 없음.
- 운영체제의 존재 이유가 유용한 컴퓨터 시시템을 만드는 과정에서 발생하는 문제점을 해결하기 위한 적절한 방법이기 때문
- 역사가 발전해가며, 운영체제는 점점 많은 기능을 제공 하게 됨

# 1.2 컴퓨터 시스템의 구성

## 1.2.1 컴퓨터 시스템 연산

- 현대의 범용 컴퓨터 시스템은 공유 메모리에 대한 접근을 제공하는 공통 버스에 의해 연결된 이러 개의 장치 제어기와 하나 이상의 CPU로 구성됨
- 컴퓨터가 구동하기 위해 수행할 초기 프로그램을 가져야 한다. 이게 보통 펌웨어로 알려진 그것. ROM에 저장된다. 시스템의 모든 면을 초기화 함
- 커널이 적재되고 수행이 시작되면 유저에게서비스를 제공 할 수 있다. 일부 서비스는 커널이 ㅇ닌 시스템 프로그램에 의해 제공되며, 부트시에 메ㅊ모리에 적재되어 시스템 프로세스나 시스템 디먼이 된다. 완전히 부트된 상태에선 이벤트 발생을 기다린다.
- 이벤트가 발생하면 인터럽트에 의해 신호가 보내어진다. 하드웨어는 어느 순간이든 시스템 버스를 통해 CPU에 신호를 보내 인터럽트를 발생시킬 수 있다.
- CPU가 인터럽트 되면 하던일을 중단하고, 즉시 고정된 위치로 실행을 옮긴다. 이런 고정된 위치는 일반적으로 인터럽트를 위한 서비스 루틴이 실행된다. 실행이 완료되면 CPU는 인터럽트 되었던 연산을 재개한다.
-  인터럽트는 컴퓨터 구조의 중요한 부분이다. 컴퓨터 설계마다 각자의 인터럽트 메커니즘을 가진다.

## 1.2.2 저장 장치 구조

- CPU는 명령어를 메모리로 부터 가져오므로, 프로그램이 반드시 메모리에 있어야 한다. 대부분의 프로그램을 RAM에서 가져온다.
- ROM은 부트스트랩 프로그램과 같은 정적 프로그램을 저장한다.
- 주 메모리는 모든 필요한 프로그램과 데이터를 영구히 저장하기에는 너무 작다.주 메모리는 휘발성 저장장치다. 이 두이유로 인해 프로그램이 메모리에 영구히 존재하는 건 불가능하다.
- (비휘발성 저장장치에 대해선 생략하겠다.)

## 1.2.3 입출력 구조(I/O Structure)
- 범용 컴퓨터 시스템은 여러개의 장치 제어기와 CPU들로 구성된다.
- 입출력 연산을 위해 디바이스 드라이버는 장치 제어기의 적절한 레지스터에 

# 1.3 컴퓨터 시스템 구조
컴퓨터 시스템은 사용된 범용 처리기의 수에 따라 분류 가능하다.

## 1.3.1 단일 처리기 시스템
최근까지 대부분의 시스템은 하나의 처리기를 사용했다. 단일 처리기 시스템은 사용자 프로세스의 명령어를 포함하여 범용 명령어 집합을 수행할 수 있는 하나의 주 CPU를 가진다. 또한 대부분의 시스템들이 특수 목적의 전용처리기를 가진다. 범용 CPU가 하나 있는 시스템은 단일 처리기 시스템이다.

## 1.3.2 다중 처리기 시스템
다중 처리기 시스템은 다음과 같은 세 가지 주요 장점을 갖는다.
1. 증가된 처리량: 처리기의 수를 증가시킴으로써 짧은 시간동안 더욱 많은 일을 수행시킨다. 하지만 오버헤드로 인해 증가율이 N배가 되진 않고, 그보다 적다.
2. 규모의 경제: 다중처리기는 여러개의 단일 시스템에 비해 비용을 절약할 수 있는데, 이는 처리기가 주변 장치, 대용량 저장 장치, 전원공급 장치를 공유하고 있기 때문이다.
3. 증가된 신뢰성: 만약 기능들이 적절하게 분산된다면 한 처리기가 고장 나더라도 시스템이 정지하는 것이 아니라 단지 속도만 느려지게 된다.

- 컴퓨터 시스템의 증가된 신뢰성은 많은 응용프로그램에게 필수적이다. 이렇게 살아남은 하드웨어의 수준에 비례해 계속적인 서비스를 제공하는 능력을 *우아한 퇴보*라고 한다.
- 어떤 시스템은 어느 한 구성요소의 고장에도 불구하고 동작을 계속할 수 있기 때문에 우아한 퇴보를 넘어 *결함 허용* 이라고 불리다. 결함 허용 시스템은 오류를 탐지하고 진단하고 가능하다면 교정할 수 있게 하는 기법이 필요하다는 사실을 주목하자.
- 어떤 시스템은 각 처리기에 특정 태스크가 할당되는 비대칭적 다중 처리를 사용한다. 하나의 주 처리기가 시스템을 제어한다. 다른 처리기들은 주 처리기의 명령을 수행하거나 미리 정의된 태스크를 수행한다.
- 가장 일반적인 다중 처리기 시스템은 각 처리기가 운영체제 내의 모든 작업을 수행하는 대칭적 다중 처리를 사용한다. 대칭적 다중 처리는 모든 처리기가 대등하다는 것을 의미한다.
- 하나의 칩에 여러개의 코어를 포함시키는 것을 멀티코어라 부른다. 멀티 코어는 단일 코어를 여러개 가지는 것보다 효율적인데, 이는 칩 내의 통신이 칩사이의 통신보다 빠르고, 단일 칩보다 전력을 훨씬 덜 소모한다.
- 마지막으로 블레이드 서버가 가장 최근에 개발된 형태이며 이 블레이드 서버는 다수의 처리기 보드 및 입출력 보드, 네트워킹 보드들이 하나의 섀시 안에 장착되는 형태를 가진다.

## 1.3.3 클러스터형 시스템 
- 여러 CPU를 가진 시스템의 또 다른 유형은 클러스터형 시스템이다. 클러스터 시스템은 둘 이상의 독자적 시스템 또는 노드들을 연결하여 구성한다는 점에서 다중 처리기 시스템과 차이가 난다. 그러한 시스템은 약결합이라고 간주된다. 각 노드는 단일 처리기 시스템 또는 멀티코어 시스템일 수 있다. 
- 클러스터링은 통상 높은 가용성을 제공하기 위해 사용된다. 즉, 클러스터 내 하나 이상의 컴퓨터 시스템이 고장 나더라도 서비스는 계속 제공된다. 일반적으로 고가용성 시스템에 중복 기능을 추가함으로써 얻어진다.
- 클러스터링은 비대칭 또는 대칭적으로 구성될 수 있다. 비대칭형 클러스터링에서는 다른 컴퓨터들이 응용 프로그램을 실행하는 동안 한 컴퓨터는 긴급 대기 모드 상태를 유지한다. 이 긴급 대기 모드의 호스트는 활성 서버들을 감시하는 작업만을 수행하다가, 서버가 고장 난다면 긴급 대기 모드의 호스트가 활성 서버가 된다. 대칭형 클러스터링에서는 둘 이상의 호스트들이 응용프로그램을 실행하고 서로를 감시한다. 가용한 하드웨어를 모두 사용하기 때문에 대칭형 구성이 더 효율적이다. 대칭형 구성이 효율적으로 동작하기 위해서는 하나 이상의 응용 프로그램들이 실행 가능해야 한다.
- 한 클러스터가 네트워크로 연결된 다수의 컴퓨터 시스템으로 구성되므로 클러스터는 고성능 계산환경을 제공하도록 할 수 있다. 이러한 시스템은 클러스터 내의 모든 큠퓨터에서 응용을 병렬 수행할 수 있으므로 단일 처리기나 SMP 시스템보다 훨씬 큰 계산 능력을 제공할 수 있다. 이는 병렬화라는 기법으로 프로그램을 클러스터의 각 컴퓨터에서 수행되는 분리된 요소로 나누는 작업을 말한다.
- 다른 형태의 클러스터로 병렬 클러스터와 WAN을 이용한 클러스터링이 있다.

# 1.4 운영체제의 구조
- 운영체제의 가장 중요한 면은 다중 프로그램을 할 수 있는 능력이다. 일반적으로 단일 사용자는 CPU 또는 입출력 장치를 항상 바쁘게 유지할 수 없다. 다중 프로그래밍은 CPU가 수행할 작업을 항상 하나 가지도록 작업을 구성함으로써 CPU 이용률을 증가시킨다. 
- 운영체제는 한 번에 여러 작업을 메모리에 적재한다. 이들 작업은 처음에는 디스크의 작업 풀 내에 유지된다. 왜냐하면 메모리 내에 동시에 저장할 수 있는 작업의 수는 통상 작업 풀에 둘 수 있는 작업의 수보다 훨씬 적기 때문이다. 작업 풀은 디스크 내의 모든 프로세스로 구성되며 이들은 주 메모리의 할당을 기다린다.
- 메모리 내의 작업 집합은 작업 풀 내의 작업들의 부분 집합이다. 운영체제는 메모리 내에 있는 작업중에 하나를 선택해 실행한다. 이 작업은 입출력의 종료를 기다리는 등과 같이 어떤일을 기다리게 된다.
- 비 다중 프로그래밍 시스템에서는 CPU가 쉬게 되지만, 다중 프로그래밍 시스템에서는 운영체제가 다른 적업으로 전환해 그 작업을 수행한다. 
- 다중 프로그래밍 시스템은 여러가지 시스템 자원을 효율적으로 이용할 수 있는 환경을 제공하지만, 사용자를 위해 컴퓨터 시스템과 상호 작용은 제공하지 않는다. 시분할 시스템에서는 CPU가 다수의 작업들을 교대로 수행하지만, 매우 빈번하게 교대가 일어나기 때문에 프로그램이 실행되는 동안에 사용자들은 각자 자기의 프로그램과 상호 작용할 수 있다.
- 시분할 시스템은 사용자와 시스템 간에 직접적인 통신을 제공하는 대화식 컴퓨터 시스템을 필요로 한다. 사용자는 키보드나 마우스 등과 같은 입력장치를 사용하여 운영체제나 프로그램에 직접 명령하고 출력장치의 즉각적인 응답을 기다린다. 따라서 응답 시간이 짧아야 하며, 보통 1초 이내이다.
- 시분할 운영체제는 동시에 많은 사용자가 컴퓨터를 공유하도록 한다. 시분할 시스템에서 각각의 동작이나 명령은 대체로 짧은 경향이 있기 때문에, 각 사용자는 단지 짧은 시간동안만 CPU 시간이 필요하다.
- 시분할 운영체제는 각 사용자에게 시분할 되는 컴퓨터의 작은 부분을 제공하기 위해 CPU 스케줄링과 다중프로그래밍을 사용한다. 각 사용자는 메모리에 최소한 하나의 독립된 프로그램을 가지고 있다. 메모리에 적재되어 있고, 실행되고 있는 프로그램을 일반적으로 프로세스라고 한다. 프로세스가 수행될 때는, 전형적으로 자신이 종료되거나 입출력을 수행할 필요가 있을 때까지 아주 짧은 시간 동안만 실행된다.

- 시분할과 다중 프로그래밍 운영체제에서는 여러 작업이 메모리에 동시에 유지되어야 한다. 몇몇 작업이 메모리로 옮겨올 준비가 되었고 , 그들 전부를 메모리에 보관할만한 공간이 불충분하다면, 시스템은 그들 중 몇 개를 선택해야 한다. 이러한 결정을 하는 것이 작업 스케줄링이다.
- 여러개의 작업이 동시에 실행 준비가 되어 있으면, 시스템은 그들 중 하나를 선택해야 한다. 이러한 결정을 내리는 것을 CPU 스케줄링이라고 한다.
- 시분할 시스템에서 운영체제는 적절한 응답 시간을 보장해야 한다. 이는 종종 스와핑에 의해 달성되는 데, 스와핑은 프로세스를 주 메모리에서 디스크로 적절하게 스왑인 또는 스왑아웃 시킨다.
- 합리적인 응답시간을 보장하는 더 일반적인 방법은 가상 메모리인데, 이것은 일부만 메모리에 있는 작업의 수행을 허용하는 기법이다. 이 기법의 이점은 물리메모리의 크기보다 더 커도 된다는 것이다. 주 메모리를 크고 균등한 저장 장치의 배열로 추상화하여, 사용자에게 보이는 논리 메모리를 물리 메모리로부터 분리시킨다. 이러한 기법은 프로그래머를 메모리 저장 장치의 한계로부터 자유롭게 해준다.
- 시분할 시스템은 반드시 파일 시스템도 제공해야한다. 파일 시스템은 다수의 디스크 상에 존재하므로, 디스크 관리 기법도 제공되어야 한다. 또한 시분할 시스템은 부적당한 사용으로부터 자원을 보호하기 위한 기법을 제공해야한다. 
- 작업 동기화와 통신기법을 제공해야하며, 교착상태에 빠지지 않도록 해야한다.


# 1.5 운영체제 연산 
- 운영체제는 인터럽트 구동식이다. 사건은 거의 항상 인터럽트나 트랩을 발생시켜 신호를 보낸다. 트랩은 오류, 혹은 운영체제 서비스 수행 요청에 의휴 유발되는 소프트웨어에 의해 생성된 인터럽트이다. 
- 운영체제와 사용자는 컴퓨터 시스템의 하드웨어와 소프트웨어 자원을 공유하기 때문에, 사용자 프로그램의 오류가 현재 수행중인 프로그램에만 문제를 일으키도록 보장해야 한다.

## 1.5.1 이중 연산 모드
- 운영체제의 적절한 동작을 보장하기 위해, 운영체제 코드의 실행과 사용자 정의 코드의 실행을 구분할 수 있어야 한다.
- 적어도 두 개의 독립된 연산  즉 사용자 모드와 커널모드를 필요로  모드 비트라고 하는 하나의 비트가 현재의 모드를 나타내기 위해컴퓨터의 하드웨어에 추가되었다. 이 비트는 커널 모드 또는 사용자 모드를 나타낸다. 모드비트의 사용으로 응용프로그램을 실행하는 것과 서비스를 요청하는 모드를 분리할 수 있다.
시스템  하드웨어는 커널 모드에서  이어 운영체제가 적재되고 사용자 모드에서 사용자 프로세스가  
- 이중모드는 잘못된 사용자로부터 운영체제와 그리고 잘못된 사용자 서로를 보호하는 방법을 제공한다.
- 일부 명령을 특권 명령으로 지정함으로써 이러한 보호를 달성한다.
- 하드웨어는 특권 명령이 커널 모드에서만 수행되도록 허용한다. 사용자 모드에서 특권 명령을 수행하려고 시도하면, 하드웨어는 이를 실행하지 않고, 불법적인 명령으로 간주해 운영체제로 트랩을 건다.
- 커널 모드로 전환하는 명령어가 특권 명령의 한 예이다. 또 다른 예는 입출력 제어, 타이머 관리, 그리고 인터럽트 관리를 위한 명령어 들이 있다.
- 모드 개념은 두개의 모드 이상으로 확장될 수 있다. 가상화를 지원하는 CPU는 시스템을 가상 기계 관리자(VMM)가 제어하고 있다.
- VMM은 커널 보다는 작지만 사용자 프로세스보다는 큰 권한을 가진다. 
- 시스템 호출은 사용자 프로그램 대신, 운영체제가 수행하도록 지정되어 있는 작업들을 운영체제에 요청할 수 있는 방법을 제공한다. 시스템 호출은 컴퓨터 시스템의 처리기가 지원하는 기능에 따라 다양한 방법으로 호출된다. 어떠한 형태를 가지든 시스템 호출은 운영체제에 의해 수행될 동작을 사용자 프로세스가 요청할 수 있게한다.
- 시스템 호출이 수행될 때, 시스템 호출은 하드웨어에 의해 하나의 소프트웨어 인터럽트로 취급된다. 제어가 인터럽트 벡터를 통해 운영체제 내의 서비스 루틴으로 전달되고, 모드 비트가 커널 모드로 설정된다. 시스템 호출 서비스 루틴은 운영체제의 일부이다. 커널은 인터럽트를 발생시킨 명령을 검사하여 어떤 시스템 호출이 발생했는지를 결정한다. 이때 전달된 인수가 사용자 인수가 사용자 프로그램이 요청하는 서비스 타입을 표시한다. 
- 하드웨어가 이중 모드를 지원하지 않을 경우 운영체제에 심각한 결점을 초래할 수 있다. 예를 들어 MS-DOS는 모드 비트가 없는, 따라서 이중모드가 없는 Intel 8088을 위해 작성되었다. 그러므로 잘못된 사용자 프로그램이 데이터를 운영체제 부분에 덮어 기록함으로써 운영체제를 지워버릴 수 있고, 또한 여러 프로그램이 동시에 한 장치에 기록할 수 있으며, 그 경우 불행한 결과가 발생할 수 있다.
- 최근의 CPU는 이중모드를 지원하고, 대부분의 현대 운영체제들은 이중 모드 기능을 이용하여 운영체제에게 더 많은 보호를 제공한다.
- 하드웨어 보호 기능이 제공되면 모드 규칙을 위반하는 오류가 하웨어에 의해 탐지된다. 이러한 오류는 일반적으로 운영체제가 처리한다. 사용자 프로그램이 불법적인 명령을 수행하려 하거나 사용자 주소 공간이 아닌 메모리에 접근을 시도하는 등과 같은 오류가 발생하면, 하드웨어는 운영체제로 트랩을 발생시킨다.

## 1.5.2 타이머
- 운영체제가 CPU에 대한 제어를 유지할 수 있도록 보장해야한다. 사용자 프로그램이 무한루프에 빠지거나 시스템 서비스 호출에 실패하여, 제어가 운영체제로 복귀하지 않는 경우가 없도록 반드시 방지해야 한다. 이러한 목적을 달성하기 위해, 타이머를 사용할 수 있다.
- 타이머는 지정된 시간 후 컴퓨터를 인터럽트 하도록 설정할 수 있다. 이 시간은 고정 혹은 가변일 수 있다. 가변 타이머는 일반적으로 고정률의 클락과 계수기로 구현한다. 운영체제는 계수기 값을 설정한다. 클락이 똑딱할 때마다 계수기가 감소한다 계수기가 0이 될 때 인터럽트가 발생한다. 예를 들면 1밀리초 클락의 10비트 계수기는 1밀리초 단위로 1밀리초에서 1,024밀리초 사이의 간격을 갖는 인터럽트를 허용한다. 

# 1.6 프로세스 관리
- 프로그램은 명령이 CPU에 의해 수행되지 않으면 아무 일도 할 수 없다. 실행중인 프로그램은 프로세스이다. 컴파일러와 같은 하나의 시분할 사용자 프로그램은 하나의 프로세스가 된다.
- 프로세스는 자신의 일을 수행하기 위해 CPU 시간, 메모리, 파일, 그리고 입출력 장치를 포함한 여러 가지 자원을 필요로 한다. 이러한 자원은 프로세스가 생성될 때 제공될 수 있고, 실행되는 동안 할당될 수도 있다. 프로세스가 생성될 때 흭득하는 여러 가지 물리적 논리적인 자원 외에, 여러 초기화 데이터도 아울러 전달될 수도 있다.
- 프로그램 그 자체는 프로세스가 아니다. 즉, 하나의 프로그램은 디스크에 저장된 파일의 내용과 같이 수동적 개체인 반면, 프로세스는 다음 수행할 명령을 지정하는 프로그램 카운터를 가진 능동적인 개체이다.
- 한 프로세스의 수행은 반드시 순차적이라야 한다.CPU는 그 프로세스가 끌날 때까지 그 프로세스의 명령들을 차례대로 수행한다.

# 1.7 메모리 관리
- 주 메모리는 현대 컴퓨터 시스템의 작동에 중추적인 역할을 한다. 메모리는 크기가 수십 만에서 수십 억까지의 범위를 갖는 바이트의 대용량 배열이다. 각 바이트는 자신의 주소를 가진다. 주 메모리는 CPU와 입출력 장치에 의하여 공유되는 빤르 접근이 가능한 데이터의 저장소이다.
- 폰 노이만 방식의 컴퓨터에서는 중앙 처리기가 명령어 인출 주기 동안 주 메모리로부터 명령어를 읽고, 자료 인출 주기 동안 주 메모리로부터 데이터를 읽고 또한 쓴다.
- 프로그램이 수행되기 위해서는 반드시 절대 주소로 매핑되고 메모리에 적재되어야 한다. 프로그램을 수행하면서, 이러한 절대 주소를 생성하여 메모리의 프로그램 명령어와 데이터에 접근한다. 결국 프로그램이 종료되고, 프로그램이 차지하던 메모리 공간은 가용공간으로 선언되고, 다음 프로그램이 적재되어 수행될 수 있다.
- CPU 이용률과 사용자에 대한 컴퓨터의 응답 속도를 개선하기 위해, 우리는 메모리에 여러 개의 프로그램을 유지해야 하며 이를 위해서 메모리 관리 깁버이 필요하다. 이러한 메모리 관리 기법에는 여러 종류가 있다. 이러한 기법은 메모리 관리에 대한 여러 접근 방법을 반영하는데, 각 알괴르ᄌᆷ의 효율성은 특정 상황에 따라 다르다. 특정 시스템에 대한 메모리 관리 기법의 선택은 여러 요인에 의해 결정되지만, 특히 시스템의 하드웨어 설계에 좌우된다. 
- 운영체제는 메모리 관리와 관련하여 다음과 같은 일을 담당해야 한다.
  - 메모리의 어느 부분이 현재 사용되고 있으며 누구에 의해 사용되고 있는지를 추적해야 한다.
  - 어떤 프로세스들을 메모리에 적재하고 제거할 것인가를 결정해야 한다.
  - 필요에 따라 메모리 공간을 할당하고 회수해야 한다.


# 1.8 정장자치 관리 
컴퓨터 시스템의 편리한 사용을 위해, 운영체제는 정보 저장 장치에 대한 균등한 논리적 관점을 제공한다. 운영체제는 저장 장치의 물리적 특성을 추상화하여 논리적인 저장 단위인 파일을 정의한다. 운영체제는 파일을 물리적 매체로 맵하며, 저장 장치를 통해 이들 파일에 접근한다.

## 1.8.1 파일 시스템 관리
- 파일 관리는 가장 눈에 띄는 운영체제 구성 요소 중의 하나이다. 컴퓨터는 여러 타입의 물리적 매체에 정보를 저장할 수 있다. 자기 디스크, 광 디스크, 자기 테이프 등이 가장 일반적인 매체들이다. 이러한 각 매체는 자신의 특성과 물리적 구성을 가지고 있다. 각 매체는 디스크 드라이브 혹은 테이프 드라이브와 같은 장치에 의해 제어되며, 이들 장치 또한 자신의 독특한 특성을 가지고 있다. 이러한 속성에는 접근 속도, 용량, 데이터 전송률, 그리고 접근 방식 등이 있다. 
- 파일은 파일 생성자에 의해 정의된 관련 정보의 집합체이다. 일반적으로 파일은 프로그램과 데이터를 나타낸다. 데이터 파일은 숫자, 영문자, 영숫자 등으로 구성된다. 파일은 텍스트 파일과 같은 자유 형태일 수도 있고, 엄격하게 포맷된 형태일 수도 있다. 명백히 파일의 개념은 지극히 일반적이다.
- 운영체제는 테이프와 디스크 같은 대량 저장 매체와 그것을 제어하는 장치를 관리함으로써 파일의 추상적인 개념을 구현한다. 또한 파일은 사용을 쉽게 하기 위해 통상 디렉토리들로 구성된다. 마지막으로, 다수의 사용자가 파일에 접근하려고 할 때는 누구에 의해서, 그리고 어떤 방법으로 파일이 접근되어야 하는가를 통제하는 것이 바람직하다.
- 운영체제는 파일 관리를 위하여 다음과 같은 일을 담당한다.
  - 파일의 생성 및 제거
  - 디렉토리 생성 및 제
  - 파일과 디렉토리를 조작하기 위한 프리미티브의 제
  - 파일을 보조 저장 장치로 매
  - 안정적인 저장 매체에 파일을 백

## 1.8.2 대용량 저장장치 관리
- 주 메모리는 모든 데이터와 프로그램을 수용하기에 용량이 너무 작고, 또한 전원이 꺼질 경우 저장하고 있던 데이터가 사라지기 때문에, 컴퓨터 시스템은 반드시 주 메모리에 내용을 저장하기 위해 보조 저장 장치를 제공해야 한다. 대부분의 현대의 컴퓨터 시스템은 디스크를 프로그램과 데이터를 위한 주된 온라인 저장 매체로 사용하고 있다. 컴파일러, 어셈블러, 워드 프로세서 편집기 등을 포함한 대부분의 프로그램은 주 메모리에 적재 될때까지 디스크에 저장되어 있고, 그들의 처리의 출발지와 목적지로서 디스크를 사용하고 있다. 그러므로 디스크 저장 장치의 적절한 관리는 컴퓨터 시스템에서 매우 중요하다. 
- 운영체제는 디스크 관리를 위하여 다음과 같은 기능을 담당한다.
  - 자유 공간의 관
  - 저장 장소 할
  - 디스크 스케줄
- 보조 저장 장치는 매우 빈번하게 사용되므로, 효율적으로 사용해야 한다. 컴퓨터의 전체 동작 속도는 디스크 서브시스템과 그것을 조작하는 알고리즘의 속도에 의해 결정될 수 있다.
- 보조 저장 장치보다 더 느리고 비용은 적게 들고 때로는 용량이 더 큰 장치를 필요로 하는 경우도 많다. 디스크 데이터의 백업, 가끔 쓰이는 데이터 및 장기간 보존이 필요한 데이터를 저장하는 것이 대표적인 예이다. 자기 테이프 드라이브와 그 테이프, CD와 플래더들은 전형적인 3차 저장 장치이다. 
- 3차 저장 장치는 시스템 성능에는 중요하지 않지만 운영체제에 의해 관리되어야만 한다. 어떤 운영체제들은 이 역할을 직접 수행하며 다른 운영체제들은 이 역할을 응용 프로그램에게 맡기기도 한다. 운영체제가 제공해야 할 기능에는 마운트와 언마운트 기능, 프로세스의 배타적 사용을 위한 장치의 할당과 반환 그리고 보조 저장 장치로 부터 3차 저장 장치로의 데이터 이주 등이 포함된다.

# 1.8.3 캐싱(Caching)
- 캐싱은 컴퓨터 시스템의 중요한 원리이다. 정보는 통상 어떤 저장 장치에 보관된다. 정보가 사용됨에 따라, 보다 빠른 장치인 캐시에 일시적으로 복사된다. 그러므로 우리가 특정 정보가 필요할 경우 우리는 먼저 캐시에 그 정보가 있는지를 조사해 보아야 한다. 
- 인덱스 레지스터와 같은 CPU 내부의 프로그램 가능한 레지스터들은 주 메모리를 위한 고속의 캐시로 볼 수 있다. 프로그래머는 어느 정보를 주 메모리에 두고 어느 정보를 레지스터에 둘 것인지를 결정하는 레지스터 할당 정책과 교체 알고리즘을 구현한다.
- 전적으로 하드웨어로 구현된 캐시도 있다. 예를 들어 대부분의 시스템은 다음에 수행될 것으로 예상되는 명령을 넣어 두는 명령 캐시를 갖고 있다. 만약 명령 캐시가 없다면, CPU는 주 메모리로부터 다음 명령을 인출해 올 동안 몇 사이클을 기다려야 한다. 유사한 이유로 대부분의 시스템들은 하나 이상의 고속 데이터 캐시를 가지고 있다. 
- 캐시의 크기에 한계가 있기 때문에, 캐시 관리가 설계의 중요한 문제가 된다. 캐시의 크기와 교체 정책을 잘 선택하면 모든 접근의 80~99%를 캐시에서 얻을 수 있어 극도로 높은 성능을 얻게 된다.
-  주 메모리는 보조 메모리를 위한 빠른 캐시로 볼 수 있는데, 이는 보조 메모리의 데이터를 사용하기 위해서는 주 메모리에 복사되어야 하고, 안전하게 보관하기 위해 보조 메모리로 이동되기 전에 데이터는 반드시 주 메모리에 있어야 하기 때문이다. 파일 시스템 데이터는  저장 장치 계층의 여러 단계에 존재할 수 있다. 최상위 단계에서는, 운영체제가 주 메뫼ㄹ 안에 파일 시스템 데이터의 캐시를 유지할 수 있다. 
-  저장 장치의 계층구조에서 각 수준간의 정보 이동은 하드웨어 설계나 제어하는 운영체제에 따라 명시적 또는 묵시적으로 이루어진다. 예를 들어 캐시로부터 CPU및 레지스터로의 데이터 전송은 통상 운영체제의 간섭 없이 하드웨어적으로 이루어진다. 반면 디스크와 메모리간의 데이터 전송은 통상 운영체제에 의해 제어된다.
-  메모리의 계층 구조에서, 동일한 데이터가 서로 다른 수준의 저장 장치 시스템에 나타나게 된다. 예를 들어 B라는 파일에 있는 A라는 변수가 1 증가되어야 한다고 생각해 보자. 파일 B가 자기 디스크에 있다고 가정해 보자. 증가 연산은 먼저 A가 있는 디스크 블록을 주 메모리 복사해오는 입출력 연산을 호출함으로써 진행된다. 이어서 A를 캐시에 복사하고, 이어 A를 다시 내부 레지스터로 복사한다. 따라서 A의 복사본이 여러 곳에 존재하게 된다. 내부 레지스터에서 증가가 일어나면, A의 값은 여러 저장 장치 시스템에서 서로 달라진다. A의 값은 자기 디스크에 쇄루ᄋᆫ A의 값이 다시 기록된 이후에야 비로소 동일해진다.
-  어떤 시간에 단지 하나의 프로세스만 실행하는 환경에서는, 이러한 기법이 아무런 문제가 없다. 왜냐하면 정수 A에 대한 접근은 항상 계층 구조의 최상위 값을 참조하기 때문이다. 그러나 CPU가 여러 개의 프로세스들의 사이에서 전환되는 멀티태스킹 환경에서는, 여러 개의 프로세스가 A에 접근하기를 원할 경우, 이들 각 프로세스가 가장 최근에 갱신된 A의 값을 보장하기 위해서 많은 신경을 써야한다.
-  CPU가 내부 레지스터를 유지할 뿐만 아니라 로컬캐시도 갖고 있는 다중 처리기 환경 하에서는 상황이 더욱 복잡해진다. 이런 환경에서는, A의 복사본이 동시에 여러 캐시에 존재할 수 있다. 여러 개의 CPU가 모두 동시에 실행될 수 있으므로, 한 캐시에 있는 A값이 갱신될 경우 그겅은 A가 존재하는 모든 캐시에 즉각적으로 반영되어야 한다. 이러한 상황을 캐시 일관성 문제라고 하며, 이는 일번적으로 하드웨어적 문제이다.
-  분산 환경에서는  문제가 더욱 더 복잡해진다. 분산 환경에서는, 동일한 파일의 다수의 복사본이 공간적으로 분산된 여러 컴퓨터에 유지될 수 있다. 여러 개의 복사본이 병렬로 접근되고 갱신될 수 있으므로, 우리는 한 장소에서 한 복솨ᄇᆫ이 갱신될 경우, 가능한 한 빨리 모든 복사본이 갱신될 것을 보장해야 한다.

# 1.8.4 입출력 시스템(I/O Systems)
- 운영체제의 목적 중의 하나는 사용자에게 특정 하드웨어 장치의 특성을 숨기는 것이다. 예를 들면 UNIX에서 입출력 장치의 특성은 입출력 서브시스템에 의해 운영체제 자체의 대부분으로부터 숨겨져 있다.
- 입출력 시스템은 다음과 같이 구성되어 있다.
  - 버퍼링, 캐싱, 스풀링을 포함한 메모리 관리 구성요
  - 일반적인 장치 드라이버 인터페이
  - 특정 하드웨어 장치들을 위한 드라이

# 1.9 보호와 보안
- 퓨터 시스템이 다수의 사용자를 가지며, 다수의 프로세스의 병렬 수행을 허용한다면, 데이터에 대한 접근은 반드시 규제되어야 한다. 파일, 메모리 세그먼트, CPU 및 다른 자원들에 대해 운영체제로부터 적절한 허가를 흭득한 프로세스만이 작업할 수 있도록 보장하는 기법이 필요하다. 예를 들면, 메모리 주소 지정 하드웨어는 프로세스가 자신의 주소 영역에서만 실행될 수 있도록 보장한다. 타이머는 모든 프로세스가 CPU의 제어를 얻은 후 결국에는 제어를 양도하도록 보장한다. 여러 주변 장치의 무결성이 보호받도록, 장치제어 레지스터들에 사용자가 접근할 수 없게 한다.
- 보호란 컴퓨터 시스템이 정의한 자원에 대해 프로그램, 프로세스, 또는 사용자들의 접근을 제어하는 기법이다. 이 기법은 시행될 제어에 대한 명세와 이들을 강제 시행하기 위한 방법을 규정하는 수단을 반드시 제공해야 한다.
- 보호는 구성 요소 서브시스템간의 인터페이스에서 잠재적인 오류를 검출함으로써 시스템의 신뢰성을 증가시킬수 있다. 인터페이스 오류를 초기에 발견하면 종종 고장난 서브시스템에 의해 정상적인 서븨쓰템이 오염되는 것을 방지할 수 있다. 보호되지 않는 자원은 권한이 없거나 또는 무자격 사용자에 의해 사용되는 것을 방지할 수 있다.
- 보호와 보안을 제공하기 위해서는 시스템의 모든 사용자들을 구분할 수 있어야 한다. 대부분의 운영체제들은 사용자 이름과 연관된 사용자 식별자의 리스트를 유지한다. 
- 어떤 상황에서는 각 사용자가 아니라 사용자의 집합을 구분하기를 원한다. 예를 들면 UNIX 시스템에서는 파일의 소유주에게는 모든 연산을 허용하고 일부 사용자들에게는 읽기 연산만을 허용할 수도 있다. 이를 위하여 그룹이름과 그 그룹에 속한 사용자들을 정의할 필요가 있다. 그룹 기능은 시스템 전체에 존재하는 그룹 이름과 그룹 식별자의 리스트로 구현될 수 있다.
- 시스템을 정상적으로 사용하는 동안 사용자 식별자외 그 식별자만 있으면 충분하다. 그러나 사용자는 때때로  원하는 작업을 수행하기 위해서는 권한을 상승해야 한다.

# 1.10 커널 자료 구조(Kernel Data Structures)
## 1.10.1 리스트, 스택 및 큐
- 배열은 각 원소가 직접 접근될 수 있는 단순한 자료구조이다. 주 메모리는 하나의 배열로 구축된달. 저장된 데이터가 한 바이트보다 크면 그 데이터에 다수의 바이트가 할당되며, 그 데이터는 데이터 수 * 데이터 크기로 주소가 지정된다. 
- 배열의 각 항은 직접 접근할 수 있으나 리스트의 항들은 특정 순서로 접근해야 한다. 즉 리스트는 데이터의 값들의 집단을 하나의 시퀀스로 표시한다. 이 구조를 구현하는 가장 일반적인 방법이 연결 리스트이다. 연결 리스트에서는 각 항이 다른 하나에 견결되어 있다. (리스트 그림 37페이지 참조)
- 열결 리스트는 가변 수의 항들을 수용하며 항의 삭제와 삽입이 쉽다. 리스트를 사용할 경우 가능한 단점은 크기가 n인 리스트에서 특정 항을 인출할 때의 성능이 선형 즉 O(n)이라는 점이다. 왜냐하면 최악의 경우 전체 n개의 항을 전부 살펴보아야 할 수 있기 때문이다. 리스트는 자주 커널 알고리듬에 의해 직접 사용된다. 그렇지만 리스트는 자주 스택이나 큐 같은 보다 강력한 자료구조를 구축하는데 사용된다.
- 스택은 순차적으로 순서를 가진 자료구조로 항을 넣거나 꺼내는데 후입선출을 사용한다. 
- 반면 큐는 순차 순서의 자료구조로 선입선출을 사용한다.

## 1.10.2 트리
트리는 데이터의 서열을 표시하는 데 사용 가능한 자료 구조이다. 트리 구조에서 데이터 값들은 부모-자식 관계에 의해 연결된다. 일반 트리에서 부모는 임의의 수의 자식을 가질 수 있다. 이진 트리에서 부모는 최대 두 개의 자식을 가질 수 있으며, 이들은 좌칙 자식, 우측 자식이라 부른다. 이진 탐색 트리는 추가로 부모의 두 자식 사이에 좌측 자식 <= 우측 자식의 순서를 요구한다. 이진 탐색 트리에서 한 항을 찾으려면 최악 경우 성능이 O(n)이다 이러한 상황을 방지하기 위해, 균형 이진 탐색 트리를 만드는 알고리듬을 사용할 수 있다. 그 경우 n개의 항을 갖@는 트리는 최대 깊이가 log n 이며 따라서 최악 경우 성능 O(log n)을 보장한다.

## 1.10.3 해시 함수와 맵
- 해시 함수는 데이터를 입력으로 받아 이 데이터에 산술 연산을 수행하여 하나의 수를 복귀한다. 이 수는 그 데이터를 인출하기 위해 테이블의 인덱스로 사용할 수 있다. 크기 n인 리스트에서 데이터를 찾는 데 최악의 경우 O(n)의 비교가 필요한 반면에 테이블에서 해시 함수를 사용하여 데이터를 인출할 경우 최악 경우 O(1)이 될 수도 있으며, 이는 구현에 좌우된다. 이러한 성능 때문에 해시 함수는 운영체제에서 광범위하게 사용된다.
- 해시 함수의 어려운 점은 두개의 입력이 하나의 출력 값을 가질 수 있다는 것이다. 즉 이들이 테이블의 동일 위치로 색인될 수 있다. 이를 우리는 해시 충돌이라 하며, 테이블의 각항에 연결 리스트를 두어 동일한 해시 값을 갖는 모든 항을 수록하게 한다. 물론 충돌이 많을수록 해시 함수의 효율이 떨어진다.
- 해시 함수의 용도중 하나는 해시 맵을 구현하는 일이다. 해시 맵은 해시 함수를 사용하여 [키:값]을 연관시킨다. 예를 들면 operating이란 키를 system이란 값으로 맵할 수 있다. 일단 매핑이 성립되면 키에 해시 함수를 적용하여 해시 맵으로부터 그 값을 얻을 수 있다.
- 한 사용자 이름이 한 패스워드로 맵된다고 하자. 패스워드 인증은 다음과 같이 진행될 수 있는데, 한 사용자가 자신의 사용자 이름과 패스워드를 입력한다. 이어 사용자 이름에 해시 함수를 적용하여 그 결과 값으로 패스워드를 인출한다. 인출된 패스워드와 사용자가 인증을 위해 입력한 패스워드와 비교한다.


## 1.10.4 비트맵
- 비트맵은 n개의 항의 상태를 나타내는 데 사용 가능한 n개의 이진 비트의 스트링이다.예를 들어 다수의 자원이 있다하자. 각 자원의 가용 여부를 이진 비트의 값으로 표시한다. 0은 자원이 사용 가능함을 표시하고, 1은 사용 불가능함을 표시한다. 비트맵에서 i번째 위치의 값은 i번째 자원과 연관되어 있다.
- 비트맵의 힘은 이들의 공간 효율을 생각하면 된다. 단일 비트 대시 8비트의 부울 값을 사용한다면 데이터 구조는 8배의 크기가 될 것이다. 따라서 비트맵은 대량의 자원의 가용성을 표시할 때 일반적으로 사용된다. 디스크 드라이브가 좋은 예가 될 수 있다. 중간 크기의 디스크 드라이브는 디스크 블록이라 불리는 수천 개의 독립된 단위로 나누어진다. 각 디스크 블록의 가용여부를 나타내기 위해 비트맵을 사용할 수 있다. 

# 1.11 컴퓨팅 환경(Computing Environments)
## 1.11.1 전통적 컴퓨팅(Trandtional Computing)
- 컴퓨터 환경이 발전함에 따라 전통적인 컴퓨터 환경의 경계가 흐려지고 있다.
- 현재의 추세는 원격 접근과 이동성을 다양하게 제공하려 한다.
- - 20세기 후반에는 컴퓨팅이 리소스가 귀했기에, 계산 자원을 최적으로 사용하기 위하여 다중 사용자들이 시스템 시간을 나누어 사용했다.
- 시분할 시스템은 자원의 일정량을 각 사용자에게 프로세스를 CPU에 하나씩 순환시켜 가면서 실행시키기 위하여 타이머와 스케줄링 알고리즘을 이용한다.
- 오늘날 전통적 시분할 시스템은 흔하지 않다. 동일한 스케줄링 기법이 우크스테이션과 서버에서 사용되고 있지만, 모든 프로세스는 한 사용자가 전부 소유하거나 또는 단일 사용자와 운영체제가 소유하고 있는 형태를 취한다. 사용자 프로세스와 사용자에게 서비스를 제공하는 시스템 프로세스는 계산 시간을 빈번하게 얻을 수 있도록 관리된다. 예를 들어 PC 상에서 작업하는 동안 생성된 동시에, 다른 태스크를 수행할 수 있는 윈도들을 생각해보자.각 웹사이트별로 한 프로세스를 갖도록 다수의 프로세스로 구성될 수 있으며 각 웹 브라우저들에 시분할이 적용된다.

## 1.11.2 이동형 컴퓨팅(mobile computing)
- 이동형 컴퓨팅은 휴대용 스마트폰과 태블릿 컴퓨터등을 말한다. 이들은 이동 가능하고 가볍다는 물리적 특성을 가진다.
- 온라인 서비스에 접근을 허용하기 위해 휴대장치는 보통 IEEE 표준 802.11 무선 또는 휴대 전화 데이터망을 이용한다. 
- 메모리 용량과 처리 속도는 PC보다 제한적이다.
- 전력소모가 작고, 느리며, 적은 수의 코어를 갖는다.
- 현재 안드로이드와 IOS가 시장을 지배하고 있다.

## 1.11.3 분산 시스템(Distributed Systems)
- 분산 시스템은 물리적으로 떨어져 있는 이기종 컴퓨터들의 집합이다. 분산 시스템의 컴퓨터들은 사용자가 시스템 내의 다양한 자원들을 접근할 수 있도록 네트워크로 연결되어 있다. 공유 자원에 대한 접근은 계산 속도와 기능, 데이터 가용성 및 신뢰성을 향상시킨다.
- 네트워크는 가장 단순하게 보면 두개 이상 시스템 간의 통신 경로이다. 분산 시스템의 많은 기능들은 네트워킹에 의존한다. 모든 범용 운영체제를 포함하여, 대부분의 운영체제는 TCP/IP를 지원한다.
- 네트워크는 노드간의 거리에 의해 타입이 결정된다. LAN, WAN, MAN, PAN 등 
- 어떤 운영체제들은 네트워크 연결을 제공하는 차원을 넘어 네트워크와 분산 시스템의 개념을 더 확대한다. 네트워크 운영체제는 네트워크를 통한 파일의 공유, 다른 컴퓨터상에 존재하는 프로세스들끼리의 메시지 교환을 제공하는 운영체제이다. 네트워크 운영체제를 수행하는 컴퓨터는 네트워크상의 다른 컴퓨터들과 연결되어 있고 통신이 가능하더라도 독짜억르ㅗ 행동한다. 분산 운영체제는 그에 비해 덜 독자적으로 환경을 제공한다. 다른 운영체제들끼리 하나의 운영체제가 네트워크를 제어하는 것처럼 보이기 위하여 서로 밀접하게 통신한다.

## 1.11.4 클라이언트 서버 컴퓨팅(Client-Server Computing)
- PC가 빠르고, 강력하고, 저렴해짐에 따라 설계자들은 중앙식 시스템으로부터 이탈하기 시작했다. 중앙식 시스템에 연결된 터미널은 PC와 휴대용 장치에 의해 대체되고 있다. 따라서 과거에는 중앙 시스템에 의해 직접 취급되던 사용자 인터페이스 기능들도 PC에 의해 종종 종종 웹 인터페이스를 통해 처리되고 있다. 그 결과 오늘날의 시스템은 클라이언트 시스템에 의해 생성되는 요구를 만족시키기 위한 서버 시스템으로 동작한다. 이런 특별한 분산 시스템을 클라이언트-서버 시스템이라고 부른다.
- 서버 시스템은 넓게는 컴퓨팅 서버와 파일서버로 분류될 수 있다.
  -계산-서버 시스템은 클라이언트가 어떤 작업을 요청할 수 있는 인터페이스를 제공한다. 그 결과로 서버는 그 작업을 수행하고 결과를 클라이언트에게 돌려보낸다. 클라이언트의 데이터 요청에 응답하는 데이터베이스를 실행하고 있는 서버가 이러한 시스템의 한 예이다.
  - 파일-서버 시스템은 클라이언트가 파일을 생성, 갱신, 읽기 및 제거할 수 있는 파일 시스템 인터페이스를 제공한다. 이러한 시스템의 예로서 웹 브라우저 클라이언트에게 파일을 전달하는 웹서버를 들 수 있다.

## 1.11.5 피어 간 컴퓨팅(Peer-to-Peer Computing)
- 분산 시스템의 다른 구조는 피어간 시스템이다. 이 모델에서는 클라이언트와 서버ㅂ가 서로 구별되지 않는다. 대신 시스템 상의 모든 노드가 피어로 간주되고 각 피어는 서비스를 요청하느냐 제공하느냐에 따라 클라이언트나 서버로 동작한다. 피어 간 시스템은 전통적인 클라이언트 서버에 비해 장점을 제공한다. 클라이언트 서버 시스템에서는 서버가 병목으로 작용한다. 그러나 피어 간 시스템에서는 서비스가 네트워크에 분산된 여러 노드에 의해 제공될 수 있다.
- 피어 간 시스템에 참여하기 위해서는 노드는 먼저 피어간 네트워크에 참가해야 한다. 네트워크에 참가하게 되면 네트워크의 다른 노드에게 서비스를 제공하거나 서비스를 요청할 수 있다. 어떤 서비스가 가능한 지를 결정하는 방법에는 다음의 두 일반적인 방법이 있다.
  - 노드가 네트워크에 참가할 때 네트워크의 중앙 검색 서비스에 자신이 제공하는 서비스를 등록한다. 특정 서비스를 원하는 노드는 먼저 이 중앙 검큑 서비스를 통하여 어떤 노드가 서비스를 제공하는지 확인하여야 한다. 나머지 통신은 클라이언트와 서비스 제공자 사이에서만 발생하게 된다.
  - 위의 대안 기법은 중앙 검색 서비스를 사용하지 않는다. 클라이언트로 동작하는 피어는 원하는 서비스를 제공하는 노드를 찾아내기 위하여 네트워크상의 모든 노드에게 서비스 요청 메시지를 발송한다. 그 서비스를 제공하는 노드 또는 노드들은 피어에게 응답 메시지를 보낸다. 이 방법을 지원하기 위해서는 피어들이 다른 피어들이 제공하는 서비스를 발견하기 위한 발견 프로토콜이 제공되어야 한다. 

## 1.11.6 가상화(Virtualiztion)
- 가상화는 운영체제가 다른 운영체제 내에서 하나의 응용처럼 수행될 수 있게 한다. 언뜻 보아서는 그러하ᅡᆫ 기능이 별 이유가 없는 것처럼 보인다. 그러나 가상화 산업은 방대하며 성장하고 있어 그 용도와 중요성을 증거하고 있다.
- 넓게 말하면 가상화는 에뮬레이션을 포함하는 소프트웨어 집단의 한 구성원이다. 에뮬레이션은 소스 CPU 유형이 목표 CPU가 유형과 다를 때 사용된다. 예를 들면 애플사가 자신의 데스크톱과 랩톱 컴퓨터의 CPU를 IBM Power CPU에서 인텔 x86 CPU로 전환했을ㅌ태 "로제타"라는 에뮬레이션 설비를 포함하여 IBM CPU용으로 컴퓨일 되었던 응용을 인텔 CPU에서 수행할 수 있게 하였다. 이러한 개념이 한 기계를 위해 작성된 전체 운영체제를 다른 기계에서 수행되도록 확장될 수 있다.
- 에뮬레이션 비용은 크다. 소스 시스템에서 수행되는 기계어 수준의 모든 명령은 목표 시스템에서 동등한 기능으로 번역되어야 하고 종종 이는 다수의 목표 명령어가 되기도 한다. 만일 소스와 목표 CPU가 비슷한 성능 수준일 경우 에뮬레이트된 코드는 원래의 코드보다 훨씬 느리게 수행된다. 에뮬레이션의 일반적 예는 컴퓨터 언어가 기계어 코드가 아니라 고급 수준 형태로 수행되거나 또는 중간형태로 변역되었을 경우 발생한다. 이는 인터프리테이션이라 알려져 있다.
- 가상화에서는 특정 CPU를 위해 컴파일된 운영체제가 동일 CPU 용의 다른 운영체제 내에서 수행된다. 가상화는 다수 사용자가 작업을 병행 수행하기 위한 방법으로 IBM 대형 컴퓨터에 처음 등장하였다. 다수의 가상기계를 수행함으로써 많은 사용자들이 단일 사용자를 위해 설계된 시스템에서 작업을 수행할 수 있게 되었다. 후에 다수의 마이크로소프트 Windows XP 응용을 인텔 x86 CPU에 수행하는 문제를 해결하기 위해 VMware가 XP상에서 수행되는 응용의 형태로 새로운 가상화 기술을 만들었다. 이응용은 게스트라 불리는 하나 이상의 Windows나 다른 x86용 운영체제 복사본들을 수행하며 각 게스트는 자신의 응용을 수행한다. 윈도우는 호스트 운영체제이고 VMWare 은용은 VMM이다. VMM은 게스트 운영체제를 수행하고 그들의 자원이용을 관리하며 서로로부터 보호한다.
- 현대의 운영체제가 다수의 응용을 시뢰성 있게 수행할 능력이 있다고 해도, 가상화의 이용은 지속적으로 성장하고 있다. 랩톱과 데스크톱에서 VMM은 체험을 위하거나 또는 다른 호스트용으로 작성된 운영체제 상의 응용을 수행하기 위해 다수의 운영체제를 설치할 수 있게한다. 예를 들면 x86 CPU 위에서 Mac OS를 수행하는 애플 랩톱은 윈도우 응용을 수행하기 위해 윈도우 게스트를 수행할 수 있다. 다수 운영체제를 위해 소프트웨어를 작성하는 회사는 가상화를 이용하여 하나의 물리 서버를 이용하여 이들 모든 운영체제를 수행하여 개발, 테스팅, 디버깅을 수행할 수 있다. 데이터 센터에서는 가상화가 계산 환경을 수행하고 관리하는 데 일반적인 방법이 되었다.

## 1.11.7 클라우드 컴퓨팅
- 클라우드 컴퓨팅은 계산, 저장장치는 물론 응용조차도 네트워크를 통한 서비스로 제공하는 컴퓨팅이다. 어떤 면에서 클라우드 컴퓨팅은 가상화를 그 기능의 기반으로 사용하기 때문에 가상화의 논리적 확장이다. 예를 들면 아마존 EC2 설비는 수천의 서버, 수백만의 가상 기계 및 페타 바이트의 저장장치를 인터넷 상의 누구든지 사용할 수 있게한다.
- 아래를 포함하여 여러 유형의 클라우드 컴퓨팅이 존재한
  - 공중 클라우드: 서비스를 위해 지불 가능한 사람은 누구나 인터넷을 통해 사용 가능한 클라우드
  - 사유 클라우드: 한 회사가 사용하기 위해 운영하는 클라우
  - 혼합형 클라우드 공공과 사유 부분을 모두 포함하는 클라우
  - 소프트웨어 서비스: 인터넷을 통해 사용 가능한 하나 이상의 응용 프로그
  - 플랫폼 서비스: 인터넷을 통해 사용하도록 응용에 맞게 준비된 소프트웨어 스
  - 하부구조 서비스: 인터넷을 통해 사용 가능한 서버나 저장장

## 1.11.8 실시간 내장형 시스템(Real-Time Embedded Systems)
- 내장형 시스템은 현재 가장 유행하는 컴퓨터의 형태이다. 이 장치들은 자동차 엔진, 공장용 로봇에서 VCR, 전자파 오븐 등 어느 곳에서나 볼 수 있다. 이 장치들은 아주 특정한 작업만을 숭해하는 경향이 있다. 이 장치들이 수행되는 시스템은 매우 원시적이며 따라서 운영체제도 제한된 기능만을 제공한다. 일반적으로 사용자 인터페이스가 거의 없으며 자동차 엔진이나 로봇 팔과 같은 하드에우ㅓ 장치들을 감시하고 관리하는 데 시간을 보낸다.
- 다른 시스템은 필요한 기능만을 제공하는 내장형 운영체제를 가지고 있는 하드웨어의 형태를 취한다. 또 다른 시스템은 운영체제 없이 필요한 작업을 수행하는 응용 전용 칩만을 갖는 하드웨어 장치들이다. 
- 내장형 시스템은 거의 언제나 실시간 운영체제를 수행한다. 실시간 시스템은 처리기의 작동이나 데이터의 흐름에 엄격한 시간 제약이 있을 때 사용된다. 따라서 실시간 씨ㅡ템은 종종 전용 응용에서 제어 장치로 사용된다. 감지기가 데이터를 컴퓨터로 가져온다. 컴퓨터는 반드시 데이터를 분석하고, 아마도 감지기의 입력을 변경할 수 있도록 제어를 조정할 것이다. 과학 실험을 제어하는 시스템, 의학 영상 시스템, 산업 제어 시스템, 그리고 몇몇디스플레이 씨트ᅦᆷ 등이 실시간 시스템이다. 


## 1.12 오픈소스 운영체제(Open-Source Operating Systems)
오픈소스 운영체제는 컴파일된 이진 형태보다는 소스 코드 형태로 받을 수 있는 운영체제를 의미한다.
소스 코드로 부터 프로그래머는 한 시스템에서 실행 가능한 이진코드를 생성할 수 있다. 역공학을 통하여 이진코드로 부터 소스 코드를 얻어내는 반대의 작업은 매우 많은 노력을 필요로 하며 주석가ㅗ 같은 유용한 사항들을 복원해 낼 수는 없다. 소스코드를 분석하여 운영체제를 배우는 것은 매우 유용하다.
(오픈 소스 운영체제 역사와 배포판에 관한 내용은 생략한다.)
