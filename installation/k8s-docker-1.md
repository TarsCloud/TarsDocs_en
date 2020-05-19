
# Directory
> * [Intro](#chapter-1)
> * [Deploy In K8S](#chapter-2)

# 1 <a id="chapter-1"></a>Intro

This page introduces a scheme of deploying tars on K8. The main steps are as follows:
- Container framework services, using docker: tarscloud/framework
- The tarsnode node is also containerized, using the docker: tarscloud/tars-node
- The tars framework and tarsnode nodes are deployed on k8s as pods, and the container that the pod runs as a virtual machine
- Publish services to these containers through tars Web

This way of release and disaster recovery still depends on the ability of tars, k8s is only a container management platform

## 2 <a id="chapter-2"></a>Deploy In K8S

First install helm(you can learn helm by yourself). In short, helm is a tool for deploying services to k8s.

Here is a way to install helm. Please refer to helm website for other information:
```
#download helm : helm (https://helm.sh/docs/using_helm/#installing-helm)
#Tiller is the server side of helm, which will be deployed on the framework to complete the work of deploying other dockers to k8s
#The tiller who creates the helm is bound to the cluster admin, and has the administrative authority of the whole cluster
kubectl -n kube-system create serviceaccount tiller
kubectl create clusterrolebinding tiller   --clusterrole=cluster-admin   --serviceaccount=kube-system:tiller

#install tiller
helm init --service-account tiller --tiller-image  sapcc/tiller:v2.14.3 --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

#check helm is installed succuess
helm version
kubectl -n kube-system  rollout status deploy/tiller-deploy

```

Now use helm to install the tars framework, and replace the ```tars-test``` with the desired namespacee
```
helm repo add tars-stable https://tarscloud.github.io/TarsDocker/charts/stable

kubectl create namespace tars-test

#Deploy tars (two main framework and five node machines)
helm install tars-stable/tars --name tars-test --namespace tars-test \
    --set tars.namespace=tars-test,tars.replicas=2,tarsnode.replicas=5,tars.host=domain.com,tars.port=6080


```

Access url of web is ```http://$namespace.$host.$port```, this example is ```http://tars-test.domain.com:6080```