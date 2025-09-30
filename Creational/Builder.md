# Builder Design Pattern

This pattern is preferred when we are dealing with complex object creation which has so many parameters and we have the choice to choose parameters i.e some are compulsory, others optional, and some default.  

Instead of using standard class, parameters, getters, setters, constructors, and the `new` keyword to create objects directly, companies prefer using the **Builder Design Pattern** because of the versatility it provides.

---

## Issues Which Lead to Builder Pattern Creations

### 1. Telescopic Constructors / Constructor Overloading
When we have a lot of parameters in which some are optional, default, or compulsory, there are high chances we might create multiple constructors to deal with different object requirements.  

**Example: HTTPRequest object creation to make an HTTP Request**

```java
class HttpRequest {
    private String url;                     // required
    private String method;                  // required
    private Map<String, String> headers;
    private Map<String,String> queryParams;
    private String body;
    private int timeout;                    // required

    // Constructor with only required parameter (1-arg)
    public HttpRequest(String url) {
        this.url = url;
        this.method = "GET";       // Default method
        this.timeout = 30;         // Default timeout
        this.headers = new HashMap<>();
        this.queryParams = new HashMap<>();
    }

    // 2 - args
    public HttpRequest(String url, String method) {
        this.url = url;
        this.method = method;
        this.timeout = 30;
        this.headers = new HashMap<>();
        this.queryParams = new HashMap<>();
    }

    // 3 - args
    public HttpRequest(String url, String method, int timeout) {
        this.url = url;
        this.method = method;
        this.timeout = timeout;
        this.headers = new HashMap<>();
        this.queryParams = new HashMap<>();
    }

    // 4 - args
    public HttpRequest(String url, String method, int timeout, Map<String, String> headers) {
        this.url = url;
        this.method = method;
        this.timeout = timeout;
        this.headers = headers;
        this.queryParams = new HashMap<>();
    }

    // 5 - args
    public HttpRequest(String url, String method, int timeout,
                       Map<String, String> headers, Map<String,String> queryParams) {
        this.url = url;
        this.method = method;
        this.timeout = timeout;
        this.headers = headers;
        this.queryParams = queryParams;
    }

    // 6 - args
    public HttpRequest(String url, String method, int timeout,
                       Map<String, String> headers, Map<String,String> queryParams, String body) {
        this.url = url;
        this.method = method;
        this.timeout = timeout;
        this.headers = headers;
        this.queryParams = queryParams;
        this.body = body;
    }
}
```

### 2. Mutable Objects
Depending on use case, certain objects need to be immutable — you can’t change their values. But generic classes have both getters and setters.

So we need to avoid setters for the class. However, even if we remove all setters and make the object immutable, Issue 1 (Telescoping Constructors) still remains true.

On a high note, generic classes do not give us clean provisions to make an object immutable.
```java
// Setters (leads to mutable object)
    public void setUrl(String url) {
        this.url = url;
    }

    public void setMethod(String method) {
        this.method = method;
    }

    public void addHeader(String key, String value) {
        headers.put(key, value);
    }

    public void addQueryParam(String key, String value) {
        queryParams.put(key, value);
    }

    public void setBody(String body) {
        this.body = body;
    }

    public void setTimeout(int timeout) {
        this.timeout = timeout;
    }
```

### 3. Inconsistent state of object
Let’s say we need to make an HTTPRequest which has timeout and body parameters as compulsory. But we have the provision to make a call without body.

Because of our constructors, we cannot make these complex decisions (like whenever it is a POST call, we need to have a compulsory body).

We can do this, but remember it will be play-dough code. This is possible but the code will look very ugly — you need to keep validations everywhere. There is no clear way to avoid this.
```java
public class WithoutBuilder {
    public static void main(String[] args) {
        // Using constructors (telescoping constructor problem)
        HttpRequest request1 = new HttpRequest("https://api.example.com");
        HttpRequest request2 = new HttpRequest("https://api.example.com", "POST");
        HttpRequest request3 = new HttpRequest("https://api.example.com", "PUT", 60);

        // Using setters (mutable object problem)
        HttpRequest request4 = new HttpRequest("https://api.example.com");
        request4.setMethod("POST");
        request4.addHeader("Content-Type", "application/json");
        request4.addQueryParam("key", "12345");

        // request4.execute()  ----> Inconsitent state problem
        //  We can make this method call here even though we know POST requires a body, because we cannot stop this from happening here. And if proper validation
        // is not in place; this will cause a runtime error. But we always want a compile error to tell us what to do, not in runtime production we realize this

        request4.setBody("{\"name\": \"Aditya\"}");
        request4.setTimeout(60);

        // The problem: what if we forgot to set an important field?
        request4.execute();
    }
}
```

### 4. Scattered validation
Because of our above play-dough code, we need to keep validation almost everywhere. Otherwise, we will get runtime errors.

On a high note, generic classes and objects are not ideal for complex objects which have a lot of parameters and complex use cases to deal with (like HTTP request calls).
```java
// Method to execute the HTTP request -- so may validations
    public void execute() {
        System.out.println("Executing " + method + " request to " + url);

        if (!queryParams.isEmpty()) {
            System.out.println("Query Parameters:");
            for (Map.Entry<String,String> param : queryParams.entrySet()) {
                System.out.println("  " + param.getKey() + "=" + param.getValue());
            }
        }

        System.out.println("Headers:");
        for (Map.Entry<String,String> header : headers.entrySet()) {
            System.out.println("  " + header.getKey() + ": " + header.getValue());
        }

        if (body != null && !body.isEmpty()) {
            System.out.println("Body: " + body);
        }

        System.out.println("Timeout: " + timeout + " seconds");
        System.out.println("Request executed successfully!");
    }
```


