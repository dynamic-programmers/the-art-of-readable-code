## 코드 작성
1. 항상 다른 사람을 위해서 코드를 작성하라

1. 몇 줄 더 쓰는게 오히려 가독성이 좋을 수있다

1. 줄 수를 최소화하는 일보다 다른 사람이 코드 읽는 시간을 최소화하는게 더 중요하다.

1. 단항연산자보다 if문 여러줄 작성

1. 코드는 항상 다른 사람이 보기 편하도록 작성하라

1. 코드를 보기 좋게 작성하라.
    - 주석의 위치, 읽기 쉽도록 줄 바꿈 처리, 의미있는 순서
    - 아래와 같이 여러 그룹으로 분리해 코드 작성
        - ```java
            class Test {
                // 헬퍼 클레스들
                ClassA A = new void ClassA(...);
                ClassB B = new void ClassB(...);
                ClassC C = new void ClassC(...);

                // ~를 위한 계산 작업 수행
                ClassA.calc();
                ClassB.calc();
                ClassC.calc();
            }
            ```
    - 코드 스타일의 일관성 추구
        - ```js
            // (1)
            class Logger {

            }
            // (2)
            class Logger 
            {
                
            }
            // 두 가지 중 어떤 것을 써도 무방하나, 프로젝트의 일관성을 따라가는 것이 더 중요하다.
          ```

## 코드 분리하기

1. 설명 변수를 만든다.
```py
if line.split(':')[0].strip() == 'root'

# 위를 아래로 변경한다.

username = line.split(':')[0].strip()
if username == "root":
```
1. 요약 변수를 사용한다.
```js
if (request.user.id == document.owner_id) { }
if (request.user.id != document.owner_id) { }

// 위를 아래와 같이 바꾼다.

const user_owns_document = request.user.id == document.owner_id

if (user_owns_document) { ... }
if (!user_owns_document) { ... }
```

1. 드모르간의 법칙 사용해 조건문 간단하게 하기
```js
// 드모르간의 법칙
// (A U B)^c = A^c U B^c 

!(a || b || c) = !a && !b && !c
!(a && b && c) = !a || !b || !c

!(a && !b) = !a || b
```

1. 중복되는 코드를 줄인다.
```js
var ans = 10
if (ans >= 10) {
    ans += 1+1 + 2+2 + 3+3
} else {
    ans += 1+1 + 2+2
}

// 위의 코드를 아래와 같이 만든다.

var ans = 10
var a = 1+1
var b = 2+2
var c = 3+3
if (ans >= 10) {
    ans += a+b+c
} else {
    ans += a+b
}
```
1. 불필요한 임시 변수
```js
// 다음의 now 변수는 필요하지 않다 왜냐면
// 1. 복잡한 표현을 잘게 나누지 않는다.
// 2. 명확성에 도움이 되지 않는다. new Date.now()는 그 자체로 명확하다.
// 3. 한 번만 사용되어 중복된 코드를 압축하지 않는다.
// 단, now는 부산물인 경우일 수도 있다.(이전에 여러곳 사용됐지만 지금은 아님)
// now가 중복적으로 사용될 것을 예상하고 이렇게 구현했다.
const now = new Date.now()
time.now = now
```
## 명명법
1. 변수 이름에 정보를 구체적으로 담아라(--run_locally => --extra_logging)
1. 꼭 그래야 하는 이유가 없다면 보편적인 변수 사용 피하기(보편적인 변수 명이 오히려 좋을 때도 있음)
1. 변수명에 세부 정보를 덧붙여라(_ms, _sec, …)
1. class Thread { void stop() } => kill, resume이 더 의미있는 동작이다.
1. 좁은 범위에서는 짧은 이름 괜찮다. (예: m, ans)
1. 긴 이름 사용해도 된다.
1. 약어 사용 자제
1. 불필요한 단어 제거 (ConvetToString() => toString())
1. 여러 의미로 해석될 수 있는 이름보다는 더 구체적인 이름 사용(Filter, 고르는 것인지, 제거하는 것인지?? => select, max_length, 바이트, 문자, 단어 수 중 어떤 것? => max_chars)

1. 경계를 포함하는 한계값 다룰 때 (min, max)
```
min      max
1   ~     10 (최대 10개 까지)
```
1. 경계를 포함하는 범위 (first, last)
```
first    last
🔻       🔻
[a, b, c, d, ""]
```
1. 경계를 포함하고 배제하는 범위(begin, end) 
```
begin       end  (끝을 제외함)
🔻       🔻
[a, b, c, d, e]

예: 오늘까지의 시간을 정한다. 입력 = (begin: 00:00 ~ end: 24:00), 결과 = (00:00 ~ 23:59:59:59.9999)
```
1. 불리언 변수는 의미를 부정하는 용어는 피한다. (disable_ssl=false => use_ssl=true)
    -   is, has, can, should 사용시 더 명확해짐
1. get*()의 함수명을 사용시 간단한 계산만 한다. 오래걸리는 계산은 compute*() 사용 권장

## 주석 쓰는 법
1. 코드를 읽고 빠르게 유추할 수 있는 내용은 주석으로 달지 말라(단, 사람은 주석을 보고 더 빠르게 이해한다. 적절하게 사용하는게 좋을 지도...)
    - 다음의 코드는 가치가 없다.
    - ```js
        // classA를 생성한다.
        const classA = new ClassA()
      ```
1. 함수의 구체적인 로직을 주석으로 작성하는 것이 더 좋다.
1. 냐쁜 이름에 주석을 달지 말고 이름을 고쳐써라
1. 생각을 기록하라(왜 함수를 이와 같이 작성했는지)
1. 코드에 있는 결함을 설명하라(TODO:, XXX:(위험) , HACK:(아름답지 않음), FIXME: 와 같은 주석 사용)
1. 상수에 대한 설명(상수의 의도 주석으로 명시)
1. 다른 사람이 보고 궁금해할 것같은 부분을 주석으로 표현해라
1. 다른 사람들이 빠질 것같은 함정을 경고하라
1. 큰 그림에 대한 주석
1. 요약 주석
1. 주석도 리팩토링하라(짧고 간결하게)
1. 그것, 이것 보다 무엇인지 명확하게 명시하라.
1. 때로는 좋은 예제가 더 명확한 설명이 될 수도 있다.
1. 코드의 의도를 명시하라.(리스트를 역순으로 바꾸는 코드가 있다면 왜 그렇게 하는지 명시)
1. 루프반복자를 쓰기전 목적을 명시한다.


## 읽기 쉬운 제어문 만들기
1. 어순과 일치하는 것이 더 읽기 쉽다.(왼쪽 유동적인 값, 오른쪽 고정적인 값) if(10 <= length) 보다 if(length >= 10) 가 더 읽기 쉽다.
1. 조건문은 긍정을 다루는게 더 잘 읽힌다.
```js
if (true) {

} else { }
```
1. 간단한 것을 먼저 처리하라.(한 화면에서 if와 else 구문을 나타낼 수도 있다.)
1. 더 흥미롭고 확실한 것을 먼저 다루어라.
1. 간단한 조건문은 삼항연산자를 사용하라 그렇지 않다면 삼항연산자가 더 복잡할 수 있으므로 if, else 사용 권장
1. do while루프를 피하라(조건이 아래에 명시되어있어 코드를 두 번 읽기 때문에)
1. 중첩을 최소화하기
```js
if (user_result == SUCCESS) {
    if (permission_result != SUCCESS) {
        reply.WriteErrors("erro reding permission");
        reply.Done()
        return;
    }
    reply.WriteErrors("")
} else {
    reply.WriteErrors(user_result)
}
reply.Done()

// 함수 중간에서 반환하여 중첩을 제거한다.
// 아래와 같이 바꿀 수 있다.
if (user_result != SUCCESS) {
    reply.WriteErros(user_result)
    reply.Done()
    return
}

if (permission_result != SUCCESS) {
    reply.WriteErrors(permission_result)
    reply.Done()
    return
}
reply.WriteErrors("")
reply.Done()

// 루프 내부에 있는 중첩 제거하기
for (int i = 0; i<results.size(); i++) {
    if (results[i] != NULL) {
        non_null_count++
        if (results[i]->name != "") {
            cout << "Considering candidate..." << endl;
        }
    }
}

// 아래와 같이 바꿀 수 있다.
for (int i = 0; i<results.size(); i++) {
    if (results[i] == NULL) continue
    non_null_count++

    if (results[i]->name == "") continue
    cout << "Considering candidate..." << endl;
}
```
1. 실행흐름을 따라올 수 있게 코드를 작성하는 비율을 높인다. 
```
아래의 기능을 사용할 시 흐름을 따라가기 힘들어진다. 이러한 기능들이 차지하는 비율이 너무 높지 않아야한다.

스레딩: 어느 코드가 언제 실행되는지 불분명하다.

시그널/인터럽트 핸들러: 어떤 코드가 어떤 시점에 실행될지 모른다,

예외: 예외처리가 여러 함수 호출을 거치면서 실행될수있다. 

함수 포인터 & 익명함수: 실행 함수가 런타임에 결정되기 때문에 예측하기 어렵다.

가상 메소드: object.virtualMethod()는 알려지지 않은 하위클래스의 코드를 호출할지도 모른다.
```
