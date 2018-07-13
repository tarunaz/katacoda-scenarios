Now that you've logged into OpenShift, let's deploy our new micro-trader Vert.x microservice:

**Build and Deploy**

Red Hat OpenShift Application Runtimes includes a powerful maven plugin that can take an
existing Eclipse Vert.x application and generate the necessary Kubernetes configuration.

We have defined additional config, like ``src/main/fabric8/configmap.yml`` {{open}} and ``src/main/fabric8/deploymentconfig.yml``{{open}} which defines
the deployment characteristics of the app (in this case we mount a config file from a ConfigMap), but OpenShift supports a wide range of [Deployment configuration options](https://docs.openshift.org/latest/architecture/core_concepts/deployments.html) for apps).

Build and deploy the project using the following command, which will use the maven plugin to deploy:

`mvn fabric8:deploy -Popenshift -DskipTests`{{execute T1 interrupt}}

The build and deploy may take a minute or two. Wait for it to complete. You should see a **BUILD SUCCESS** at the
end of the build output.

After the maven build finishes it will take less than a minute for the application to become available.
To verify that everything is started, run the following command and wait for it complete successfully:

`oc rollout status -w dc/quote-generator`{{execute}}

**3. Access the application running on OpenShift**

 Click on the
[route URL](http://cart-cart.[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com)
to access the sample UI.

> You can also access the application through the link on the OpenShift Web Console Overview page.

![Overview link](/redhat-middleware-workshops/assets/reactive-microservices/routelink.png)


## Congratulations!

You have deployed the quote-generator as a microservice. In the next component, we are going to implement an event bus service. 
