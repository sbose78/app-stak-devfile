### What is a stack ?

Set of metadata, artifacts and example code needed to develop an application. 
The scope of AppStaK goes beyond the traditional software stack and covers development 
and GitOps activities.

#### What information does a stack have ?

A stack contains the following information with respect to an application 
that is associated with a stack:

* Sample application(s). For example, https://github.com/snowdrop/spring-boot-http-booster
* How to compile the application. For example, `mvn package`
* How to run the application. Example, For example, `java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App`
* How to turn the application into a container(s) . For Example, a Kaniko Build using a Dockerfile.
* How to debug the application.
* How to deploy the application. For example, a Knative Service using a specific pod template
* Sample CI/CD Pipeline(s)


#### How does one define a stack ?

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


#### Guidance on building an image

_To Be Added_

   
#### Guidance on deploying the image

_To Be Added_

#### Guidance on a CI/CD pipeline

_To Be Added_