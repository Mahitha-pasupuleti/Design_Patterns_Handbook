# Step Builder
# Why Do We Need Step Builder?

In many cases, we want to build objects **step by step**, in a defined sequence.  
Other builder patterns exist, but they don’t enforce order or required fields as clearly.  
This is where the **Step Builder Pattern** is useful.

---

## Example 1: HTTP Request Builder
We may want to enforce the order of method calls:

1. `withURL()` → must be called first  
2. `withMethod()` → must be called second  
3. `withBody()` → must be called third  

This guarantees that an HTTP request is constructed in a **valid and predictable order**.

---

## Example 2: Pizza Builder
Similarly, when building a pizza:

1. **Crust** → must be chosen first  
2. **Pizza Sauce** → must be chosen second  
3. **Toppings** → chosen last  

This ensures that required components are always set before moving to optional ones.

---

## Why Step Builder?
- Enforces **build order** (no skipping steps or calling in the wrong sequence).  
- Clearly separates **required vs. optional fields**.  
- Reduces the need for runtime validation since correctness is enforced at compile time.  

With a Step Builder, required values are **segregated from optional values**, so you can’t accidentally build an invalid object.

---

## Key Benefits
- No need for extra validation like in traditional builders.  
- Compiler ensures correct usage.  
- Code becomes **self-documenting** (you know what comes next in the chain).  

