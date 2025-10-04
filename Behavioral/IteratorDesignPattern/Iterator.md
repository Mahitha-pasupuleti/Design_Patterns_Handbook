# Iterator Pattern

## What is the Iterator Pattern?
The **Iterator Pattern** provides a way to **access elements of a collection** (array, list, tree, graph, etc.) **without exposing the internal representation** of that collection.

Instead of worrying *how* the data is stored (Array? LinkedList? Tree?), we get a simple and consistent way to **traverse** it:
- `hasNext()` → Do we have more elements?
- `next()` → Give me the next element.

---

## Why Do We Need It?
Different data structures have different traversal logic:
- Array → index-based loop
- Linked List → pointer traversal
- Tree → DFS / BFS
- Graph → adjacency traversal

If the traversal logic is tied directly to the data structure:
- Changing the internal structure (Array → LinkedList) means rewriting traversal everywhere.
- This makes the code rigid, error-prone, and difficult to maintain.

👉 **Iterator solves this by separating traversal from storage.**

---

## Real-World Analogy
Think of a **music playlist**:
- Today, songs are stored in an Array.
- Tomorrow, songs are stored in a LinkedList.
- Next week, songs are stored in a Graph (e.g., recommended songs network).

Without Iterator → traversal code must change everywhere.  
With Iterator → you just swap the iterator; the client code still calls:
```java
while (iterator.hasNext()) {
    Song s = iterator.next();
    play(s);
}
```

<img src="" >

