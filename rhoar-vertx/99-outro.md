In this scenario, you learned a bit more about what Reactive Systems and Reactive programming are and why it's useful when building Microservices. Note that some of the code in here may have been hard to understand and part of that is that we are not using an IDE, like JBoss Developer Studio (based on Eclipse) or IntelliJ. Both of these have excellent tooling to build Vert.x applications.

You create a quote generator for 3 fictional companies and published a market data Verticle to the Vert.x Event Bus for other services to consume. You also published a HTTP endpoint that consumed the market data service and exposed a REST HTTP endpoint.

In the next scenario, , we are going to implement a Portfolio service. A Portfolio stores the owned shares and the available cash.