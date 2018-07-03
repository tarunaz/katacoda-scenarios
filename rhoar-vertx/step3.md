# The quote REST endpoint

It’s time for you to develop some parts of the application (I know you have pins and needles in your fingers). Open the RestQuoteAPIVerticle. It’s a verticle class extending AbstractVerticle. In the start method you need to:

Register an event bus consumer to collect the last quotations (in the quotes map)

Handle HTTP requests to return the list of quotes, or a single quote if the name (query) param is set.

Let’s do that…​.


**1. Task - Implementing a Handler to receive events**

The first action is about creating a Handler, so a method that is invoked on event. Handlers are an important part of Vert.x, so it’s important to understand what they are.

In this task, the Handler is going to be called for each message sent on the event bus on a specific address (receiving each quote sent by the generator). It’s a message consumer. The message parameter is the received message.

Implement the logic that retrieve the body of the message (with the body() method. Then extract from the body the name of the quote and add an entry name → quote in the quotes map.

Open the file in the editor: ``src/main/java/io/vertx/workshop/quote/RestQuoteAPIVertile.java``{{open}}
Then, copy the below content into the file between the ``// ----//``(or use the `Copy to Editor` button):
      
<pre class="file" data-filename="src/main/java/io/vertx/workshop/quote/RestQuoteAPIVerticle.java" data-target="replace">
JsonObject quote = message.body(); // 1
quotes.put(quote.getString("name"), quote); // 2
</pre>