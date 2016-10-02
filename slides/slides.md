<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
# CMPT231
## Lecture 5: ch10, 12
### Dynamic Data Structures:
### Linked Lists and Binary Search Trees

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
## Devotional

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
## Outline for today
+ Dynamic data structures: review of **pointers**
+ **Linked lists**
  + Variants: **doubly**-linked, **circular**
+ **Stacks** and **queues**
+ **Trees**
  + Binary search trees (**BST**)
  + Tree **traversal**
  + **Searching**
  + **Min**/max and **successor**/predecessor
  + **Insert** and **delete**

---
## Stack frame vs heap
+ Where are program **variables** stored in memory?
  + **Static** vars + formal **parameters** &rArr; **stack frame**
    + Size known at **compile** time
  + **Local** vars dynamically allocated &rArr; **heap**
    + Typically **deallocated** when program/function exits
+ A **pointer** is a var whose **value** <br/>
  is a memory **location** in the heap
  + Usually has its own **type** (e.g., "*pointer to float*")
  + But really is just an **unsigned int** itself!

---
## Using pointers
+ **Declare** a var of type "*pointer to int*" (or other type)
+ Get **address** of a var by using "*address-of*" operator
+ Read **memory location** pointed to by using "*dereference*" operator

```
int myAge = 20;
int* myAgePtr = &myAge;     // get address of myAge
cout << *myAgePtr;          // prints "20"
```

Pointer **arithmetic** can be dangerous!

```
*( myAgePtr + 1 );          // segmentation fault!
*( 1033813 );               // random spot in memory!
```

---
## Pointer-less languages
+ To prevent **segfaults**, most languages (besides C/C++)
  do **not** have explicit pointers
+ Instead, you create **references** ("aliases")
  + Variables are simply entries in a **namespace**
  + Map an **identifier** to a **location** in the heap
  + **Multiple** identifiers can map to same location
+ Be aware of when a **reference** is made vs a **copy**!

```
ages = [ 3, 5, 7, 9 ]       # Python list (mutable)
myAges = ages               # create alias
myAges[ 2 ] = 11            # overwrite 7
ages                        # [ 3, 5, 11, 9 ]
```

---
## Outline

---
## Linked lists
+ Linear, **array-like** data structure, but:
+ **Dynamic**: can change length (not fixed at compile time)
+ Fast mid-list **insert** / delete (vs **shifting**)
+ But **random** access slower than array

```
class Node:
  def __init__( self, key=None, next=None ):
    ( self.key, self.next ) = ( key, next )

head = Node( 9 )
head = Node( 7, head )
head = Node( 5, head )
head = Node( 3, head )
```

>>>
TODO: diagram of list

---
## Doubly-linked lists
+ Track **both** *.prev* and *.next* pointers in each node
+ Allows us to move forwards and **backwards** through list

```
class Node:
  def __init__( self, key=None, prev=None, next=None ):
    self.key = key
    self.prev = prev
    self.next = next
```

---
## Node vs LinkedList
+ Create a separate **datatype** for the overall **list**
+ Keep both **head** and **tail** pointers
  + So we can **jump** directly to either end of the list

```
class LinkedList:
  def __init__( self, head=None, tail=None ):
    ( self.head, self.tail ) = ( head, tail )

x = LinkedList( Node( 3 ), Node( 5 ) )
x.head.next = x.tail
x.tail.prev = x.head
```

What **result** does this code produce?

---
## Circularly-linked lists
+ *.next* pointer of **tail** node wraps back to **head**
+ When **traversing** list, ensure not to **circle** forever!
  + e.g., store **length** of list, and track how many nodes we've traversed
  + or, append a **sentinel** node with special key
+ Circularly-linked lists can also be **doubly**-linked
  + **Both** *.prev* and *.next* pointers wrap around

>>>
TODO: diagram

---
## Insert into linked list
+ **Create** new node with the given *key*
  + and prepend to the **head** of the list
+ Also called **push** since it only inserts at **head**

```
class LinkedList:
  def insert( self, key=None ):
    self.head = Node( key, self.head )
```

**Try it**: insert *3* into list `[5, 7, 9]`

---
## Search on linked list
+ Return a **reference** to a node with the given *key*
  + Or return *None* if key not found in list

```
class LinkedList:
  def search( self, key ):
    cur = self.head
    while cur != None:
      if cur.key == key:
        return cur
      cur = cur.next
    return None
```

**Try it**: search for *7* in list `[3, 5, 7, 9]`

---
## Delete from linked list
+ **Splice** a referenced node out of the list
  + If given *key* instead of pointer, just **search** first
+ **Update** *.prev*/*.next* links in neighbouring nodes
  + So they **skip** over the deleted node
+ **Free** the unused memory so it can be reused
  + **Garbage**: allocated but unused/unreachable memory
  + **Memory leak**: heap grows indefinitely

```
class LinkedList:
  def delete( self, key ):
    node = self.search( key )
    node.prev.next = node.next
    node.next.prev = node.prev
    del node
```

---
## Outline

---
## Stacks and queues

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
## Outline

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" class="empty" -->
