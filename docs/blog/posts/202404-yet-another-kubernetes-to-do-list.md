---
date: 2024-04-02
authors: [alvsanand]
categories:
  - DevOps
  - Datahub.local
---
# Yet Another Kubernetes To-Do List

Ever noticed how easy it is to kickstart your app on Kubernetes locally? Just a couple of commands and boom – it's up and running! But let's face it, real-world setups are a whole different ball game.

Take my [Datahub.local](https://datahub-local.alvsanand.com/) project, for example. It's not just a data platform; it's a whole ecosystem of things running on Kubernetes. I've also got monitoring, storage solutions, CI/CD pipelines and many more. So suddenly, The initial simplicity feels like a distant dream.

![Kubernetes To-Do List](/img/kubernetes-to-do-list.png){ width="80%" }

## 1. Select a Kubernetes Distribution

Choosing the right Kubernetes distribution is crucial, especially considering your specific needs and infrastructure requirements. Options range from full-fledged, self-managed distributions like vanilla Kubernetes to lightweight solutions tailored for low-end devices.

- **Vanilla**: Ideal for environments where customization and control are paramount, [vanilla Kubernetes](https://kubernetes.io/) offers the flexibility to tailor the cluster to your exact specifications. However, it may require more manual configuration and maintenance efforts.
- **Managed**: Platforms like [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine), [Amazon Elastic Kubernetes Service (EKS)](https://aws.amazon.com/es/eks/), and [Microsoft Azure Kubernetes Service (AKS)](https://azure.microsoft.com/es-es/products/kubernetes-service/) provide fully managed Kubernetes clusters, abstracting away much of the underlying infrastructure management. They're great for teams looking to offload operational overhead and focus on application development.
- **Enterprise**: Solutions like [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift), [Rancher](https://www.rancher.com/) or [VMware Tanzu](https://www.vmware.com/products/vsphere/vsphere-with-tanzu.html) offer enterprise-grade features and support, making them suitable for organizations with complex deployment requirements and regulatory compliance needs.
- **Lightweight**: For resource-constrained environments or low-end devices, lightweight Kubernetes distributions like [K3s](https://docs.k3s.io/) or [MicroK8s](https://microk8s.io/) are excellent choices. k3s is optimized for production workloads in resource-constrained environments, while MicroK8s provides a lightweight, easy-to-install Kubernetes for development and testing purposes. These distributions offer reduced resource overhead and simplified installation, making them ideal for edge computing or IoT deployments.

Consider factors such as ease of deployment, scalability, support, and compatibility with your existing infrastructure when evaluating Kubernetes distributions. Choose the one that best fits your project requirements and long-term goals, ensuring a smooth and efficient Kubernetes deployment experience.

## 2. Storage and Backup Strategy

Now it's time to review storage and backup – the backbone of our data resilience. Now, when it comes to picking the right storage solutions, we've got some solid options on the table. **Longhorn** and **Rook/Ceph** are likely your best bets for storage services.

Think of [Longhorn](https://longhorn.io/) as your go-to buddy for block storage. It's sleek, easy to use, and packs a punch when it comes to reliability. With features like built-in backup and restore capabilities, it's like having a safety net for your critical data.

Now, if you're into distributed file systems, [Rook/Ceph](https://rook.io/) is the big name of the market. It's like having your own personal cloud storage solution right in your Kubernetes cluster including its own S3 compatible solution. Plus, with Rook's seamless integration with Kubernetes, setting up and managing Ceph clusters becomes a breeze.

So, whether you're storing mission-critical databases or hosting media files for your next big app, Longhorn and Rook/Ceph have got you covered. Keep your data safe, keep it resilient.

## 3. Securing External Services

Alright, let's talk about locking down those external services trying to cozy up with your Kubernetes cluster. You don't want just anyone waltzing in and causing chaos, right?

So, first things first, let's beef up that security. Consider using an Ingress controller with TLS termination. You are ensuring encrypted communication between your services and the outside world. Plus, it adds that extra layer of protection against eavesdroppers.

Now, onto authentication – because not everyone should get access. [OAuth2 Proxy](https://oauth2-proxy.github.io/oauth2-proxy/) can be your best friend here. Think of them as the gatekeepers who verify everyone's credentials before they're allowed in. With an OAuth2 Proxy you can use providers like Google, Github, Keycloak or Dex, you can enforce user authentication and authorization policies, making sure only authorized users can interact with your services.

But wait, there's more! Ever heard of [Cloudflare Tunnel](https://www.cloudflare.com/es-es/products/tunnel/)? It's like your service's secret agent, keeping it hidden from prying eyes on the internet. Instead of exposing your services directly, Cloudflare Tunnel acts as a secure conduit, routing traffic through Cloudflare's network without exposing your server's IP address.

## 4. Certificate Management

What about managing certificates in Kubernetes? [Cert-Manager](https://cert-manager.io/)'s got your back. It's like your personal assistant for all things TLS certificates. With Cert-Manager, you can automate the whole shebang – issuing, renewing, and revoking certificates – without breaking a sweat.

Oh, and here's a pro tip: even for your local setups, consider using your own domain. Yeah, I know it sounds fancy, but trust me, it's worth it. It keeps things consistent across all environments, from local to production.

## 5. Securing Secrets

Keeping your sensitive data safe is like guarding the treasure chest of your app. With Kubernetes, you've got options aplenty for secret storage. You could start with the classic vanilla [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/), where your secrets are stored in your cluster's etcd database. It gets the job done, but it's like hiding your keys under the doormat – not the most secure option out there.

If you want to step up your game, consider [Sealed Secrets](https://sealed-secrets.netlify.app/). Think of them as secret messages locked tight with encryption, so even if someone snoops around, they can't make heads or tails of your precious data.

Then there's [External Secrets](https://external-secrets.io/latest/), where you outsource secret management to specialized tools like HashiCorp Vault or AWS Secrets Manager. It's like having a super-secure vault guarded by dragons – nobody's getting in without the right keys.

No matter which route you choose, make sure to encrypt your secrets at rest and in transit. Rotate them regularly like changing the combination on a safe, and you'll sleep soundly knowing your secrets are safe and sound.

## 6. Deployment Automation with GitOps CD Tool

Say goodbye to manual deployment headaches and hello to streamlined automation with GitOps Continuous Delivery (CD) tools like [Flux](https://fluxcd.io/) and [Argo CD](https://argo-cd.readthedocs.io/en/stable/). These tools take the hassle out of managing Kubernetes resources by syncing your cluster state with version-controlled Git repositories.

With [Flux](https://fluxcd.io/), you can define your desired cluster state in Git and let Flux automatically apply those changes to your cluster, keeping everything in sync effortlessly. It's like having a trusty assistant who ensures your applications are always up-to-date without you lifting a finger.

On the other hand, [Argo CD](https://argo-cd.readthedocs.io/en/stable/) provides a slick user interface for visualizing and managing your Kubernetes applications. Simply declare your desired state in Git, and Argo CD will continuously monitor your repository for changes, automatically applying them to your cluster. It's like having a personal Kubernetes concierge, always ready to cater to your deployment needs.

Whether you prefer Flux's simplicity or Argo CD's user-friendly interface, embracing GitOps principles with these CD tools will revolutionize your deployment workflows, making them smoother, more reliable, and ultimately more enjoyable.

## 7. Monitoring and Observability Stack

Next thing you want to keep a close eye on is what's happening in your Kubernetes cluster, right?

First up, we've got the [kube-prometheus-stack](https://github.com/prometheus-operator/kube-prometheus). It's like your Swiss Army knife for Kubernetes monitoring. With [Prometheus](https://prometheus.io/) for metrics and [Grafana](https://grafana.com/) for visualization, it's a powerhouse combo that gives you all the insights you need into your cluster's health and performance. Besides, you can install [Loki](https://github.com/grafana/loki) for logs aggregation.

But hey, maybe you're not into managing all that yourself. No worries! There are some fantastic SaaS options like [Datadog](https://www.datadoghq.com/) and [Dynatrace](https://www.dynatrace.com/). These guys handle all the heavy lifting for you, giving you top-notch monitoring and observability without the hassle of managing your own stack.

Plus, there are tools like [Robusta](https://home.robusta.dev/) which can help automate essential SRE actions, saving you time and headaches.

So whether you choose one or another depends on free cost or prefer the convenience of a managed service, there's something out there to suit your monitoring needs.
