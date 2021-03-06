# 链表

链表由一个个数据节点组成，链表将数据和数据之间关联起来，从一个数据节点指向另一个数据节点。

## 链表节点

常用链表节点结构包含指向上一个(prev)和下一个节点(next)的指针，和该节点值的value

```c
//redis中链表节点
typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;
```

## 链表

链表结构一般包含头部节点(head)，末尾节点(tail)的指针和链表长度(len)

```c
//redis中链表结构
//redis的链表是双端链表，链表节点带有前置(prev)和后置节点(next)两个指针
typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr);
    void (*free)(void *ptr);
    int (*match)(void *ptr, void *key);
    unsigned long len;
} list;
```

链表一般分为`单链表` `双链表` `循环链表`这些链表都是一个数据指向另外一个数据的结构只是链表节点的指向不同

- `单链表`
    单链表，就是链表是单向的，每个节点只有一个方向不能往回找
- `双端链表`
    顾名思义，就是每个节点可以找到它之前的节点和之后的，节点是双向的
- `循环链表`
    循环链表，就是它可以一直向下，最后回到自己的那个节点，形成了一个回路。循环链表分为单链表和双链表，既一个是只能顺着一个方向走，另外一个是两个方向走

## 代码实现

```go
package main

import "fmt"

type linkNode struct {
	Prev, Next *linkNode
	Value      interface{}
}

type link struct {
	Head, Tail *linkNode
	Len        int
}

func (l *link) Push(node *linkNode) {
	if l.Len == 0 {
		l.Head = &linkNode{Value: node.Value}
		l.Tail = l.Head
		l.Head.Next = l.Tail
		l.Tail.Prev = l.Head
	} else {
		tail := l.Tail
		tail.Next = &linkNode{Prev: tail, Value: node.Value}
		l.Tail = tail.Next
	}
	l.Len++
}

func (l *link) Pop() *linkNode {
	if l.Len < 1 {
		return nil
	}
	head := l.Head
	if head.Next != nil {
		head.Next.Prev = nil
	}
	l.Head = head.Next
	l.Len--
	return head
}

func main() {
	var l = &link{}
	l.Push(&linkNode{Value: 1})
	l.Push(&linkNode{Value: 10})
	l.Push(&linkNode{Value: 3})
	l.Push(&linkNode{Value: 2})
	head := l.Head
	for head != nil {
		fmt.Printf("%d\n", head.Value)
		head = head.Next
	}
}
```
* 参考
    * [redis设计与实现](https://www.bookstack.cn/read/redisbook/f8fc3415f3c66c78.md)
    * [数据结构(golang)](https://www.bookstack.cn/read/hunterhug-goa.c/algorithm-link.md)