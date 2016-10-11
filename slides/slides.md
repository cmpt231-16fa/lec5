<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
# CMPT231
## Lecture 5: ch10, 12
### Dynamic Data Structures:
### Linked Lists and Binary Search Trees

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
## Devotional

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
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
+ Read from **memory location** with "*dereference*" operator

```
int myAge = 20;
int* myAgePtr = &myAge;     // get address of myAge
cout << *myAgePtr;          // dereference: prints "20"
```

Pointer **arithmetic** can be dangerous!

```
*( myAgePtr + 1 );          // segmentation fault!
*( 1033813 );               // random spot in memory!
```

---
## Pointer-less languages
+ To prevent **segfaults**, most languages (besides C/C++)
  <br/> do **not** have explicit pointers
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

<div class="imgbox"><div>
![doubly-linked list](static/img/Fig-10-3a.svg)
</div></div>

---
## Node vs LinkedList
+ Create a wrapper **datatype** for the overall **list**
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
  + e.g., store **length** of list, <br/>
    and **track** how many nodes we've traversed
  + or, append a **sentinel** node with special key
+ Circularly-linked lists can also be **doubly**-linked
  + **Both** *.prev* and *.next* pointers wrap around

<div class="imgbox"><div>
![Circular doubly-linked list](static/img/Fig-10-4b.svg)
</div></div>

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
+ **Stack** ("LIFO"): last-in-first-out *(memo spike)*
+ **Queue** ("FIFO"): first-in-first-out *(pipeline)*
+ **Interface** (defining set of operations):
  + `length()`, `isempty()`: **number** of items
  + `push(item)`: **add** item to stack/queue
  + `peek()`: get item **without** deleting
  + `pop()`: peek and **delete** item
+ **Underflow**: peek/pop on an **empty** stack/queue
+ **Overflow**: push on a **full** stack/queue

---
## Implementing stacks/queues
+ Abstract data type (ADT): only specifies **interface**
+ Can be **implemented** using arrays, linked-lists, or other
  + Memory usage, computational efficiency **trade-offs**

```
class Stack:
  def __init__( self ):
    self.head = None
  def push( self, key ):            # overflow not a worry
    self.head = Node( key, self.head )
  def pop( self ):                  # watch for underflow!
    item = self.head
    key = item.key
    self.head = self.head.next
    del item
    return key
```

---
## Outline

---
## Implementing trees
+ For **Binary** trees, use *3* pointers:
  + **Parent**, **left** child, **right** child
+ For **d-way** trees (unknown **degree** *d*):
  + **Parent**, **first** child, **next** sibling

<div class="imgbox"><div>
![binary tree](static/img/Fig-10-9.svg)
</div><div>
![d-way tree](static/img/Fig-10-10.svg)
</div></div>

---
## Special trees for fast search
+ Impose additional **constraints** on the tree
+ Optimise for fast **search** and insert/delete:
  + Run in &Theta;(height of tree): if **full**, this is *&Theta;(lg n)*
+ Can be used to implement a **dictionary** or **priority queue**
+ Various **types**:
  + **Binary search** tree (BST) *(ch12)*,
  + **Red-black** tree *(ch13)*
  + **B-tree** *(ch18)*
  + and many others!

---
## Binary search trees (BST)
+ **BST property**: at any node *x* in the tree,
  + *y* &le; *x* &forall; nodes *y* in *x*'s **left** sub-tree
  + *y* &ge; *x* &forall; nodes *y* in *x*'s **right** sub-tree

<div class="imgbox"><div>
![BST with 6 nodes](static/img/bst-538.svg)
</div></div>

---
## Tree traversals
+ Walk through tree, **printing** out each node
+ **Preorder**: print **self** before children
  + Sample output (prev slide): `5 3 2 4 5 8`

```
def preorder( node ):
  print node.key
  preorder( node.left )
  preorder( node.right )
```

+ **Postorder**: print both **children** before self
  + Output?  Pseudocode?
+ **Inorder**: print **left** child, then **self**, then **right**
  + Output?  Pseudocode?
+ Which is useful on a tree with the **BST** property?

---
## Expression trees
+ Trees are used for parsing and evaluating **expressions**:
  + e.g., tree for `( 2 * (-4) ) + 9`
  + Which **traversal** produces this expression?
  + **Try it**: draw tree for `2 * (-4 + 9)`
+ Reverse Polish Notation (**RPN**):
  + e.g., `2, 4, -, *, 9, +`
  + Which **traversal** produces RPN?
+ Can implement an RPN **calculator** using a **stack**
  + **Try it** on the above expression

---
## Outline

---
## Searching a BST
+ Compare with node's **key** to see which **subtree** to recurse
+ **Complexity**: O(height of tree): if **full**, this is *O(lg n)*
  + **Worst-case**: tree degenerates to **linked-list**!
  + Want to keep tree **balanced**

```
def search( node, key ):
  if (node == None) or (node.key == key):
    return node
  if key < node.key:
    return search( node.left, key )
  else:
    return search( node.right, key )
```

---
## Min/max in BST
+ `min()`: find the **smallest** key:
  + Keep taking **left** child as far as possible
+ Similarly for `max()`
+ **Iterative** solution faster than **recursive**:

```
def min( node ):
  while node.left != None:
    node = node.left
  return node.key
```

---
## Successor / predecessor
+ **Successor** of a node is **next** in line in an **in-order** traversal
  + **Predecessor**: previous in line
+ If **right** subtree is **not** NULL:
  + Successor is **min** of right subtree
+ Else, walk **up** tree until a parent link turns **right**:

<div class="imgbox"><div data-markdown style="flex:2">

```
def successor( node ):
  if node.right != NULL:
    return min( node.right )
  (cur, par) = (node, node.parent)
  while (par != NULL) and (cur == par.right):
    (cur, par) = (par, par.parent)
  return par
```

</div><div>
![big BST](static/img/Fig-12-2.svg)
</div></div>

**Try it**: succ( *7* ), pred( *6* ), succ( *13* )

>>>

---
## Insert into BST
**Search** to find where to add node:

<div class="imgbox"><div data-markdown style="flex:2">

```
def insert( root, key ):
  cur = root
  while cur != NULL:
    if key < cur.key:           # go left
      if cur.left == NULL:
        cur.left = new Node( key )
        cur.left.parent = cur
        return
      cur = cur.left
    else:                       # go right
      if cur.right == NULL:
        cur.right = new Node( key )
        cur.right.parent = cur
        return
      cur = cur.right
```

</div><div>
![big BST](static/img/Fig-12-2.svg)
</div></div>

**Try it**: insert( *5* )

---
## Delete from BST
+ Deleting a **leaf** is easy (update links)
+ If node has **one** child, **promote** it in place of node
  + Bring child's **subtrees** along
+ If **two** children, replace it with its **successor**
  + In **right** subtree, with **no** left child *(why?)*
  + Need some **splicing**

![12-4b, one child](static/img/Fig-12-4b.svg)
<!-- .element: style="width:60%" -->

---
## Delete node with two children

<div class="imgbox"><div data-markdown>

+ If successor *y* is the **right child** of the node to be deleted *z*:
  + Just **promote** it (successor has no left child)

</div><div>
![12-4c, direct successor](static/img/Fig-12-4c.svg)
</div></div>

+ Else, replace successor *y* with successor's own right child *x*
  + Replace node to be deleted *z* with successor *y*
  + *z*'s old right subtree becomes *y*'s right subtree

![12-4d, descendant successor](static/img/Fig-12-4d.svg)
<!-- .element: style="width:75%" -->

---
## Randomly-built BST
+ How to **build** a BST from a set of *n* distinct keys?
+ **Order** of insertion matters!
+ Worst-case, BST becomes a **linked list**
  + **Search** (and hence insert, delete, etc.) is *&Theta;(n)*
+ Try a (Fisher-Yates) **shuffle** of the keys first
  + Each of the *n!* **permutations** is equally likely
  + **Doesn't** mean each **BST** is equally likely *(try it: n=3)*
+ Expected (average) **height** of random BST is *&Theta;(lg n)*
  + **Proof** in textbook

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" -->
## Outline

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-DiKkJKvDi64-tree_road.jpg" class="empty" -->
