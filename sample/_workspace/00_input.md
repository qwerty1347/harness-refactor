# 분석 입력

- 대상: test.py
- 종류: 순수 Python 스크립트 (2 단계 표에 없는 종류 — 프레임워크 소속 아님)
- 관심사: 함수 설계, 분기 구조, 책임 분리, 예외 처리 범위
- 스택: Python / 프레임워크 없음 (표준 라이브러리 `dataclasses` 만 사용)

## 런타임 제약
- 확정 하한: **확인 불가**
  - `pyproject.toml` / `requirements.txt` / `setup.cfg` / lock 파일이 프로젝트에 존재하지 않는다
  - 프로젝트 루트는 Laravel(PHP) 이며 `composer.json` 은 이 파일과 무관하다
  - 참고값: 로컬 인터프리터 `Python 3.12.4`, 대상 파일이 이미 쓰고 있는 문법 `list[Item]` (3.9 도입)
- **버전 관련 제안을 일절 하지 않는다.** 대상 파일이 현재 쓰고 있는 문법 수준을 넘는
  신규 문법·표준 라이브러리 API 를 도입하지 않는다

## 제외 목록
확정 하한을 모르므로, 대상 파일이 실제로 쓰고 있는 수준(3.9 상당) 위의 것을 전부 제외한다.

| 문법 | 도입 |
|---|---|
| `match` 문 | 3.10 |
| `X \| Y` 유니온 표기 (`Optional[X]` 대신) | 3.10 |
| `dataclasses` 의 `slots=True`, `kw_only=True` | 3.10 |
| `typing.Self`, `StrEnum`, 예외 그룹 `except*` | 3.11 |
| `type` 별칭 문, PEP 695 제네릭 | 3.12 |

허용: `dataclass`, `list[X]` 내장 제네릭, f-string, `Enum`, `typing.Final`, `functools`,
조기 반환, 함수 분리 — 모두 대상 파일의 현재 수준 이하이다.

## 사용 가능한 근거
- [1순위] `.editorconfig` — UTF-8, LF, 스페이스 들여쓰기 4칸, 파일 끝 개행, 후행 공백 제거
- [1순위] Python 린터·포매터 설정 — **없음** (`ruff.toml`, `setup.cfg`, `pyproject.toml` 모두 부재)
- [2순위] 컨벤션 문서 — **없음** (`CLAUDE.md`, `docs/conventions/` 모두 부재)
- [4순위] 언어 표준 — PEP 8 (명명·줄 길이·공백), PEP 257 (독스트링), PEP 484 (타입 힌트)

## 사용자 요청 원문
"C:\xampp\htdocs\www\domeggook-product-api\test.py 리팩토링해줘"
