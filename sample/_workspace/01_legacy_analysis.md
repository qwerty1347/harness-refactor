# 레거시 분석 — test.py

## 1. 대상

| | |
|---|---|
| 경로 | `C:\xampp\htdocs\www\domeggook-product-api\test.py` |
| 종류 | 순수 Python 스크립트 (프레임워크 소속 아님) |
| 스택 | Python / 프레임워크 없음 (표준 라이브러리 `dataclasses` 만 사용) |
| 줄 수 | 88 |
| 역할 | 주문·사용자 데이터클래스 3개를 정의하고, 승인 판정 함수 하나와 이를 호출하는 `main()` 을 담은 단일 파일 스크립트 |

## 2. 의존 목록

| 대상 | 종류 | 사용 |
|---|---|---|
| `dataclasses.dataclass` | 표준 라이브러리 | 4곳 — 1줄(import), 4줄, 10줄, 20줄 |
| `dataclasses.field` | 표준 라이브러리 | 2곳 — 1줄(import), 17줄 |

프로젝트 내부 모듈·벤더 의존 없음. 컨테이너 키·전역 헬퍼 없음.

## 3. 관심사 지도

### 3-A. 함수·클래스 설계 — 선언 원문

| 선언 | 원문 (줄) |
|---|---|
| `Item` | `@dataclass` / `name: str` / `price: float` (4줄~7줄) |
| `Order` | `@dataclass` / `amount: float` / `has_discount: bool` / `region: str` / `currency: str` / `type: str  # e.g. "bulk" or "normal"` / `items: list[Item] = field(default_factory=list)` (10줄~17줄) |
| `User` | `@dataclass` / `is_premium: bool` / `is_admin: bool` / `is_trial: bool` / `region: str` (20줄~25줄) |
| `approve_order` | `def approve_order(order: Order, user: User) -> str:` (28줄) |
| `approve_order` 독스트링 | `"""A tangled, messy function that we’ll clean up in the video."""` (29줄) |
| `main` | `def main() -> None:` (61줄) |
| 진입 가드 | `if __name__ == "__main__":` / `    main()` (87줄~88줄) |

### 3-B. 분기 구조 + 예외 처리 범위 — `approve_order` 본문 원문

| 줄 | 원문 |
|---|---|
| 30 | `try:` |
| 31 | `if user.is_premium:` |
| 32 | `if order.amount > 1000:` |
| 33 | `if not order.has_discount:` |
| 34 | `if user.region != "EU":` |
| 35 | `for item in order.items:` |
| 36 | `if item.price < 0:` |
| 37 | `return "rejected"` |
| 38 | `return "approved"` |
| 39 | `else:` |
| 40 | `if order.currency == "EUR":` |
| 41 | `return "approved"` |
| 42 | `else:` |
| 43 | `return "rejected"` |
| 44 | `else:` |
| 45 | `return "rejected"` |
| 46 | `else:` |
| 47 | `if order.type == "bulk" and not user.is_trial:` |
| 48 | `return "approved"` |
| 49 | `else:` |
| 50 | `return "rejected"` |
| 51 | `else:` |
| 52 | `if user.is_admin:` |
| 53 | `return "approved"` |
| 54 | `else:` |
| 55 | `return "rejected"` |
| 56 | `except Exception:` |
| 57 | `# Just to be safe` |
| 58 | `return "rejected"` |

최대 중첩 깊이 6단계(`try` > `if` > `if` > `if` > `if` > `for` > `if`), 37줄의 들여쓰기 32칸.

### 3-C. 책임 분리 — `main` 본문 원문

| 구간 | 원문 (줄) |
|---|---|
| 주석 | `# Create a sample user and order that barely passes the approval rules` (62줄) |
| 픽스처 1 | `user = User(is_premium=True, is_admin=False, is_trial=False, region="US",)` — 원문은 63줄~68줄 다중행 |
| 픽스처 2 | `order = Order(amount=1500, has_discount=False, region="EU", currency="USD", type="normal", items=[Item("Keyboard", 100.0), Item("Monitor", 200.0), Item("Mouse", 50.0),],)` — 원문은 70줄~81줄 다중행 |
| 호출 | `result = approve_order(order, user)` (83줄) |
| 출력 | `print(f"Order approval result: {result}")` (84줄) |

## 4. 근거 대조

**[1순위] `.editorconfig`** (`[*]` 섹션이 `.py` 에 적용됨) — 탭 들여쓰기 **0건**(`\t` Grep 0), 후행 공백 **0건**(`[ \t]+$` Grep 0), CRLF **0건**(`\r` Grep 0), charset utf-8 **0건**(29줄 U+2019 이 정상 디코딩됨). `insert_final_newline` 은 **검사 불가(바이트 단위 확인 도구 필요)** — 통과로 적지 않는다.

**[1순위] Python 린터·포매터 설정** — 부재. 검사 항목 없음, 4순위로 내린다.

**[2순위] 컨벤션 문서** — 부재. 검사 항목 없음.

**[4순위] 언어 표준** — PEP 8: 79자 초과 줄 **0건**(`^.{80,}$` Grep 0), 클래스 CapWords·함수 snake_case 준수 **0건**, 내장 이름 shadowing **1건**(항목-05). PEP 257: 독스트링 위반 **1건 계열**(항목-07 — 존재 1곳의 내용 부적합 + 모듈·클래스 3개·`main` 부재 5곳). PEP 484: 반환 타입이 실제 값 집합을 표현하지 못하는 것 **2건**(항목-03, 항목-05).

## 5. 발견 항목

| ID | 문제 | 위치 | 근거 |
|---|---|---|---|
| 항목-01 | `approve_order` 하나가 승인 규칙 전부를 담는다. 조건 9개가 중첩 깊이 6단계로 쌓여 있고, 어느 조건이 어느 결과로 이어지는지 들여쓰기로만 구분된다 | 28줄~58줄 (조건: 31, 32, 33, 34, 35, 36, 40, 47, 52줄) | 관심사(함수 설계·분기 구조) |
| 항목-02 | 함수 본문 전체를 감싼 `try` / `except Exception` 이 모든 예외를 `"rejected"` 로 바꾼다. 정상 거부(37·43·45·50·55줄)와 오류가 같은 반환값이 되어 호출자가 둘을 구분할 수 없다. 파일에 남은 근거는 주석 `# Just to be safe` 뿐이다 | 30줄, 56줄~58줄 | 관심사(예외 처리 범위) |
| 항목-03 | 판정 결과가 `"approved"` / `"rejected"` 문자열 리터럴 10곳에 흩어져 있고, 반환 타입이 `str` 이라 두 값만 유효하다는 사실이 시그니처에 없다 | 28줄, 37, 38, 41, 43, 45, 48, 50, 53, 55, 58줄 | 4순위 PEP 484 |
| 항목-04 | 모든 분기가 `return` 으로 끝나는데 `else` 블록 7곳을 그대로 유지해 들여쓰기가 32칸까지 깊어진다 | 39, 42, 44, 46, 49, 51, 54줄 | 관심사(분기 구조) |
| 항목-05 | `Order.type` 의 허용값 집합이 주석에만 있고(`# e.g. "bulk" or "normal"`) 타입은 `str`, 비교는 문자열 리터럴이다. 필드 이름 `type` 은 내장 이름과 겹친다 | 16줄, 47줄, 75줄 | 4순위 PEP 8(내장 이름 shadowing) · PEP 484 |
| 항목-06 | `region` 이 `Order` 와 `User` 양쪽에 같은 이름으로 선언돼 있고 `main` 이 서로 다른 값을 넣는데(order `"EU"`, user `"US"`), 판정은 `user.region` 만 읽는다. 어느 쪽이 지역 판정 기준인지 파일 안에서 결정되지 않는다 | 14줄, 25줄, 34줄, 67줄, 73줄 | 관심사(책임 분리) |
| 항목-07 | 독스트링이 `approve_order` 한 곳뿐이고, 그 내용이 함수의 동작이 아니라 작업 메모(`we’ll clean up in the video`)다. 모듈·데이터클래스 3개·`main` 에는 없다 | 29줄 (부재: 1, 4, 10, 20, 61줄) | 4순위 PEP 257 |
| 항목-08 | `main` 이 픽스처 생성·판정 호출·결과 출력을 한 몸에 담고, 결과를 `print` 로만 내보낸다(반환 `None`). 판정 결과를 호출부에서 받아 확인할 수단이 없다 | 61줄~84줄 (픽스처 63줄~81줄, 호출 83줄, 출력 84줄) | 관심사(책임 분리) |
| 항목-09 | 품목 가격 음수 검사(데이터 유효성)가 승인 분기 최심부에 인라인돼 있고, `user.region != "EU"` 경로에서만 수행된다. 나머지 7개 반환 경로는 `order.items` 를 보지 않는다 | 35줄~37줄 (`order.items` 참조는 35줄 1곳뿐) | 관심사(책임 분리) |

총 9건. 생략한 항목 없음.

## 6. 검산

통과 — `return`(10), `else`(7), `"approved"`/`"rejected"`(10), `region`(5), `type`(3), `try:`/`except`(2), `"""`(1), `def `(2), `print(`(1), `@dataclass`(3), 주석 `#`(3), `dataclass`/`field`(5)

| 심볼 | Grep | 인용 | 남은 위치 | 처리 |
|---|---|---|---|---|
| `\bif\b` | 31, 32, 33, 34, 36, 40, 47, 52, 87줄 (9) | 31, 32, 33, 34, 36, 40, 47, 52줄 (8) | **87줄** | 표준 진입 가드 `if __name__ == "__main__":` — 결함 아님. `3. 관심사 지도` 에 원문으로 기록, 항목 신설하지 않음 |
| `user.` / `order.` / `item.` | 31, 32, 33, 34, 35, 36, 40, 47, 52줄 (9) | 최초 8곳 (35줄 누락) | **35줄** | 항목-01 인용에 35줄 추가 + 항목-09 신설 (`order.items` 순회가 단일 경로에만 존재) |
