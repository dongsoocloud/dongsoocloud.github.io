---
title: "[Data Structure] Linked Lists"
date: 2025-01-18 21:30:00 +0900
categories: [Data Structure & Algorithms, Data Structure]
tags: [Data structure, Linked Lists]
---
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-2941907865454687"
     crossorigin="anonymous"></script>
![HitCount](http://hits.dwyl.com/dongsoocloud.github.io/posts/garbageCollector.svg)

## **What is Linked Lists?**

---

**Linked Lists(LLs)** is a data structure consisting of multiple node. Unlike an array-based lists, where elements are stored in contiguous memory locations, the elements of LLs can be sotred sporadically in memory. Each node(element) in LLs contains 2 parts: the data and a link that connects it to the next node in the list.

Due to this structure, accessing an element by its position (like with an index in an array) is not as straightforward in LLs, because you must traverse the list sequentially from the start to reach the desired element. However, LLs offer significant advantages when it comes to inserting or deleting elements. Since the elements are not stored contiguously, there is no need to shift subsequent elements, nor do you need to allocate a new underlying array when the list grows beyond its given capacity. (Of course, sometimes it requires more steps than arrays to remove the element depending on the position of target element you remove. ex, remove the last element)

<br/>

## **Comparision between Lists vs Linked Lists**

---

| method          | Lists | Linked Lists |
| --------------- | ----- | ------------ |
| Append          | O(1)  | O(1)         |
| Prepend         | O(n)  | O(1)         |
| Insert          | O(n)  | O(n)         |
| Pop             | O(1)  | **O(n)**     |
| Pop First       | O(n)  | O(1)         |
| Remove          | O(n)  | O(n)         |
| Lookup by Index | O(n)  | O(1)         |
| Lookup by Value | O(n)  | O(n)         |

<br/>

### **Code (Python)**

---

```python
#the Node class which contains each element
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

##the LinkedList class with 3 elements (head, tail, length)
class LinkedList:
    def __init__(self, value = None):
        self.head = None
        self.tail = None
        self.length = 0
        # When the LL is initiated with not-null value
        if value:
            new_node = Node(node)
            self.head = new_node
            self.tail = new_node
            self.length = 1

    #print each node value on a new line
    def print_list(self):
        current = self.current
        while current:
            print(current.value)
            current = current.next

    #lookup element by index
    def get(self, index) -> Node:
        #edge case
        if index < 0 or index >= self.length:
            return None
        #traverse the list and find the element at the given index
        current = self.head
        for _ in range(index):
            current = current.next
        return current

    #append the new node at the end of the list
    def append(self, value) -> bool:
        #initialize a new node
        new_node = Node(value)
        #case 1) the empty list.
        if self.length == 0:
            self.head = new_node
            self.tail = new_node
        #case 2) the list has at least one element
        else:
            #no need to traverse. Just link the new node to the current tail and assign tail to the new node.
            self.tail.next = new_node
            self.tail = new_node
        #increment length + 1
        self.length += 1
        #This is optional, but can be useful for other usages.
        return True





```

--

<script src="https://utteranc.es/client.js"
        repo="dongsoocloud/blog-comments"
        issue-term="title"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-2941907865454687"
     crossorigin="anonymous"></script>
<!-- <br/>

## **References**

---

<https://blogs.oracle.com/emeapartnerweblogic/using-apache-derby-database-with-weblogic-the-express-way-by-frank-munz>

<br/> -->
