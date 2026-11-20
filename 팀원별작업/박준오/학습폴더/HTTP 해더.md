
| 헤더                | 요청·응답 | 값과 사용 규칙                                      |
| ----------------- | ----- | --------------------------------------------- |
| `Host`            | 요청    | `parking-reservation.example.com`             |
| `Authorization`   | 요청    | `Bearer ` + 로그인 토큰, 최대 2,048바이트               |
| `Content-Type`    | 요청·응답 | `application/json`                            |
| `Content-Length`  | 요청·응답 | JSON 본문의 UTF-8 바이트 수, 최대 5바이트의 10진수 문자열       |
| `X-Request-ID`    | 요청·응답 | 요청 UUID 36바이트를 응답에 그대로 반환                     |
| `Idempotency-Key` | 요청    | 연장 시도마다 생성한 UUID 36바이트. 같은 시도를 재전송하면 같은 키를 사용 |
`X-Request-ID`와 `Idempotency-Key`는 JSON 본문에 넣지 않는다. `Transfer-Encoding: chunked`는 사용하지 않는다.

- host는 http 요청을 어는 서버 주소에 보내는지 알려주는 헤더이다. host는 서버의 도메인이다. 
- Authorization은 서버가 요청한 사용자를 인증할 때 사용하는 HTTP 헤더이다. 사용자가 앱에서 로그인하면 서버가 인증 토큰을 발급한다. 앱은 예약 시간 연장을 요청할 때 이 토큰을 Authorization 헤더에 담아 보내고, 서버는 토큰을 검사해 사용자를 확인한다. 이후 해당 예약이 그 사용자의 것인지 확인한다. Bearer는 인증방식을 나타낸다.
- content-type은 http 메세지 본문이 어떤 형식으로 보낼것인가를 결정한다. application/json은 json 형싱을 의미한다. 일반 텍스트는 text/plain이다
- content-Length는 http 메세지 본문 크기이다. http 메세지 본문 크기를 바이트 단위로 표시해서 13바이트면 `Content-Length: 13` 로 작성한다
```HTTP
POST /message HTTP/1.1
Host: api.example.com
Content-Type: text/plain
Content-Length: 5

Hello
```
hello는 ascii 기준 5바이트 임을 content-length가 '5' 가되는 것이다.
- X-Request-ID 서버가 각요청을 구분하고 추적하기 위해 사용하는 식별자이다. 예를 들어 X-Request-ID: req-123 이라고 하면 req-123기준으로 고나련 서버 로그를 찾아낼 수 있다. 
- idempotnecy-key는 동일한 요청이 여러번 전달되도 의도하지 않은 중복처리를 방지하기 위한 키다. 예를 들어 키 값이 pay-001라고 가정하면 결제 요청을 보냈는데 네트오류로 안한 응답을 받지 못했다. 네트워크가 연결된뒤 두번째 결제 요청을 보냈을때 재결제가 일어나지 않고 기존 결과를 반환할때 구분자도 사용된다. 중복결제를 방지하는것은 서버에서 구현을 해야한다.