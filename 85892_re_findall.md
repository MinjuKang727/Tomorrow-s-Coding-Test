# 🐍 파이썬 정규표현식(`re` 모듈) 완벽 가이드

이 문서는 파이썬의 표준 라이브러리인 **`re` 모듈**을 이용한 정규표현식 사용법을 기본 규칙부터 구체적인 예시, 그리고 핵심 메서드인 `findall()`의 활용법까지 상세히 정리한 가이드입니다.

---

## 1. 정규표현식이란?
특정한 규칙을 가진 문자열의 집합을 표현하기 위해 사용하는 형식 언어입니다. 파이썬에서는 별도의 설치 없이 표준 내장 모듈인 `re`를 불러와 사용할 수 있습니다.

---

## 2. 정규표현식 핵심 규칙 및 메타문자

정규표현식에서 특수한 의미를 갖는 **메타 문자(Meta Characters)**와 **특수 시퀀스**의 규칙 및 사용 예시입니다.

### ① 기본 메타 문자
| 메타 문자 | 의미 | 구체적 사용 예시 및 설명 |
| :---: | :--- | :--- |
| **`.`** | 줄바꿈(`\n`)을 제외한 임의의 1개 문자 | `r'a.c'` $\rightarrow$ `abc`, `a3c`, `a c` 매치 (`@` 불가) |
| **`^`** | 문자열의 시작 | `r'^Hello'` $\rightarrow$ `Hello world` (문자열 맨 앞일 때만 매치) |
| **`$`** | 문자열의 끝 | `r'end$'` $\rightarrow$ `This is the end` (문자열 맨 끝일 때만 매치) |
| **`*`** | 직전 패턴이 0회 이상 반복 | `r'ab*c'` $\rightarrow$ `ac`, `abc`, `abbbc` 매치 |
| **`+`** | 직전 패턴이 1회 이상 반복 | `r'ab+c'` $\rightarrow$ `abc`, `abbbc` 매치 (`ac`는 불가능) |
| **`?`** | 직전 패턴이 0회 또는 1회 반복 | `r'colou?r'` $\rightarrow$ `color`, `colour` 둘 다 매치 |
| **`{m,n}`** | 직전 패턴이 $m$회 이상 $n$회 이하 반복 | `r'a{2,3}b'` $\rightarrow$ `aab`, `aaab` 매치 |
| **`[...]`** | 문자 집합 중 하나 (OR 조건) | `r'[abc]'` $\rightarrow$ `a`, `b`, `c` 중 문자 하나와 매치 |
| **`[^...]`** | 지정된 문자를 제외한 나머지 문자 | `r'[^0-9]'` $\rightarrow$ 숫자가 아닌 문자 매치 |
| **`\|`** | OR (또는) 조건 | `r'apple\|banana'` $\rightarrow$ `apple` 또는 `banana` 매치 |
| **`(...)`** | 그룹핑 (단위 묶기 및 캡처) | `r'(ab)+'` $\rightarrow$ `abab`, `ababab` 매치 |

### ② 특수 시퀀스 (단축 문자)
| 특수 시퀀스 | 의미 (기본 유니코드 기준) | 구체적 사용 예시 |
| :---: | :--- | :--- |
| **`\d`** | 10진수 숫자 (`[0-9]`와 유사) | `r'\d+'` $\rightarrow$ `12345` 등의 숫자 덩어리 매치 |
| **`\D`** | 숫자가 아닌 문자 (`[^\d]`) | `r'\D+'` $\rightarrow$ 문자와 특수문자 매치 |
| **`\s`** | 공백 문자 (띄어쓰기, 탭, 줄바꿈 등) | `r'hello\sworld'` $\rightarrow$ `hello world` 매치 |
| **`\S`** | 공백이 아닌 문자 | `r'\S+'` $\rightarrow$ 공백이 포함되지 않은 연속된 문자열 |
| **`\w`** | 문자/숫자/언더바 (`[a-zA-Z0-9_]` 등) | `r'\w+'` $\rightarrow$ 변수명이나 단어 형태 매치 |
| **`\W`** | `\w`에 해당하지 않는 문자 (특수문자 등) | `r'\W+'` $\rightarrow$ `!@#$`, 공백 등 매치 |

---

## 3. 파이썬 `re` 모듈 소개 및 사용 구조

파이썬에서 정규표현식을 사용할 때는 기본적으로 내장된 `re` 모듈을 임포트해야 합니다. 사용할 때는 다음 두 가지 방식 중 선택할 수 있습니다.

1. **함수로 직접 호출**: `re.findall(pattern, string)` 형태로 모듈 함수를 직접 실행합니다.
2. **패턴 컴파일 후 호출**: `p = re.compile(pattern)` 후 `p.findall(string)` 형태로 실행합니다. *(동일한 패턴을 반복해서 사용할 때 성능 면에서 유리합니다.)*

```python
import re

# 방법 1: 함수로 직접 사용
result = re.findall(r'[a-z]+', 'abc@xyz.com')

# 방법 2: 컴파일 후 패턴 오브젝트 메서드로 사용
p = re.compile(r'[a-z]+')
result = p.findall('abc@xyz.com')
```

---

## 4. `re` 모듈 주요 메서드 소개

### ① `match()` : 문자열의 **처음**부터 패턴이 일치하는지 확인
```python
import re

text = "Python is fun"
result = re.match(r'Python', text)
print(result)  # <re.Match object; span=(0, 6), match='Python'>
```

### ② `search()` : 문자열 **전체**를 검색해 **처음** 매치되는 부분 반환
```python
text = "I love Python and Python"
result = re.search(r'Python', text)
print(result)  # <re.Match object; span=(7, 13), match='Python'> (첫 번째 발견 지점)
```

### ③ `fullmatch()` : 문자열 **전체**가 패턴과 완벽히 일치하는지 확인
```python
text = "12345"
result = re.fullmatch(r'\d+', text)
print(result)  # <re.Match object; span=(0, 5), match='12345'>
```

---

## 5. ⭐️ 집중 탐구: `findall()` 메서드 완벽 활용법

**`findall()`**은 패턴에 매치되는 **모든 부분 문자열을 파이썬 리스트(List)로 반환**하는 메서드입니다. (매치 오브젝트가 아니라 **문자열 형태**로 결과가 담기는 점을 유의해야 합니다.)

### 예시 1: 문자열 내 모든 이메일 주소 추출하기
```python
import re

text = "연락처: test1@naver.com, admin@google.co.kr, guest@daum.net 입니다."
# 이메일 패턴 정의
pattern = r'[a-zA-Z0-9._+]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+'

emails = re.findall(pattern, text)
print(emails)
# 출력: ['test1@naver.com', 'admin@google.co.kr', 'guest@daum.net']
print(f"찾은 이메일 개수: {len(emails)}")  # 출력: 찾은 이메일 개수: 3
```

### 예시 2: 괄호 `()`를 활용한 그룹핑 추출
정규식 내부에 괄호 ``를 사용하면, 매치된 전체 결과가 아니라 **괄호 안의 그룹별 문자열이 튜플(Tuple) 리스트 형태**로 반환됩니다.
```python
text = "아이디: user1(서울), user2(부산), user3(대구)"

# 이름과 지역을 각각 괄호로 그룹화
pattern = r'([a-zA-Z0-9]+)\(([\가-힣]+)\)'
result = re.findall(pattern, text)

print(result)
# 출력: [('user1', '서울'), ('user2', '부산'), ('user3', '대구')]
```

---

## 6. 기타 유용한 메서드

* **`finditer()`**: 매치되는 모든 부분을 **이터레이터(Iterator)** 형태의 매치 오브젝트로 반환합니다. (인덱스나 위치 정보를 함께 얻고 싶을 때 사용)
* **`sub()`**: 매치된 패턴을 다른 문자열로 치환합니다.
  ```python
  text = "My phone number is 010-1234-5678"
  masked = re.sub(r'\d{4}-\d{4}', '####-####', text)
  print(masked)  # 출력: My phone number is 010-####-####
  ```
* **`split()`**: 정규표현식 패턴을 기준으로 문자열을 잘라 리스트로 반환합니다.
  ```python
  text = "apple,banana;orange/grape"
  fruits = re.split(r'[,;/]', text)
  print(fruits)  # 출력: ['apple', 'banana', 'orange', 'grape']
  ```