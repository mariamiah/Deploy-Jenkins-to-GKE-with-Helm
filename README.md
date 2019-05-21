#### Deploy Jenkins to GKE with Helm
The aim of this project is to install the Jenkins CI tool to a GKE cluster in order help automate future deployments.

#### Install Kubectl
- This is the defacto commandline tool for interacting with your Kubernetes cluster
- Follow this [link](https://kubernetes.io/docs/tasks/tools/install-kubectl/) and install Kubectl

#### Install gcloud
- The gcloud SDK manages authentication and local access to gcp
- Follow this [link](https://cloud.google.com/sdk/install) to install gcloud

#### Check cluster configuration
- In your terminal run `https://cloud.google.com/sdk/install`
- Ensure the cluster exists
- Run `gcloud container clusters get-credentials <clustername> --zone=<zone_name>`
- You will then be notified by gcloud that a `kubeconfig entry has been generated for kubectl`
- You can check if it works by typing `kubectl get pods --all-namespaces`

#### Helm
The Helm project started as a kubernetes sub-project with an aim of providing a way of simplifying complex applications through grouping together all their necessary components, parameterising them and grouping them into a single helm chart

Helm is also known as a `Package Manager for Kubernetes`

Helm charts abstract `deployments` which abstract `pods` which then abstract `containers` that abstract `applications` that run on a hypervisor which runs on an operating system.

Helm is comprised of two components: `Helm client` and `helm server a.k.a Tiller`
Tiller handles requests from the helm client and interacts with the Kubernetes API
#### Getting started with Helm
- Download and Install the Binary from [Here](https://github.com/helm/helm/releases)
- You can also run `brew install kubernetes-helm` to install it on `MacOs`
- Clone this repository by running `git clone https://github.com/mariamiah/Deploy-Jenkins-to-GKE-with-Helm.git`
- Navigate into the root folder by running `cd Deploy-Jenkins-to-GKE-with-Helm`
- Set up a service account with a defined role of `Tiller` to operate within (Check the tiller-rbac.yml file)
- Apply it to your account by running `kubectl apply -f tiller-rbac.yml`
- You can now set up `Helm` and install `tiller`
- Run `helm init --service-account tiller`
- Run `helm version`


#### Jenkins
Once you have helm and tiller configured for your cluster, deploying the Jenkins application — including persistent volumes, pods, services and a load balancer — is as easy as running one command:
`helm install --name my-release stable/jenkins`
- You are now able to access Jenkins via its its external IP
- Obtain the Admin Password from the prompt that Helm gives you
- Run `helm ls --all my-release;` to check the status of the release


#### Manage your release
- `helm status my-release`
- `helm delete --purge my-release` - Deletes the release from your cluster and purges any of its resources (services, disks etc.)
- `helm list`

#### The Parameters file
- Most Helm charts make use of a parameters file to define the attributes of a deployment such as `docker image names`, `node selectors`
- In this example, we didnot specify the parameters file because we inherited the default one from [Published Jenkins Chart](https://github.com/helm/charts/tree/master/stable/jenkins)
- You can obtain a copy of this default file and modify it
- Inorder to add a plugin forexample Blue Ocean, add ` - blueocean:1.5.0` in the values.yml file(Parameters file) under the `InstallPlugins` list
- Upgrade the release inorder to apply the new values `helm upgrade -f values.yaml my-release stable/jenkins`
- At the bottom of the `values.yml` file add 
```
rbac:
  install: true
  ```
  in order to enforce the use of RBAC(Role Based Access Control)
- Run `helm upgrade` command again