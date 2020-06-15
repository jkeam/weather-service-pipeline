# Weather Service Pipeline
This contains all the resources necessary to set up a pipeline that listens to webhook pushes from github.  In these instructions, the project name is assumed to be `knativetutorial`.  To change it to your project, double check each `yaml` file and update all references to this project name.


## Prerequisite
1.  ocp 4.4 with Tekton and Knative operators installed
2.  oc 4.4 cli (and logged into your cluster)


## Tekton Installation
```
oc new-project tekton-pipelines
oc apply -f https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
```


## Github Setup
1.  Go to github and go to Settings -> Personal access tokens -> Generate new token with `public_repo` permission
2.  Go to your project and enable a webhook, url defined below.


## Setup
1.  `oc project knativetutorial`
2.  Create resources
```
oc apply -f ./resources/git-repo.yaml
oc apply -f ./resources/image.yaml
```
3.  Create tasks
```
oc apply -f ./tasks/deploy-using-kubectl.yaml
```
4.  Create build pipeline
```
oc apply -f ./weather-service-build.yaml
```
5.  Update `./trigger/webhook_secret.yaml` with the github info
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
