We have already deployed our coolstore monolith, inventory and catalog to OpenShift. In this step we will deploy our new Shopping Cart microservice for our CoolStore application, so let's create a separate project to house it and keep it separate from our monolith and our other microservices.

1. Create project

Create a new project for the cart service:

``oc new-project vertx-demo --display-name="Micro-Trader Application"``{{execute interrupt}}

3. Open the OpenShift Web Console

You should be familiar with the OpenShift Web Console by now! Click on the "OpenShift Console" tab:

OpenShift Console Tab

And navigate to the new catalog project overview page (or use this quick link

Web Console Overview

There's nothing there now, but that's about to change.