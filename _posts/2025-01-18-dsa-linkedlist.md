---
title: "[Data Structure] Linked Lists"
date: 2025-01-18 21:30:00 +0900
categories: [Data Structure & Algorithms, Data Structure]
tags: [Data structure, Linked Lists]
---

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


```

---

<!-- <br/>

## **References**

---

<https://blogs.oracle.com/emeapartnerweblogic/using-apache-derby-database-with-weblogic-the-express-way-by-frank-munz>

<br/> -->
