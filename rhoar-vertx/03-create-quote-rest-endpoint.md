# The quote REST endpoint

Open the `RestQuoteAPIVerticle`. This verticle exposes a HTTP endpoint to retrieve the current / last values of the maker data (quotes). In the `start` method you need to:

* Register an event bus consumer to collect the last quotations (in the `quotes` map)

* Handle HTTP requests to return the list of quotes, or a single quote if the `name` (query) param is set.

Let’s do that…​.


**1. Task - Implementing a Handler to receive events**

The first action is about creating a `Handler`, i.e. a method that is invoked on event. 

In this task, the `Handler` is going to be called for each message sent on the event bus on a specific `address` (receiving each quote sent by the generator). It’s a message consumer. The `message` parameter is the received message.

Implement the logic that retrieve the body of the message (with the body() method. Then extract from the body the name of the quote and add an entry name → quote in the quotes map.

Open the file in the editor: ``quote-generator/src/main/java/io/vertx/workshop/quote/RestQuoteAPIVerticle.java``{{open}}
Then, copy the below content into the file at the ``//TODO: insert quotes`` (or use the `Copy to Editor` button):
      
<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/RestQuoteAPIVerticle.java" data-target="insert" data-marker="//TODO: insert quotes">
JsonObject quote = message.body(); // 1
quotes.put(quote.getString("name"), quote); // 2
</pre>

``First, it retrieves the message body (1). It’s a JSON object, and stores it in the quotes map (2).``

**2. Task - Implementing a Handler to handle HTTP requests**

Now that you did a first `Handler`, let’s do a second one. This handler does not receive messages from the event bus, but HTTP requests. The handler is attached to a HTTP server and is called every time there is a HTTP request to the server, the handler is called and is responsible for writing the response.

To handle the HTTP requests, we need a HTTP server. Fortunately, Vert.x lets you create HTTP servers using:
```java
vertx.createHttpServer()
    .requestHandler(request -> {...})
    .listen(port, resultHandler);
```

Write the content of the request handler to respond to the request:

1. a response with the content-type header set to application/json

2. retrieve the name parameter (it’s the company name)

3. if the company name is not set, return all the quotes as json.

4. if the company name is set, return the stored quote or a 404 response if the company is unknown

* The response to a request is accessible using request.response()
* To write the response use response.end(content).
* To create the JSON representation of an object, you can use the Json.encode method

<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/RestQuoteAPIVerticle.java" data-target="insert" data-marker="//TODO: return quotes">
String company = request.getParam("name");          
if (company == null) {
    String content = Json.encodePrettily(quotes);   
    response
        .end(content);                              
 } else {
    JsonObject quote = quotes.get(company);
    if (quote == null) {
      response.setStatusCode(404).end();            
    } else {
      response.end(quote.encodePrettily());
    }
 }
</pre>

1. Get the response object from the request
2. Gets the name parameter (query parameter)
3. Encode the map to JSON
4. Write the response and flush it using end(…​)
5. If the given name does not match a company, set the status code to 404

You may wonder why synchronization is not required. Indeed we write in the map and read from it without any synchronization constructs. Here is one of the main feature of Vert.x: all this code is going to be executed by the same event loop, so it’s always accessed by the same thread, never concurrently.

**3. Test our changes**

First, let’s build the microservice. In the terminal, execute:

``cd quote-generator``{{execute}}

``mvn vertx:run -DskipTests``{{execute}}

This command launches the application. The main class we used creates a clustered Vert.x instance and reads the configuration from src/conf/config.json. This configuration provides the HTTP port on which the REST service is published (8080).

**3. Test the static router**

Click on the [this](https://[[HOST_SUBDOMAIN]]-35000-[[KATACODA_HOST]].environments.katacoda.com/) link, which will open another tab or window of your browser pointing to port 35000 on your client.

You should now see an HTML page that looks like this:

```json

{
  "MacroHard" : {
    "volume" : 100000,
    "shares" : 51351,
    "symbol" : "MCH",
    "name" : "MacroHard",
    "ask" : 655.0,
    "bid" : 666.0,
    "open" : 600.0
  },
  "Black Coat" : {
    "volume" : 90000,
    "shares" : 45889,
    "symbol" : "BCT",
    "name" : "Black Coat",
    "ask" : 654.0,
    "bid" : 641.0,
    "open" : 300.0
  },
  "Divinator" : {
    "volume" : 500000,
    "shares" : 251415,
    "symbol" : "DVN",
    "name" : "Divinator",
    "ask" : 877.0,
    "bid" : 868.0,
    "open" : 800.0
  }
}
```
**4. Stop the application**

Before moving on, click in the terminal window and then press CTRL-C to stop the running application!

##Congratulations!
You have seen the basics of Vert.x development including Asynchronous API and AsyncResult, implementing Handler and receiving messages from the event bus

In next step of this scenario we will deploy our application to the OpenShift Container Platform.