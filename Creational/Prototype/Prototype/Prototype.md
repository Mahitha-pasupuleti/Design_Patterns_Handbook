# 🧬 Prototype Design Pattern

The **Prototype** is a **creational design pattern** that allows you to **create copies of existing objects** without depending on their exact classes.  
It’s especially useful when **object creation is expensive** — for example, when it involves complex initialization logic or database operations.

---

## 💡 Why Use the Prototype Pattern?
- Creating new objects from scratch can be **time-consuming** or **resource-intensive**.  
- If object construction involves **complex logic, database calls, or heavy computation**, cloning an existing object is much faster.  
- The Prototype pattern helps you **avoid re-executing setup logic** by duplicating an already prepared object.

---

## ⚙️ How It Works
1. Define an **interface or abstract class** (commonly called `Cloneable` or `Prototype`) with a single method: `clone()`.  
   - This serves as a **marker interface** — it signals that classes implementing it support cloning.  
2. Any class that implements this interface gains the ability to **clone its own objects**.  
3. The **copy constructor** or **custom `clone()` method** performs the actual duplication.  
4. The cloned objects are **nearly identical**, but can be **tweaked individually** afterward.  
   - If the cloned objects are entirely different in purpose, cloning no longer makes sense.

---

## 🎮 Example Scenario
Imagine a video game where you need to create **100 monsters** that share most of their attributes (health, attack, defense) but differ slightly in power or abilities.  
Instead of constructing each monster from scratch, you can:
1. Create one **template monster**.
2. **Clone** it multiple times.
3. Modify specific attributes for variation.

This saves both **time** and **computational cost**.

---

## 🪞 Shallow Copy
When cloning, certain fields (like lists, maps, or object references) may be **copied by reference** instead of by value.  
This is called a **shallow copy**, meaning both the original and cloned objects **share the same referenced data** in memory.  
As a result, changing one may unintentionally affect the other.

---

## 🧱 Deep Copy
To avoid shared references, a **deep copy** should be used.  
A deep copy ensures that:
- Both objects have **independent copies** of all referenced data.
- Modifying one clone does **not** affect the original.  

This is especially important when your object contains **mutable fields** such as collections or other class instances.

```java
interface Cloneable {
  Cloneable clone();
}
```

```java
class NPC implements Cloneable {
  String name;
  String health;
  String attack;
  String defense;

  public NPC(String name, String health, String attack, String defense) {
    this.name = name;
    this.health = health;
    this.attack = attack;
    this.defense = defense;
  }
}
```

```java
  // Copy Constructor
  public NPC(NPC other) {
    this.name = other.name;
    this.health = other.health;
    this.attack = other.attack;
    this.defense = other.defense;
  }

  // the clone method required by Prototype
  @Override
  public NPC clone() {
      return new NPC(this);
  }
```

```java
  NPC alien = new NPC("Alien", 30, 5, 2);

  NPC alienCopy1 = (NPC) alien.clone();
  NPC alienCopy2 = (NPC) alien.clone();

  alienCopy1.setName("POwerAlien");
```

```java
  // cleanup
  alien = null;
  alienCopy1 = null;
  alienCopy2 = null;
```
