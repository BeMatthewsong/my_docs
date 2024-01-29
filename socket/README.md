# 웹소켓과 socket.io의 비교
웹소켓은 양방향 소통을 위한 프로토콜입니다. <br/>
반면에 socket.io는 양방향 통신을 하기 위해 웹소켓 기술을 활용한 라이브러리입니다. <br/>

###### 웹소켓과 http 프로토콜의 차이를 그림으로 알아보기
![image](https://github.com/BeMatthewsong/my_docs/assets/98685266/c59d9083-078e-40d0-b7b9-fa754f9d3c59)


## WebSocket
- HTML5 웹 표준 기술
- 매우 빠르게 작동하며 통신할 때 아주 적은 데이터를 이용함
- 이벤트를 단순히 듣고, 보내는 것만 가능함

## Socket.io
- 표준 기술이 아니며, 라이브러리임
- 소켓 연결 실패 시 fallback을 통해 다른 방식으로 알아서 해당 클라이언트와 연결을 시도함
- 방 개념을 이용해 일부 클라이언트에게만 데이터를 전송하는 브로드캐스팅이 가능함

  > 브로드캐스팅은 정보, 메시지 또는 콘텐츠를 대규모로 여러 수신자에게 동시에 전송하는 통신 방식을 말한다.


[웹소켓과 socket.io 포스트](https://www.peterkimzz.com/websocket-vs-socket-io)


###### 참고링크
- [Next.js 에서 Socket.io 이용해서 실시간 채팅 만들기](https://powerku.tistory.com/317)
- [[Socket.io] 소켓을 이용한 서버 생성 / 클라이언트 코드](https://velog.io/@hkj0206/socket.io-%EC%86%8C%EC%BC%93%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%84%9C%EB%B2%84%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EC%BD%94%EB%93%9C-2)
- [[Socket.io] Next.JS Ver_13 로 만든 실시간 채팅](https://velog.io/@hkj0206/Socket.io-Next.js-%EB%A1%9C-%EB%A7%8C%EB%93%A0-%EC%8B%A4%EC%8B%9C%EA%B0%84-%EC%B1%84%ED%8C%85%EC%97%90%EC%84%9C-%EC%83%9D%EA%B8%B0%EB%8A%94-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%B2%84%EA%B7%B8-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%ED%95%B4%EA%B2%B0-%EA%B3%BC%EC%A0%95)
- [Socket.io + Next JS로 채팅 구현](https://velog.io/@taemin4u/Socket.io-Next-JS%EB%A1%9C-%EC%B1%84%ED%8C%85-%EA%B5%AC%ED%98%84)
