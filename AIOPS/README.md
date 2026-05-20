# AIOPS 코딩 규칙 인덱스

Cursor / AI 에이전트는 **코드를 작성하기 전에** 이 디렉터리의 규칙 문서를 읽고 따른다.  
루트 `.cursorrules` 가 이 경로를 **필수 참조**로 지정한다.

---

## 규칙 목록

| 문서 | 적용 대상 | 요약 |
|------|-----------|------|
| [frontend/REACT_RULES.md](./frontend/REACT_RULES.md) | `frontend/**/*.{tsx,jsx}` | useState 최소화, FormData 폼, 객체 state 압축 |
| [backend/entity_rules.md](./backend/entity_rules.md) | `backend/**/*.py` (모델·마이그레이션) | PK는 정수 `id` 통일, SQLModel/SQLAlchemy 패턴 |

---

## 에이전트 지시 (복사용)

```text
루트 .cursorrules 및 docs/AIOPS/README.md 를 따르세요.
코딩 전 @docs/AIOPS/ 아래 해당 영역 규칙을 읽고 구현하세요.
```

### Frontend 작업 시 추가

```text
@docs/AIOPS/frontend/REACT_RULES.md
```

### Backend (엔티티·DB) 작업 시 추가

```text
@docs/AIOPS/backend/entity_rules.md
```

---

## 규칙 추가 방법

1. `docs/AIOPS/<영역>/` 에 `*_RULES.md` 작성
2. 이 README 표에 한 줄 추가
3. 필요 시 `.cursor/rules/<이름>.mdc` 에 `globs` 연결
