
### Principles

#### The barrier for entry should be low.  

Stack authors should be able to provide little information if they wish to. However, stack authors may want to 
support extreme details about how to run an application with network policies! Both ends of the spectrum must be supported.

#### The stack spells out the "what" but not necessarily the "how"

As an example, stack authors may want to specify that they expect a BuildPacks-v3 build strategy to be used to turn source code
into an image. However, it is upto the tool to figure out how to execute a BuildPacks-v3 build based on the inputs provided. 

#### The stack should be should be self-contained

While technically, we would support use of relative URLs pointing to a path in the source code directory structure of an application. As a practice, stack authors are discouraged from doing so. 

Dockerfiles and Kubernetes manifests associated with a stack must be part of the stack package in the stack registry itself.