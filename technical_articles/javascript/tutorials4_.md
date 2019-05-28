# JavaScript

### async/await

Promise 외에도 async/await라는 비동기 코드를 처리하기 위한 새로운 구문이 있습니다.

async/await 함수의 목적은 약속을 동기적으로 사용하는 동작을 단순화하고 Promise의 그룹에서 일부 동작을 수행하는 것입니다. Primise은 구조화 된 콜백과 비슷하지만 async/await는 생성자와 Promise을 결합하는 것과 비슷합니다. 비동기 함수는 항상 Promise를 반환합니다.

```javascript
async function getGithubUser(username) { // async keyword allows usage of await in the function and means function returns a promise
  const response = await fetch(`https://api.github.com/users/${username}`); // Execution is paused here until the Promise returned by fetch is resolved
  return response.json();
}

getGithubUser('mbeaudru')
  .then(user => console.log(user)) // logging user response - cannot use await syntax since this code isn't in async function
  .catch(err => console.log(err)); // if an error is thrown in our async function, we will catch it here
```

#### Explanation with sample code

async/await 는 Promise에 기반하고 있지만 더 imperative 코드 스타일을 허용합니다.

`async`  연산자는 함수를 비동기로 표시하고 항상 `Promise`를 반환합니다. async 함수에서 `await` 연산자를 사용하면 표현식에서 반환된 `Promise`가 해결되거나 거부 될 때까지 해당 행의 실행을 일시 중지 할 수 있습니다.

```javascript
async function myFunc() {
  // we can use await operator because this function is async
  return "hello world";
}

myFunc().then(msg => console.log(msg)) // "hello world" -- myFunc's return value is turned into a promise because of async operator
```

비동기 함수의 return 문에 도달하면 Promise 값은 반환 된 값으로 충족됩니다. 비동기 함수 내에서 오류가 발생하면 Promise 상태가 rejected로 바뀝니다. 비동기 함수에서 반환 된 값이 없으면 async 함수의 실행이 완료 될 때 Promise가 반환되고 값 없이 해결됩니다.

`await` 연산자는 Promise가 수행되기를 기다리는데 사용되며 비동기 함수 본문에서만 사용할 수 있습니다. 실행이 도달하게 되면 Promise이 이행 될 때까지 코드 실행이 일시 중지됩니다.

먼저 Promise을 지닌 github 사용자를 가져올 수있는 방법을 살펴 보겠습니다.

```javascript
function getGithubUser(username) {
  return fetch(`https://api.github.com/users/${username}`).then(response => response.json());
}

getGithubUser('mbeaudru')
  .then(user => console.log(user))
  .catch(err => console.log(err));
```

다음은 async/await 입니다.

```javascript
async function getGithubUser(username) { // promise + await keyword usage allowed
  const response = await fetch(`https://api.github.com/users/${username}`); // Execution stops here until fetch promise is fulfilled
  return response.json();
}

getGithubUser('mbeaudru')
  .then(user => console.log(user))
  .catch(err => console.log(err));
```

async/await 구문은 상호 의존적인 Promise을 연결(interpoloation)해야 할 때 특히 편리합니다.

예를 들어 데이터베이스에서 블로그 글을 가져올 수 있도록 토큰을 가져온 다음 작성자 정보를 가져와야 하는 경우:

```javascript
async function fetchPostById(postId) {
  const token = (await fetch('token_url')).json().token;
  const post = (await fetch(`/posts/${postId}?token=${token}`)).json();
  const author = (await fetch(`/users/${post.authorId}`)).json();

  post.author = author;
  return post;
}

fetchPostById('gzIrzeo64')
  .then(post => console.log(post))
  .catch(err => console.log(err));
```

try/catch 블록을 표현식 주위에 추가하지 않는 한 비보호 예외는 async 함수의 본문에 던져 졌는지 또는 일시 중단 된 상태인지에 관계없이 async 함수가 반환한 Promise를 거부합니다. 비동기 함수에서 throw 문을 사용하는 것은 거부하는 Promise를 반환하는 것과 같습니다.

Promise을 통해 오류 체인을 처리하는 방법은 다음과 같습니다.

```javascript
function getUser() { // This promise will be rejected!
  return new Promise((res, rej) => rej("User not found !"));
}

function getAvatarByUsername(userId) {
  return getUser(userId).then(user => user.avatar);
}

function getUserAvatar(username) {
  return getAvatarByUsername(username).then(avatar => ({ username, avatar }));
}

getUserAvatar('mbeaudru')
  .then(res => console.log(res))
  .catch(err => console.log(err)); // "User not found !"
```

async/await 구현한 동등한 코드는:

```javascript
async function getUser() { // The returned promise will be rejected!
  throw "User not found !";
}

async function getAvatarByUsername(userId) => {
  const user = await getUser(userId);
  return user.avatar;
}

async function getUserAvatar(username) {
  var avatar = await getAvatarByUsername(username);
  return { username, avatar };
}

getUserAvatar('mbeaudru')
  .then(res => console.log(res))
  .catch(err => console.log(err)); // "User not found !"
```

---

## 참조

- [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet)

- [Make your JavaScript code shide knockout old es5 hack](https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/)

- [Mozilla JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript)