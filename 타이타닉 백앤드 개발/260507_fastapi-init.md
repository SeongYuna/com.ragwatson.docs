# FastAPI 초기화 상태 (James Cameron App)

## 프로젝트 구조

```
project/
├── main.py
└── requirements.txt
```

## `main.py` — 초기 상태

```python
'''main'''

from fastapi import FastAPI

app = FastAPI(title="James Cameron App")


class James:
    def __init__(self):
        pass


@app.get("/")
def read_root():
    return {"message": "James가 메인이다.", "docs": "/docs"}


if __name__ == "__main__":
    import uvicorn

    print("James가 메인이다. (uvicorn)")
    uvicorn.run(app, host="127.0.0.1", port=8000, reload=True)
```

## 실행 방법

```bash
# 직접 실행
python main.py

# 또는 uvicorn으로 실행
uvicorn main:app --host 127.0.0.1 --port 8000 --reload
```

## 기본 설정 요약

| 항목 | 값 |
|------|-----|
| 앱 타이틀 | `James Cameron App` |
| 호스트 | `127.0.0.1` |
| 포트 | `8000` |
| reload | `True` |
| 루트 엔드포인트 | `GET /` |
| 응답 | `{"message": "James가 메인이다.", "docs": "/docs"}` |
| API 문서 | `http://127.0.0.1:8000/docs` |

## 의존성

```txt
fastapi
uvicorn[standard]
```

설치:
```bash
pip install fastapi "uvicorn[standard]"
```

## 클래스 현황

- `James` 클래스: 현재 빈 상태 (`pass`), 추후 비즈니스 로직 추가 예정

## Cursor 규칙 (`.cursorrules` 참고용)

- 이 파일은 FastAPI 프로젝트의 **초기 기준 상태**입니다.
- 새 라우터 추가 시 `app.include_router()` 패턴을 사용하세요.
- `James` 클래스에 기능을 추가할 때는 DI(Dependency Injection) 패턴을 고려하세요.
- 환경 변수는 `pydantic-settings`의 `BaseSettings`로 관리하는 것을 권장합니다.
