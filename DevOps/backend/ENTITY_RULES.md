# 백엔드 엔티티·테이블 규칙 (Primary Key)

> Cursor / AI 에이전트용. DB 모델·SQLModel·SQLAlchemy·Alembic 마이그레이션 작성 시 **이 문서를 따른다.**  
> 인덱스: `docs/AIOPS/README.md`

---

## 1. 기본 원칙 (필수)

- **모든 테이블**은 시스템 내부용 **정수형(`int`) 자동 증가** 기본 키를 둔다.
- 그 기본 키 컬럼의 **DB 이름과 ORM 속성 이름을 모두 `id`로 통일**한다.
- 로그인 아이디·이메일 등 **업무 식별자**는 `id`가 아닌 **별도 컬럼**으로 둔다. (예: `login_id`, `username`, `email` 등)

이렇게 하면:

- 조인·인덱스·시퀀스 정책이 테이블마다 동일해진다.
- 외부에 노출되는 문자열 ID와 내부 PK를 분리할 수 있다.

---

## 2. SQLModel — 권장 패턴

프로젝트에서 SQLModel을 쓸 때는 아래와 같이 **내부 PK `id`**만 `primary_key`로 두고, `default=None`으로 두면 삽입 시 DB가 시퀀스로 채운다.

```python
from typing import Optional

from sqlmodel import Field, SQLModel


class ExampleEntity(SQLModel, table=True):
    __tablename__ = "examples"

    # 시스템 내부용 자동 증감 고유 번호 (기본 키)
    id: Optional[int] = Field(
        default=None,
        primary_key=True,
        sa_column_kwargs={"name": "id"},  # DB 컬럼명: id
    )

    # 업무 필드 예시 (PK 아님)
    name: str = Field(index=True)
```

- `Optional[int]` + `default=None` + `primary_key=True` 조합은 SQLModel에서 **자동 증가 정수 PK**로 자주 쓰인다.
- 테이블마다 동일한 `id` 필드 선언을 유지한다.

---

## 3. SQLAlchemy 2.0 (`DeclarativeBase` / `Mapped`) — 동일 규칙

`database.py`의 `Base`를 쓰는 경우에도 **동일한 의미**로 맞춘다.

```python
from sqlalchemy import Integer
from sqlalchemy.orm import Mapped, mapped_column

from database import Base


class ExampleEntity(Base):
    __tablename__ = "examples"

    id: Mapped[int] = mapped_column(Integer, primary_key=True, autoincrement=True)
```

- DB 컬럼명은 기본적으로 속성명 `id`와 같다.
- PostgreSQL에서 `SERIAL` / `IDENTITY` 등은 엔진·마이그레이션 설정에 맞게 생성한다.

---

## 4. 마이그레이션·기존 테이블

- 새 테이블은 위 규칙으로 생성한다.
- 이미 **문자열 PK** 등으로 만들어진 테이블이 있으면, 규칙과 불일치한다. 변경 시 **데이터 이전·Alembic 리비전**을 별도 설계한다. (문서만으로 자동 수정하지 않는다.)

---

## 5. 체크리스트

- [ ] PK 컬럼명이 `id`인가?
- [ ] PK 타입이 정수(`int` / `Integer`)인가?
- [ ] 로그인·이메일 등은 `id`가 아닌 별도 컬럼인가?
- [ ] API 응답에 내부 `id`를 노출할지 여부가 제품 정책과 맞는가?

---

## 6. 에이전트 지시 (복사용)

```text
@docs/AIOPS/backend/entity_rules.md 를 따르세요.
모든 테이블의 기본 키는 정수형 id 하나로 통일하고, 업무 키는 별도 컬럼으로 두세요.
```
