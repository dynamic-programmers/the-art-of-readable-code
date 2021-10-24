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

1. 설명 변수를 만든다.(단, 아래의 "불필요한 임시 변수" 항목을 기억하라)
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

1. 흐름 제어 변수 제거하기
```js
boolean done = false;
while (/* 조건 */ && !done) {
    ...
    if (...) {
        done = true
        continue;
    }
}
```
done과 같은 변수를 우리는 흐름 제어 변수라고 부른다. 이러한 흐름 제어 변수는 프로그램의 구조를 잘 설게하면 제거할 수 있다.


```js
while (/*조건*/) {
    ...
    if (...) {
        break;
    }
}
```
위 예제에서 만약 중첩된 여러 루프 때문에 break가 추가로 필요하다면 루프안에서 반복되는 코드를 새로운 함수로 만들면 된다.(루프 안에 있는 코드나 루프 전체를 함수로 만들 수 있다.)

1. 변수의 범위를 좁혀라
전역 변수를 피하라. 전역 변수의 이름과 지역 변수의 이름이 중복되어 네임스페이스가 더러워질 수도 있고, 어떤 코드가 지역 변수를 변경할 때 실수로 전역 변수를 변경하거나 혹은 그 반대의 경우가 일어 날 수도 있으므로 타당하다.

전역 변수뿐만 아니라 모든 변수의 범위를 좁히는 일은 언제나 좋다. 왜냐면 코드를 읽는 사람이 한꺼번에 생각해야 하는 변수 수를 줄여주기 때문이다.

1. 많은 메소드를 정적(static)으로 만들어서 클래스 멤버 접근을 제한해라. "이 코드는 저 변수들로부터 독립적"이라는 사실을 알려주는 매우 좋은 방법이다.

1. 커다란 클래스를 여러 작은 클래스로 나눠라. 이 방법은 작은 클래스들이 서로 독립적일 때 유용하다. 만약 클래스를 두 개의 작은 클래스로 나누었는데 서로의 멤버를 참조한다면, 실제로 성취한 일을 아무 것도 없게 된다.

커다란 파일을 작은 여러 개의 파일로 나누거나, 커다란 함수를 여러 개의 작은 함수로 나눌 때도 마찬가지다.

예:
```c++
PaymentInfo* info = database.ReadPaymentInfo();
if (info) {
    ...
}
// 아래와 같이 바꾼다.
if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
}
```

```js
// 함수 하나에만 사용되는 전역 변수가 있을 때 
a = false
var func = () => {
    if (a) {
        return;
    }
    ...
    a = true;
}

// 클로저를 사용하면 아래와 같이 바꿀 수 있다.
var func = (function () {
    a = false;
    return function() {
        if (a) {
            return;
        }
        a = true
    }
})();
```
1. 변수의 정의를 실제로 사용하기 바로 직전 위치로 옮기는 게 좋다. 왜냐면 시선이 왔다갔다 하므로
```py
def f():
    a=1
    b=1
    c=1

    print(a+b)

# 아래와 같이 바꾼다.

def f():
    c=1

    a=1
    b=1
    print(a+b)
```

1. 변수값이 달라지는 곳이 많은수록 현재값을 추측하기 더 여려워진다. => 상수를 쓰자.

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

## 10장 상관없는 하위문제 추출하기
큰 흐름과 관계가 적은 하위문제를 적극적으로 발견해서 추출하라.

1. 스스로에게 질문하라 "상위수준에서 본 이 코드의 목적은 무엇인가?"
2. 코드의 모든 줄에 질문을 던져라. "이 코드의 목적은? 혹은 목적을 위해 필요하지만 목적 자체와 직접적으로 상관없는 하위문제를 해결하는가?"
3. 만약 상당히 원래의 목적과 직접적으로 관련되지 않은 하위문제를 해결하는 코드 분량이 많으면, 이를 추출해서 별도의 함수로 만든다.

예제 코드
```js
// 다음 코드의 상위수준 목적은 주어진 점과 가장 가까운 장소를 찾는 것이다.
var findClosestLocation = function (lat, lng, array) {
    var closest;
    var closest_dist = Number.MAX_VALUE;
    for (var i = 0; i < array.length; i+=1) {
        // 두 점 모두를 라디안으로 변환한다.
        var lat_rad = radians(lat);
        ...

        // '코사인의 특별법칙' 공식을 사용한다. 
        var dist = Math.acos(Math.sin(...) ... )

        if (dist < closest_dist) {
            closest = array[i];
            closest_dist = dist;
        }
    }
    return closest;
}

// 루프의 내부에 있는 코드는 대부분 주요 목적과 직접 상관없는 하위문제를 다룬다.
// 두 개의 위도/경도 점 사이의 거리 계산 로직을 분리한다.

var findClosestLocation = function (lat, lng, array) {
    var closest;
    var closest_dist = Number.MAX_VALUE;
    for (var i = 0; i < array.length; i+=1) {
        var dist = spherical_distance(lat, lng, array[i].latitude, array[i].longtitude);

        if (dist < closest_dist) {
            closest = array[i];
            closest_dist = dist;
        }
    }
    return closest;
}

// 코드를 읽는 사람은 밀도 높은 기하 공식에 방해받지 않고 상위수준의 목적에 집중할 수 있으니 전반적으로 코드의 가독성이 높아졌다.

// 추가적으로 spherical_distance()는 독립적인 테스트와 재사용에 더 용이하다.
```

4. 단, 지나친 수준으로 코드를 더 잘게 쪼갠다면 오히려 가독성을 해칠수 있다.

## 11장 코드 재작성하기
1. 한 번에 하나의 작업만 수행하게 코드를 구성해야 한다.
    - 한 번에 여러 가지 일을 수행하는 코드는 이해하기 어렵다.
    - 코드가 수행하는 모든 작업(객체가 정상적으로 존재하는지 확인, 트리 안에 있는 모든 노드 방문, 등)을 나열한다. 
    - 이러한 작업을 분리하여 서로 다른 함수 혹은 논리적으로 구분되는 영역에 놓을 수 있는 코드를 작성하라

```js
// 추천을 누르면 현재 점수 +1
// 반대는 현재 점수 -1을 진행하는 코드
// 만약 반대를 누른 상태에서 추천을 누를 때 또는 그 반대의 경우도 고려한다.
var vote_changed = function(old_vote, new_vote) {
    var score = get_score();
    if (new_vote !== old_vote) {
        if (new_vote === 'Up') {
            score += (old_vote === 'Down' ? 2:1)
        } else if (new_vote === 'Down') {
            score -= (old_vote === 'Up' ? 2:1) 
        } else if (new_vote === '') {
            score += (old_vote === 'Up' ? -1:1)
        }
    }
    set_score(score)
}
// 위의 코드는 2가지의 일을한다. 
// 1. old_vote와 new_vote가 수치값으로 해석 된다.
// 2. 점수가 변경된다.
// 다음의 코드로 변경될 수 있다.
var vote_value = function(vote) {
    if (vote === 'Up') {
        return +1
    }
    if (vote === 'Down') {
        return -1
    }
    return 0
}

var vote_changed = function(old_vote, new_vote) {
    var score = get_score()

    score -= vote_value(old_vote) // 이전 값을 제거한다.
    score += vote_value(new_vote) // 새 값을 더한다.
    set_score(score)
}
```

```js
// 객체에서 값을 추출한다.
var func1 = () => {
    const = { LocalityName, SubAdministrativeAreaName, AdministrativeAreaName, CountryName } = location_info
    let city = 'Middle-of-Nowhere'

    if (LocalityName) {
        city = LocalityName
    } else if (SubAdministrativeAreaName) {
        city = SubAdministrativeAreaName
    } else if (AdministrativeAreaName) {
        city = AdministrativeAreaName
    }

    if (CountryName) {
        city += `, ${CountryName}`
    } else {
        city += `, Planet Earth`
    }

    return city
}
/* 위의 함수가 하는 일들 
1. location_info 딕셔너리에서 값들을 읽는다.
2. '도시'의 값을 설정하기 위해서 정해진 선호도 순으로 값을 읽는다. 아무런 값도 찾을 수 없으면 "아무 곳도 아닌 곳"이라는 기본값을 설정한다.
3. '나라'값을 설정한다. 값이 없으면 기본값인 '지구'로 설정한다.
4. city를 변경한다.
*/

var func1 = () => {
    // 1. location_info 딕셔너리에서 값들을 읽는다.
    const = { LocalityName: town, SubAdministrativeAreaName: city, AdministrativeAreaName: state, CountryName: country } = location_info

    // 기본값부터 시작하라.
    var second_half = "Planer Earth";
    if (country) {
        second_half = country;
    }
    if (state && country === "USA") {
        second_half = state;
    }

    // 탑 다운 형식으로 바꾼다.
    var first_half = "Middle-of-Nowhere";
    if (state && country !== "USA") {
        first_half = state;
    }
    if (city) {
        first_half = city;
    }
    if (town) {
        first_half = town;
    }

    return first_half + ", " + second_half;
}

// 위에 코드 또한 두 가지 작업이 동시에 이뤄지고 있다.
// 1. 변수의 리스트를 하나씩 읽어서, 존재하는 값 중 가장 선호되는 값을 선택한다.
// 2. 나라가 'USA'인지 아닌지에 따라서 다른 리스트를 사용한다.

var first_half, second_half;

if (country === 'USA') {
    first_half = town || city || 'Middle-of-Nowhere';
    second_half = state || 'USA';
} else {
    first_half = town || city || state || 'Middle-of-Nowhere';
    second_half = country || 'Planer Earth';
}

return first_half + ", " + second_half;
```