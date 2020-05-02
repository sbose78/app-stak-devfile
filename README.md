### What is a stack ?

Set of metadata, artifacts and example code needed to develop an application. 
The scope of AppStaK goes beyond the traditional software stack and covers development 
and GitOps activities.

#### What information does a stack have ?

A stack contains the following information with respect to an application 
that is associated with a stack:

* How to turn the application into a container(s)
* How to run the application
* How to run tests
* How to debug the application
* How to deploy the application
* Pipeline(s)
* Sample application(s)


#### How does one define a stack ?

A stack author defines a stack by specifying the following information in a manifest expresseded 
as a `devfile.yaml` file.

* Metadata for setting up a container-based developer workspace for developing an app with the 
specific stack.
* Commands for compiling the app in a container in the developer workspace. 
Example, `mvn package`.
* Commands for running the app in a container in the developer workspace. 
Example, `java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App` 
* Guidance for building an image from the app's source code.
* Guidance for deploying the above image as a container on Kubernetes.


### Sections in a stack's devfile

#### Project Samples

```
projects:
  - name: spring-boot-http-booster
    git:
      location: https://github.com/snowdrop/spring-boot-http-booster
      branch: master
```

#### Command(s) for running the app in a workspace

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

#### Guidance on building an image 

```
build:
  strategy:
    name: buildpacks-v3 
  builder:
    image: heroku/buildpacks:18
```
   
#### Guidance on deploying the image

##### Deploying from a podSpec

```
deploy:

  workload:
      apiVersion: serving.knative.dev/v1
      Kind: Service   
  podSpec:
    containers:
    - image: gcr.io/knative-samples/helloworld-go
      env:
        - name: TARGET
          value: "Go Sample v1"
```

##### Deploying from a helm chart

The stack author may specify the helm chart that could be used to deploy the stack.
The chart must have a variable to let the chart consumer inject the built image url.

`imageVar` lets the stack author convey to the stack consumer which helm template variable 
is to be set as the built image's url.

```
deploy:

  chart:
    url: https://technosophos.github.io/tscharts/mink-0.1.0.tgz
    imageVar: REPLACE_IMAGE
    values:
      - name: license
        value: true 
      - name: REPLACE_IMAGE 
        value: quay.io/myorg/myapp:0.1 # This will be overriden by the stack consumer
```

##### Deploying using a list of Kubernetes resources

The stack author may specify the list of Kubernetes objects to deploy the stack either 
* as a list of objects OR 
* as a url to the raw yaml OR
* as a path to the directory in the repository.

```
deploy:

  resource:
  
    # option 1
    url: https://raw.githubusercontent.com/fabric8-services/fabric8-wit/master/openshift/core.app.yaml

    # option 2
    objects:
    - apiVersion: serving.knative.dev/v1
      kind: Service
      metadata:
        name: helloworld-go
        namespace: will-be-overwritten
        annotations:
          apps.devfile.io/workload-type: function
      spec:
        template:
          spec:
          containers:
            - image: gcr.io/knative-samples/helloworld-go
              env:
                - name: TARGET
                  value: "Go Sample v1"
        
    # option 3
    path: deploy 
```

