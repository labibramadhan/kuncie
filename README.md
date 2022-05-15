## Cost Estimation
This architecture will be hosted on Google Cloud Platform in Singapor (asia-southeast1) region.

### Jenkins VM

For Jenkins, we could start with only 1 VM but with at least 4 vCPU, we can choose e2-standard-4 instance with at least 200GB disk, that will cost [$158.09 / month](https://cloud.google.com/products/calculator/#id=2476e65b-8c4f-47c4-a535-379ebbc8b364)

### Kubernetes Cluster

This cluster require at least 3 nodes, i assume daily 10K active users only need 3 nodes with medium compute resource.

But i will take an average assumption, the first 4 months will be 3 nodes, the next 4 months will be 4 nodes, and the last 4 months will be 5 nodes.

For 3 nodes it will cost [$237.14 / month](https://cloud.google.com/products/calculator/#id=0410191e-2135-4848-9afe-4feac1e87510), for 4 nodes it will cost [$316.19 / month](https://cloud.google.com/products/calculator/#id=e0b26344-fb47-4402-8bca-80d6c798cfa0), and for 5 nodes it will cost [$395.23 / month](https://cloud.google.com/products/calculator/#id=24b92a0f-434f-4c24-b823-19648538602a)


### 1 Year Cost Estimation

Jenkins VM: $158.09 * 12 months = $1897.08
Kubernetes first 4 months with 3 nodes: $237.14 * 4 months = $948.56
Kubernetes next 4 months with 4 nodes: $316.19 * 4 months = $1264.76
Kubernetes last 4 months with 5 nodes: $395.23 * 4 months = $1580.92

So the total will be $5691.32 for the first 1 year.

## Operational Excellence

### Develop Faster
The CI/CD will help software engineer deliver faster, engineer only focus to code and the deployment or the test is going to be taken care by the CI/CD tools. And the delivery of the service that is later to be deployed to the Kubernetes is going to be taken care by Helm. Helm will help us on mostly to deliver safely to Kubernetes by using Rolling Upgrade, if the Rolling Upgrade failed, Helm is ready to rollback the deployment to the previous successful deployment.

### Operate Easier
Jenkins is easy to operate in terms of monitoring the CI/CD progress, Jenkins also provide notification plugin to make deployment process more fun without the need of checking the deployment progress regularly.

But how about the server monitoring? So Grafana, Prometheus, Metrics Server, Blackbox Exporter, and Loki stack will help the server monitoring. With the help of Grafana and Loki, we able to observe the logs of our services, and with the help of Grafana, Prometheus, and Metrics Server, we basically have Kubernetes cluster monitoring in the box, but because we already have Prometheus in the cluster, later we can add more monitoring metrics to our own backend to be consumed by Prometheus and later we can display our own metrics to the Grafana dashbord. The end game of this monitoring stack is to have required alerts that we must develop later, one of them is downtime alert by utilizing Blackbox Exporter, the rest is some of our cluster alerts and backend alerts.


## Security

### Protect Assets from Unwanted Use
When it come to protecting assets from unwanted use, this one is likely talking about microservices concept that some of the services may only be accessed internally, so protecting our internal services can be done by not exposing the service itself, so we wouldn’t configure any ingress for the private services. But if this private service required to be accessed from outside of the cluster, then we may add ingress to the services but we should whitelist the inbound IPs of those services. If some of the services expected to be accessed at certain limited requests per second, than we can utilize nginx ingress rate limit feature.


## Reliability

### Run Correctly and Consistently in Time of Failure
Kubernetes have in the box probe monitoring that is going to check every single service regularly to notice wether one of our service is down or alive, if it is down then Kubernetes will restart our service automatically. So by utilizing this probe monitoring, what we will have is consistent uptime for our services, of course we should never only serve 1 pod to our service, always more than 1 pod for avoiding any downtime.


## Performance Efficiency

### Compute Resources Efficiency
What i would like to mention in this section is the Kubernetes autoscaling feature, there is 2 types autoscaling that we can utilize here, Horizontal Autoscaling and Cluster autoscaling. Horizontal Autoscaling able to protect our workload by controlling our service by the configured cpu / memory metrics, if the current running workload metrics is over than the our configured metrics, then Kubernetes will add more pods for our services, when the new pods is having very low cpu/memory usage then Kubernetes will eliminate unneeded pods. This is very efficient for our workload.

The other one is Cluster autoscaling, Kubernetes for production usage usually start with 3 nodes (virtual machines), this autoscaling will add more node based on detection like if there are any pending pods on our cluster because of Kubernetes couldn’t serve more pods due to all of the available nodes cpu / memory are full, so autoscaling will act at this moment. And also the autoscaling will remove unneeded nodes if the current total pods is capable to be handled without additional nodes.

So this 2 types of autoscaling will help a lot in terms of performance efficiency. No matter what time our peak time will be, our Kubernetes cluster will ready most of the time.


## Cost Optimization

### Lowest Price Point While Serving the Requirements

In this architecture i proposing, Jenkins may only require 1 VM to operate, and we may start only with 3 nodes for the Kubernetes cluster, so with total 4 virtual machines, this schema already covering CI/CD, Monitoring, Alerts, and a production grade Kubernetes cluster that is ready to serve whatever the workload will be as long as the cluster and the services able to autoscale.
