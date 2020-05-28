---
title: "vue-router history mode 설정"
author: swkim
categories: vue
tag: [vue]
---
# History Mode 배포시 유의 사항
spa앱을 router history 모드로 배포하면<br/>
서버에서 route경로를 file경로로 인식해서 `404 Not Found` 가 뜰 수 있다.<br/>
아래는 vue cli guide에 nginx 설정 예시다.<br/>
```nginx
http {
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;
  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
  access_log  /var/log/nginx/access.log  main;
  sendfile        on;
  keepalive_timeout  65;
  server {
    listen       80;
    server_name  localhost;
    location / {
      root   /app;
      index  index.html;
      try_files $uri $uri/ /index.html;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
      root   /usr/share/nginx/html;
    }
  }
}
```

예시는 root('<br/><br/>') 경로 나와있어서
subfolder를 적용할 경우 빼먹을 수 있는 것들이 많아서 주의해야 한다.
### subfolder 설정시 추가로 해야 할 것들
1. publicPath 설정<br/>
vue.config.js에서 publicPath 설정을 해야 한다.
링크를 불러 올 때 다음과 같이 불러 올 수 있다.<br/>
`<script src="{publicPath}/app.js"></script>`<br/>
[공식 가이드 참조](https://cli.vuejs.org/config/#publicpath)
2. router에 base 설정
```javascript
export default new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes,
})
```
mode만 history로 설정하면
pushState경로가 root(`<br/>`)로 잡히기 때문에 history가 정상적으로 작동하지 않는다.<br/>
사용할 subfolder 이름을 base에 지정해 주어야 한다.<br/>
[공식 가이드 참조](https://router.vuejs.org/api/#base)

3. nginx설정
```nginx
location /app {
  alias   /front;
  try_files $uri $uri/ /front/index.html;
}
```
이제 nginx에서 이와 같이 설정하면 정삭적으로 history가 작동한다.
