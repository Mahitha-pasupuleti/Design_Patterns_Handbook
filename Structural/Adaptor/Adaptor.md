# 🧩 Adapter Design Pattern

The **Adapter Design Pattern** allows two incompatible interfaces to work together. It acts as a **bridge** between a client that expects one type of interface and a service (or class) that provides a different one.

---

## 💡 Concept

- Suppose we have **Service1** that wants to communicate with **Service2**, but their interfaces are **not compatible**.  
- **Service1** can only send requests or data of **Type A**, while **Service2** only accepts **Type B**.  
- To solve this, we introduce an **Adapter** between them.  
- The Adapter **receives the request** from Service1 (Type A), **translates or converts** it into the format that Service2 understands (Type B), and **forwards it**.  
- This allows both services to **work together seamlessly**, without changing their existing code.

---

## 🧱 Roles in the Adapter Pattern

1. **Client (Service1)** → The component that wants to use another class’s functionality but expects a specific interface.  
2. **Adaptee (Service2)** → The existing class that has the functionality the client needs, but with an incompatible interface.  
3. **Adapter** → The middle layer that **implements the interface expected by the client** and internally uses the Adaptee’s methods, converting calls or data as necessary.

---

## ⚙️ Example Analogy

Think of a **power plug adapter**:
- Your laptop has a **Type A plug**.  
- The wall socket is **Type B**.  
- The **adapter** allows the Type A plug to fit and work with the Type B socket — neither the laptop nor the socket needs to be changed.

---

### Client only works with this format of data i.e. json
```java
interface IReports {
    // now takes the raw data string and returns JSON
    String getJsonData(String data);
}
```
### Adaptee only works with XML data
```java
class Adaptee {
    public String getXMLData(String jsonData) {
        // convert json data to xml data
        String xmlData = convertJsonToXML(jsonData);
        return xmlData;
    }
}
```
### Adapter takes in json data
```java
class Adapter implements IReports {
    private Adaptee adaptee;
    public Adapter(Adaptee a) {
        this.adaptee = e;
    }

    @Override
    public String getJsonData(String jsonData) {
        // get xml data from adaptee
        String xmlData = adaptee.getXMLData(jsonData);
        // convert xml data back to json data
        String jsonData = convertXMLDataToJson(xmlData);
        return jsonData;
    }
}
```
### Client - interacts with IReports only because only it can handle json data format
```java
class Client {
    public void getReport(IReports reports, String rawData) {
        reports.getJsonData(rawData);
    }
}
```
### Main
```java
IReports adapter = new Adapter(new Adaptee());
Client client = new Client();
client.getReport(adapter, "Alice:42");
```
