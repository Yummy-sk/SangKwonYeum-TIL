[https://www.youtube.com/watch?v=-2TgkKYmJt4](https://www.youtube.com/watch?v=-2TgkKYmJt4)

![스크린샷 2021-11-14 오후 7.07.45.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b119ac67-e1dc-42d7-a55c-ee975296292e/스크린샷_2021-11-14_오후_7.07.45.png)

## SOP ( Same Origin Policy )

### 😄 **Sop란?**

**다른 출처의 리소스를 사용하는 것을 제한하는 보안 방식을 말함.**

### 😎 출처란?

![스크린샷 2021-10-06 오후 3.38.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e161bc75-1284-438c-b59e-5d600d72bca3/스크린샷_2021-10-06_오후_3.38.13.png)

-   **Protocol** → `https://`
-   **Host** → `github.com`
-   **Port** → `:443`
-   **Path** → `/da-nyee`
-   **Query String** → `?tab=repositories`
-   **Fragment** → `#example`

그래서, A url과 B url의 `Protocol` , `Host` , `Port` 중 **한개라도 다르면 다른 출처가 되는것이고 모두 같으면 A와 B는 같은 출처라고 할 수 있습니다.**

-   **문제**
    `[http://localhost](http://localhost)` 와 동일 출처인 것은?
    1. `https://localhost`
    2. `http://localhost:80`
    3. `http://127.0.0.1`
    4. `http://localhost/api/cors`

### 😏 SOP를 왜 사용할까?

![스크린샷 2021-10-06 오후 3.54.19.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21a45a56-76fe-4730-9159-477f109e443c/스크린샷_2021-10-06_오후_3.54.19.png)

1. **선량한 염상권이 페이브북 서비스에 로그인 이후 사용**

    로그인은 내가 염상권이다라고 페이스북에 증명할 수 있는 인증 여권 즉, 토큰을 가지고 있어야 됩니다.

2. **해커가 자극적인 어그로성 내용과 링크를 염상권한테 보낸다.**
3. **염상권은 혹해서 해당 링크에 들어가게 된다.**

    해당 링크는 `[http://hacker.ck](http://hacker.ck)` 이고 해당 링크에는 **염상권의 인증 토큰을 갈취해 염상권의 담벼락에 광고성 글을 작성하라는 명령형 스트립트가 작성되어 있다.**

4. **광고성 글을 작성하라는 명령형 스크립트가 실행된다.**

    `http:hacker.ck` 의 스크립트는 `[https://www.facebook.com](https://www.facebook.com)` 으로 담벼락을 작성하라고 `post` 요청을 보낸다.

5. **이때, SOP의 진가가 발휘됩니다.**

    ![스크린샷 2021-12-05 오후 4.39.41.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f2417cd8-b959-45d7-90e5-2fbe22c6805b/스크린샷_2021-12-05_오후_4.39.41.png)

    `[https://www.facebook.com](https://www.facebook.com)` 에서는 이 요청이 어디에서 왔는지 확인 합니다.

    But, 다른 출처인 `[http://hacker.ck](http://hacker.ck)` 로 부터 온것을 확인합니다.

    `facebook` BackEnd에서는 해당 요청이 다른 출처 즉, `cross origin` 으로 부터 왔다는 것을 확인해 해당 요청을 막아버립니다.

### 🤭 그럼, 다른 Origin이면 요청을 못하는 것인가요?

물론, 다른 Origin에서 리소스가 필요한 경우가 있습니다.

만약, **SOP 정책만 허용되어있다면 다른 Origin은 해당 요청을 처리하지 못할 것 입니다.**

**⇒ 정답은 CORS이다!!!**

## CORS

### 😝 CORS 정의

> 교차 출처 리소스 공유 ( Cross-Origin Resource Sharing, CORS ) 는 **추가 HTTP 헤더를 사용**하여, 한 **출처**에서 실행 중인 웹 애플리케이션이 **다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 정책**입니다.

-   Mozilla -
    >

### 🙃 CORS 접근제어 시나리오

-   프리플라이트 요청 ( Preflight Request )
-   단순 요청 ( Simple Request )
-   인증정보 포함 요청 ( Credentialed Request )

### 1️⃣ Preflight

**Preflight는 사전 확인 작업이라 생각하면 됩니다.**

예를 들어, 친구 집에 놀러가기 전에 놀러가도 되는지 사전에 물어보고 어떻게 해야할지 정하는 작업입니다.

따라서, 본 요청 전에 서버에게 **이 요청을 보내도 되는지 서버에게 의사를 물어보는 작업이라고 생각**하면 됩니다.

기본적으로 브라우저는 `cross-origin 요청`을 전송하기 전에 `OPTIONS` 메소드로 `preflight`를 전송합니다.

이때 `Response`로 `Access-Control-Allow-Origin`과 `Access-Control-Allow-Methods`가 넘어오는데 이는 서버에서 `어떤 origin`과 `어떤 method`를 **허용하는지 브라우저에게 알려주는 역할**을 합니다.

이후 Preflight의 결과에 따라 요청이 가능하다면 Actual Request 즉, `cross origin` 요청을 보내게 되는 것 입니다.

![스크린샷 2021-10-09 오후 2.19.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/78881fe4-fe8b-48dc-a582-3f77bbc8be09/스크린샷_2021-10-09_오후_2.19.57.png)

따라서, 그림과 같이 두 번 요청이 보내지는 것을 알 수 있습니다.

![스크린샷 2021-10-09 오후 2.21.11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ca090d80-acf5-4676-9060-5e38d9eaacff/스크린샷_2021-10-09_오후_2.21.11.png)

그래서 그림과 같이 클라이언트는 사전 요청을 보내고 서버는 사전 요청에 대한 응답을 보내게 됩니다.

이후, **Preflight 요청이 허용되었다면 실제 요청을 보내고 실제 응답을 받는 시나리오인 것** 입니다.

**조금 더 자세하게 알아보겠습니다.**

😗 **그럼 우리는 Preflight 요청을 보낼 때 무엇을 보내야 되고 Preflight 응답으로는 무엇을 받을까요?**

-   **Preflight 요청**
    ![스크린샷 2021-10-09 오후 2.26.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5f5c6b83-5caa-4a51-bdda-1b03dca64026/스크린샷_2021-10-09_오후_2.26.13.png)
    -   **Origin** → 이 요청을 어디서 부터 오는거다 ~
    -   **Access-Control-Request-Method** → FT에서 이런 요청 메서드를 보낼건데 보내도 되니?
    -   **Access-Control-Request-Headers** → 실제 요청에서 추가 헤더를 위와 같이 보내도 되는지?
-   **Preflight 응답**

![스크린샷 2021-10-09 오후 2.29.10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3d44ba35-b106-4927-8a03-91009da72731/스크린샷_2021-10-09_오후_2.29.10.png)

-   Access-Control-Allow-Origin → 해당 Origin은 허가가 되어있어 ~
-   Access-Control-Allow-Methods → 이 요청 메서드는 허가가 되어있어 ~
-   Access-Control-Allow-Headers → 이 헤더는 허가가 되어있어 ~
-   Access-Control-Max-Age → 응답 캐시 기간

**응답 캐시 기간이란?**

Preflight를 보내게 되면 한 요청을 할 때마다 **Preflight 요청과 Actual 요청 즉, 두 번의 요청**을 보내게 됩니다.

따라서, 이렇게 매번 한 요청에 대해 두 번 요청을 보내면 아무래도 성능에 좋지 않을 수 있습니다.

이를 위해 브라우저 내에서 Preflight를 캐싱을 하고 **다시 요청을 보낼때, 해당 요청이 cache hit이 되었다면 Preflight를 보내지 않고 Actual 요청을 보내게 됩니다.**

그리고 **Preflight Response가 가져야 하는 특징은 다음과 같다.**

![스크린샷 2021-12-05 오후 4.46.21.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9af9ad6c-6c68-4df0-9788-887853485008/스크린샷_2021-12-05_오후_4.46.21.png)

-   응답 코드는 200대 여야 하며
-   응답 바디는 비어있는 것이 좋다.

### 2️⃣ Simple Request

Preflight **요청 없이 바로 요청을 날리면서 이 요청이 Cross-Origin인지 확인**을 하는 것 입니다.

이는 다음 조건을 모두 만족해야 합니다.

-   **GET, POST, HEAD 메서드**
-   **Content-Type**
    -   application/x-www-form-urlencoded
    -   multipart/form-data
    -   text/plain
    ⇒ 콘텐트 타입은 이 셋중에 하나여야 함.
-   **헤더는 Accept, Accept-Language, Content-Language, Content-Type 만 허용합니다.**

⇒ 따라서, **위 조건 모두가 해당이 되어야지만 Simple Request를 날릴 수 있습니다.**

![스크린샷 2021-10-09 오후 2.43.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8c51de1-2140-4172-b668-1a89f9b55e28/스크린샷_2021-10-09_오후_2.43.57.png)

그래서 Simple Request로 Origin을 `foo.example` 로 보냈다면, **서버에서 이 Origin이 허용된 Origin인지 확인을 합니다.**

확인된 결과 `*` 즉, **모든 Origin 을 허용해준다고 했으니 성공적인 Request가 반환**되게 됩니다.

하지만, 만약 `Access-Control-Allow-Origin` 에 `func.example` 로 설정을 해두었다면 즉, 허용되지 않는 Orign으로 부터 요청이 왔을 때 바로 `CROS` 에러가 나게 되는것 입니다.

### 3️⃣ Credentialed Request

**Credentialed Request의 경우 인증 관련 헤더를 사용하는 경우 사용되는 요청**입니다.

예를 들어, **쿠키나 jwt 토큰을 브라우저에서 자동적으로 서버에게 보내고 싶을 때 사용**됩니다.

-   클라이언트 측 → `credentials: include`
-   서버 측 → `Access-Control-Allow-Credentials: true`
    만약, `Access-Control-Allow-Origin: *` 를 하면 자체 오류를 발생시킵니다.

### 🤔 그럼, 왜 Preflight 요청을 날리는 걸까?

정답은 CORS를 모르는 서버를 위해서 입니다.

![스크린샷 2021-10-09 오후 2.49.52.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d802c3d1-ce12-4e1a-bdd2-7acf795220b6/스크린샷_2021-10-09_오후_2.49.52.png)

1. Client는 서버에게 Origin `[bom.com](http://bom.com)` 으로 요청을 합니다.
2. **Server는 CORS가 뭔지 모르기 때문에 요청 그대로 처리하고 브라우저에게 보냅니다.**
3. 하지만, 중간에 **브라우저는 CORS 설정이 없는 요청에 대해 Block하고 CORS 에러를 발생**시킵니다.

**즉, 일단 서버는 요청에 대한 처리는 일단 처리하게 되는 거죠..**

하지만, 일반적인 GET 요청이면 다행이지만 DB를 통채로 삭제하는 DELETE 요청을 해커 염상권이 보냈다고 가정해봅시다.

1. 해커 염상권은 팀 브라키오를 망쳐놓기 위해 브라키오 서버를 통채로 삭제하는 요청을 보냅니다.
2. 서버는 순수하게 서버를 삭제하는 요청에 대한 처리를 하고 삭제 완료에 대한 응답을 보냅니다.
3. 하지만, 브라우저 입장에서 CORS 위반이기 때문에 CORS 에러를 해커 염상권한테 알려줍니다.

위와 같은 경우 염상권의 요청은 의도대로 실행 되었을까요?

CORS에러는 **서버가 아닌 브라우저 단에서 발생시킨 것 이기 때문에 에러가 났다고 해서 롤백이 되는 것이 아닙니다...**

### 😎 그래서 필요하다! Preflight

![스크린샷 2021-10-09 오후 3.00.26.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c966bc6-8b03-4958-a325-342d3193fdc4/스크린샷_2021-10-09_오후_3.00.26.png)

따라서, **Preflight는 CORS를 모르는 서버를 위해 꼭 필요**하며 위와 같은 불상사를 위해 Preflight 작업은 꼭 필요합니다.

## CORS 해결하기

1. 프록시 서버 사용하기
2. http-proxy-middleware 사용하기
3. Access-Control-Allow-Origin 헤더 세팅하기
4. CORS 미들웨어 사용하기

### 1️⃣ 프록시 서버 사용하기

![스크린샷 2021-10-10 오후 6.05.02.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7eec0726-c889-4938-aa1f-684532ad7919/스크린샷_2021-10-10_오후_6.05.02.png)

![스크린샷 2021-12-05 오후 4.56.56.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/515b0bf6-5911-4dc7-b689-d79866392874/스크린샷_2021-12-05_오후_4.56.56.png)

클라이언트에서 외부 서버로 바로 요청을 하는 것이 아닌 프록시 서버를 사용하는 방법입니다.

즉, **브라우저의 요청을 중간에 프론트 프록시 서버를 거쳐 실제 서버에게 요청하는 방법**입니다.

그리고 해당 요청이 프론트 프록시 서버에 도착했다면, **프론트 프록시 서버는 서버의 SOP에 맞는 Origin으로 바꿔서 보내줄 수 있습니다.**

따라서, **CORS는 브라우저에 관련된 정책이기 때문에 브라우저를 통하지 않고 서버 간 통신을 할 때는 이 정책이 적용되지 않습니다.**

### 2️⃣ http-proxy-middleware 사용하기

배포하고 나서는 동일한 출처에 요청하고 응답하기 때문에 CORS 에러가 발생하지 않지만, **배포하기 전 개발 단계에 문제가 발생합니다.**

로컬 환경에서 프론트 Origin으로 `[localhost:3000](http://localhost:3000)` 번 포트를 사용하고 백엔드는 `[localhost:5000](http://localhost:5000)` 을 사용하고 있다면, 3000번 포트에서 5000번 포트로 요청을 보내기 때문에 CORS가 발생합니다.

다만, 이는 로컬 환경일 경우 한정 `http-proxy-middleware` 라이브러리를 사용하면 클라이언트단에서 쉽게 해결할 수 있습니다.

![스크린샷 2021-12-05 오후 5.09.00.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fec0d506-a962-44eb-b5b4-ae2f3dfc51cb/스크린샷_2021-12-05_오후_5.09.00.png)

`http-proxy-middleware` 라이브러리를 설치하고 위와 같이 설정하면 `[http://localhost:3000/api](http://localhost:3000/api)` 의 요청을 서버의 포트인 `[http://localhost:5000/api](http://localhost:5000/api)` 로 바꿔주는 **프록싱**을 하게 됩니다.

따라서 브라우저는 클라이언트와 서버의 출처가 다르지만, 같은 것으로 받아들여 CORS를 일으키지 않게 되는 것 입니다.

### 3️⃣ Access-Control-Allow-Origin 헤더 세팅하기

이 방법은 **클라이언트에서 해결하는 방법이 아닌 서버에서 헤더를 셋팅하는 방법**입니다.

그래서, 요청에 대한 응답의 헤더를 통해 `Access-Control-Allow-Origin` 에 **허용하고자 하는 도메인을 설정 후 응답**하는 방법이 있습니다.

```jsx
app.get('/api', (req, res) => {
    res.header('Access-Control-Allow-Origin', '허용하고자 하는 도메인');
    res.send('~~~~~~');
});
```

즉, 서버 측에서 **내가 허용하고자 하는 도메인을 설정하면 헤더에 허용한 프론트의 Origin이 존재하므로, 브라우저는 허용된 출처임을 알게 됩니다.** 따라서, 이와 같이 CORS에러를 막을 수 있습니다.

다만, 주의해야 할 것은 `Access-Control-Allow-Origin` 을 `*` 로 처리하는 것입니다. 이는 모든 출처의 요청을 허용해주겠다는 말이기에 지양하는 것이 좋습니다.

### 4️⃣ CORS 미들웨어 사용하기

만약, `express` 서버를 따로 구축 했다면, Node.js 미들웨어인 CORS를 사용하는 방법도 존재합니다.

```jsx
...
const cors = require('cors');

const corsOptions = {
		origin: '허용하고자 하는 도메인',
		crenentials: true
}
```

`corsOptioins` 에 내가 허용하고자 하는 도메인을 설정하면 `Access-Control-Allow-Origin` 내용이 추가됩니다.

따라서, 위와 같이 설정한 `corsOptions` 을 미들웨어에 추가하면 됩니다.

```jsx
const express = require('express');
const cors = require('cors');

const app = express();

const corsOptions = {
    origin: '허용하고자 하는 도메인',
    crenentials: true,
};

app.use(cors(corsOptions));
```

만약, `app.use(cors())` 와 같이 파라미터 없이 미들웨어를 등록하는 것은 `Access-Control-Allow-Origin: *` 와 같은 의미 임으로 이점 주의하면 됩니다.
