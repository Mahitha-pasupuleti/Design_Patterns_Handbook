# 🏗️ Director Builder
- Some objects are **repeatedly created** and often share the same configuration. These are referred to as **Default Objects**.  
- Instead of manually creating them each time, we can **predefine default objects** that the user can directly use.  
- The system provides **two options** for the user:  
  1. **Default Objects** – commonly used configurations ready for use.  
  2. **Manual Objects** – users can build objects from scratch using the Builder.   

## Director Builder class
- Default objects should be made available through **static methods**, so there is **no need to create an instance** of the `HttpRequestDirector` class each time.

```java
public class HttpRequestDirector {
    public static HttpRequest createGetRequest(String url) {
        return new HttpRequest.HttpRequestBuilder()
                .withUrl(url)
                .withMethod("GET")
                .build();
    }

    // Creates a JSON POST request
    public static HttpRequest createJsonPostRequest(String url, String jsonBody) {
        return new HttpRequest.HttpRequestBuilder()
            .withUrl(url)
            .withMethod("POST")
            .withHeader("Content-Type", "application/json")
            .withHeader("Accept", "application/json")
            .withBody(jsonBody)
            .build();
    }
}
```

## Main Class
```java
public class Main {
    public static void main(String[] args) {
        // Normal Request from Builder Directly
        HttpRequest normalRequest = new HttpRequest.HttpRequestBuilder()
            .withUrl("https://api.example.com")
            .withMethod("POST")
            .withHeader("Content-Type", "application/json")
            .withHeader("Accept", "application/json")
            .withQueryParams("key", "12345")
            .withBody("{\"name\": \"Aditya\"}")
            .withTimeout(60)
            .build();

        normalRequest.execute(); // Guaranteed to be in a consistent state

        System.out.println("\n----------------------------\n");

        HttpRequest getRequest = HttpRequestDirector.createGetRequest("https://api.example.com/users");
        getRequest.execute();

        System.out.println("\n----------------------------\n");

        HttpRequest postRequest = HttpRequestDirector.createJsonPostRequest(
            "https://api.example.com/users",
            "{\"name\": \"Aditya\", \"email\": \"aditya@example.com\"}");
        postRequest.execute();
    }
}
```
