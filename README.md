## Hello there, Comrade, this is the repo that governs the Youverify assessment I was given

The repo consists of the different helm charts for the services, and also the supporting application including Redis and the likes

### The State Of The Microservices
I used a single repo to showcase the manner in which I would handle a Nodejs application, I used the nestjs framework if you don't mind. I'm a big fan of Nestjs actually.
The repository is located at [Awesome Repo](https://github.com/Rapixar/youverify_assessment). It has all the binaries for the application, including a very robust CI/CD declaration. I also added a spice of GitHub Actions to run tests on every Pull Requests. I didn't set the environment variables to on GitHub for my Docker registry connection hence the jobs failed, but it worked at some point where I only built the image without pushing to a registry.
In the repo, a sample Jenkinsfile and Dockerfile was inclunded that defines the holistic process for Continous Integration and Continous Delivery. The [Jenkinsfile](https://github.com/Rapixar/youverify_assessment/blob/main/Jenkinsfile) defines the CI and CD process in general, including the automatic building of the artifact and deployment to Kubernetes using a parametrized Jenkins job that syncs with Helm.

### The Pipeline
The CI process installs the npm modules, based on the package.json and the particular branch and commit. After this is complete, the image is build based on the Dockerfile and pushes the image using credentials set on the Jenkins server, we shouldn't expose our secrets
The CD process, is dependent on the parameters for the Kubernetes deployment job is also generated from this Jenkins pipeline, which would be used for deployment to Kubernetes based on the Charts on this repo, the parametrized job isn't scripted but below is the helm CLI command for doing the update;

```
helm init --client-only --skip-refresh # initializes helm on the client side, which is also where Jenkins run
helm repo update # Here the repo is updated based on any change done on the this repository, GitOps to the world
helm upgrade --install --kubeconfig='/opt/config' --debug --values youverify_test/$APP/$ENVIRONMENT.yaml --set image.tag=$VERSION $APP chartsprod/$APP --namespace=$NAMESPACE
```
The *$ENVIRONMENT* is the Charts yaml that defnes the particular environment we want to make changes to, for prod, this would be values-prod.yaml, for staging it would be value-staging.yaml. Of course, there's a default of values.yaml for values that wasn't predifned on the repo.

