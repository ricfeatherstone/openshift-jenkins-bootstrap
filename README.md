# Openshift Jenkins Bootstrap

Bootstrap OpenShift Jenkins with the 
[OpenShift Client DSL](https://github.com/openshift/jenkins-client-plugin) plugin.

This is required until the base image includes the plugin.

### Run a S2I Build to Install the OpenShift Client DSL

```bash
oc new-build jenkins:2~https://github.com/ricfeatherstone/openshift-jenkins-bootstrap.git \
    --name=jenkins-bootstrap \
    --context-dir='s2i/bootstrap'
```

### Update the Jenkins Version

With the OpenShift Client DSL installed you can run the pipeline in `pipelines/update` to update Jenkins.

The initial run will use the `openshift/jenkins-2-centos7` base image so you probably want to cancel this and trigger
it manually using the image created above. 

In order for Jenkins to pick up the parameters, you'll have to let the first run of the build to start first before 
cancelling.

```bash
oc new-build jenkins:2~https://github.com/ricfeatherstone/openshift-jenkins-bootstrap.git \
    --name=jenkins-update \
    --strategy=pipeline \
    --context-dir='pipelines/update'
oc delete build jenkins-update-1
```

Running with `oc-cluster up` I can start the build with `baseImage = 172.30.1.1:5000/vault-cicd/jenkins-bootstrap` to 
update Jenkins in the image bootstrapped to include the OpenShift Client DSL.
