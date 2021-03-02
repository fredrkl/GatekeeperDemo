# Introduction 
This demo demonstrate how you can use the K8s admission controller to enforce policies with code.

When you make a request to the K8s API server it goes through a series of steps. Some steps can be augmented. The steps are:
![Pipeline](/Images/API-Server-pipeline.png). What Open Policy Agent (OPA) is doing is to register itself as a part of the validating admission webhook. When you send a request to the API server it will eventually get to the validating admission webhook and sendt to the endpoints that are registered as a query, the OPA will answer yes/no based on its rules and data.

To easily use OPA with its constraints and templates in K8s we can use the Gatekeeper project. It creates Custom Resource Definitions (CRDs) so you can create the constraints and templates just as you would create a regular K8s Deployment.

# Install Gatekeeper
To install Gatekeeper you simply run the 

```
> kubectl apply -f ./10-Gatekeeper/installGatekeeper.yaml
```
We also want a UI to be able to see the OPA policies and violations.

```
> kubectl apply -f ./10-Gatekeeper/ui
```

In this demo we will access the Gatekeeper ui tool with:
````
> kubectl port-forward deployment/gatekeeper-policy-manager 8080:8080
````

And then you can see the UI if you go to: http://127.0.0.1:8080/

# Demo
If you look in the policies folder you will find the templates for our 2 policies. The actual policies are using them with application spesific values, e.g, priorityClass.yaml which define the valid priority classes.

Run the following commands to get the policies up and running:
````
> kubectl apply -f 10-Gatekeeper/templates
> kubectl apply -f 10-Gatekeeper/policies
````

You will hopefully see the templates and policies in the UI that we set up earlier.

If you now try to run
````
> kubectl apply -f 10-Gatekeeper/demoapp
````
you will get messages from the OPA letting you know how you are violating the policies and how you can correct it.