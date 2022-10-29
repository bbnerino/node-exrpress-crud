# node.js 시작하기

홈페이지에서 노드를 다운로드 해줍니다.

나는 LTS 가 좋아

# node.js 실행하기

실행시키기 위한 파일을 준비해줍니다.

index.html

app.js

```jsx
var http = require('http');
var fs = require('fs');
var app = http.createServer(function(request,response){
    var url = request.url;
    if(request.url == '/'){
      url = '/index.html';
    }
    if(request.url == '/favicon.ico'){
      return response.writeHead(404);
    }
    response.writeHead(200);
    response.end(fs.readFileSync(__dirname + url));
 
});
app.listen(3000);
```

`node app.js`

를 통해 실행시키게 되면 index.html 코드가 나오게 됩니다. 

# URL qeury string

`localhost/?name=heyhey`

형태의 url 주소로 들어오게 된다면 qeury를 parse 해서 찾을 수 있습니다.

```jsx
var http = require('http');
var fs = require('fs');
**var url = require('url');**
 
var app = http.createServer(function(request,response){
    **var _url = request.url;
    var queryData = url.parse(_url, true).query;**
    if(_url == '/'){
      _url = '/index.html';
    }
    if(_url == '/favicon.ico'){
      return response.writeHead(404);
    }
    response.writeHead(200);
    response.end(queryData.name);
 
});
app.listen(3000);
```

request 의 url 을 확인해보면?

console.log(_url) ⇒ `/?id=dkdk`

이 url을 query 로 parse 해서 새로운 queryData를 만들어줍니다 .

console.log(queryData); ⇒  `{name:heyhey}`

오케이 이해완료

response 로 [queryData.name](http://queryData.name) 을 보내주게 된다면 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c0f7ea78-4051-4e17-9f5f-3a34f1578df1/Untitled.png)

---

# CRUD

이제 서버의 형태로 사용하기 위해 CRUD 방식으로 시작하겠습니다. 

## 조회하기

no.js

```jsx
var fs = require('fs');
fs.readFile('example.txt', 'utf8',(err, data)=>{
  console.log(data);
});
```

fs = file system

example.txt의 파일을 조회해서 읽어 오기 위해서 fs의 readfile을 사용 했습니다.

example.txt의 내용을 잘 읽어온다는 것을 확인했습니다.

### 적용해보기

data 라는 폴더 안에 HTML,CSS,JavaScript 파일을 만들고 내용들을 입력해둡니다.

```jsx
var http = require('http');
var fs = require('fs');
var url = require('url');
 
var app = http.createServer(function(request,response){
    var _url = request.url;
    var queryData = url.parse(_url, true).query;
    var title = queryData.id;
    if(_url == '/'){
      title = 'Welcome';
    }
    if(_url == '/favicon.ico'){
      return response.writeHead(404);
    }
    response.writeHead(200);
    fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
      var template = `
      <!doctype html>
      <html>
      <body>
        <h1><a href="/">WEB</a></h1>
        <ul>
          <li><a href="/?id=HTML">HTML</a></li>
          <li><a href="/?id=CSS">CSS</a></li>
          <li><a href="/?id=JavaScript">JavaScript</a></li>
        </ul>
        <h2>${title}</h2>
        <p>${description}</p>
      </body>
      </html>
      `;
      response.end(template);
    })
 
 
});
app.listen(3000);
```

같은 방식으로 readFile을 하게되면 우리가 적어놓은 파일에서 값을 읽어 description에 넣어주고 조회할 수 있습니다. 

### 404 NOT FOUND 보내기

없는 페이지로 접속시에 보내줄 값이 필요합니다.

pathname이라는 값이 있습니다.

- [localhost/udada](http://localhost/udada) ⇒ /udada
- [localhost/?id=udada](http://localhost/?id=udada) ⇒ /

이런식으로 쿼리스트링 전까지의 내용을 return 해줍니다.

```jsx
var _url = request.url;
var pathname = url.parse(_url, true).pathname;
// pathname = '/hi'
```

이 값을 이용해서 이제 조건문을 걸어주면 없는 정보는 notfound 가 보내집니다.

```jsx
if(pathname==='/'){
  fs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
    var template = `
    <html>...</html>
    `;
    response.writeHead(200);
    response.end(template);
  })
}
else{
  response.writeHead(404);
  response.end('NOT FOUND')
}
```