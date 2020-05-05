## Application Stacks 
codenamed "AppStaks"


### What is an application stack ?

An application stack is a set of metadata, artifacts and example code needed to develop an application. 
The scope of AppStaK goes beyond the traditional software stack and covers development 
and GitOps activities.

### Who consumes an application stack ?

Developer tools which enables developers to get started with writing applications which would be eventually deployed in a Kubernetes environments, would use Application Stacks to offers users a variety of optioins 

### What information does a stack have ?

A stack contains the following information with respect to an application 
that is associated with a stack:

* Sample application(s). For example, https://github.com/snowdrop/spring-boot-http-booster
* How to compile the application. For example, `mvn package`
* How to run the application. Example, For example, `java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App`
* How to turn the application into a container(s) . For Example, a Kaniko Build using a Dockerfile.
* How to debug the application.
* How to deploy the application. For example, a Knative Service using a specific pod template
* Sample CI/CD Pipeline(s)