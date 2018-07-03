# Review the project structure

Let’s have a look to the project, as every other project are structured the same way.

```
.
├── README.md <--- component description
├── pom.xml <--- the Maven file
└── src
    ├── conf
    │   └── config.json <--- a configuration file that is passed when the application starts
    └── main
        └── java
            └── io.vertx.workshop.quote
                            ├── GeneratorConfigVerticle.java <--- the verticles
                            ├── QuoteVerticle.java
                            └── RestQuoteAPIVerticle.java

```

Let’s start with the pom.xml file. This file specifies the Maven build:

1. Define the dependencies

2. Compile the java code and process resources (if any)

3. Build a fat-jar

A fat-jar (also called uber jar or shaded jar) is a convenient way to package a Vert.x application. It creates a über-jar containing your application and all its dependencies, including Vert.x. Then, to launch it, you just need to use java -jar …​., without having to handle the CLASSPATH. Wait, I told you that Vert.x does not dictate a type of packaging. It’s true, fat jars are convenient, but it’s not the only way, you can use plain (not fat) jars, OSGi bundles…​

The created fat-jar is configured to use a main class provided in vertx-workshop-common (io.vertx.workshop .common.Launcher). This Launcher class creates the Vert.x instance, configure it and deploys the main-verticle. Again, it’s just a convenient way, you can use your own main class.

