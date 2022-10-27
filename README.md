# 원티드 FE 프리온보딩 7차 1-1 과제
원티드 프리온보딩 프론트엔드 7차 7팀의 1-1 과제 레포지토리입니다.

## 과제 설명

- 이번 과제는 기업과제가 아닌 멘토가 부여하는 과제입니다.
- 팀에서 생각한 원티드 프리온보딩 프론트엔드 선발 과제의 Best Pratice를 만들고 제출해주세요

<aside>
💡 Best Practice란 모범사례라는 말로서, 특정 문제를 효과적으로 해결하기 위한 가장 성공적인 해결책 또는 방법론을 의미합니다. 과제 수행 과정에서 Best Practice란 팀원들이 각자의 구현방법을 설명하고 토론했을 때 팀 안에서 이 방법이 가장 효율적이라고 판단되는 것을 정하고 그것을 팀의 Best Practice로 삼는것입니다. 이때 특정한 팀원의 과제 전체를 Best Practice로 선정하는 것이 아닌, 과제의 각 부분이나 중점을 둬야할 부분을 단위를 나눈뒤, 각 단위마다의 Best Practice를 토론하고, 단위별로 Best Practice를 모아서 팀의 최종 결과물을 만들어내는 방식으로 진행해주세요

</aside>

## 과제 목적

- 이번 과제의 목적은 **동료학습, 팀으로 일하는 법**에 익숙해지는 것입니다.
- 각자 구현한 결과물을 통해 토론하면서 특정 문제를 어떻게 해결하는게 가장 좋은 방안인지 토론해서 팀의 Best Pratice를 산출해주세요
- Best Practice를 찾아가는 과정에서 서로 많은 토론을 통해서 의견을 교환하고 소통하는법을 연습해주세요 그리고 중요하다고 생각되는 부분들은 왜 이것이 Best Practice라고 생각했는지에 대한 이유를 기술해주세요
- 기술은 README.md에 바로 하셔도 되고, GitHub Wiki, 블로그 등 외부에 작성 후 README에 링크를 연결해주셔도 됩니다.
- 오늘 학습한 과제 제출시에 포함되어야 하는 요소들과 Git Commit Message, History 관리, 협업 툴 등을 신경써서 과제를 진행해주세요

## 제출 기한

- 금요일 12:00PM (정오)

## 👥 팀원소개

|이름|Github|
|:-----:|:------:|
|신상오(팀장)|[so0112](https://github.com/so0112)|
|권내영(부팀장)|[nyoung113](https://github.com/nyoung113)|
|임채동|[Chaedie](https://github.com/Chaedie)|
|소재현|[socow](https://github.com/socow)|
|문민종|[viaDPBell](https://github.com/viaDPBell)|
|문이슬|[Leeseul-Moon](https://github.com/Leeseul-Moon)|
|이재하|[idjaeha](https://github.com/idjaeha)|
|한승범|[hanseungbum](https://github.com/hanseungbum)|

## 🔗 배포 링크
https://team7-week1-1.vercel.app/todo

## 📝 프로젝트 실행 방법
1. 패키지 설치 <br />
`npm install`
2. 실행 <br />
`npm run start`
3. http://localhost:3000 에서 확인가능 <br />
`open http://localhost:3000`

## 🗂️ 디렉토리 및 파일구조
![image](https://user-images.githubusercontent.com/55952886/198363817-55fe093d-14f3-4b17-9378-76ca12ff3f35.png)




# 👍 Best Practice 

## 1. 로그인 / 회원가입
- 회원가입 이메일, 비밀번호 유효성 검사
```javascript
useCheck(checkEmail, email, setIsEmail);
useCheck(checkPassword, password, setIsPassword);
```
- 유효성 검사 커스텀훅 src/hooks/useCheck.js
```javascript
/** 유효성 검사 커스텀 훅
 *
 * checkFunction : 유효성 검사 함수
 * checkedArg : checkFunction의 인자
 *
 * setIsState : 변경할 상태
 */
export default function useCheck(checkFunction, checkedArg, setIsState) {
  useEffect(() => {
    setIsState(checkFunction(checkedArg));
  }, [checkFunction, checkedArg, setIsState]);
}
```
- 로그인 form submit 함수
```javascript
  const submitLogin = async event => {
    event.preventDefault();
    postLogin(LOGIN_URL, email, password, setIsError);
  };
```
- 로그인 post 요청
```javascript
export const postLogin = async (LOGIN_URL, email, password, setIsError) => {
  await instance
    .post(LOGIN_URL, {
      email,
      password,
    })
    .then(res => {
      localStorage.setItem('token', res.data.access_token);
      window.location.replace('/todo');
    })
    .catch(error => {
      setIsError(true);
    });
};
```

👍 Best Practice 선정 이유

회원가입, 로그인 컴포넌트에서 유효성 검사 함수, Hooks, api 요청부를
분리하여 기능별로 쉽게 구분할 수 있도록 작성했습니다



## 2. 리다이렉션 
```javascript
const isLogin = Boolean(localStorage.getItem('token'));
const navigate = useNavigate();

useEffect(() => {
    if (isLogin) {
      navigate('/todo');
    }
  }, [isLogin, navigate]);
```
👍 Best Practice 선정 이유

페이지 렌더링시에 토큰의 유무를 확인하여 간단하게 페이지 리다이렉션이 가능하도록 작성했습니다


## 3. TODO CRUD
- axios inpercepter 사용
`src/apis/api.js`
```javascript
import axios from 'axios';

const ACCESS_TOKEN = localStorage.getItem('token');

// baseURL: process.env.REACT_BASE_URL,
export const instance = axios.create({
  baseURL: `https://pre-onboarding-selection-task.shop`,
  headers: {
    'Content-Type': 'application/json',
  },
});

instance.interceptors.request.use(
  function (config) {
    if (ACCESS_TOKEN) {
      config.headers.Authorization = `Bearer ${ACCESS_TOKEN}`;
    } else {
      config.headers.Authorization = `Bearer ${localStorage.getItem('token')}`;
    }
    return config;
  },
  function (error) {
    return Promise.reject(error);
  }
);
```

👍 Best Practice 선정 이유

axios inpercepter 를 통해서 api 통신시 반복되는 header, token을 
생략할 수 있도록 코드 작성, 불필요한 코드 반복을 피하고 가독성을 높일 수 있었습니다


## 📝 팀 컨벤션


### Code Convention

- 변수명 : 카멜케이스
- 함수명
    - 기능 함수 → 화살표 함수 : 카멜케이스
    - export 사용
    
    ```jsx
    export const foo = () => {
    	return 'bar'
    }
    ```
    
    - 컴포넌트 함수 → 일반 함수 : 파스칼케이스
    
    ```jsx
    export function Foo() {
    	return (<h1>hello world</h1>)
    }
    ```
    
- 폴더 구조
    - 폴더명 : 카멜케이스 <복수>
    - 파일명
        - 컴포넌트 파일 → 파스칼 케이스, ‘.jsx’ / tsx
        - 이외 파일 → 카멜 케이스, ‘.js’ / ts

```jsx
[src]
	⌙ [components]
		⌙ index.js
		⌙ Login.jsx
	⌙ [apis]
		⌙ api.js
		⌙ todo.js
		⌙ user.js
	⌙ [hooks]
		⌙ useCheck.js
	⌙ [pages]
		⌙ index.js
		⌙ LoginPage.jsx
		⌙ TodoPage.jsx
```

- 파일 명
    
    페이지 컴포넌트는 `~Page` 으로 작성하고, 파일 명도 컴포넌트와 동일하게 작성합니다. 
    

```jsx
export function LoginPage(){
	...
}
```

- 파일 작성 순서
    
    각 파일은 Import / Interface(type) - ts 사용 시 / main / styled / export 순으로 작성
    
    ```tsx
    import ...
    
    interface ...
    
    function component(){
    }
    
    const styledSome = styled.div``
    
    export ...
    ```
    
- styled-component 사용 시 객체에 담아서 사용
    
    ```jsx
    const S = {
    	Wrap: styled.div`
    		display : flex;
    		align-items : center;
    	`
    }
    ```
- Branch Convention
    - main
    - 이름 : 각자이름으로 브랜치를 파주세요.
        - 이름/feature : 기능별 브랜치 분리하여 작업
- Merge Convention (PR 승인한 인원 )
    - merge approval by Chaedie
	
- Commit Convention
	
|Tag Name|Description|
|:-----:|:------|
|`Feat`|새로운 기능 추가|
|`Fix`|새로운 기능 추가|
|`Docs`|문서 수정|
|`Style`|코드 포맷팅, 세미콜론 누락, 코드 변경이 없는 경우|
|`Refactor`|코드 리팩토링|
|`Test`|테스트 추가, 테스트 리팩토링|
|`Chore`|빌드 업무 수정, 패키지 매니저 수정|







