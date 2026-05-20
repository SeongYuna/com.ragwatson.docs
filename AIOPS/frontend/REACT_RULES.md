# React 코딩 규칙 (Frontend)

> Cursor / AI 에이전트용 지침. 프론트엔드 React·Next.js 코드 작성·리팩터 시 **이 문서를 따른다.**  
> 루트 `.cursorrules` → `docs/AIOPS/README.md` → 본 문서 순으로 참조한다.  
> `.cursor/rules/react-frontend.mdc`, `docs-coding-standards.mdc` 와 연동되어 있다.

---

## 1. useState 사용 원칙

- **`useState`는 필드마다 하나씩 두지 않는다.** (남용 금지)
- 관련된 값은 **하나의 객체 state**로 묶는다.
- 폼 입력은 가능하면 **비제어(uncontrolled) + `FormData`** 로 처리해 입력마다 `setState`를 줄인다.
- UI 전용·서로 무관한 값(예: `mode: "login" | "signup"`)만 별도 `useState`를 써도 된다.

### ❌ 피할 패턴

```tsx
const [id, setId] = useState("")
const [password, setPassword] = useState("")
const [nickname, setNickname] = useState("")
const [email, setEmail] = useState("")
```

### ✅ 권장: 객체로 압축

```tsx
const [signupForm, setSignupForm] = useState({
  id: "",
  password: "",
  nickname: "",
  email: "",
})

// 필드 하나만 갱신
setSignupForm((prev) => ({ ...prev, id: e.target.value }))
```

### ✅ 더 권장: FormData (폼 제출 시 한 번에 읽기)

입력마다 state를 두지 않고, `<form onSubmit>` + `name` 속성으로 값을 모은다.

```tsx
const handleSignup = async (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault()
  const formData = new FormData(e.currentTarget)
  const formProps = Object.fromEntries(formData.entries()) as {
    id: string
    password: string
    nickname: string
    email: string
  }

  const res = await fetch(`${API_BASE_URL}/signup`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(formProps),
  })
  // ...
}
```

```tsx
<form onSubmit={(e) => void handleSignup(e)} noValidate>
  <input name="id" defaultValue="" placeholder="아이디" />
  <input name="password" type="password" defaultValue="" />
  <input name="nickname" defaultValue="" />
  <input name="email" type="text" defaultValue="" />
  <button type="submit">회원가입</button>
</form>
```

---

## 2. 로그인 / 회원가입 같이 쓸 때

- `mode`(`login` | `signup`)만 단일 `useState`로 둔다.
- 로그인·회원가입 폼을 **둘 다 제어 컴포넌트**로 유지해야 하면, 폼별로 객체 state **최대 2개**까지 허용한다.
- 리팩터 시 **FormData 방식 우선** 검토한다.

```tsx
type AuthMode = "login" | "signup"

const [mode, setMode] = useState<AuthMode>("login")

// FormData 사용 시 login/signup 각각 onSubmit 핸들러만 두고
// 필드 state는 제거 가능
```

---

## 3. Cursor 에이전트 지시문 (복사·자동 적용용)

아래 블록은 사람이 채팅에 붙여 넣거나, 규칙 파일이 로드될 때 에이전트가 따르는 내용이다.

```text
@docs/AIOPS/frontend/REACT_RULES.md 를 따르세요.

React에서 useState는 많이 쓰지 마세요.
폼 리팩터 시 다음을 참고하세요:

1) 여러 useState / 필드별 state → 관련 필드는 하나의 useState 객체로 압축
2) 가능하면 FormData + form onSubmit 패턴 적용:

const handleSignup = async (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault();
  const formData = new FormData(e.currentTarget);
  const formProps = Object.fromEntries(formData.entries());
  // formProps로 API 호출
};

3) input에는 name 속성을 붙이고, type="submit" 버튼 사용
4) 기존 API·UI 동작은 유지
```

---

## 4. 적용 범위

| 대상 | 경로 |
|------|------|
| React 컴포넌트 | `frontend/**/*.tsx`, `frontend/**/*.jsx` |
| 이 문서 | `docs/AIOPS/frontend/REACT_RULES.md` |

---

## 5. 체크리스트 (리뷰 시)

- [ ] 필드마다 `useState`가 있지 않은가?
- [ ] 폼을 `FormData` + `onSubmit`으로 바꿀 수 있는가?
- [ ] `e.preventDefault()` 후 `Object.fromEntries(formData.entries())` 로 값을 읽는가?
- [ ] `name` 속성이 모든 input에 있는가?
- [ ] 불필요한 `value` / `onChange` 쌍을 제거했는가?
