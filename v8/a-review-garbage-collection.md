해당 포스팅은 Javascript Modern Deep Dive 4장을 읽는 와중에 메모리 관리 기법에 관심이 생겨서 작성하였습니다.

모던 딥 자바스크립트를 모딥다로 줄여서 이야기하겠습니다.

모딥다 4장 내용은 변수입니다. 변수에 대한 내용을 읽으면서 스코프, 실행 컨텍스트, 클로저, const와 let 차이 등등에 대한 글을 작성하고 싶었지만 이번 포스팅은 GC(가비지 컬렉터)에 대한 초점을 맞춰서 설명하려고 합니다.

만약 여러분이 c++, c, rust 중 하나의 언어를 접하고 Javascript를 배웠으면 다음과 같은 궁금중이 생길 겁니다.

_개발자가 직접 변수를 선언하고 사용되지 않는 변수는 어떻게 자동으로 회수되는 것인가? 그리고 회수의 기준은 무엇인가? 회수의 시점은? 그리고 만약 잘못 회수되는 경우에는 어떻게 처리되는 것인가...? 등등 메모리에 대한 생각이 들 겁니다._

물론 자바스크립트는  일반적으로 자바스크립트 엔진이 자동적으로 메모리 관리를 해주기 때문에 개발자는 메모리 관리에 대한 시간을 절약하여 다른 퍼포먼스에 집중할 수 있는 장점이 있습니다.

**진짜로 자바스크립트 엔진이 자동적으로 메모리를 관리하기 때문에 메모리 관리에 대한 내용을 학습하지 않아도 될까요?**

아닙니다. 실제로 c++, c에서 등장하는 메모리 누수라는 개념이 javascript에서도 **메모리 누수의 현상이** 발생합니다. 그 말은 자바스크립트 엔진이 완벽하게 메모리를 관리하면 메모리 누수 현상이라는 것이 존재하지 않아야 하는데 우리는 메모리 누수 현상을 발견합니다.

그러기 때문에 자바스크립트라는 언어를 사용한다고 메모리 누수를 막는 것은 아닙니다. 즉 자바스크립트는 언제든지 메모리 누수 현상이 일어날 수 있기 때문에 메모리에 대한 내용을 한 번은 공부해야 한다고 생각합니다. (메모리 누수 -> 서비스 장애에 대한 경험에 대응할 수 있는가?)

그러기 때문에 우리는 Javascript에서 동작하는 GC를 알아봅시다.

가비지 컬렉터란 무엇인가요?

가비지 컬렉터란 Javascript에서만 등장하는 용어가 아닙니다. 여러분이 JAVA랑 Python을 접해보았으면 반드시 한 번쯤은 GC에 대한 내용을 학습했을 겁니다. 많은 레퍼런스와 책에 등장하는 한 줄 요약은 다음과 같습니다.

> 가비지 컬렉터란  사용되지 않는 메모리를 자동으로 회수하여 메모리 누수를 방지하는 메모리 관리 기능입니다.

여기서 우리는 아래의 궁금증이 생길 겁니다.

\- **사용되지 않는 메모리라고** 판단하는 기준은 무엇인가?

\- **자동으로 회수**된다는 표현은 어떠한 알고리즘을 사용하는가?

\- **메모리 누수**가 정말로 발생하지 않는가?

\- 자바스크립트에서 사용하는 **메모리 구조**는?

먼저 Javascript에서 사용되는 GC는 Javascript enigine에 따라 구현 방식이 다릅니다. 그래서 저는 최근에 Node.js에 관심이 생겨서 **V8 기준**으로 설명하겠습니다. 그리고 V8을 설명하면서 간략하게 Apple 사의 JavaScriptCore 엔진이랑 어떤 차이가 있는지 소개하겠습니다.

오늘은 V8엔진에서 구동하는 GC에서 대해서 탐구할 예정이므로 V8에 대한 소개는 다음과 같은 링크로 대체하겠습니다.

[https://v8.dev/](https://v8.dev/)

일반적으로 GC의 중요하고 **필수적인 작업**은 다음과 같습니다

\- Object이 살아있는지 혹은 죽어 있는지 식별해야 합니다.

\- 죽은 Object가 남긴 메모리 공간을 재활용하고 재사용해야 합니다.

\- 파편화된 메모리 공간의 틈을 압축하고 최적화를 시킵니다. (이러한 작업은 선택적으로 수행됩니다.)

**방금 소개한 과정이 순차적으로 진행할 경우에는 어떠한 문제점이 있을까요?**

물론 순차적으로 진행하는 것이 자연스럽게 느껴질 수도 있습니다. 왜냐하면 살아있는지 죽어 있는지 판단한 다음 죽어서 남긴 공간을 새로운 변수가 선언할 때 재사용할 수 있으니 그렇게 판단할 수 도 있습니다.

하지만 첫 번째 과정을 곰곰이 생각해 보면 살아있는지 죽어 있는지 판단하는 작업에 걸리는 시간이 매우 길어지면, 변수를 할당하기까지 시간도 매우 늘어나며 결과적으로 **Javascript의 런타임 하는 과정이 매우 느리게 진행됩니다**. 그래서 이 포스팅에서 나중에 소개하겠지만 순차적으로 진행하지 않고 다양한 최신기술을 도입할 필요성이 있습니다.

그러면 이제 v8에 구동하는 가비지 컬렉터의 종류에 대해서 알아봅시다. 크게 두 가지 유형으로 나누어집니다.

**Major GC와 Minor GC**입니다.

실행 순서는 Minor GC가 먼저 수행되고 Major GC가 실행됩니다. 하지만 우리는 Minor GC의 개념이 왜 탄생되었는지 이해를 하려면 Major GC에 대한 개념이 필요하므로 먼저 Major GC를 소개하겠습니다.

### **Major GC란? (Full Mark-Compact)**

Major GC는 크게 3가지 특징이 있습니다.

**1\. Marking**

**2\. Sweeping**

**3\. Compaction**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/major-gc.jpeg?raw=true)

#### Marking이란?

여러분은 메모리 구조를 생각하면 **heap**과 **stack**을 생각할 수 있습니다. 자바스크립트의 GC는 Reference 기반으로 동작하므로 Heap에 대해서 이야기하겠습니다. (정확히 말하자면 Reference 가 아니라 **도달할 수 있는지** 에 동작합니다!)

Marking은 GC의 가장 필수 적인 내용입니다. 바로 Object이 살아 있는지 죽어 있는지 판단하는 것입니다. 즉 전체 힙에서 판단합니다.

그런데 지금 계속 살아 있는지 죽어있는지 용어는 추상적인 단어이므로 용어를 조금 좁게 표현하겠습니다.

**살아있다 --> 도달 가능성이라고 표현하겠습니다**. 줄여서 이야기하겠습니다.

그리고 이 내용을 이해하려면 **재귀, 스택 포인터, DFS 개념이 필요합니다.** 그리고 V8에서 이야기하는 GC 루트 집합이라는 정의를 알아야 합니다.

**GC 루트 집합:**  객체 포인터 집합입니다. 여기서 포인터라고 당황할 필요가 없습니다.  루트 집합에는 전역 객체와 실행 스택이 포함되어 있습니다.

즉 마킹이란 가비지 컬렉터가 어떤 객체가  사용 중인지 (살아있는지) == GC 루트 스택에서 javascript 객체에 대한 포인터를 따라가면서 도달 가능한 것만 표시합니다. 이때 도달할 수 있는 모든 객체를 찾을 때까지 마킹합니다. (즉 식별합니다.)

**즉 v8에서 말하는 가비지 컬렉터는 죽어있는 메모리를 자동 회수가 아니라 도달할 수 없는 메모리를 자동 회수합니다.** 도달 가능성은 매우 중요한 개념입니다. 반드시 알아두시면 다음에 소개하는 내용을 이해할 수 있습니다.

#### Sweeping 이란?

도달하지 못한 Object(죽어있는)가 남긴 메모리 공백을 "**free-list"**라는 데이터 구조에 메모리 주소를 기록합니다. 즉 이 개념은 Marking이 완료 후 할 수 있는 작업입니다.

**_free-list에 왜 도달하지 못한 Object을 추가할까요?_**

바로 GC가 수행 후 새로운 변수를 선언할 때 비어진 메모리를 찾습니다 그때 free-list에 있는 공간부터 먼저 탐색하고 해당 메모리를 사용합니다. 그러면 이러한 과정을 **조금 더 빠르게 하기 위해** free-list는 **메모리 청크 크기에 따라 구분합니다.**

즉  변수를 선언하거나 할당할 때 메모리를 찾는다라는 의미는 free-list에 표시한 메모리 청크를 본다는 의미입니다.

쉽게 표현한다면 10이라는 메모리 공간이 필요한데 Marking 하면서 죽어 있는 메모리 공백의 틈은 1... 3.... 5.... 7....입니다. 이때 우리가 10이라는 메모리 공간이 필요하면 free-list에 메모리 청크 크기에 따라 구분되지 않으면 1.... 3.... 5.... 7을 찾게 됩니다. 그러면 지연되는 속도가 있기 때문에 처음부터 10이라는 공백이 없다는 정보가 있으면 빠르게 조회가 됩니다.

#### Compaction 이란?

앞에서 소개한 일반적으로 GC의 중요하고 필수적인 작업에서 마지막 파트 영역입니다. 기억하십니까?

> 파편화된 메모리 공간의 틈을 압축하고 최적화를 시킵니다 (선택적)

**Sweeping** 과정을 곰곰이 생각해 보면 살아남는 객체들 사이에 수많은 틈이 생길 겁니다. 그러기 때문에 활성화된 객체는 free-list의 공간을 활용해서  압축되지 않는 다른 페이지에 복사합니다.

여기서 **GC의 약점**이 생깁니다. 왜냐하면 **살아남은 객체를 복사하는 과정은 비용이 드는 작업입니다.**

그래서 V8 Major GC는 **너무 심하게 파편화된 페이지만 압축하**고 나머지 살아남은 객체는 복사하는 방식을 선택하지 않기 때문에 약점을 줄일 수 있습니다.

파편화의 기준에 대해서 다음 포스팅에서 이야기하겠습니다. (스왑 vs 휴리스틱)

그다음 Minor GC를 소개하기 전에 먼저 Heap의 구조를 알아야  왜 Minor GC가 탄생했는지 알 수 있습니다.

Heap을 소개하기 앞서 해당 내용은 V8 기준으로 작성했다는 것을 다시 알립니다.

### Generation 영역

Heap은 Genreations이라는 영역으로 구분합니다. **Young Generation, Old Generation**이라고 불리는 영역 들로 구분되어 있습니다.

#### Young Generation 이란?

v8에서는 *Young Generation*이라고 부르며 Apple의 JavaScriptCore은 *Nursey*이라고 부릅니다. 둘 다 같은 것을 가리키는 명칭입니다.

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/young-generation.jpeg?raw=true)

Young Generation은 또한 두 가지 영역으로 부릅니다. **Nursery , Intermediate generation입니다.** 사실 이  두영역은 서로 스위칭도 되기 때문에 일단  두 가지 영역으로 구분된다 정도만 이해하고 이 영역이 **무조건 Nursey라고 생각하시면 안 됩니다.** (From-Space, To-Space)

그렇지만 우리는 처음에 nusery, Intermediatie라고 부르고 나중에는 **two-space**라고 부르겠습니다.

Object은 처음에 Young Generation 안에 Nusery라는 공간에 할당됩니다. 그리고 GC가 수행하고 살아남으면 Young Generation에서 Intermidate로 남겨집니다. 그리고 다시 GC에서 살아남으면 **Old Generation으로 이동합니다.**

**요약:**

Heap에는 Young Generation과 Old Generation이 있다. (GC 관점에서)

Young Generation은 두 가지 영역이 존재한다. 하지만 구분하기 위해서 Nusery, intermeditate라고 표현하지만 서로 나중에 스위칭되므로 그냥 편의상으로 붙인 이름이라고 생각하자

**Nursey:** 처음 Object이 할당된 공간

**Intermeditate**: 첫 GC에서 살아남아 이동한 공간

**Old Generation:** 두 번째 GC에서 살아남아 이동한 공간

앞에서 소개한 Major GC와 이번 포스팅에서 가장 중요한 핵심은 **Generational Hypothesis**입니다.

### Generation Hypothesis란 무엇인가?

번역한다면 말 그대로  Generation 가설입니다. 즉 모든 Object은 Young Generation에서 Intermeditate 영역으로 가지 못한다고 합니다. **즉 대부분의 객체는 도달하지 못하고 죽습니다.** (할당 이후 바로 죽음) 이러한 개념은 Javascript 뿐만 아니라 Java 역시 그렇게 적용됩니다.

**V8의 Generation 영역 구분도 이러한 가설을 기반하여 설계했습니다.**

즉 앞에서 이야기한 GC는 압축 및 이동이므로 살아남는 객체만 복사합니다. 복사라는 작업은 매우 큰 비용이 들어서 안 좋다고 생각하지만 가설을 기반으로 생각하면 **실제로 살아남는 객체는 극소수이므로  할당하는 객체에 비해서 살아남는 객체만 복사하는 방식은 효율적**입니다.

\=> 가설 : 살아남는 객체는 매우 극소수이다. 즉 할당 객체  >>>> 살아남는 객체 == 0

할당 객체를 복사하지 않고 살아남는 객체만 복사하는 방식을 채택

지금까지 v8에서 힙의 Generation 영역과 Major GC, Generational Hypothesis를 알아보았습니다.

### Minor GC(스캔빈 저 너) 이란?

이제는 Minor GC를 알아볼 차례입니다.

앞에서 이야기했듯이 v8에서는 두 가지 GC가 존재합니다. **Major GC는** **전체의 Heap**에서 GC를 실행하며 **Minor GC는 Young Generation에서 실행**합니다.

_**왜 Minor GC가 필요할까요?**  이때 필요한 개념이 앞에서 이야기 한 Generation Hypothesis입니다._

즉 가설에 따르면 대부분 Object은 할당 후 즉시 도달하지 못하기 때문에(바로 죽음) Old Generation에 남는 Object이 거의 없습니다. 그러므로 **전체의 Heap을 쓰레기 수집을 하는 것은  매우 비효율적입니다.**

예) Old Generation: 0.9 ^ (1000)  개 Young Generation: 99.. 99999 라면 즉 Young Generation에서 쓰레기 수집이 매우 많이 필요하기 때문에 **Young Generation에 만 특화되어 동작하는 GC가** 있어야 GC 동작하는 시간을 줄일 수 있습니다.

이러한 비효율성의 문제로 Young Generation에 특화된 Minor GC가 탄생되었습니다.

앞에서 Young Generation은 두 가지 공간이 존재한다고 이야기했습니다. Nusery, Intermetation

이 용어를 안 쓰고 **v8에서 제공하는 용어**를 사용하겠습니다. 왜냐하면 앞에서 소개한 두 용어로 인해 기능에 대한 오해를 불러일으킬 수 있습니다.

v8은 Young Generation에서 **"semi-space"**라는 개념을 사용합니다. 즉 항상 Young Generation 공간의 절반은 항상 비어두게 합니다. 왜냐하면 GC를 수행하고 살아남은 Object들이 대피할 수 있는 공간을 마련하기 위해서입니다.

GC가 실행하는 동안 Young Generation에서 처음 **비워 있는 영역**을 **To-Space**라고 합니다.

그리고 **복사하는 영역을 From-Space**라고 합니다.

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/from-to-space.jpeg?raw=true)

즉 처음 GC가 실행하는 동안 From-Space에서 살아남은 모든 Object은 To-Space 공간으로 이동합니다. 그러면 From-Space에는 도달하지 못한 죽은 Object만 남게 됩니다. 그러면 해당 공간을 완전히 지울 수 있는 장점이 됩니다.(즉 From-Space에 남은 Object은 메모리를 해제합니다.)

GC 가 완료되면 To-Space에 이동한 Object는 **To-Space의 메모리 주소 값으로 포인터가 업데이트**됩니다.

그리고 기존의 From-Space와 To-Space 역할은 **스위칭**하기 때문에 From -> NewTo , To -> NewFrom 으로 변경됩니다.

그러면 NewFrom으로 새로운 객체를 할당받을 수 있습니다. 그리고 다시 GC가 수행하면 새로 생긴 객체 중 생존한 객체는 NewTo로 대피합니다. **그리고 기존에 한번 생존한 객체는 NewTo 가 아닌 Old Space로 이동합니다.**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/newto-not-oldspace.jpeg?raw=true)

**왜 기존에 생성된 객체는 새로운 객체처럼 NewTo로 이동하지 않고 Old Space로 이동할까요?**

From - To로 계속 스위칭하는 전략은 **Young Generaton 공간이 금방 부족해지기 때문입니다.** 그리고 Young Generation은 Old Generation 보다 빈번하게 GC를 수행하다 보니 한 번이라도 생존한 객체는 새로운 객체보다 **살아남을 가능성이 매우 높기 때문에** 다른 공간으로 이동할 필요가 있습니다. (**Generational Hypothesis**)

그리고 Old Generation으로 이동할 수 있다는 것은 **몇 번 생존했는지** 데이터를 가지고 있다는 것입니다. 그러므로 생존한 객체들은 몇 번 생존했는지에 대한 데이터를 추가적으로 가지고 있습니다.

### **V8의 GC의 최신 기술 및 변화**

이렇게 지금까지 V8에서 소개하는 Major GC와 Minor GC의 동작원리와 차이점을 알아봤습니다.

하지만 이러한 GC 들은 전통적이므로 GC를 수행할 때 프로그램이 **지연됩니다**. 즉 자바스크립트는 **단일 스레드**로 동작하는데 GC가 동작하는 동안 **JS는 메인 스레드에서 일시 중지 되었습니다.**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/main-thread-stop.jpeg?raw=true)

이러한 일시 중지 되는 시간을 줄이는 것이 GC의 큰 목표입니다. 그래서 V8은 **Orinoco**라는 프로젝트 시작했습니다.

Orinoco 프로젝트는 다음과 같이 소개됩니다.

> Orinoco is the codename of the GC project to make use of the latest and greatest **parallel**, **incremental** and **concurrent** techniques for garbage collection, in order to free the main thread.

orinoco 프로젝트는 가장 최신의 parallel, incremental and concurrent 기술들을 사용한다고 합니다.  
그럼 이 세 가지 개념을 살펴보겠습니다.

**1\. parallel**

**2\. incremental**

**3\. concurrent**

#### Parallel 이란?

GC 수행을 여러 스레드에서 작업하므로 GC에 걸리는 시간을 줄이고 애플리케이션의 성능을 향상합니다. 그리고 3가지 방식 중에 가장 간단합니다.

하지만 여전히 메인 스레드에서 GC가 동작할 때 힙에서 **JS 코드는 수행되지 않으므로 일시중지 시간만 최소화하는 방식**입니다.

즉 전통적인 GC의 접근 방식(stop-the-world)은 같지만 일시중지 시간을 줄이는 점에 차이가 있습니다.

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/parallel.jpeg?raw=true)

#### Incremental 이란?

말 그래도 증분 작업입니다. 즉 GC에서 수행하는 작업을 분할하여 메인 스레드에서 JS-GC-JS-GC 형식으로 수행합니다. (전체 작업의 일부분만 수행)

이렇게 증분 하는 방식은 Heap의 상태가 계속 변경되어서 Parallel보다 어려운 방식입니다.

이 방식은 전통적인 GC의 JS 코드가 실행되지 않는 메인 스레드 지연시간을 해결하는 관점은 좋습니다.

**하지만 이 방식은 메인 스레드의 소요시간을 줄이지 않는 방식입니다.**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/incremental.jpeg?raw=true)

**실제로 메인 스레드를 소요시간이 늘어난다고 합니다. 왜 그럴까요?**

그 이유는 **Heap의 상태 변화가 자주 변경**되므로 이전에 작업했던 GC의 과정을 다시 해야 할 수 있는 문제점이 있습니다.

하지만 JS로 웹 분야에 활용할 때 우리는 1초라는 시간도 GC 때문에 **지연되면 매우 사용자의 경험을 나쁘게 만듭니다.** 그래서 **GC의 실행 과정이 길어지더라도 메인 스레드에서 JS가 지연하는 시간을 줄이는 방식**을 채택한 것입니다.

**요약:** GC 처리하는 시간이 길더라도 메인 스레드에서 JS 코드가 지연되는 시간을 줄이기 위해 총시간이 길어지는 Incremental 방식을 사용함

#### Concurrent 이란?

말 그대로 JS 코드와 GC가 **동시에 수행할** 수 있습니다. 전통적인 GC에서 발생하는 **Stop-the-world** 방식을 해소하는 아름다운 방식입니다.

JS는 단일 스레드이므로 메인 스레드에서  수행하며 GC는 **백그라운드에서** 작업을 수행합니다.

하지만 이러한 방식은 Stop-the-world 방식에서는 아름답지만 생각보다 신경 써야 하는 어려운 기법입니다.

왜냐하면 메인 스레드에서 Object을 참조하거나 할당할 때 GC에서도 Object의 상태를 변경하고 있어서 충돌하는 현상이 생길 수도 있습니다. 즉 GC와 JS 코드 간의 상호작용이 하나라도 어긋나면 **JS에서 개발자의 의도에 맞지 않게 다른 메모리 주소를 참조할 수 있는 오버헤드 현상이 일어납니다.**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/concurrent.jpeg?raw=true)

**요약:**

장점 - GC의 일부 단계를 메인 스레드와 병렬로 수행함으로써  **GC에 걸리는 시간을 줄이고 애플리케이션 성능을 향상**

단점 - GC와 JS에서 동일한 Object을 참조하거나 상태를 변경하면 동기화에 오버헤드 현상을 일어남

지금까지 v8에서 제공하는 GC의 최신 개념을 알아봤습니다. **그러면 이 세 가지 기능이 전부 Major GC와 Minor GC에 적용이 될까요?**

**아닙니다!**  먼저 Minor GC(Scavenging)은 어떠한 변화가 있었는지 알아봅시다.

![](https://t1.daumcdn.net/keditor/emoticon/friends2/large/035.png)

#### **최신 MinorGC의 변화**

**Scavenging은 과거랑 달리 Parallel 방식을 사용합니다**. 그러므로 각 스레드에게 **도달한 Object**을 To-space 공간으로 이동시킵니다.

그리고 남겨진 From-Space에는 한 스레드만 GC를 수행하는 것이 아니기 때문에 **빠르게 청소를 수행할 수 있습니다**.

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/minorgc-new.jpeg?raw=true)

왜 Parallel 방식을 수행하면 **Generational 가설에 기반하여** Old Generation 보다 청소해야 하는 양이 많기 때문입니다.

#### **최신 MajorGC의 변화**

Minor GC 보다 MajorGC는 매우 많이 발전하였습니다.

Major GC는 **Concurrent Marking**을 수행합니다. 즉 메인스레드에서 JS 코드를 수행하면서 동시에 **헬퍼 스레드**에서 도달할 수 있는 Object인지 식별합니다.(Marking)

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/major-gc.jpeg?raw=true)

그리고 Concurrent Marking 이 완료되면 메인 스레드에서 JS 코드가 일시 중지가 시작됩니다.

**일시중지되는 다른 요인이 없나요?**

_\-> 힙의 크기는 **가변적이므로** Concurrent Marking이 완료되지 않아도 **한도에 도달하면** 메인스레드는 즉시 일시 중지 기간으로 시작되며 **남은 마킹은 메인 스레드에서 수행합니다.**_

그리고 일시 중지기간 동안 수행하는 **작업은** 다음과 같습니다.

한번 더 메인 스레드에서 **루트부터 다시 확인하여** 모든 Object이 마킹이 된 것을 확인 한 다음 **Parallel** 방식을 수행합니다.

이때 여러 헬퍼 스레드에서 메인스레드와 함께 **압축과** Pointer **업데이트**를 진행합니다. 그리고 압축을 설명할 때 모든 page가 압축되지 않는다고 이야기했습니다.

> (그래서 V8 Major GC는 너무 심하게 파편화된 페이지만 압축하고 나머지...)

그러기 때문에 **sweep 과정은 압축 과정보다 길기 때문에** 다른 헬퍼 스레드에서 수행합니다. 이때 Sweep 작업은 **Parallel 압축과 메인 스레드에 동시에 실행합니다. (Concurrent)**

\--> 실제로 Parrel 압축보다 먼저 수행합니다!

**요약:**

1\. GC는 처음에 Concurrent Marking을 수행한다.

2\. Heap의 한도 도달 혹은 Concurrent Marking을 완료하면 Main 스레드 일시중지 기간을 가진다. 이때 잠시 메인 스레드에서 마킹을 다시 확인하고 Concurrent 하게 헬퍼 스레드에서 Sweep을 수행한다.

3\. 일시 중지 기간에서 **Marking 완료와 헬퍼 스레드에서 Sweep 수행을 시작 후**  메인 스레드에서 Parallel 압축 및 포인터 업데이트를 수행한다.

4.  압축과 포인터 업데이트 끝나면 일시 중지기간은 끝나며 메인 스레드에는 JS가 동작한다. 이때 **Sweep 과정이 길면 Concurrent 하게 움직인다**. 즉 Sweep을 처리하는 헬퍼 스레드랑 JS 코드를 실행하는 메인스레드가 동시성을 가진다.

**중요한 점: Helper T1, T2 그리고 Main 스레드의 미묘한 시작하는 타이밍을 주의 깊게 확인해야 합니다. 서로 동작하는 타이밍이 다릅니다.**

![](https://github.com/seunghyeokleeme/js-note/blob/main/v8/timing.jpeg?raw=true)

### **결론**

v8에서 제공하는 GC의 종류와 동작원리에 대해서 알아보는 시간을 가졌습니다. 해당 글을 작성할 때 어느 정도 깊이 있게 작성해야 할지 많은 고민이 있었고 그리고 어느 정도 분량까지 작성해야 할지 고민이었습니다. 다음 GC 포스팅 때는 이번 GC에서 깊게 다루지 못한 알고리즘과 GC가 동작하는 타이밍과 실제 GC 개선으로 인한 처리속도의 그래프에 대해서 이야기하도록 하겠습니다.

그리고 생각보다 Javascript 엔진에 따라 구현방식이 달라서 처음에는 Apple에서 제공하는 JavaScriptCore 랑 v8에 이야기하는 Young Generation의 원리 및 용어에 대해서 혼동이 왔습니다.

그리고 마지막으로 제가 **Old Generation보다 Young Generation를 집중적으로 이야기한 것 같습니다. Old Generation에도 두 가지 영역으로 나눠지는데 이 부분은 따로 포스팅을 하겠습니다.  [https://github.com/v8/v8/blob/main/src/heap/spaces.h#L41](https://github.com/v8/v8/blob/main/src/heap/spaces.h#L41)**

그리고 JS에 대한 개념이 부족해서 앞으로도 JS 관련 글 위주로 작성하겠습니다. 첫 포스팅이라 많이 부족하니 피드백 부탁드립니다.

### **참고**

- [https://v8.dev/blog/concurrent-marking](https://v8.dev/blog/concurrent-marking)[https://v8.dev/blog/trash-talk](https://v8.dev/blog/trash-talk)
- [https://v8.dev/blog/concurrent-marking](https://v8.dev/blog/concurrent-marking)
- [https://github.com/v8/v8/blob/main/src/heap/spaces.h#L41](https://github.com/v8/v8/blob/main/src/heap/spaces.h#L41)
- [Concurrent marking in V8](https://v8.dev/blog/concurrent-marking)