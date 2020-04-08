---
title: "Typescript를 이용한 vue 구조화하기"
author: swkim
categories: vue
tag: typescript, vue
---
## 이전 프로젝트와 크게 다른 부분
1. typescript 적용
2. vue-bootstrap 적용
3. jquery 제거
4. 비지니스 로직을 container로 분리

## 프로젝트 생성
vue-cli를 사용
vuex, bootstrap 추가

### 이외 추가 library
```
@fortawesome/fontawesome-svg-core"
@fortawesome/free-solid-svg-icons
@fortawesome/vue-fontawesome
vuex-module-decorators
reflect-metadata
@vue/composition-api
```
- vue-fontawesome  
  기존에 fontawesome을 CDN으로 가져오는데 용량이 14.54MB로  
  사용자가 다운 받아사용하기에 상당히 많은 data traffic을 만든다.  
  필요한 부분만 들여와 빌드하면 상당히 많은 부분을 줄일 수 있다.  
  [자세한 내용](https://github.com/FortAwesome/vue-fontawesome)

- vuex-module-decorators
  js vuex는 type적용을 하기가 매우 어렵다.  
  또한 action이벤트는 스트링으로 발생시켜야 해서 실수의 여지가 크다.
  vuex-module-decorators를 사용하면 vuex module를 class로 관리할 수 있다.
  action, mutation, getter등 호출시 type을 적용할 수 있다.  
  [자세한 내용](https://championswimmer.in/vuex-module-decorators/pages/overview.html)

- @vue/composition-api
  typescript 적용을 위한 library  
  vue.extend로 type을 적용하는 것은 상당히 고통스러운 일이다.  
  보통 vue-class-component로 type적용을 하지만  
  composition-api의 type적용이 더 뛰어나다.  
  예를 들어 jsx로 렌더링 할 경우 type적용을 할 수 있다.  
  또한 Vue3.0에서 권장하는 component형식으로  
  추후 Vue3 프로젝트를 할 경우 브릿지 역할을 할 수 있다.  
  현재 프로젝트에서는 type적용을 위한 것이 주요 목적이며  
  presentation component에 적용한다.  
  [Vue2.0 Composition API](https://github.com/vuejs/composition-api)  
  [Vue3.0 Composition API](https://vue-composition-api-rfc.netlify.com/)

- bootstrap-vue
  bootstrap3는 jquery와 강하게 결합되어 있어서  
  vue의 가상돔 방식과 작동박싱에 괴리감이 크다.  
  bootstrap4부터는 jquery를 걷어냈다.  
  npm설치시 jquery peer warning이 뜨지만 무시해도 된다.  
  [자세한 내용](https://bootstrap-vue.js.org/docs/components/)

### container와 component
Vue 권장 스타일은 container와 component를 폴더로 구분하지 않는다.  
파일 컨벤션 권장하지만 한계가 있고 작명에 소요되는 시간을 늘릴 것으로 예상된다.
## Container
  1. 비지니스 로직만을 다룬다.
  2. html을 다루지 않는다.  
    (하지만 실제로는 slot을 사용, 경우에 따라서 div태그를 생성한다.)
## Component
  1. composition-api를 사용한다.
  2. 상태를 가지지 않는다.  
    하지만 v-bind와 data는 reactive로 연결돼 있다.  
    form같은 경우 input value를 container와 연결 시키면  
    프로그램 복잡도가 불필요하게 늘어난다.
    이런 부분은 유연하게 대처할 필요가 있다.
    Container와 Component의 분리 이유에 집중한다.
