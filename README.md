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

> Try deploying the Spring Petclinic REST using Recreate deployment strategy and document
your attempt.

> Prepare different manifest files for executing Recreate deployment strategy.

> What do you think are the benefits of using Kubernetes manifest files? Recall your experience
 in deploying the app manually and compare it to your experience when deploying the same app
 by applying the manifest files (i.e., invoking `kubectl apply-f` command) to the cluster.
