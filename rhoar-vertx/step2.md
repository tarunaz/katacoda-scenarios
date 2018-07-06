# Verticle

For your convenience, this scenario has been created using the OpenShift Launcher found [here](https://launch.openshift.io/launch/filtered-wizard/all). This launcher will automatically generate a zip file of a project that's in a ready-to-deploy state. We've selected the `Externalized Configuration` project and will be using the Spring Boot runtime option.

**Build the application**
``mvn clean install``{{execute}}

**1. Understanding the Application**

The project is a simple Greeting application, where a user inputs a fruit and is greeted by our service. Opening up our ``quote-generator/src/main/java/io/vertx/workshop/quote/GeneratorConfigVerticle.java``{{open}} file we can see the logic used to respond to our user. The interesting part of this logic is right here, where we retrieve the message:

```java
@Override
public void start() {
    super.start();

    JsonArray quotes = config().getJsonArray("companies");
    for (Object q : quotes) {
      JsonObject company = (JsonObject) q;
      // Deploy the verticle with a configuration.
      vertx.deployVerticle(MarketDataVerticle.class.getName(),
         new DeploymentOptions().setConfig(company));
    }

    vertx.deployVerticle(RestQuoteAPIVerticle.class.getName());

    publishMessageSource("market-data", ADDRESS, rec -> {
      if (!rec.succeeded()) {
        rec.cause().printStackTrace();
      }
      System.out.println("Market-Data service published : " + rec.succeeded());
    });

    publishHttpEndpoint("quotes", "localhost", config().getInteger("http.port", 8080), ar -> {
      if (ar.failed()) {
        ar.cause().printStackTrace();
      } else {
        System.out.println("Quotes (Rest endpoint) service published : " + ar.succeeded());
      }
    });
}
```

Verticles can retrieve a configuration using the `config()` method. Here it gets the details about the companies to simulate. The configuration is a `JsonObject`. Vert.x heavily uses JSON, so you are going to see a lot of JSON in this lab. For each company found in the configuration, it deploys another verticle with the extracted configuration. Finally, it deploys another verticle providing a very simple HTTP API.

The last part of the method is about the service discovery mentioned in the microservice section. This component generates quotes sent on the event bus. But to let other components discover where the messages are sent (where means on which address), it registers it. market-data is the name of the service, ADDRESS is the event bus address on which the messages are sent. The last argument is a Handler that is notified when the registration has been completed. The handler receives a structure called AsyncResult.

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
 
```java
 publishHttpEndpoint("quotes", "localhost", config().getInteger("http.port", 8080), ar -> {
   if (ar.failed()) {
     ar.cause().printStackTrace();
   } else {
     System.out.println("Quotes (Rest endpoint) service published : " + ar.succeeded());
   }
 });
```
 