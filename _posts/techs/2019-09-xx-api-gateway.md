---
layout: post
title: "API Gateway의 주요 기능 이해"
author: sk
categories:
  - Technology
tags:
  - API 게이트웨이
  - Microservice
image: assets/images/programming/complicated-road.jpg
caption: Photo by John Lockwood
caption_url: https://unsplash.com/photos/FcLq69V7Rsc
---

MSA(Microservice Architecture: 마이크로 서비스 아키텍쳐)를 도입하려 할 때 함께 고려해야 하는 것 중에 API 게이트웨이가 있다. API 게이트웨이는 잘게 쪼개져 분산되어 있는 각 서비스의 엔드포인트를 단일화하고, 인증과 인가를 비롯한 공통 기능 처리 등을 담당한다.

## 공통 기능 처리
유저에게 제공되는 서비스를 개발할 때, 어떤 서비스던 공통적으로 필요한 기능들이 있다. 대표적으로 인증 및 인가, 로깅과 같은 기능들이다. API 게이트웨이가 없다면 MSA에서 각 서비스 컴포넌트가 이러한 공통 기능들을 중복으로 가져야 하는 문제가 발생한다.  
{:refdef: style="text-align: center;"}
![duplicated-call]({{ site.baseurl }}/assets/images/programming/api-gateway-duplicated-call.png)
{:refdef}
클라이언트와 서비스 컴포넌트 사이에 API 게이트웨이가 개입하여 공통 기능을 처리하는 것으로 중복 개발 문제가 해결되고, 서비스 컴포넌트는 서비스의 비즈니스 로직에만 집중할 수 있게 된다.
{:refdef: style="text-align: center;"}
![common]({{ site.baseurl }}/assets/images/programming/api-gateway-common-functions.png)
{:refdef}

## API 라우팅
API 게이트웨이는 API 호출을 라우팅 하는 것에도 유용하다. 동일한 API에 대한 호출이 있더라도, 대상 서비스나 클라이언트에 따라 다른 엔드포인트를 이용하도록 할 수 있다.
### 로드 밸런싱
API 게이트웨이를 지나 여러 개의 API 서버를 갖는 구성에서, API 게이트웨이는 로드 밸런서 역할을 수행해 여러 개의 API 서버로 부하를 분산시킬 수 있다. API 게이트웨이는 API 서버의 장애와 장애 복구 상태를 감지하고, 이에 따라 클라이언트로부터의 요청을 적절한 API 서버로 요청할 수 있어야 한다.
### 서비스 및 클라이언트 별 엔드포인트 제공
하나의 시스템이 다양한 서비스나, 다양한 클라이언트 등으로 나뉘어 제공할 때, 각각 다른 서비스, 클라이언트 별로 다른 엔드포인트를 제공할 수 있다.  
따라서 API 서버가 공통적인 API를 가지더라도, 다르게 제공하는 엔드포인트를 통해 다른 서비스 혹은 클라이언트를 위한 API로 서비스가 가능하다.
### 메시지/헤더 기반 라우팅
메시지나 헤더의 내용을 기반으로 API 라우팅을 할 수 있다. 특히 글로벌 단위로 배포되는 시스템에서, 데이터 센터 간 메시지 라우팅 시 유용하게 사용될 수 있다. 라우팅 정보 추출 시, API 게이트웨이가 메시지를 파싱하는 데에 많은 파워가 소모될 수 있다는 점을 주의해야 한다. API 게이트웨이가 REST API 형태의 엔드포인트를 클라이언트에게 제공한다면, 라우팅 정보를 HTTP 헤더 부분에 넣고, API 게이트웨이는 헤더만 파싱하여 라우팅 정보를 추출, API 서버로는 HTTP 바디를 포워딩 하는 용도로만 사용하여 API 게이트웨이의 부하를 줄여주는 것이 좋다.

## Mediation
메디에이션 기능은, 클라이언트에서 호출하는 요청과 API 서버가 제공하는 API의 스펙에 차이가 발생할 때, 이를 중간에서 중재해주는 기능을 이야기한다.
### 메시지 포맷 변환
메시지 포맷 변환은 클라이언트와 서버가 서로 다른 메시지 포맷을 채택하고 있을 때, 이를 적절하게 변환시켜주는 기능을 의미한다.
### 프로토콜 변환
다양한 서비스 및 클라이언트를 지원하게 되면, 각 서비스 및 클라이언트가 다른 통신 프로토콜을 사용해야 하는 경우가 생길 수 있다.
특히, 내부 API는 gRPC와 같은 차세대 프로토콜을 이용해 구현하면서도, 외부로는 REST API를 통해 서비스함으로써 내부 API의 성능을 올리면서도 범용성을 높이는 것이 가능하다.
### Aggregation
Agregation이란 서로 다른 API를 묶어서 하나의 API로 제공하는 것을 의미한다.  
다만, 과도한 aggregation은 API 게이트웨이에 큰 부담을 줄 위험이 있다. 이와 같은 문제를 해결하기 위해서, 여러 API를 조합하는 목적의 mediation API 서버 계층을 도입할 수 있다.

## References
- [조대협의 블로그: 대용량 웹서비스를 위한 마이크로 서비스 아키텍쳐의 이해][1]
- [조대협의 블로그: MSA 아키텍쳐 구현을 위한 API 게이트웨이의 이해 (API GATEWAY)][2]

[1]: https://bcho.tistory.com/948
[2]: https://bcho.tistory.com/1005
