# Gitlab Openshift Integrations

## Goal - To Checks Integration points of GitLab With Openshift Containers Platform.

### Objectives

- Deploy GitLab Operator on Openshift using OLM and operator Hub.
- Deploy GitLab Instance Using Operator.
- Checks Integration of GitLab With Openshift, Including:
   1. Configure the gitlab identity provider in order to connect to Openshift using GitLab instance 
   2. Trigger a build of a BuildConfig using BuildConfig Gitlab WebHook Invoked by GitLab upon push event to a sample repository with sample application.
   3. Trigger an Openshift Pipeline From a sample repository with sample application in GitLab(Using HTTP callback/WebHooks in GitLab triggered by push event to repo).
       Define a Openshift (Tekton) Pipelines EventListener, TriggerBinding , And TriggerTemplate, and attach it to some ref Pipeline.
   4. Try to Trigger gitLab Job to deploy application Using webhook invoked from a CI Tekton Pipeline.
   5. Configuring a GitLab identity provider in Openshift, using the gitlab Instance installed on openshift as the Identity provider.
   6. Configure A Gitops CD pipeline using RH Openshift GitOps (ArgoCD) to sync the cluster state with a GitLab Repository containing Helm Chart/Ocp Resources.

### Installing the operator

1. Get the domain of the Openshift cluster:
```shell
export CLUSTER_DOMAIN=$(oc get route -n openshift-console console -ojsonpath='{.status.ingress[0].host}' | awk -F . '{$1="";print $0}' | tr ' ' '.' | sed -E 's/^.//g')
```

2. Inject the value into GitLab Custom Resource
```shell
sed -i 's/CLUSTER_DOMAIN/'$CLUSTER_DOMAIN'/g' gitlab.yaml
```

3. Install The operator:

   i. Via the command line:
   ```shell
   oc apply -f operator-group.yaml subscription.yaml
   ```
   ii. Via Operaturhub on Openshift:
     Go to Openshift WebConsole --> Administrator --> Operators --> Operator Hub --> Search for GitLab --> Click on it, and Install. 


4. Wait for the Operator Installation to finish Successfully , you can check the status with the following command, you need to see PHASE= Succeeded:
```shell
oc get csv -n gitlab-system | grep -E 'NAME|gitlab'
# If want to wait until it reach to PHASE = Scceeded without re-enter the command over and over, kindly type, and in the end press CTRL+Z/ CTRL+C
watch "oc get csv -n gitlab-system | grep -E 'NAME|gitlab'"
```


5. Now you can create a gitHub Instance using the operator and GitLab CustomResource:
```shell
oc apply -f gitlab.yaml
```

6. Track The progress of the installation
```shell
watch oc get all
```
