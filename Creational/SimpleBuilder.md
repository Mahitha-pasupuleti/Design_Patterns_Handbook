# 🏗️ Simple Builder
The challenges of using generic classes for object creation can be addressed by applying the **Builder Design Pattern**.  

## Restrict Direct Creation of HttpRequest Objects
- The main class **HttpRequest** has a **private constructor**, so nobody can create an object directly.  
- Only the **HttpRequestBuilder** class should be used to create an **HttpRequest** object.  
- The **HttpRequestBuilder** class has direct access to all the parameters of the **HttpRequest** class.  
- The **HttpRequestBuilder** exists as a **nested class** inside the **HttpRequest** class.  


```java
public class HttpRequest {
    private String url;
    private String method;
    private Map<String, String> headers;
    private Map<String,String> queryParams;
    private String body;
    private int timeout; // in seconds

// Private constructor - can only be accessed by the Builder
    HttpRequest() {
        headers = new HashMap<>();
        queryParams = new HashMap<>();
        body = "";
    }
```

## Method to just display content
```java
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

        if (body != null && !body.isEmpty()) {
            System.out.println("Body: " + body);
        }

        System.out.println("Timeout: " + timeout + " seconds");
        System.out.println("Request executed successfully!");
    }
```

## The Main Builder Class
- By not exposing the **HttpRequest** object directly, this class allows controlled construction by adding only the desired parameters.  
- Each method returns the current **HttpRequestBuilder** reference, enabling method chaining to add parameters step by step.  
- Internally, these methods modify the same underlying **HttpRequest** object.  
- All validation logic, which would otherwise be scattered across different methods, can now be centralized inside the **build()** method before returning the final **HttpRequest** object to the client.  

```java
// Builder class as a nested class to access private members

    public static class HttpRequestBuilder {
        private HttpRequest req;         // instance variable

        public HttpRequestBuilder() {    // creates HttpRequest object internally in the constructor, but won't return it until .build() is executed
            req = new HttpRequest();
        }

        // Method chaining
        public HttpRequestBuilder withUrl(String u) {
            req.url = u;          // adds url value to internally created HttpRequest object
            return this;          // return current HttpRequestBuilder reference not HttpRequest reference
        }

        public HttpRequestBuilder withMethod(String method) {
            req.method = method;
            return this;
        }

        public HttpRequestBuilder withHeader(String key, String value) {
            req.headers.put(key, value);
            return this;
        }

        public HttpRequestBuilder withQueryParams(String key, String value) {
            req.queryParams.put(key, value);
            return this;
        }

        public HttpRequestBuilder withBody(String body) {
            req.body = body;
            return this;
        }

        public HttpRequestBuilder withTimeout(int timeout) {
            req.timeout = timeout;
            return this;
        }

        // Build method to create the immutable HttpRequest object
        public HttpRequest build() {
            // Validation logic can be added here
            // All validations can be done in the build() method
            // In case u want to make body as mandatory in a post method. You can validate it here

            if (req.url == null || req.url.isEmpty()) {
                throw new RuntimeException("URL cannot be empty");
            }
            return req;  // Finally HttpRequest Object returned
        }
    }
```

## Main class
```java
public class Main {
    public static void main(String[] args) {
        // Using Builder Pattern (nested class)
        HttpRequest request = new HttpRequest.HttpRequestBuilder()
            .withUrl("https://api.example2.com")
            .withMethod("POST")
            .withHeader("Content-Type", "application/json")
            .withHeader("Accept", "application/json")
            .withQueryParams("key", "12345")
            .withBody("{\"name\": \"Aditya\"}")
            .withTimeout(60)
            .build();

        request.execute(); // Guaranteed to be in a consistent state
    }
}
```

# Issues Being Solved

## Inconsistent State
- The Builder Pattern prevents the **inconsistent state problem**, where a user might create and use an `HttpRequest` object without supplying all required parameters.  
- (See `request4` in `builder.md` for an example of this issue.)  
- This is resolved by introducing the **`.build()`** method. The `HttpRequest` object cannot be accessed until `.build()` is executed.  
- The `.build()` method enforces **validation** and throws an error if the request is incomplete or invalid.  

## Mutable Object
- The `HttpRequest` is designed to be **immutable**.  
- No setters are provided in the code. After `.build()` returns the `HttpRequest` object, its values cannot be modified directly.  
- Fields are private, and only getters (if provided) allow read-only access.  
- If you need to change values, you must create a **new object** via the Builder.  

## Scattered Validation
- Traditionally, each constructor required its own independent validation logic depending on the type of request.  
- With the Builder Pattern, this is centralized.  
- All validation logic resides in the **`.build()`** method, which ensures correctness before returning the final `HttpRequest` object.  

