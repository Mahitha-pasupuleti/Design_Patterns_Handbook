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


## Initial HttpRequest class
```java
public class HttpRequest {
    private String url;
    private String method;
    private Map<String, String> headers;
    private Map<String, String> queryParams;
    private String body;
    private int timeout; // in seconds

    // Private constructor - can only be accessed by the Builder
    private HttpRequest() {
        headers = new HashMap<>();
        queryParams = new HashMap<>();
        body = "";
    }

    // Method to execute the HTTP request
    public void execute() {
        System.out.println("Executing " + method + " request to " + url);

        if (!queryParams.isEmpty()) {
            System.out.println("Query Parameters:");
            for (Map.Entry<String, String> param : queryParams.entrySet()) {
                System.out.println("  " + param.getKey() + "=" + param.getValue());
            }
        }

        System.out.println("Headers:");
        for (Map.Entry<String, String> header : headers.entrySet()) {
            System.out.println("  " + header.getKey() + ": " + header.getValue());
        }

        if (!body.isEmpty()) {
            System.out.println("Body: " + body);
        }

        System.out.println("Timeout: " + timeout + " seconds");
        System.out.println("Request executed successfully!");
    }
```

## Step-by-Step Interfaces
## Why Use Interfaces in Step Builder?

In the **Step Builder Pattern**, we use interfaces for each step.  

### Benefits:

1. **Order Enforcement**
   - Each step interface defines exactly what the *next valid step* is.
   - For example:
     - `UrlStep` → must return a `MethodStep`
     - `MethodStep` → must return a `HeaderStep`
     - `HeaderStep` → must return an `OptionalStep`

2. **Multiple Inheritance**
   - Java classes cannot inherit from multiple classes, but **can implement multiple interfaces**.
   - This allows the **Builder class** to implement all step interfaces at once, so it can provide the actual implementation for every step.

```java
## Step Interfaces

```java
// Step 1: Define the sequence of building steps using nested interfaces
interface UrlStep {
    MethodStep withUrl(String url);
    // Adds URL to the HttpRequest object and moves to the next step (MethodStep)
}

interface MethodStep {
    HeaderStep withMethod(String method);
    // Adds HTTP method and moves to the next step (HeaderStep)
}

interface HeaderStep {
    OptionalStep withHeader(String key, String value);
    // Adds a header and moves to OptionalStep
}

interface OptionalStep {
    OptionalStep withBody(String body);      // optional
    OptionalStep withTimeout(int timeout);   // optional
    HttpRequest build();                     // mandatory final step
}
```

# Step Builder Implementation – HttpRequest

Below is a **step-by-step explanation** of implementing a Step Builder for `HttpRequest` in Java.

---

## 1. Implementing the Methods in Order

The **Builder class** implements all step interfaces (`UrlStep`, `MethodStep`, `HeaderStep`, `OptionalStep`) and overrides methods to build the object step by step.

```java
// Concrete Step Builder as static nested class
static class HttpRequestStepBuilder implements UrlStep, MethodStep, HeaderStep, OptionalStep {
    private HttpRequest req;

    private HttpRequestStepBuilder() {
        req = new HttpRequest();
    }

    // UrlStep implementation
    public MethodStep withUrl(String url) {
        req.url = url;
        return this;
    }

    // MethodStep implementation
    public HeaderStep withMethod(String method) {
        req.method = method;
        return this;
    }

    // HeaderStep implementation
    public OptionalStep withHeader(String key, String value) {
        req.headers.put(key, value);
        return this;
    }
}
```

## Optional Step Implementation
After the required fields, optional fields can be set in any order before calling build().
```java
// OptionalStep implementation
public OptionalStep withBody(String body) {
    req.body = body;
    return this;
}

public OptionalStep withTimeout(int timeout) {
    req.timeout = timeout;
    return this;
}
```

## Build Method
The build() method finalizes the object. You can optionally include runtime validation if needed.
```java
public HttpRequest build() {
    if (req.url == null || req.url.isEmpty()) {
        throw new RuntimeException("URL cannot be empty");
    }
    return req;
}
```

## Starting the Build Process

Whenever the builder is invoked, an object of type `HttpRequestStepBuilder` is created internally.  

However, the **returned type** is the **first step interface (`UrlStep`)**.  

This ensures that:

- The client can **only access the first step** (`withUrl()`) initially.
- Subsequent steps become available **only after completing the previous step**, enforcing the correct build sequence.
- It prevents the client from skipping required steps or calling methods out of order.

```java
// Static method to start the building process
      public static UrlStep getBuilder() {
          return new HttpRequestStepBuilder();
      }
    }
}
```
