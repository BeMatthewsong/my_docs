## next-auth란
next-auth 라이브러리는 next.js 로 구현되어 있는 페이지에서 로그인을 쉽게 구현할 수 있도록 관련 기능을 제공하는 3rd Party 라이브러리이다.

## next-auth를 써야 하는 이유
- Oauth Provider 제공 : 애플리케이션 내에서 세션 객체를 공유할 수 있다
- 안전한 토큰 저장: 토큰을 로컬 스토리지가 아닌 쿠키 (`http-only`와 `Secure` 설정)에 저장한다.
> http-only 설정을 하면 자바스크립트로 토큰 정보에 접근할 수 없다. 이를 위해서 next-auth는 jwt, session 콜백 함수 등을 이용해 client에서도 토큰 정보 일부를 노출시킬 수 있고, useSession 과 같은 훅을 통해 이를 참조할 수 있다

## next-auth에서 사용할 수 있는 것

- **useSession** (client component) : 로그인 완료 이후에는 useSession API를 통해 로그인 정보를 가지고 올 수 있다.
  > 로그인 정보에는 user에 대한 이름, 이미지, 이메일 등이 있습니다.
  ```ts
  const { data } = useSession();
  ```
- **getServerSession** (server component or api) : useSession과 역할은 동일하지만 서버측에서 세션 정보를 가져올 수 있습니다.  
```ts
import {authOptions} from "@/app/api/auth/[...nextauth]/route";
 
const session = await getServerSession(authOptions);
```

- **로그인, 로그아웃** : next-auth를 통해 signIn, signOut API를 가져와 로그인 혹은 로그아웃 기능을 구현할 수 있다.
> signIn
파라미터가 없을 경우, 소셜 로그인 선택하는 화면으로 이동하게 되고, 콜백 url를 지정하면 로그인 후 그 주소로 이동하게 된다
> 
```ts
// 파라미터 없는 경우
signIn();

// 파라미터 있는 경우
signIn('kakako');

// 콜백 url이 있는 경우
signIn('google', { callbackUrl: 'http://localhost:3000/bar' })
```
파라미터에 로그인 정보를 입력하게 되면 바로 로그인 화면으로 진행하게 됩니다.
  

## next-auth 사용 방법
next-auth는 기본적으로 Next js의 동적 API route을 이용해 로그인을 구현한다.

### 1. 설치
```
npm install next-auth
```

### 2. API route 추가
`[...nextauth].js`라는 파일을 특정 위치에 맞게 아래와 같이 구성하면 된다.
next-auth 에서 제공하는 signIn/signOut 함수 역시 이 api로 요청을 보내는 것이다.  `e.g api/auth/signIn/kakao`, `api/auth/signOut`
> All requests to `/api/auth/*`(signIn, callback, signOut, etc.) will automatically be handled by NextAuth.js.
> 
```ts
// app/api/auth/[...nextauth].js
import NextAuth from "next-auth"
import GithubProvider from "next-auth/providers/github"
import NaverProvider from "next-auth/providers/naver"
import KakaoProvider from "next-auth/providers/kakao

export const authOptions = {
  // Configure one or more authentication providers
  // providers 설정하기
  providers: [
    GithubProvider({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    NaverProvider({
        clientId: process.env.NAVER_CLIENT_ID,
        clientSecret: process.env.NAVER_SECRET,
      }),
    KakaoProvider({
        clientId: process.env.KAKAO_CLIENT_ID,
        clientSecret: process.env.KAKAO_SECRET,
      }),
    // ...add more providers here
  ],
}

export default NextAuth(authOptions)
```

### 3. Oauth Provider 구성하기
우선 Oauth 프로토콜 방식의 로그인을 구현하기 위해서, next-auth에서 제공하는 Auth Provider를 가져온다. 네이버와 카카오 Provider 역시 제공된다.
`app/api/auth/[...nextauth].ts` 에서 providers에 추가하고 각 Provider에 입력할 client ID와 Secret 키는 카카오, 네이버 개발자 센터에서 등록한 앱의 ID와 Secret을 넣으면 된다. 


Session Provider 만들기
```ts
// components/providers/session-provider.tsx
"use client";
 
import { SessionProvider } from "next-auth/react";
import { ReactNode } from "react";
import { Session } from "next-auth";
 
type Props = {
    session?: Session | null;
    children: ReactNode;
};
 
export default function AuthSession({ session, children }: Props) {
    return <SessionProvider session={session}>{children}</SessionProvider>;
}
```

layout에 provider 넣기
```ts
// app/layout.tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
 
import AuthSession from '@/components/providers/session-provider'
import LoginButton from '@/components/login-button'
 
const inter = Inter({ subsets: ['latin'] })
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
      <AuthSession>
          <div className="h-[48px] bg-black flex items-center">
              <ul className="ml-auto mr-5">
                   <li><LoginButton></LoginButton></li>
              </ul>
          </div>
        {children}
      </AuthSession>
      </body>
    </html>
  )
}
```

### 로그인 버튼 구현하기

```ts
"use client";
 
import {signIn, signOut, useSession } from 'next-auth/react';
import React from 'react';
import {Avatar, AvatarFallback, AvatarImage} from "@/components/ui/avatar";
 
const LoginButton = () => {
    const {data} = useSession(); // next-auth에서 session 데이터 가져오기
 
    const onClick = async (e: React.MouseEvent) => {
        e.preventDefault();
 
        if (data) {
            await signOut();
        } else {
            await signIn();
        }
    }
    return (
        <div className="flex items-center gap-3">
            {data?.user && <Avatar>
                <AvatarImage src={data.user.image ?? ""} alt="user image" />
                <AvatarFallback>CN</AvatarFallback>
            </Avatar>
            }
            <a href="#" onClick={onClick} className="text-sm text-white">{data ? "로그아웃": "카카오 아이디로 로그인"}</a>
        </div>
    );
};
 
export default LoginButton;
```

###### 참고 링크
- [카카오 로그인 예시](https://github.com/mxx-kor/nextjs-practice/pull/2/commits)
- [next-auth 이용해서 10분만에 소셜 로그인 구현 하기 (앱라우터)](https://powerku.tistory.com/312)
- [next-auth signIn](https://next-auth.js.org/getting-started/client#signin)
