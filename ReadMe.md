### Prerequisites: (You do not have to be a guru but at least know their definitions and purpose)

[-] Kubernetes Knowledge( Basic resources, CRDs, Secrets, APIs,RBAC)

[-] Gitops Concepts

[-] Docker (image registries)

[-] Flux- concepts (Gitops toolkit, components, CRDs, Helm, Kustomize)

### Resources: 

#### Official Documentation: https://fluxcd.io/flux/

##### Installation:

Reference -  https://fluxcd.io/flux/installation/

[-] install the latest version:

`curl -s https://fluxcd.io/install.sh | sudo bash`

[-] Install specific CLI version: (switch to root user if you have limited rights)

`curl -s https://fluxcd.io/install.sh | sudo FLUX_VERSION=0.39.0 bash`

[-] Check releases: https://github.com/fluxcd/flux2/releases 

### Bootstrapping:

[-] installs Flux on a Kubernetes cluster and configures it to manage itself from a Git repository

> Kindly note we have “flux install“ and “flux bootstrap”:

> flux install is used to install "Flux" on an existing Kubernetes cluster, while flux bootstrap is used to create and configure a new Kubernetes cluster with "Flux".

- Regardless I usually use bootstrap for both new and existing cluster

[-] There are various flux bootstrap Options for different Git providers: 

[-] Reference:- https://fluxcd.io/flux/installation/#bootstrap 

[-] Using Gitlab.com and Gitlab enterprise:

```
flux bootstrap gitlab \
  --owner=my-gitlab-username \
  --repository=my-repository \
  --branch=master \
  --path=clusters/my-cluster \
  --token-auth \
  --personal
```

[-] practical example:

 ```
 flux bootstrap gitlab --hostname=gitlab.mycompany.com \
--components-extra=image-reflector-controller,image-automation-controller \
--owner=devsecops/fluxv2  --repository=fluxv2-aws   --branch=master   \
--path=clusters/AWS-sample1-UAT   --token-auth --personal  --network-policy=true \
--watch-all-namespaces=true
```

> PAT ( is the Access token for the user who has read-write to the specific repo- consider using a bot user)

>The first example will install/bootstrap your cluster with the 4 basic flux controllers:

1. Kustomize controller- The kustomize-controller is a Kubernetes operator, specialized in running continuous delivery pipelines for infrastructure and workloads defined with Kubernetes manifests and assembled with Kustomize (https://fluxcd.io/flux/components/kustomize/ )

2. Source Controller - The main role of the source management component is to provide a common interface for artifacts acquisition. The source API defines a set of Kubernetes objects that cluster admins and various automated operators can interact with to offload the Git and Helm repositories operations to a dedicated controller (https://fluxcd.io/flux/components/source/ )

3. Notification Controller - The controller handles events coming from external systems (GitHub, GitLab, Bitbucket, Harbor, Jenkins, etc) and notifies the GitOps toolkit controllers about source changes(https://fluxcd.io/flux/components/notification/ )

4. Helm Controller - The Helm Controller is a Kubernetes operator, allowing one to declaratively manage Helm chart releases with Kubernetes manifests. ( https://fluxcd.io/flux/components/helm/)

[-]  But you need to automate image releases (Image automation), and for that, you need 2 more Controllers:

1. Image Reflector controller - scans image repositories and reflects the image metadata in Kubernetes resources

2. Image Automation controller - updates YAML files based on the latest images scanned, and commits the changes to a given Git repository

[-] This is achieved by adding the Options components-extra when bootstrapping

`--components-extra=image-reflector-controller,image-automation-controller`

### Putting it together :

#### Flux End to End (e2e) -  https://fluxcd.io/flux/flux-e2e/

[-] This gives you the bigger picture of how each component of flux works together to give us the complete desired result of Continous Delivery.

[-] Concepts like secret management using Mozilla (https://fluxcd.io/flux/guides/mozilla-sops/ )

[-] Monitoring -  https://fluxcd.io/flux/guides/monitoring/

[-] Ways of structuring your repo -  https://fluxcd.io/flux/guides/repository-structure/

[-] Image Automation and tagging - https://fluxcd.io/flux/guides/image-update/,  https://fluxcd.io/flux/guides/sortable-image-tags/

[-] Jenkins and Flux -  https://fluxcd.io/flux/use-cases/jenkins/

[-] Cheatsheet: https://fluxcd.io/flux/cheatsheets/ 

### Multi-Tenancy setup

#### Why Multi-Tenancy?

This involves managing and separating different groups into tenants for easy management, and reduction of impact in case there is an issue among others

The process is achieved by having 2 sets of Repos. 1. Cluster management repo( This involves cluster infrastructure, secrets handling, tenant onboarding n management, policies, RBAC among others. 2. Application Repo- this separates various teams/groups into tenants and each manages their own deployment separately( enables one to enforce policies and stuff without affecting other tenants)

This is very customizable and depends on organization requirements/setup, the important bit is to understand how to use Kustomize Controller to achieve the kind of setup you need.

examples:https://github.com/fluxcd/flux2-multi-tenancy   - the official Multi-tenancy setup features deploying the same application to different environments(Uat, dev, prod). Our Setup uses the knowledge and incorporates several related clusters (OCP, EKS) but for the same environment (UAT).  https://gitlab.com/fluxcd2/fluxcd-fosdem2023/-/tree/main/

[-] To apply the above knowledge:

1. Consider hands-on for knowledge application: use flux on kind cluster - . Flux on EKS cluster (https://github.com/winniegakuru/FluxCD_With_k8s_kind_cluster

2. Bootstrapping Flux on an EKS cluster.

3. Practice and Practice
## Happy GitOps with FLUXCD!!!
