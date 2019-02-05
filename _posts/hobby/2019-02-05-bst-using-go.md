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
<div class="small-6 small-centered columns t30">
<img src="{{ site.urlimg }}programming/bst.png" alt="">
</div>

## Definition
Binary search tree는 각 노드에 값이 있으며 그 값이 특정한 순서로 정렬이 가능할 때, 그 순서에 따라 정렬이 되어 있는 Binary tree를 의미한다. 이 글에서 Binary tree에 대한 정의는 [위키피디아 링크][1]로 대체한다.  
노드의 순서는 대체로 대상 노드의 왼쪽 자식 트리는 대상 노드의 값보다 작은 값들을 가지며, 오른쪽 자식 트리는 대상 노드의 값보다 큰 값들을 가진다.

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
{% highlight go %}
// Node is a single node in the tree
type Node struct {
	key   int
	value int
	left  *Node
	right *Node
}
{% endhighlight %}
각 노드는 key와 value를 가지며, 2개의 자식 노드를 가리키는 포인터를 가진다. 자식 노드는 left와 right로 구별하였다.

그리고 전체 트리 구조를 파악할 수 있도록 루트 노드를 가리키는 Binary search tree 타입을 정의한다.
{% highlight go %}
// Bst is the binary search tree
type Bst struct {
	root *Node
}
{% endhighlight %}

## Searching
원하는 key를 입력했을 때 해당 키를 갖는 노드를 반환하는 함수를 구현한다.
만약 해당 key를 갖는 노드를 찾을 수 없는 경우 `nil`을 반환하도록 하였다.
{% highlight go %}
// Search searchs the key value
func (bst *Bst) Search(key int) *Node {
	return search(bst.root, key)
}

// search searchs the key recursively
// return nil if there is no node found
func search(n *Node, k int) *Node {
	if n == nil || n.key == k {
		return n
	}
	if k < n.key {
		return search(n.left, k)
	}
	// k > n.key
	return search(n.right, k)
}
{% endhighlight %}

## Insertion
key와 value를 입력으로 받아 대상 트리에 새로운 노드를 추가하는 함수를 구현한다.
만약 트리가 root 노드가 존재하지 않는 빈 트리라면, 새로운 노드를 root 노드로써 추가한다.
{% highlight go %}
// Insert inserts the new node into the tree
func (bst *Bst) Insert(key int, value Item) {
	n := &Node{key, value, nil, nil}
	if bst.root == nil {
		bst.root = n
	} else {
		insert(bst.root, n)
	}
}
{% endhighlight %}

만약 트리에 이미 root 노드가 존재하면, 새로운 노드를 추가할 올바른 위치를 찾아야한다.
key를 비교하며 새로운 노드가 추가될 위치를 찾는 재귀함수 `insert(n *Node, new *Node)`를 구현한다.
{% highlight go %}
// insert inserts the new node recursively
func insert(n *Node, new *Node) {
	if new.key < n.key {
		if n.left == nil {
			n.left = new
		} else {
			insert(n.left, new)
		}
	} else {
		if n.right == nil {
			n.right = new
		} else {
			insert(n.right, new)
		}
	}
}
{% endhighlight %}

## Deletion
Binary Search Tree에서 노드의 삭제는 다른 동작들보다 복잡하다. 노드를 삭제한 뒤에도 전체 Binary Search Tree는 Binary Search Tree로써의 기능을 유지해야 한다. 따라서 어떤 노드가 삭제되는가에 따라 Binary Search Tree 형태를 유지하기 위한 추가 처리가 필요할 수 있다. 이해를 쉽게 하기 위해서, 우선 어떤 경우가 있는지 살펴보자.
1. 자식 노드를 가지지 않는 leaf 노드를 삭제하는 경우
2. 하나의 자식 노드만 가지는 노드를 삭제하는 경우
3. 좌/우 자식을 모두 가지는 노드를 삭제하는 경우

먼저, Binary Search Tree에 대한 함수 `Delete(k int)`와 실제 삭제 처리를 담당할 내부 재귀함수 `delete(n *Node, k int)`의 기본은 다음과 같다.
{% highlight go %}
func (bst *Bst) Delete(k int) {
	delete(bst.root, k)
}

func delete(n *Node, k int) *Node {
	// if current node is not the target node
	if n == nil {
		return nil
	} else if k < n.key {
		n.left = remove(n.left, k)
		return n
	} else if k > n.key {
		n.right = remove(n.right, k)
		return n
	}

	// if current node is the target node
	...
}
{% endhighlight %}

재귀함수 `delete(n *Node, k int)`에서 현재 노드가 삭제할 노드일 경우에 대한 구현은 아래에서 살펴본다.

#### 1. leaf 노드의 제거
leaf 노드의 제거는 간단하다.
{% highlight go %}
func delete(n *Node, k int) *Node {
	...
	if n.left == nil & n.right == nil {
		n = nil
		return nil
	}
}
{% endhighlight %}

#### 2. 자식을 하나만 가지는 노드의 제거
위에서 leaf 노드의 경우를 먼저 처리했기 때문에, 하나의 자식 노드가 존재하지 않는 것만 체크하는 것으로 자식을 하나만 가지는 노드를 가릴 수 있다.
{% highlight go %}
func delete(n *Node, k int) *Node {
	...
	// if the node has right child only
	if n.left == nil {
		n = n.right
		return n
	}
	// if the node has left child only
	if n.right == nil {
		n = n.left
		return n
	}
}
{% endhighlight %}

#### 3. 좌/우 자식을 모두 가지는 노드의 제거
{% highlight go %}
func delete(n *Node, k int) *Node {
	...
}
{% endhighlight %}

## Traversal

## Verification


[1]: https://en.wikipedia.org/wiki/Binary_tree
