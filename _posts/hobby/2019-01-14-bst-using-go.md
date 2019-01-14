---
layout: page
subheadline: "data structure"
title: "(WIP) Go로 구현하는 Binary Search Tree"
teaser: "기본적인 자료 구조 중 하나인 Binary Search Tree(이진 탐색 트리)를 Go언어를 이용해 구현해본다."
categories:
  - programming
tags:
  - Data structure
  - Binary search tree
  - Golang
header:
    image_fullwidth: programming/tree.jpg
image:
    thumb: programming/tree-thumb.jpg
    homepage: programming/tree.jpg
    caption: Photo by niko photos
    caption_url: https://unsplash.com/photos/tGTVxeOr_Rs
---
# Overview
## Definition
Binary search tree는 각 노드에 값이 있으며 그 값이 특정한 순서로 정렬이 가능할 때, 그 순서에 따라 정렬이 되어 있는 Binary tree를 의미한다. 이 글에서 Binary tree에 대한 정의는 [위키피디아 링크][1]로 대체한다.
기초적인 자료 구조를 얘기할 때 빠지지 않고 등장하는 Binary search tree는 정렬 및 검색을 효율적으로 할 수 있도록 도와줄 뿐 아니라, 기초적인 자료 구조답게 구현이 쉽다. 뿐만 아니라 소프트웨어 엔지니어의 면접 질문에도 자주 등장하곤 한다.

## Operations
위키피디아를 기준으로, Binary search tree의 기본적인 기능은 다음과 같다.
- 검색 (Searching)
- 삽입 (Insertion)
- 삭제 (Deletion)
- 순회 (Traversal)
- 검증 (Verification)

# Implementation
Binary search tree의 각 기능을 구현하기 이전에, 노드 타입을 정의할 필요가 있다.
~~~go
// Node is a single node in the tree
type Node struct {
	key   int
	value int
	left  *Node
	right *Node
}
~~~



[1]: https://en.wikipedia.org/wiki/Binary_tree
