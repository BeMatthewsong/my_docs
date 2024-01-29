# ☑️ Router 객체

## ☑️ next/router 사용하기

1. import 후
```ts
import {useRouter} from 'next/router';
```
2. router 객체 호출하기
```ts
const router = useRouter();
```

## ☑️ router 객체 뜯어보기

router 객체를 콘솔로 확인하면 아래와 같습니다.

![image](https://github.com/final-project-temporaryName/youth_frontend/assets/98685266/9ed2072f-93ad-4d07-959d-ba8a83bf19a1)

### pathname (현재 경로)

주로 경로를 지정하기 위해서나 url에 따른 조건부 처리를 할 때 사용하면 좋습니다. 

```ts
const urlObject = {
    pathname: "/about/[id]",
    query: { id: movie.id, name: movie.name },
};
```
<br/>

콘솔로 찍어보면 아래와 같습니다. <br/>

![image](https://github.com/final-project-temporaryName/youth_frontend/assets/98685266/2c44dc0f-9ae9-4b2a-b6df-281c1791ad70)

### basePath (기본 경로 접두사)

앱 내에 설정되는 경로 접두사로 `next.config.js`에서 설정 가능하다.
아래 예시라면 빌드 시 `/about` 경로는 `/docs/about`로 바뀐다.

```ts
module.exports = {
  basePath: '/docs',
}
```

### query (객체로 구문 분석된 쿼리 문자열)

router.query 값을 사용하면 페이지 주소에서 Params 값이나 쿼리스트링 값을 참조할 수 있습니다.

```ts
// 1번째 예시
const router = useRouter();
const id = router.query['id']; // id 값 가져오기

// 2번째 예시
const router = useRouter();
const q = router.query['q']; // q 값 가져오기 (/search?q=신발이라면 '신발'이라는 값 가져온다)
```

## ☑️ router 객체 메소드 뜯어보기

### 페이지 전환 (router.push | router.replace)
push로 이동시키면 history stack에 쌓여서 뒤로가기가 가능하고
replace로 이동시키면 history stack에 안 쌓여서 뒤로가기 불가능

다시 말해 이전의 라우팅 히스토리를 모두 유지하고 싶다면 `router.push`를 사용하면 되고 현재 라우팅 히스토리를 다른 url로 변경하고 싶다면 `router.replace`를 사용하면 된다.

router.push | router.replace의 옵션으로는 다음과 같습니다.
```ts
router.push(url, as, options)
router.replace(url, as, options)
```
1. url
- 이동할 url > url 객체 사용 가능
2. as
- 이동 후 브라우저에 표시될 URL
3. option
- scroll: default: true  탐색 후 페이지 맨 위로 스크롤 제어 
- shallow: default: false , getStaticProps, getServerSideProps, getInitialProps를 다시 실행하지 않고 현재 페이지 경로 업데이트
- locale: 새로운 페이지의 locale

```ts
// as 사용 안하고 option (scroll) 사용하는 경우 예제
router.push({
  pathname: router.pathname,
  query: { ...router.query, currency: newCurrency.value },
}, undefined, { scroll: false });
```

```ts
useEffect(() => {
   if (!(user || loading)) {
   router.push('/login')
 }
}, [user, loading])
```

### 페이지 뒤로가기 (router.back)

`window.history.back()` 실행
히스토리에서 전단계로 이동합니다. 

```ts
import { useRouter } from 'next/router'
 
export default function Page() {
  const router = useRouter()
 
  return (
    <button type="button" onClick={() => router.back()}>
      Click here to go back
    </button>
  )
}
```

### 페이지 새로고침 (router.reload)
`window.history.reload()` 실행

```ts
import { useRouter } from 'next/router'

export default function Page() {
  const router = useRouter()

  return (
    <button type="button" onClick={() => router.reload()}>
      Click here to reload
    </button>
  )
}
```

## ☑️ 더 많은 훅 알아보기

- **usePathname()** : 현재 url 출력
- **useSearchParams()** : 쿼리스트링 출력
- **useParams()** : 유저가 입력한 동적 라우트 출력

```ts
'use client'
 
import { usePathname } from 'next/navigation'
 
export default function ExampleClientComponent() {
  const pathname = usePathname()
  return <p>Current pathname: {pathname}</p>
}
```

```ts
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SearchBar() {
  const searchParams = useSearchParams()
 
  const search = searchParams.get('search')
 
  // URL -> `/dashboard?search=my-project`
  // `search` -> 'my-project'
  return <>Search: {search}</>
}
```

```ts
'use client'
 
import { useParams } from 'next/navigation'
 
export default function ExampleClientComponent() {
  const params = useParams<{ tag: string; item: string }>()
 
  // Route -> /shop/[tag]/[item]
  // URL -> /shop/shoes/nike-air-max-97
  // `params` -> { tag: 'shoes', item: 'nike-air-max-97' }
  console.log(params)
 
  return <></>
}
```

---
###### 참고 링크
- [[Next.js] next/router 사용하기 (공식문서 내용 정리)](https://im-designloper.tistory.com/102)
- [Next 공식 문서 (useRouter)](https://nextjs.org/docs/pages/api-reference/functions/use-router#router-object)
