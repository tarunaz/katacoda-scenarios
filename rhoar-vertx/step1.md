The sample project shows the components of a basic Vert.x project laid out in different
subdirectories according to Maven best practices.

**1. Examine the Maven project structure.**

> Click on the `tree` command below to automatically copy it into the terminal and execute it

``tree``{{execute}}

```sh
.
├── README.md <--- component description
├── pom.xml <--- the Maven file
└── src
    ├── conf
    │   └── config.json <--- a configuration file that is passed when the application starts
    └── main
        └── fabric8
            └── configmap.yml
            └── deployment.yml
        └── java
            └── io.vertx.workshop.quote
                            ├── GeneratorConfigVerticle.java <--- the verticles
                            ├── MarketDataVerticle.java
                            └── RestQuoteAPIVerticle.java

```

>**NOTE:** To generate a similar project skeleton you can visit the [Vert.x Starter](http://start.vertx.io/) webpage.

If you have used Maven and Java before this should look familiar. This is how a typical Vert.x Java project would looks like.

It consists of 3 Verticles. The `GeneratorConfigVerticle` generates the quotes for 3 fictional companies that are passed in the config `src/conf/config.json`. It uses `MarketDataVerticle` to publish the market data on the Vert.x Event Bus. The `RestQuoteAPIVerticle` Verticles provides an HTTP endpint to access the company quotes.