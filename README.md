## Application Stacks 
codenamed "AppStaks"


### What is an application stack ?

An application stack is a set of metadata, artifacts and example code needed to develop an application. 
The scope of AppStaK goes beyond the traditional software stack and covers development 
and GitOps activities.

### Who/What consumes an application stack ?

Developer tools which enable developers to develop and 
deploy apps in a Kubernetes environment.

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

### Build guidance

The stack author could choose to provide either:
1. The kubernetes manifest that builds an image : it should most likely be a Task definition, Or
2. The Build Strategy to be used to build an image.

### Deployment Guidance

The stack author could choose to provide different levels of guidance.

1. The apiVersion and kind.
2. The Pod template.
3. The Helm Chart. 
4. The Parameterized list of kubernetes objects ( `oc process -f ...` ).
5. The list of manifests ( `kubectl apply -f ...` ).

### Example

```
# outer loop.
services:
  - deploy:
      manifests:
        - ${STACK_ROOT}/manifests/postgres.yaml

  - build:
      type: 
        Dockerfile: '${STACK_ROOT}/build/Dockerfile

    deploy:
      type:
        apiVersion: serving.knative.dev/v1
        kind: Service

      #### option 2
      podTemplate:
        containers:
            readinessProbe:
              httpGet: ""
              path: /api/health/readiness
              port: 8080
              scheme: HTTP

      #### option 3
      helm:
        chart: https://redhat-developer.github.com/redhat-helm-charts/charts/nodejs-ex-k-0.1.1.tgz

      #### option 4
      objects:
        - apiVersion: serving.knative.dev/v1alpha1
          kind: Service
          metadata:
          name: greeter
          spec:
            template:
              spec:
                containers:
                  - image: dev.local/quarkus-quickstarts/getting-started-knative
                    livenessProbe:
                    httpGet:
                        path: /health/live
                    readinessProbe:
                    httpGet:
                        path: /health/ready


      #### option 5  
      manifests: 
        - ${STACK_ROOT}/manifests/binding-secret.yaml

    

    
```
