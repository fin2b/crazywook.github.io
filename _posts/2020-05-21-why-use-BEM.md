---
title: "BEM을 써야 하는 이유"
author: swkim
categories: vue
tag: [vue]
---
# CSS 어떻게 작성할까?
## 글로벌 설정을 통해서
1. tag 이름으로 작성하는 방법<br/>
html, body에는 폰트 관련 설정들을 한다.
li를 쓸 때 기본 disc를 안쓰는 경우가 더 많다.
ul이나 ol에 들어있는 기본 패딩은 마크다운 문서 스타일이 아닌 이상 오히려 불편다.
이런 기본 적인 것들을 없애는 초기화 css에 대해 reset.css라고 부르는 정립된 개념이 있다.
https://meyerweb.com/eric/tools/css/reset/

    여기에 약간의 스타일을 가미한 형태를 normalize.css라고 부르고 꾸준히 업데이트 되고 있다.
https://github.com/necolas/normalize.css/blob/master/normalize.css

    normalize css도 뭔가를 없애는 방향으로 구현되어 있고 색깔이나 굵기 너비, 높이 같이 구체성을 뛰는 것들은 설정하지 않는다.

    글로벌 css에 tag에 대한 구체적인 것을 명시하는 것은 좋지 못하다.
    하나의 앱에서도 input은 다양한 모양을 갖을 수 있다.
    길이 높이는 정말 다양하고 색깔도 다양하게 쓰인다.

2. 대표성을 뛰는 class를 지정한다.<br/>
    많은 css framework들은 style에 들어갈 것들을 함축해서 class이름으로 만든다.
    이 방식은 style을 class로 옮기는 방식이라고 생각하면 된다.
    세밀하게 조절하기 힘들지만 style 작성보다는 code양을 줄일 수 있다.
    하지만 inline style을 지양하는 것처럼 utility class를 한 곳에 다수 지정하는 것도 좋은 방법이 아니다.
    3개 정도까지는 괜찮지만 그 이상 적는 것은 가독성을 떨어트린다.
    3개가 넘어가면 하나의 class로 합치는 것이 좋다.

## BEM을 사용하는 이유
> ### BEM이란?<br/>
Block Element, Modifiers의 약자다.
---


```html
<ul class="menu">
  <li class="menu__item">
    <a class="menu__item__link">즐겨찾기</a>
  </li>
  <li class="menu__item">
    <a class="menu__item__link--active">마이 페이지</a>
  </li>
</ul>
```


child element로 넘어갈경우 __로 class명을 이어가고
상태를 나타내는 modifiers는 --로 이어간다.

SSR처럼 페이지에 들어갈 때 마다 새로 asset을 내려 받는 경우
page별로 설정된 css가 서로 영향을 미치지 않지만
SPA방식에서는 모든 css가 html안에 style로 들어간다.
위의 1, 2번 방식에서 같은 class이름이 있을 경우 서로 간섭받게 된다.
page가 늘어날 수록 문제 되는 부분을 찾아야 하는 경우가 늘어나고 개발하기 고통스럽게 된다.<br/>
BEM은 보통 unique한 class명을 쓰기고 그렇게 된다면 element와 1:1 대응이 된다.
따라서 css수정시 다른 곳에 영향을 미칠 것을 걱정하지 않아도 된다.

## BEM의 단점에 대하여
-  BEM은 안전한 class명을 확보할 수 있지만 child가 많아 질 수록 코딩해야 하는 양이 늘어나서 타이핑 하기가 귀찮아 진다.  
대신 BEM은 element의 역할을 명확히 표현해 줄 수 있어서 훌륭한 주석으로 사용할 수 있다.

## SCSS와 BEM의 결합
BEM은 class와 element의 1:1대응을 지향하는데
scss를 쓴다면 코드 관리를 편하게 할 수 있다.
- scss를 이용하면 계속해서 block코드를 써줄 필요가 없다.
```scss
.bottom-bar {
  display:flex;
  align-items: center;
  justify-content: space-between;
  &__manage-lines {
      font-size: 12.5px;
  }
  &__page-number {
    display:flex;
    align-items:center;
    justify-content: flex-end;
    font-size: 12px;
    &__type {
      color: #000000;
      opacity: 0.7;
    }
  }
}
```
- modifier같은 경우 똑같은 css를 중복해서 만들어야 하는 불편함이 있는데 scss로 깔끔하게 정리할 수 있다.<br/>
아래 예제에는 padding밖에 없지만 내용이 많아 질 수록 효율은 커진다.
```scss
.match-condition {
  padding-bottom: 3px;

  &--matched {
    @extend .match-condition;
    color: #33a055;
  }
  &--unmatched {
    @extend .match-condition;
    color: #bbbbbb;
  }
}
```
- 공통 css는 mixin을 활용한다.
```scss
// _form.scss
@mixin base-input {
  border: 0;
  border-bottom: 1px solid #dbdde1;
  outline: 0;
}
@mixin base-label {
  font-size: 15px;
  font-weight: 500;
  color: #404040;
  margin-bottom: 0;
}
```
```scss
@use '/stylesheet/form';

.account-form__label {
  @include form.base-label
}
.account-form__input {
  @include form.base-input;
}
// 예시는 vue에서 scss를 모듈 방식으로 부르기때문에 form. 으로 접근한다.
```

공통 컨셉을 수정할 경우는 _form.scss를 수정하고
특정 부분만 수정할 경우 해당 class만 수정할 수 있다.
