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


### How does one define a stack ?

A stack author defines a stack by specifying the aforementioned metadata in a manifest called 
a Devfile.


### Contents of a stack's Devfile

#### Sample application(s)

```
projects:
  - name: spring-boot-http-booster
    git:
      location: https://github.com/snowdrop/spring-boot-http-booster
      branch: master
```

#### Guidance on compiling the application

```
commands:
  - exec:
      id: build 
      component: maven
      commandLine: mvn -Duser.home=${HOME} -DskipTests clean install
      workingDir: '${PROJECTS_ROOT}/spring-boot-http-booster'
      env:
        - name: MAVEN_OPTS
          value: "-Xmx200m"
```


#### Guidance on runnning the application

```
commands:
  - exec:
        id: run
        component: maven
        commandLine: 'mvn -Duser.home=${HOME} spring-boot:run'
        workingDir: '${PROJECTS_ROOT}/spring-boot-http-booster'
        env:
          - name: MAVEN_OPTS
            value: "-Xmx200m"
```

#### Guidance on building Image(s)

The stack author may specify one of more strategies to build images
from the application source code. 

```
images:
  - name: hello-world
    strategy:
      name: source-to-image
    builder:
      image: openshift/java8

  - name: hello-world-ui
    source:
      context: src/frontend
    strategy:
      name: kaniko
    Dockerfile: ${STACK_ROOT}/build/Dockerfile.build
```

Tools using the devfile as a guidance may implement flows to optionally resolve `builder.image` to ImageStreams.

#### Guidance on deploying the Image as Workloads

A 'component' is a Kubernetes workload associated with one of the images built off source
in this current stack.

A 'dependency' is a  service that the component depends on. Example, a mongo DB database.

##### Guidance on the type of the workload

```
workloads:
  components:
    - type:
        apiVersion: serving.knative.dev/v1
        kind: Service
```

##### Guidance on the pod template

The stack author may specify the workload type and the pod template
the app component needs to be deployed as.



```
workloads:
  components:
    - description: NodeJS App built from source
      imageRef : hello-world
      podTemplate:
        containers:
            readinessProbe:
              httpGet: ""
              path: /api/health/readiness
              port: 8080
              scheme: HTTP 
            livenessProbe:
              httpGet: ""
              path: /api/health/liveness
              port: 8080
              scheme: HTTP
```

##### Deploying from a helm chart

The stack author may specify the helm chart that could be used to deploy the stack.

```
workloads:
  components:
    - description: Deploys the image hello-world
      imageRef : hello-world-ui
      chart:
        url: https://technosophos.github.io/tscharts/mink-0.1.0.tgz
        values:
          - name: license
            value: true
          - name: REPLACE_IMAGE 
            value: ${imageRef} # The Helm Chart expects the REPLACE_IMAGE to be set with `hello_world` image.
```

##### Deploying using a list of Kubernetes resources

The stack author may specify the list of raw Kubernetes objects to be created to deploy an application
associated with the stack.

```
workloads:
  components:
    - description: "Deploys the springboot app as a Knative Service"
      imageRef : hello-world
      objects:
        - apiVersion: serving.knative.dev/v1
          kind: Service
          metadata:
            name: helloworld-springboot
            namespace: will-be-overwritten
            annotations:
              apps.devfile.io/workload-type: function
          spec:
            template:
              spec:
              containers:
                - image: ${DEPLOY_IMAGE}
                  env:
                    - name: TARGET
                      value: "springboot Sample v1"
        
```

##### Deploying using a 'yaml' manifest

```
workloads:
  dependencies:
    # The node app's service dependencies
    - description: Mongo DB deployment
      manifests: 
        - ${STACK_ROOT}/dependencies/postgres.yaml
```


#### Guidance on a CI/CD pipeline

_To Be Added_

### Samples

Get started with samples stacks in the `/stacks` directory.