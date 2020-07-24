# Weather Service Pipeline
This contains all the resources necessary to set up a pipeline that listens to webhook pushes from github.  In these instructions, the project name is assumed to be `knativetutorial`.  To change it to your project, double check each `yaml` file and update all references to this project name.


## Prerequisite
1.  ocp 4.4 with Tekton and Knative operators installed
2.  oc 4.4 cli (and logged into your cluster)
3.  For this pipeline, we are assuming you are using the [weather service project](https://github.com/jkeam/weather-service)


## Tekton Installation
```
oc new-project tekton-pipelines
oc adm policy add-scc-to-user anyuid -z tekton-pipelines-controller
oc apply -f https://storage.googleapis.com/tekton-releases/pipeline/latest/release.notags.yaml
oc apply -f https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
# to verify
oc get pods --namespace tekton-pipelines --watch
```


## Github Setup
1.  Go to github and go to Settings -> Developer Settings -> Personal access tokens -> Generate new token with `repo -> public_repo` permission
2.  Go to your project and enable a webhook, url defined below in step 8.


## Setup
1.  `oc project knativetutorial`
2.  Create resources
```
oc apply -f ./resources/git-repo.yaml
oc apply -f ./resources/image.yaml
```
3.  Create tasks
```
oc apply -f ./tasks/mybuildah.yaml
oc apply -f ./tasks/apply-manifests.yaml
oc apply -f ./tasks/deploy-using-kubectl.yaml
```
4.  Create build pipeline
```
oc apply -f ./weather-service-build.yaml
```
5.  Update `./secrets/webhook_secret.yaml` with the github info
6.  Create trigger resources
```
oc apply -f ./trigger/trigger_template.yaml
oc apply -f ./trigger/trigger_binding.yaml
oc apply -f ./trigger/trigger_role.yaml
oc apply -f ./trigger/trigger_listener.yaml
oc apply -f ./trigger/trigger_route.yaml
```
7.  Apply secrets
```
oc apply -f ./secrets/webhook_secret.yaml
oc apply -f ./secrets/weather_token_secret.yaml
```
8.  Double check the route that was created and use that as the webhook from the `Github Setup` instructions above
