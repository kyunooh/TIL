# Swift 둘러보기
스위프트의 Hello world

```swift
println("Hello, world")
```

Swift에서는 이 한줄의 코드가 완전한 프로그램이다. 입/출력이나 문자열을 다루기 위한 함수들 같은 기능들을 사용하기위해 분리된 라이브러리를 불러올 필요가 없다. 전역 범위에 해당하는 코드는 프로그램의 진입점으로 사용되기 때문에 main 함순느 필요하지 않는다. 또한 모든 문장끝에 세미콜론을 쓸 필요도 없다.

## 간단한 값
let 을 사용하면 상수를 만들고 var 를 사용하면 변수를 만들 수 있다. 상수는 컴파일할 때 값을 알 필요가 없다. 그러나 한 번만 값을 할당할 수 있다. 이는 상수에 한 번만 값을 할당한 다음, 해당 상수 이름을 여러곳에 사용할 수 있다는 것을 의미한다.

```swift
var myVariable = 42
Myvariable = 50
let myConstant = 42
```

상수나 변수 값 모두에 할당하고 싶은 값의 타입을 지정할 수 있다. 그러나 항상 타입을 명시해야만 하는 건 아니다. 그러나 한 번만 각을 할당할 수 있다. 이는 상수에 한 번만 각을 할당한 다음, 해당 상수 이름을 여러곳에 사용할 수 있다는 것을 의미한다.
위의 예를 보면, myVariable 이 정수 타입 각으로 초기화되었기 때문에 컴파일러는 정수 타입이라고 추측한다.

만약 초기값이 충분한 정보를 제공하지 못한 경우 특정한 타입을 변수명 뒤에 콜론으로 분리하여 써줘야 한다.
```swift
let inplicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

명시적으로 Float 타입인 상수를 만들고 4라는 값을 할당해보자 

> ``` let explicitFloat: Float = 4 ```


할당된 값은 절대 다른 타입의 값으로 암시적인 변환을 할 수 없다. 만약 다른 타입의 값으로 변화해야 한다면 원하는 형태의 인스턴스로 만들어 타입을 명시해야 한다.

let label = "the width is "
let width = 94
let widthLabel = label + string(width)

마지막 줄에 명시한 String 타입 변환 부분을 제거해보자. 어떤 에러가 발생하는가?

>repl.swift:6:24: error: binary operator '+' cannot be applied to operands of type 'String' and 'Int'

>let widthLabel = label + width

>                 ~~~~~ ^ ~~~~~

>repl.swift:6:24: note: overloads for '+' exist with these partially matching parameter lists: (Int, Int), (String, Stringn), (UnsafeMutablePointer<Memory>, Int), (UnsafePointer<Memory>, Int)

>let widthLabel = label + width

>                       ^

문자열 안에 값들을 쉽게 포함하는 방법도 있다. 괄호 안에 값을 쓰고 괄호 앞에 백슬래시(\)를 쓰면 된다. 예를 들면
```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) piece of fruit."
```
\() 를 이용해 문자열 안에 실수형 계산을 포함하도록 해보고, 인사말 안에 누군가의 이름을 넣어보자.
```swift
let doubleFive = 5.0
let doubleThree = 3.0
let myName = "Jelly"
let resultString = "I am \(myName), and three minus five is \(doubleThree - doubleFive)."
```
