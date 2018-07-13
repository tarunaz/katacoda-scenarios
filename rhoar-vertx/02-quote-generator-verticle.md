# Verticle

**Build the application**

``mvn clean install -DskipTests``{{execute}}

**1. Understanding the Application**

As you may have noticed, the code is structured in 3 `verticles`, but what are these? Verticles is a way to structure Vert.x application code. It’s not mandatory, but it is quite convenient. A verticle is a chunk of code that is deployed on top of a Vert.x instance. A verticle has access to the instance of `vertx` on which it’s deployed, and can deploy other verticles.

Open the file by clicking on the link below and look at the `start` method
``quote-generator/src/main/java/io/vertx/workshop/quote/GeneratorConfigVerticle.java``{{open}}

As you review the content, you will notice that there are 3 TODO comments. Do not remove them! These comments are used as a marker and without them, you will not be able to finish this scenario.

Verticles can retrieve a configuration using the `config()` method. Here it gets the details about the companies to simulate. The configuration is a `JsonObject`. Vert.x heavily uses JSON, so you are going to see a lot of JSON in this lab. For each company found in the configuration, it deploys the market data verticle with the extracted configuration. 

Copy the below content into the file at ``//TODO: market data`` (or use the `Copy to Editor` button):
      
<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/GeneratorConfigVerticle.java" data-target="insert" data-marker="//TODO: market data">
vertx.deployVerticle(MarketDataVerticle.class.getName(),
   new DeploymentOptions().setConfig(company));
</pre>

The next part in the method is about the service discovery mentioned in the microservice section. This component generates quotes sent on the event bus. But to let other components discover where the messages are sent (where means on which address), it registers it. market-data is the name of the service, ADDRESS is the event bus address on which the messages are sent. The last argument is a Handler that is notified when the registration has been completed. The handler receives a structure called AsyncResult.

Copy the below content into the file at ``//TODO: service discovery`` (or use the `Copy to Editor` button):

<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/GeneratorConfigVerticle.java" data-target="insert" data-marker="//TODO: service discovery">
publishMessageSource("market-data", ADDRESS, rec -> {
      if (!rec.succeeded()) {
        rec.cause().printStackTrace();
      }
      System.out.println("Market-Data service published : " + rec.succeeded());
    });
</pre>

Finally, it deploys another verticle providing a very simple HTTP API.

Copy the below content into the file at ``//TODO: http endpoint`` (or use the `Copy to Editor` button):

<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/GeneratorConfigVerticle.java" data-target="insert" data-marker="//TODO: http endpoint">
publishHttpEndpoint("quotes", "localhost", config().getInteger("http.port", 8080), ar -> {
      if (ar.failed()) {
        ar.cause().printStackTrace();
      } else {
        System.out.println("Quotes (Rest endpoint) service published : " + ar.succeeded());
      }
    });
</pre>

Remember, Vert.x is promoting an asynchronous, non-blocking development model. Publishing the service may take time (actually it does as it creates a record, write it to the backend, and notifies everyone), as we cannot block the event loop, the method is asynchronous. Asynchronous methods have a Handler as last parameter that is invoked when the operation has been completed. This Handler is called with the same event loop as the one having called the async method. As the asynchronous operation can fail, the Handler receives as parameter an AsyncResult telling whether or not the operation has succeeded. You will see the following patterns a lot in Vert.x applications:

```java
// Asynchronous method returning an object of type X
 operation(param1, param2, Handler<AsyncResult<X>>);

 // Handler receiving an object of type X

 ar -> {
   if (ar.succeeded()) {
      X x = ar.result();
      // Do something with X
   } else {
      // it failed
      Throwable cause = ar.cause();
   }
 }
```
 
