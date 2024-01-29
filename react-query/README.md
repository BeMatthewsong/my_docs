# ☑️ 리액트 쿼리 구성 요소

## queryKey
쿼리 키를 이용해 받아온 데이터를 캐싱할 수 있습니다. 
즉 데이터를 캐싱한 곳이라고 할 수 있습니다. <br/>
그리고 쿼리 키는 **문자열 혹은 배열**로 받아올 수 있습니다. 더불어 쿼리 함수가 **변수**에 의존한 경우에는 해당 변수를 쿼리 키에 포함할 수 있습니다.
```js
function Todos({ todoId }) {
  const result = useQuery(['todos', todoId], () => fetchTodoById(todoId))
}
```


⚠️ 쿼리 키에서 주의할 점으로는 쿼리 키를 배열로 썼을 때 **배열 항목 순서**가 중요합니다. 
아래 예시 코드는 서로 다른 결과를 가지고 있습니다.
```js
useQuery(['todos', status, page], ...)
useQuery(['todos', page, status], ...)
useQuery(['todos', undefined, page, status], ...)
```

## queryFn
쿼리 함수는 **프로미스**를  반환하는 함수입니다. 그렇기 때문에 백엔드로부터 데이터를 불러올 수 있고 데이터를 불러오는 상태를 알 수 있습니다. 
쿼리 함수로 상태를 알 수 있기 때문에 로딩, 에러 처리를 할 수 있습니다.

```js
// 에러 처리 예시
const { error } = useQuery(['todos', todoId], async () => {
  if (somethingGoesWrong) {
    throw new Error('Oh no!')
  }

  return data
})
```

## status
대표적인 상태로는 아래와 같습니다.
- isError
- isFetching
- isLoading
- isError
- isSuccess
- isPreviousData

[공식 문서를 통해 더 많이 알아보기](https://tanstack.com/query/v3/docs/react/reference/useQuery)

# ☑️ 리액트 쿼리 훅

## 마운트 당시 패칭 (`useQuery()`)
useQuery는 컴포넌트가 마운트되면 queryFn을 자동으로 실행하여 queryKey에 데이터를 저장하고 캐싱합니다. 즉, 초기에 데이터를 받아올 때 useQuery()를 사용하면 됩니다.
```js
useQuery({
  queryKey: ['posts', username],
  queryFn: () => getPostsByUsername(username),
  staleTime: 60 * 1000,
});
```

더 나아가 useQuery()에서 상태를 불러올 수 있고 그 상태를 통하여 로딩, 에러 처리를 할 수 있습니다. 
```js
const {
  data: postsData,
  isPending,
  isError,
} = useQuery({
  queryKey: postsQueryKey,
  queryFn: postsQueryFn,
});

if (isPending) return '로딩 중입니다...';

if (isError) return '에러가 발생했습니다.';
```

## 생성/수정/삭제에 의한 패칭 (`useMutation()`)
useMutation()은 useQuery()와 달리 데이터를 생성/업데이트/삭제할 때 사용됩니다.
그리고 추가적으로 useMutation()은 useQuery()와 달리 자동적으로 실행되지 않고 **mutate()**라는 트리거 함수를 이용해야 합니다. <br/>
그래서 useMutation을 사용하기 위해서는 아래와 같은 과정을 가집니다.
- **useMutation() 선언하기 (mutationFn, onSuccess, onError, onMutate, onSettled)**
- **원하는 이벤트나 함수에 mutate 함수 달기 **

```js
const uploadPostMutation = useMutation({
  mutationFn: (newPost) => uploadPost(newPost),
    onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['posts'] }); // 자동 새로고침
  },
});

const handleUploadPost = (newPost) => {
  uploadPostMutation.mutate(newPost, {
    onSuccess: () => {
      toast('포스트가 성공적으로 업로드 되었습니다!');
    },
  });
};

```

## 의존 쿼리 (Dependant Query)
특정 조건에 따라 쿼리 함수를 실행하기 위해서는 의존 쿼리가 필요합니다. <br/>
의존 쿼리를 만드는 방법은 `enable` 옵션을 사용하면 됩니다. 
그리고 논리 부정 연산자 (`!!`)를 이용하면 쉽게 불리언 값을 얻어내서 enabled 옵션에 넣으면 됩니다. 

```js
const { data: userInfoData } = useQuery({
  queryKey: queryKey,
  queryFn: queryFn,
  enabled: !!username,
});

```


## 페이지네이션 (`Paginated Query`)

## 인피니트 스크롤 

## 옵티미스틱 업데이트
