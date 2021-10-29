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
1. 꼭 그래야 하는 이유가 없다면 보편적인 변수(i, j, temp, ary...) 사용 피하기(다만, 경우에 따라서 보편적인 변수 명이 오히려 좋을 때도 있음)
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

## 12장 생각을 코드로 만들기
1. 도움을 구하기 앞서 인형 또는 스스로에게 설명하라. 이 방법을 통해 해결책을 찾는 것에 도움이 될 수도 있다.

1. 논리를 명확하게 설명하기(코드를 작성하기 전 말로 간결하게 정리해본다.)

예제1.

```js
var is_admin = is_admin_request();
if (document) {
    if (!is_admin && (document['username'] != SESSION['username'])) {
        return not_authorized()
    } 
} else {
    if (!is_admin) {
        return not_authorized()
    }
}
/*
위의 논리를 정리하면 사용이 허가되는 방법은 두 경우다.
1. 관리자다.
2. 만약 문서가 있다면 현재 문서를 소유하고 있다. 그렇지 않으면 허가되지 않는다.

(!is_admin && (document['username'] != SESSION['username'])) 이 줄을 간단하게 하기위해 드모르간의 법칙을 사용하면

is_admin || document['username'] == SESSION['username']
*/

if (is_admin_request()) {
    // 허가
} else if (document && document['username'] == SESSION['username']) {
    // 허가
} else {
    return not_authorized()
}

// 비어 있는 본문 두 개를 포함하고 있어 조금 이상하지만 더 간결해졌다.
```

예제2.
![db_table](./db_table.jpg)

```py
# 세 개의 테이블을 SQL의 JOIN 연산처럼 결합하는 프로그램을 만든다.
# 1. 모든 행들이 time을 기준으로 순서대로 정렬되어 있다.
# 2. 하지만 어떤 행들은 아예 존재하지 않는다.
# 3. 존재하지 않는 행들을 무시하면서 세 개의 테이블에서 time 값이 동일한 행을 연결한다.
# 4. time은 primary key이다. 

def PrintStockTransactions():
    stock_iter = db_read('SELECT time, ticker_symbol FROM ...")
    price_iter = ...
    num_shares_iter = ...

    # 테이블의 모든 행을 동시에 순차적으로 반복한다.
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = prrice_iter.time
        num_shares_time = num_shares_iter.time

        # 만약 세 개의 행이 같은 time을 갖지 않으면 가장 오래된 행은 건너뛴다.
        if stock_time != price_time or stock_time != num_shares_time:
            # stock_time이 젤 낮다면 다음 행 값을 가져온다.
            if stock_time <= price_time and stock_time <= num_shares_time:
                stock_iter.NextRow()
            # price_time도 위와 마찬가지
            if price_time ...
                price_iter.NextRow()
            # 마찬가지...
            if num_shares_time ...
                num_sharees_iter.NextRow()
            else
                assert False # 찾기 불가능하다.
            continue
        assert stock_time == price_time == num_shares_time
    
    # 일치된 행을 출력한다.
    print(...)

'''
뒤로 한걸음 물러나서 쉬운 말로 묘사해보자.
1. 세 개 반복자를 병렬적으로 동시에 읽는다.
2. 어느 행의 time이 일치하지 않으면, 앞으로 하나 더 나아가서 일치하게 한다.
3. 일치된 행을 출력하고, 다시 앞으로 나아간다.
4. 일치되는 행이 더 이상 없을 때까지 이를 반복한다.

2번째 부분을 AdvanceToMatchingTime()이라는 함수로 분리해본다.
'''
def PrintStockTransactions():
    stock_iter = db_read('SELECT time, ticker_symbol FROM ...")
    price_iter = ...
    num_shares_iter = ...

    # 테이블의 모든 행을 동시에 순차적으로 반복한다.
    while True:
        time = AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter)
        if time is None:
            return
    
    # 일치된 행을 출력한다.
    print(...)

def AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter):
    while stock_iter and price_iter and num_shares_iter:
        stock_time = stock_iter.time
        price_time = price_iter.time
        num_shares_time = num_shares_iter.time

        if stock_time != price_time or stock_time != num_shares_time:
            # stock_time이 젤 낮다면 다음 행 값을 가져온다.
            if stock_time <= price_time and stock_time <= num_shares_time:
                stock_iter.NextRow()
            # price_time도 위와 마찬가지
            if price_time ...
                price_iter.NextRow()
            # 마찬가지...
            if num_shares_time ...
                num_sharees_iter.NextRow()
            else
                assert False # 찾기 불가능하다.
            continue
        assert stock_time == price_time == num_shares_time
        return stock_time

'''
AdvanceToMatchingTime의 작업을 말로 풀어보면
1. 각 테이블의 현재 행에서 time을 확인한다. 값이 모두 같으면 작업이 완료된 것이다. 그렇지 않으면 값이 '뒤처진' 행을 한 칸 전진시킨다.
2. 행이 모두 동일한 time을 가질 때까지 혹은 반복자 중의 하나가 끝에 이를 때까지 작업을 반복한다.

한 가지 주목할 부분은 이 셜명이 stock_iter나 우리가 해결하려는 문제와 관련된 어떠한 사항도 언급하지 않는다는 점이다. 따라서 변수명을 더 간단하고 일반적이게 바꿀 수 있다.
'''
def AdvanceToMatchingTime(row_iter1, row_iter2, row_iter3):
    while row_iter1 and row_iter2 and row_iter3:
        t1 = row_iter1.time
        t2 = row_iter1.time
        t3 = row_iter1.time

        if t1 == t2 == t3:
            return t1

        tmax = max(t1, t2, t3)

        # 어떤 행이 뒤쳐져 있으면 한 칸 앞으로 전진시킨다.
        if t1 < tmax: row_iter1.NextRow()
        if t2 < tmax: row_iter2.NextRow()
        if t3 < tmax: row_iter3.NextRow()
    return None # 일치되는 행이 없다.
```

1. 라이브러리를 적절하게 활용하면 간결한 코드를 작성할 수 있다.

## 13장 코드분량 줄이기
1. 요구사항에 질문을 던지고 질문을 잘게 나누어 분석하라(작은 요구사항에 큰 기능을 도입하려고 애쓰지 마라, 시간이 많이 필요하니까.)
    - 프로그램이 반드시 빠르게 동작하고 100% 정확하고 모든 예외를 고려해야하는 것은 아니다. 주어진 요구사항을 잘 분석하면 적은 코드로 간단하게 문제를 정의할 수 있다.
```
상점위치 추적기

사용자의 경도/위도가 주어지면, 그 장소에서 가장 가까운 상점을 찾는다. 다음 상황을 처리해야 한다.


1. 장소가 날짜 변경선의 어느 한 쪽에 있을 때
2. 장소가 북극이나 남극에 가까울 때
3. 1마일마다 경도의 값이 달라지므로 지구의 곡률에 따라 조정하기

하지만 우리가 만들려는 애플리케이션이 텍사스 주에 있는 가게 30곳만 대상으로 한다면...

텍사스에 있는 가게 중 대략적으로 가장 가까운 가게를 찾으면 된다.
```

1. 코드베이스를 작게 유지하기
    - 규모가 커질 수록 전체 프로젝트를 이해하기는 어려워진다.
    -   일반적인 유틸리티를 많이 생성하여 중복된 코드를 제거하라(함수 재사용성 증가 시키기)
    -   사용하지 않는 코드, 필요 없는 기능 제거 
    - 프로젝트가 서로 분절된 하위프로젝트로 구성되게 하라(라이브러리로 분리, 프로젝트 나누기)
    - 항상 코드베이스의 무게를 의식하고 날렵하게 유지시켜라
1. 라이브러리와 친숙해져라
    - 완숙한 라이브러리 안에 있는 코드는 한 줄 한 줄 엄격한 프로세스를 거쳐 살아남았다.
    - 코드가 줄어든다.
1. 코딩 대신 명령어 활용하기

## 14 테스트와 가독성
1. 읽거나 유지보수하기 쉽게 테스트를 만들어라
    - 테스트 코드가 크고 두렵게 느껴진다면...
    - 코드를 수정하는 일이 두려워진다.
    - 새로운 코드를 작성하면 그에 따르는 새로운 테스트를 작성하지 않는다.
    - [내 의견] 아래의 예제를 읽고나면 CheckScoresBeforeAfter 에서부터는 헬퍼함수가 길어져 오버엔지니어링이 아닌가 하는 생각도 든다. 하지만 대부분의 라이브러리에서 간단한 인터페이스에 비해 정작 내부 로직은 복잡하다는 점, 아래의 "'완벽한' 입력을 사용하기 보다는 작은 테스트를 여러 개 사용"에서와 같이 입력이 많은 상황에서 간결하게 사용할 수 있다는 점이 좋기 때문에 참고하는 점이 좋지 않을까 하는 생각.
```c++
// 'docs'를 내림차순으로 정렬하고 점수가 0보다 작은 문서를 제거한다.
// 다음의 코드를 테스트한다.
void SortAndFilterDocs(vector<ScoredDocument>* docs)

// 수정할 테스트 케이스
void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url="http://example.com";
    docs[0].score=-5.0;
    docs[1].url="http://example.com";
    docs[1].score=1;
    docs[2].url="http://example.com";
    docs[2].score=4;
    docs[3].url="http://example.com";
    docs[3].score=-99998.7;
    docs[4].url="http://example.com";
    docs[4].score=3.0;

    SortAndFilterDocs(&docs);

    assert(docs.size() == 3)
    assert(docs[0].score == 4)
    assert(docs[1].score == 3.0)
    assert(docs[2].score == 1)
}

// 다음과 같은 수정할 점이 있다.
// 1. 작업을 분리한다.(덜 중요한 사항은 숨겨 내용이 더 눈에 띄게 한다.)
// 값을 설정하는 부분을 헬퍼 함수로 만든다.
// 2. 새로운 테스트를 추가하기 쉽지 않다. 복붙은 코드가 더 길고 중복되게 한다.
// 3. 테스트 실패 메시지가 별로 도움이 되지 않는다. 테스트에 실패하면 단지 Assertion failed: docs.size() == 3과 같은 내용을 출력한다. 테스트 실패에 사용된 값을 볼수 있어야 한다.
// 4. 모든 것을 한꺼번에 테스트하려고 한다. 음수를 필터링하는 기능과 수를 정렬하는 기능을 동시에 테스트한다. 여러개의 테스트로 나누면 읽기 더 쉬울 것이다.
// 5. 테스트 입력이 간단하지 않다. -99998.7과 같은 입력은 중요하지 않음에도 시선을 끈다. 더 간단한 음수값을 사용해도 충분하다.
// 6. 테스트 입력값들이 코드 구석구석을 확인하지 않는다. 예를 들어 0인 경우는 다루지 않는다.
// 7. 빈 값, 매우 큰 값, 중복된 값과 같이 비정상적인 값을 테스트하지 않는다.
// 8. Test1()이라는 이름은 아무 의미 없다. 테스트되는 함수나 상황을 설명해야 한다.

`void MakeScoredDoc(ScoredDocument* sd, double score, string url) {
    sd->score = score;
    sd->url = url;
}

void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    MakeScoredDoc(&docs[0], -5.0, "http://example.com")
    MakeScoredDoc(&docs[1], 1, "http://example.com")
    MakeScoredDoc(&docs[2], 4, "http://example.com")
    MakeScoredDoc(&docs[3], -99998.7, "http://example.com")
}

// 더 리팩토링 할 수 있다. docs.resize(5), &docs[n], url 부분이다.
void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
}

// 더 깔끔해졌지만, 점수가 매겨진 문서 중에 새로운 테스트를 추가해야 한다면, 여전히 코드를 복붙하는 과정을 되풀이해야 한다.
void Test1() {
    vector<ScoredDocument> docs;
    AddScoredDoc(docs, -5.0)
    AddScoredDoc(docs, 1)
    AddScoredDoc(docs, -4)
    AddScoredDoc(docs, -99998.7)

    //...
}

/*
다음과 같이 만들어 본다.
[-5, 1, 4, -99998.7, 3]과 같은 점수를 가지는 문서 리스트가 있다.
SortAndFilterDocs()를 호출한 다음에 문서는 [4,3,1]이라는 순서대로 비교해야한다.
다음과 같이 구현한다.
*/

void Test1() {
    CheckScoresBeforeAfter("-5, 1, 4, -99998.7", "4, 3, 1") 
}

void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
}

vector<ScoredDocument> ScoredDocsFromString(string scores) {
    vector<ScoredDocument> docs;

    replace(scores.begin(), scores.end(), ',', ' ');

    // 빈 칸으로 구분된 숫자로 이루어진 문자열로부터 'docs'를 채운다.
    istringstream stream(scores);
    double score;
    while (stream >> scores) {
        AddScoredDoc(docs, score);
    }

    return docs;
}

string ScoredDocsToString(vector<ScoredDocument> docs) {
    ostringstream stream;
    for (int i = 0; i < docs.size(); i++) {
        if (i > 0) stream << ", ";
        stream << docs[i].score;
    }

    return stream.str()
}

void CheckScoresBeforeAfter(string input, string expected_output) {
    vector<ScoredDocument> docs = ScoredDocsFromString(input);

    SortAndFilterDocs(&docs);

    string output = ScoredDocsToString(docs);
    assert(output == expected_output)
}
```
1. 읽기 편한 메시지 만들기
더 정교한 버전의 테스트 라이브러리를 사용하면 더 자세한 내용을 담은 메시지를 볼 수 있다.(테스트 실패에 사용된 값을 볼수 있는)

1. 좋은 테스트 입력값의 선택
    - 좋은 입력값은 코드를 구석구석 철저하게 테스트한다. 하지만 간단해서 읽기도 쉬워야 한다.
```c++
// 이 테스트는 간단하지만 '음수인 점수를 필터링하는' SortAndFilterDocs()의 기능을 테스트하지 않는다.
CheckScoresBeforeAfter("1,2,3", "3,2,1") 

// 이러한 값은 필요 이상으로 복잡하다. 심지어 철저하게 코드를 테스트하지도 않는다.
CheckScoresBeforeAfter("12301, -1231231, 23232, 34343") 

// -99998.7는 단지 '임의의 음수'를 의미할 뿐이므로 그냥 -1로 적어도 상관없다. 만약 -99998.7이 '매우큰 음수'를 의미한다면 -1e100처럼 표현하여 더 의미가 뚜렷해 보이게 할 수 있다. 
CheckScoresBeforeAfter("-5, 1, 4, -99998.7", "4, 3, 1") 

// 커다란 입력값으로 테스트, 버퍼 오버런이나 다른 예상치 못한 종류의 버그를 드러낼 수도 있다.
// 다만, 지나치게 크고 무시무시해 보이며, 코드에 효과적인 스트레스-테스트를 수행하지도 않는다.
// 이 방법보다는 대량의 입력을 프로그램으로 생성하는 방법이 더 효율적이다.
CheckScoresBeforeAfter("1, 2, 3, 4, 5, ... , n") 

// Good, 참고: 내림차순으로 정렬하고 음수는 제거한다.
CheckScoresBeforeAfter("1, 2, -1, 3", "3, 2, 1") 
```

1. '완벽한' 입력을 사용하기 보다는 작은 테스트를 여러 개 사용하는 방식이 더 쉽고 효과적이다.
```c++
CheckScoresBeforeAfter("1, 2, 3", "3, 2, 1") // 기본적인 정렬
CheckScoresBeforeAfter("0, -0.1, -10", "0") // 0보다 작은 값을 모두 제거
CheckScoresBeforeAfter("1, -2, 1, -2", "1, 1") // 중복은 문제되지 않는다.
CheckScoresBeforeAfter("", "") // 빈 입력도 OK    
```
1. 테스트 함수에 이름 붙이기
    - 다음과 같은 사항을 빠르게 파악할 수 있다면 큰 도움이 된다.
    - 테스트되는 클래스, 테스트되는 함수, 테스트되는 상황이나 버그

1. 테스트에 친숙한 개발
    - 테스트를 고려한 코드 작성은 더 나은 코드를 작성하게 됨을 의미한다.

![테스트하기 어려운 코드의 특징과 테스트하기 좋은 특징](./test_table.jpg)

1. 지나친 테스트를 주의하라
    - 테스트를 가능케 하려고 실제 코드의 가독성을 희생시킨다. 실제 코드에 지저분한 코드를 넣어야 한다면, 뭔가 잘못된 것이다.
    - 100% 코드 테스트에 집착하는 일, 90%를 테스트하는 노력이 종종 나머지 10%를 테스트하는 비용보다 적은 노력이 들기도 한다. 또 그 10%는 버그로 인한 비용이 별로 높지 않기 때문에 굳이 테스트할 필요가 없을 수도 있다.
    - 사실, 코드를 100% 테스트하는 일은 일어나지 않는다. 테스트되지 않은 버그가 있을 수도, 기능, 요구사항이 달라졌을 수도 있다.
    - 버그가 야기하는 비용이 어느 정도인지에 따라서, 테스트 코드가 의미가 있을 수도 있고 없을수도 있다. 프로토타입에는 테스트 코드가 의미 없지만, 우주선 소프트웨어는 의미가 있다.
    - 테스트 코드가 실제 제품 개발에 차질을 빚게 되는 일, 프로그래머들은 자신의 시간이 다른 일에 쓰이는 것이 더 나을수도 있다.
    
