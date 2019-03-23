---
layout: post
title: "Props vs Data in Vue"
author: sk
teaser: (본 포스트는 Props vs Data in Vue by Michael Thiessen의 번역글입니다.) Vue에서는 변수를 저장하는 두 가지 방법으로 props와 data가 있다. 이들이 비슷한 일을 하는 것처럼 보여 처음에는 헷갈릴 수 있으며, 각각을 어떤 때에 사용해야 하는 지 명확하지 않을 수 있다. 그렇다면 props와 data 사이엔 어떤 차이점이 있는 것일까?
categories:
  - Technology
tags:
  - "Vue.js"
  - 번역
image: assets/images/programming/props-vs-data-in-vue.jpeg
caption: Photo by Marius Masalar
caption_url: https://unsplash.com/photos/CyFBmFEsytU
---

> 본 포스트는 [*Props vs Data in Vue by Michael Thiessen*][1]의 번역글입니다.

Vue에서는 변수를 저장하는 두 가지 방법으로 props와 data가 있다.

이들이 비슷한 일을 하는 것처럼 보여 처음에는 헷갈릴 수 있으며, 각각을 어떤 때에 사용해야 하는 지 명확하지 않을 수 있다.

그렇다면 props와 data 사이엔 어떤 차이점이 있는 것일까?

**`data`는 필요한 어떤 종류의 변수라도 저장할 수 있는 각 컴포넌트의 private 메모리이다. `props`는 부모 컴포넌트로부터 자식 컴포넌트로 전달하는 방법이다.**

이 포스트를 통해 아래 내용들을 습득할 수 있다:
- `props`가 무엇이고, 왜 이 데이터는 위가 아닌 **아래로만 흘러가는 지**
- `data` 옵션이 무엇을 위해 사용되는지
- **reactivity(반응성)**가 무엇인지
- props와 data 사이의 **변수명 충돌(naming collisions)**을 방지하는 방법
- 즐겁게 **props와 data를 함께 사용**하는 방법

## props란 무엇인가?
Vue에서 props(또는 프로퍼티)는 부모 컴포넌트로부터 자식 컴포넌트들로 데이터를 건네주는 방법이다.

컴포넌트들을 이용해 어플리케이션을 만들 때, 트리라 불리는 자료 구조를 작성하게 된다. 가족 관계와도 비슷하다.
- 부모
- 자식
- 조상
- 자손

데이터는 이 트리의 가장 위에 있는 루트 컴포넌트로부터 아래로 흐른다. 유전자가 한 세대로부터 다음 세대로 전달되는 것과 같이, **부모 컴포넌트는 props를 그들의 자식 컴포넌트로 전달한다**.

Vue에서는 코드 내 `<template>` 섹션 안에서 컴포넌트에 props를 추가한다.
```html
<template>
  <my-component cool-prop="hello world"></my-component>
</template>
```

위의 예제에서, props `cool-prop`에 값 `"hello world"`를 전달하였다. 그러면 `my-component`안에서 이 값에 접근하는 것이 가능해진다.

그러나, 컴포넌트 안에서 props에 접근하는 것이 props를 소유하고 있음을 의미하는 것이 아니므로, **값을 수정할 수는 없다** (부모님으로부터 받은 유전자를 마음대로 바꿀 수 없는 것처럼).

> Note: 컴포넌트 안에서 프로퍼티를 변경할 수 있게 되더라도, 그것은 정말 나쁜 생각이다. 부모가 사용하던 값을 컴포넌트에서 사용하게 되면, 결국 많은 혼란을 야기할 것이다.

만약 변수를 변경하는 게 불가능하다면, 꽤나 곤란할 것이다.

이럴 때 `data`가 등장한다.

## data란 무엇인가
[data][2]는 각 컴포넌트의 메모리이다. 어떤 종류의 데이터이던 저장하고 추적할 수 있는 장소이다.

카운터 앱을 만든다고 가정하면, 카운트를 계속해서 추적할 필요가 있다. 이럴 때에 `data`에 `count`를 추가한다.

{% raw %}
```html
<template>
  <div>
    {{ count }}
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </div>
</template>
```
{% endraw %}
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

만약 데이터를 다른 컴포넌트로 전달해야 한다면, props를 이용해 데이터를 트리의 아래로(자식 컴포넌트로) 내려보내거나, 이벤트로 데이터를 트리의 위로(부모 컴포넌트로) 올려보낼 수 있다.

## 데이터와 프로퍼티는 둘 다 리액티브(reactive)다.
Vue를 이용하면서 언제 컴포넌트가 자신을 업데이트하고 변화를 화면에 그리는 지 크게 생각할 필요가 없다.

**왜냐하면 Vue는 리액티브이기 때문이다.**

뭔가를 바꾸기 위해 `setState`를 매 번 호출하는 대신, 그저 변화를 주기만 하면 된다!
**리액티브 프로퍼티**(props, computed props, 그리고 `data`안의 무엇이던)를 업데이트 하기만 하면, Vue는 변화를 알아챈다.

우리의 카운터 앱으로 돌아가, 메소드들을 자세히 살펴보자.
```js
methods: {
  increment() {
    this.count += 1;
  },
  decrement() {
    this.count -= 1;
  }
}
```
그저 `count`만 업데이트하면, Vue는 변화를 감지한다. 그리고 앱을 새로운 값을 이용해 다시 렌더링한다!

Vue의 반응성 시스템은 훨씬 더 많은 미묘한 차이들이 있고, Vue를 이용해 높은 생산성을 가지고 싶다면 이 차이를 잘 이해해야 한다고 생각한다. Vue의 반응성 시스템을 더 깊이 이해하고 싶다면, [이곳][3]을 참고하자.

## 변수명 충돌 방지하기
Vue가 개발을 *조금 더 훌륭하게* 만들어주는 또다른 좋은 점이 있다.

한 컴포넌트에서 props와 data를 선언해보자:
```js
export default {
  props: ['propA', 'propB'],
  data() {
    return {
      dataA: 'hello',
      dataB: 'world',
    };
  },
};
```
메소드 안에서 props와 data에 접근하고 싶을 때, 우리는 `this.props.propA` 또는 `this.data.dataA`와 같은 짓을 할 필요가 없다. Vue는 `props`와 `data`를 완전히 생략해주어 코드를 더욱 간결하게 한다.

그저 `this.propA`나 `this.dataA`를 이용해 접근할 수 있다:
```js
methods: {
  coolMethod() {
    // prop에 접근
    console.log(this.propA);

    // data에 접근
    console.log(this.dataA);
  }
}
```
이로 인해, `props`와 `data`에 실수로 같은 이름을 사용하게 되면, 문제를 직면하게 될 것이다.

Vue는 접근하고 싶어하는 것이 `props`인지 `data`인지 알지 못하기 때문에, 이런 문제가 발생했을 때 주의(warning)를 준다!
```js
export default {
  props: ['secret'],
  data() {
    return {
      secret: '1234',
    };
  },
  methods: {
    printSecret() {
      // 원하는 값이 무엇인가?
      console.log(this.secret);
    }
  }
};
```
이런 마법은 Vue에서 `props`와 `data`를 함께 사용할 때 겪을 수 있다.

## props와 data를 함께 사용하기
지금까지 props와 data가 어떻게 다른 지 살펴보았다. 이제 왜 이것들이 **둘 다 필요한지**, 기본적인 앱을 만들며 살펴보자.

우리가 소셜 네트워크의 프로필 페이지를 만든다고 해보자. 이미 몇 가지 기능들은 완성되어 있고, 유저의 연락처 정보만 추가해야 한다.

연락처 정보는 `ContactInfo`란 이름의 컴포넌트를 이용해 나타낼 것이다.
{% raw %}
```html
// ContactInfo
<template>
  <div class="container">
    <div class="row">
      Email: {{ emailAddress }}
      Twitter: {{ twitterHandle }}
      Instagram: {{ instagram }}
    </div>
  </div>
</template>
```
{% endraw %}
```js
export default {
  name: 'ContactInfo',
  props: ['emailAddress', 'twitterHandle', 'instagram'],
};
```
`ContactInfo` 컴포넌트는 props `emailAddress`, `twitterHandle`, 그리고 `instagram`을 가지며, 그들을 페이지에 표시한다.

프로필 페이지 컴포넌트 `ProfilePage`는 다음과 같다:
{% raw %}
```html
// ProfilePage
<template>
  <div class="profile-page">
    <div class="avatar">
      <img src="user.profilePicture" />
      {{ user.name }}
    </div>
  </div>
</template>
```
{% endraw %}
```js
export default {
  name: 'ProfilePage',
  data() {
    return {
      // 실제 앱에서는 이 데이터를 서버로부터 받아올 것이다.
      user: {
        name: 'John Smith',
        profilePicture: './profile-pic.jpg',
        emailAddress: 'john@smith.com',
        twitterHandle: 'johnsmith',
        instagram: 'johnsmith345',
      },
    }
  }
};
```
`ProfilePage` 컴포넌트는 현재 유저의 프로필 사진을 이름과 함께 나타내고 있다. 또한 유저 데이터 객체를 가지고 있다.

어떻게 부모 컴포넌트(`ProfilePage`)로부터 자식 컴포넌트(`ContactInfo`)가 데이터를 내려받을 수 있을까?

**이 때 props를 이용해 데이터를 보내야 한다.**

먼저 `ContactInfo` 컴포넌트를 `ProfilePage`에 import 해야 한다:
```js
// 컴포넌트 import
import ContactInfo from './ContactInfo.vue';

export default {
  name: 'ProfilePage',

  // 의존성 추가
  components: {
    ContactInfo,
  },

  data() {
    return {
      // 실제 앱에서는 이 데이터를 서버로부터 받아올 것이다.
      user: {
        name: 'John Smith',
        profilePicture: './profile-pic.jpg',
        emailAddress: 'john@smith.com',
        twitterHandle: 'johnsmith',
        instagram: 'johnsmith345',
      },
    }
  }
}
```
다음으로, 컴포넌트를 `<template>` 섹션에 추가해야 한다:
{% raw %}
```html
// ProfilePage
<template>
  <div class="profile-page">
    <div class="avatar">
      <img src="user.profilePicture" />
      {{ user.name }}
    </div>

    <!-- 컴포넌트를 props와 함께 추가 -->
    <contact-info
      :email-address="emailAddress"
      :twitter-handle="twitterHandle"
      :instagram="instagram"
    />
  </div>
</template>
```
{% endraw %}
이제 `ContactInfo`가 필요로 하는 모든 유저 데이터는 컴포넌트 트리를 따라 `ProfilePage`로부터 `ContactInfo`로 흘러 내려왔다!

데이터를 `ContactInfo`가 아닌 `ProfilePage`에 보관하는 이유는 프로필 페이지의 다른 부분들이 유저 객체에 접근해야 하기 때문이다.

**data는 아래로만 흐르기 때문에**, 데이터는 컴포넌트 트리에서 되도록 높은 곳에 두어 필요한 곳에 내려보낼 수 있도록 해야 한다.

[1]: http://michaelnthiessen.com/vue-props-vs-data/
[2]: https://vuejs.org/v2/guide/instance.html#Data-and-Methods
[3]: https://vuejs.org/v2/guide/reactivity.html