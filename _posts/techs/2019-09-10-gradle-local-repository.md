---
layout: post
title: "Gradle에서의 로컬 저장소 이용"
author: sk
teaser: Gradle에서 로컬 저장소(local repository)를 통해 라이브러리를 제공하는 방법에 대해 알아본다.
categories:
  - Technology
tags:
  - Gradle
image: assets/images/programming/
caption: Photo by ..
caption_url: https://unsplash.com/photos/....
---
## 



https://stackoverflow.com/questions/5901378/what-exactly-is-a-maven-snapshot-and-why-do-we-need-it


./gradlew publishToMavenLocal :fido2:protocol:jar
implementation "com.linecorp.fido.fido2:fido2-protocol:0.0.11-SNAPSHOT"

https://gradle.org