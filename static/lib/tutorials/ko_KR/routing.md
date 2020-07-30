## Routing

_이 튜터리얼은 hapi v17과 호환됩니다._

hapi에서 라우트를 정의할 때 다른 프레임워크와 마찬가지로 3개의 기본 요소가 필요합니다.: 경로, 메소드, 처리기. 서버에 객체로 전달되고 다음처럼 간단할 수 있습니다.:

```javascript
server.route({
    method: 'GET',
    path: '/',
    handler: function (request, h) {

        return 'Hello!';
    }
});
```

## 메소드

이 라우트는 문자열 `Hello!`로 `/`에 `GET` 요청에 응답합니다. 메소드 옵션은 유효한 HTTP 메소드 또는 메소드의 배열일 수 있습니다. 사용자가 `PUT` 또는 `POST` 요청에 같은 응답을 원한다고 가정하면 다음과 같이 할 수 있습니다.:

```javascript
server.route({
    method: ['PUT', 'POST'],
    path: '/',
    handler: function (request, h) {

        return 'I did something!';
    }
});
```

## 경로

경로 옵션은 문자열이어야 하지만 명명된 인자를 포함할 수 있습니다. 경로 안에 인자에 이름을 주기 위해서는 단순히 `{}`로 감싸주면 됩니다. 예:

```javascript
server.route({
    method: 'GET',
    path: '/hello/{user}',
    handler: function (request, h) {

        return `Hello ${encodeURIComponent(request.params.user)}!`;
    }
});
```

위에서 볼수 있듯이 경로에 `{user}` 문자열이 있습니다. 경로의 해당 부분을 명명된 인자에 할당하도록 요청하는 것입니다. 이 인자는 처리기 안에 `request.params` 객체에 저장됩니다. 인자를 `user`라 이름 붙였기 때문에 콘텐츠 주입 공격을 막기 위한 URI 인코딩 후 `request.params.user` 속성에 값에 접근할 수 있습니다.

### 선택적 인자

이 예에서 user 인자가 필요합니다.: `/hello/bob` 또는 `/hello/susan`에 대한 요청은 잘 동작하지만 `/hello`에 대한 요청은 동작하지 않습니다. 인자를 선택적으로 만들려면 인자 이름 끝에 물음표를 붙여줍니다. 여기에 같은 라우트이지만 `user` 인자를 선택적으로 만들기 위해 수정했습니다.:

```javascript
server.route({
    method: 'GET',
    path: '/hello/{user?}',
    handler: function (request, h) {

        const user = request.params.user ?
            encodeURIComponent(request.params.user) :
            'stranger';

        return `Hello ${user}!`;
    }
});
```

이제 `/hello/mary`에 대한 요청은 `Hello mary!`로 응답하고 `/hello`에 대한 요청은 `Hello stranger!`로 응답할 것입니다. 경로에서 *마지막* 명명된 인자만이 선택적으로 될 수 있습니다. 선택적 인자 뒤에 다른 인자가 있기 때문에 `/{one?}/{two}`는 무효한 경로임을 의미합니다. 경로의 부분에서 일부분을 다루는 명명된 인자를 가질 수 있습니다. 예를 들어 `/{filename}.jpg`는 유효합니다. 한 부분에서 인자 사이에 인자가 아닌 구분자가 제공되면 여러 인자를 가질 수 있습니다. 이는 `/{filename}.{ext}`는 유효하지만 `/{filename}{ext}`는 유효하지 않음을 의미합니다.

### 복수 부분 인자

선택적 경로 인자와 함께 복수 부분에 일치하는 인자를 사용할 수 있습니다. 그렇게 하려면 별표와 숫자를 사용합니다. 예:

```javascript
server.route({
    method: 'GET',
    path: '/hello/{user*2}',
    handler: function (request, h) {

        const userParts = request.params.user.split('/');
        return `Hello ${encodeURIComponent(userParts[0])} ${encodeURIComponent(userParts[1])}!`;
    }
});
```

이 설정으로 `/hello/john/doe`에 대한 요청은 문자열 `Hello john doe!` 응답을 받을 것입니다. 중요한 것은 인자는 `"john/doe"` 문자열이라는 것입니다. 그래서 두 개의 분리된 부분을 얻기 위해 그 글자로 나누었습니다. 별표 뒤에 숫자는 인자에 할당될 경로 부분의 개수를 나타냅니다. 숫자를 생략할 수도 있습니다. 그러면 인자는 가능한 부분의 개수와 일치합니다. 선택적 인자같이 복수 부분 인자는(예 `/{files*}`) *오직* 경로의 마지막 인자로만 나타날 수 있습니다. 

특정 요청에 대한 처리기를 결정할 때 hapi는 가장 구체적인 것부터 덜 구체적인 순서로 경로를 찾습니다. 만약 하나는 `/filename.jpg`와 다른 하나는 `/filename.{ext}` 이렇게 2개의 라우트를 가지고 있다면 `filename.jpg`에 대한 요청은 첫 번째 라우트에 일치하고 두 번째 라우트에는 일치하지 않습니다. `/{files*}` 경로의 라우트는 테스트 되는 *마지막* 라우트가 될 것이고 다른 라우트가 모두 실패할 경우에만 일치한다는 것을 의미합니다. 

## Handler 메소드

handler 옵션은 `request`와 `h` 2개의 인자를 받습니다.

`request` 인자는 경로 인자,연관된 페이로드, 인증 정보, 헤더 같은 최종 사용자에 대한 자세한 정보를 가진 객체입니다. `request` 객체에 대한 전체 문서는 [API reference](/api#request-properties)에서 찾을 수 있습니다.

두 번째 인자인 `h`는 요청에 응답하기 위해 사용하는 몇 가지 메소드를 가지고 있는 객체인 응답 도구입니다. 앞의 예제에서 본 것처럼 요청에 어떤 값으로 응답하기를 원하면 처리기에서 단순히 그 값을 반환하면 됩니다. payload는 문자열, 버퍼, 직렬화된 JSON 객체, 스트림 또는 Promise 이어야 합니다. 

또는 같은 값을 `h.response(value)`에 전달하고 처리기에서 반환하면 됩니다. 이 호출의 결과는 응답 객체이고 이 객체는 응답을 전송하기 전에 응답을 변경하는 추가적인 메소드로 연결될 수 있습니다. 예를 들어 `h.response('created').code(201)`은 HTTP 상태 코드 `201`과 함께 `created`의 payload를 전송할 것입니다. 헤더, 내용물 종류, 내용물 길이를 설정하거나 리다이렉션 응답을 전송할 수 있습니다. 많은 다른 것들에 대해서는 [API reference](/api#response-toolkit)에 정리되어 있습니다.

## 설정

이 세가지 기본 요소를 제외하고 각 라우트에 `options` 인자를 지정할 수 있습니다. 이 인자로 [validation](/tutorials/validation), [authentication](/tutorials/auth), 전처리, 페이로드 처리, 캐시 옵션 같은 것을 설정합니다. 더 자세한 것은 연결된 튜터리얼 및 [API reference](/api#route-options)에서 찾을 수 있습니다.

여기에서는 문서를 생성하는데 도움이 되는 몇 가지 옵션을 살펴봅니다.

```javascript
server.route({
    method: 'GET',
    path: '/hello/{user?}',
    handler: function (request, h) {

        const user = request.params.user ?
            encodeURIComponent(request.params.user) :
            'stranger';

        return `Hello ${user}!`;
    },
    options: {
        description: 'Say hello!',
        notes: 'The user parameter defaults to \'stranger\' if unspecified',
        tags: ['api', 'greeting']
    }
});
```

이 옵션에 대해 기능적으로 말하자면 아무런 효과가 없지만 API에 대한 문서를 생성하는 [lout](https://github.com/hapijs/lout)같은 플러그인을 사용할 때는 매우 유용할 수 있습니다. 라우트와 관련된 메타데이는 나중에 검사 또는 표시가 가능해집니다.
