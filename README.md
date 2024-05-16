# Module 11 - Deployment on Kubernetes

## Reflection on Hello Minikube

> Compare the application logs before and after you exposed it as a Service.

Before exposing the application as a Service, the logs only contain entries related
to the application's startup, indicating that the HTTP and UDP servers have been started
on ports 8080 and 8081, respectively. There are no logs indicating any incoming request.

After exposing the application as a Service and accessing it through the proxy, additional
log entries appear. Specifically, there are logs indicating incoming HTTP GET requests to
the root ("/") endpoint. Each time the app is opened through the proxy, there are new log
entries for each incoming request. This demonstrates that the Service is successfully
routing incoming traffic to the application. So, yes, the number of logs increases each
time the app is opened through the proxy, reflecting the incoming HTTP requests handled by
the Service.

> Notice that there are two versions of `kubectl get` invocation during this tutorial
section. The first does not have any option, while the latter has `-n` option with value
set to `kube-system`. What is the purpose of the `-n` option and why did the output not 
list the pods/services that you explicitly created?

The purpose of the `-n`option in `kubectl get` is to specify the namespace for which we
want to list resources. By default, Kubernetes resources are created in the `default`
namespace. So, because the first invocation of `kubectl get` doesn't have any option, 
it lists resources from the default namespace. On the other hand, by specifying `kube-system`, 
the second invocation only shows resources belonging to the `kube-system` namespace. This
namespace is typically reserved for system components like `metrics-server`, `kube-dns`, and
other core Kubernetes services. The reason the output of the second `kubectl get` command 
did not list the post/services that were explicitly created is because they were created in 
the default namespace, not in the `kube-system` namespace.

## Reflection on Rolling Update & Kubernetes Manifest File

> What is the difference between Rolling Update and Recreate deployment strategy?

Rolling Update strategy updates the application without downtime by gradually replacing
the old Pods with the new ones. It ensures that a certain number of Pods are available
throughout the update process. Otherwise, in Recreate strategy, all existing Pods are
terminated before new Pods are created with the updated version. This approach may cause
downtime as there is no overlap between old and new Pods.

> Try deploying the Spring Petclinic REST using Recreate deployment strategy and document
your attempt.

First, I recreate the deployment for Spring Petclinic REST app version 3.0.2 and scale it 
to 4 replicas. 

![img](https://i.imgur.com/wYMF7uV.png)

Then, I do these steps to try deploying the app using Recreate strategy.

#### 1. Patch Deployment Strategy

Create a patch file `patch-recreate.yaml` to change the deployment strategy to Recreate:
```yaml
spec:
  strategy:
    $retainKeys:
    - type
    type: Recreate
```

#### 2. Apply Patch

Apply the patch to the existing deployment using the following command:
```shell
kubectl patch deployments spring-petclinic-rest --patch-file ./patch-recreate.yaml
```

#### 3. Verify Change

![](https://i.imgur.com/EyLRSpH.png)
We can see that after applying the patch, the strategy changed to Recreate

#### 4. Update Image

![img](https://i.imgur.com/2gn6nnf.png)
After updating the image to version 3.2.1, we can see that the rollout process indicating the
termination of old pods and the creation of new ones.

> Prepare different manifest files for executing Recreate deployment strategy.

The file is similar with the manifest file from the tutorial and is included in this project.
I used the following command to generate the deployment YAML file: 
`kubectl get deployments/spring-petclinic-rest -o yaml > deployment-recreate.yaml`

> What do you think are the benefits of using Kubernetes manifest files? Recall your experience
 in deploying the app manually and compare it to your experience when deploying the same app
 by applying the manifest files (i.e., invoking `kubectl apply-f` command) to the cluster.

Based on my experience, deploying applications using Kubernetes manifest files offers several 
distinct advantages compared to manual deployment. Here are some key benefits:

1. Consistency and Reproducibility:
    - **Manual Deployment**: Manually deploying an application involves running a series of kubectl
    commands. Each deployment might slightly differ depending on the execution order or 
    parameters provided, leading to inconsistencies.
    - **Manifest Files**: Using manifest files ensures that the deployment configuration is 
    consistent every time. The `kubectl apply -f` command applies the same configuration repeatedly, 
    reducing the risk of errors and discrepancies.

2. Version Control:
    - **Manual Deployment**: Tracking changes in deployment configurations manually can be challenging. 
    Any modifications need to be documented separately, and there's a higher chance of human error.
    - **Manifest Files**: Manifest files can be stored in version control systems (e.g., Git), allowing 
    for easy tracking of changes over time. This makes it straightforward to roll back to a previous
    configuration if needed.
