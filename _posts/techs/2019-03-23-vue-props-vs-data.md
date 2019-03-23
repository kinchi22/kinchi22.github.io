---
layout: post
title: "Props vs Data in Vue"
author: sk
teaser: (본 포스트는 Props vs Data in Vue by Michael Thiessen의 번역입니다.) Vue에서는 변수를 저장하는 두 가지 방법으로 props와 data가 있다. 이들이 비슷한 일을 하는 것처럼 보여 처음에는 헷갈릴 수 있으며, 각각을 어떤 때에 사용해야 하는 지 명확하지 않을 수 있다. 그렇다면 props와 data 사이엔 어떤 차이점이 있는 것일까?
categories:
  - Technology
tags:
  - "Vue.js"
  - 번역
image: assets/images/programming/props-vs-data-in-vue.jpeg
caption: Photo by Marius Masalar
caption_url: https://unsplash.com/photos/CyFBmFEsytU
---

> 본 포스트는 [*Props vs Data in Vue by Michael Thiessen*][1]의 번역입니다.

Vue에서는 변수를 저장하는 두 가지 방법으로 props와 data가 있다.

이들이 비슷한 일을 하는 것처럼 보여 처음에는 헷갈릴 수 있으며, 각각을 어떤 때에 사용해야 하는 지 명확하지 않을 수 있다.

그렇다면 props와 data 사이엔 어떤 차이점이 있는 것일까?

`data`는 필요한 어떤 종류의 변수라도 저장할 수 있는 각 컴포넌트의 개인 메모리이다. `props`는 부모 컴포넌트로부터 자식 컴포넌트로 전달하는 방법이다.

이 포스트를 통해 아래 내용들을 습득할 수 있다:
- `props`가 무엇이고, 왜 이 데이터는 위가 아닌 아래로만 흘러가는 지
- `data` 옵션이 무엇을 위해 사용되는지
- reactivity가 무엇인지
- props와 data 사이의 변수명 충돌(naming collisions)을 방지하는 방법
- 즐겁고 득을 볼 수 있도록 props와 data를 동시에 사용하는 방법

## props란 무엇인가?
Vue에서 props(또는 프로퍼티)는 부모 컴포넌트로부터 자식 컴포넌트들로 데이터를 건네주는 방법이다.

컴포넌트들을 이용해 어플리케이션을 만들 때, 트리라 불리는 자료 구조를 작성하게 된다. 가족 관계와도 비슷하다.
- 부모
- 자식
- 조상
- 자손
데이터는 이 트리의 가장 위에 있는 루트 컴포넌트로부터 아래로 흐른다. 유전자가 한 세대로부터 다음 세대로 전달되는 것과 같이, 부모 컴포넌트는 props를 그들의 자식 컴포넌트로 전달한다.

Vue에서는 코드 내 `<template>` 섹션 안에서 컴포넌트에 props를 추가한다.
```html
<template>
  <my-component cool-prop="hello world"></my-component>
</template>
```

위의 예제에서, 프로퍼티 `cool-prop`에 값 `"hello world"`를 전달하였다. 그러면 `my-component`안에서 이 값에 접근하는 것이 가능해진다.

그러나, 컴포넌트 안에서 프로퍼티에 접근하는 것이 프로퍼티를 소유하고 있음을 의미하는 것이 아니므로, **값을 수정할 수는 없다** (부모님으로부터 받은 유전자를 마음대로 바꿀 수 없는 것처럼).

> Note: 컴포넌트 안에서 프로퍼티를 변경할 수 있게 되더라도, 그것은 정말 나쁜 생각이다. 부모가 사용하던 값을 컴포넌트에서 사용하게 되면, 결국 많은 혼란을 야기할 것이다.

만약 변수를 변경하는 게 불가능하다면, 꽤나 곤란할 것이다.

이럴 때 `data`가 등장한다.

## data란 무엇인가
[data][2]는 각 컴포넌트의 메모리이다. 어떤 종류의 데이터이던 저장하고 추적할 수 있는 장소이다.

카운터 앱을 만든다고 가정하면, 카운트를 계속해서 추적할 필요가 있다. 이럴 때에 `data`에 `count`를 추가한다.

```html
<template>
  <div>
    {{ count }}
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </div>
</template>
```
```js
export default {
  name: 'Counter',
  data() {
    return {
      // 시작 시 0으로 초기화
      count: 0,
    }
  },
  methods: {
    increment() {
      this.count += 1;
    },
    decrement() {
      this.count -= 1;
    }
  }
}
```
이 값은 private이며 **컴포넌트 자신만을 위해** 사용한다. 다른 컴포넌트들은 이 값에 접근할 수 없다.

> Note: 다시 말하지만, 다른 컴포넌트가 이 데이터에 접근할 수 있더라도, 같은 이유로, 이런 짓을 하는 건 정말 나쁜 생각이다.

만약 데이터를 다른 컴포넌트로 전달해야 한다면, 프로퍼티를 이용해 데이터를 트리의 아래로(자식 컴포넌트로) 내려보내거나, 이벤트로 데이터를 트리의 위로(부모 컴포넌트로) 올려보낼 수 있다.

## WIP


[1]: http://michaelnthiessen.com/vue-props-vs-data/
[2]: https://vuejs.org/v2/guide/instance.html#Data-and-Methods
