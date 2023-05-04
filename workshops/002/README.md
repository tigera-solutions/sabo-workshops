# Microsoft Azure: Hands-on workshop: Container security in AKS

Attend this in-depth, hands-on, AKS-focused security workshop with Microsoft Azure and Calico experts to learn how to prevent, detect, and mitigate threats in containers running on AKS. The 90-minute interactive lab comes with your own provisioned Calico Cloud environment and a sample application. You will leave this workshop with hands-on experience on how to:

## Agenda 

- Scan container images and block deployment of vulnerable images
- Preview and enforce security policies to protect vulnerable workloads
- Implement workload access controls to secure egress and lateral movement during runtime

## Notes

### Image Assurance

Detect vulnerabilities in container images at build and runtime.

Before deploying our application, let's [scan the images for vulnerabilities](https://docs.calicocloud.io/image-assurance/scan-image-registries) using the CLI.

```
docker pull quay.io/jsabo/log4shell-vulnerable-app:latest
tigera-scanner scan quay.io/jsabo/log4shell-vulnerable-app:latest
```

### Admission Controller

Use policy to prevent vulnerable container images from being deployed.

Before [deploying the admission controller](https://docs.calicocloud.io/image-assurance/install-the-admission-controller), we have to create some certificates to secure the communication between the controller and the Kubernetes api.  Once we have the certs we can add them to the admission controller deployment and apply them to the cluster.  

```
curl https://installer.calicocloud.io/manifests/v3.14.1-16/manifests/generate-open-ssl-key-cert-pair.sh | bash
sed -i '' "s/BASE64_CERTIFICATE/$(base64 < admission_controller_cert.pem)/g" workshop/iaac/tigera-image-assurance-admission-controller-deploy.yaml
sed -i '' "s/BASE64_KEY/$(base64 < admission_controller_key.pem)/g" workshop/iaac/tigera-image-assurance-admission-controller-deploy.yaml
kubectl create -f workshop/iaac
```

Let's deploy the workshop applications.

```
kubectl apply -f apps
```

Notice the following error in the output.

```
Error from server (Action 'Reject' enforced by ContainerPolicy reject-failed rule index 1): error when creating "apps/java-app.yaml": admission webhook "image-assurance.tigera.io" denied the request: Action 'Reject' enforced by ContainerPolicy reject-failed rule index 1
```

The deployment of the `java-app` will fail because the Admission Controller policy is preventing the deployment of vulnerable workloads.


### Vulnerability Management

Our admission controller should prevent running applications with CVSS scores above 7.  These are the vulnerabilities with `critical` and `high` CVSS scores.  We want to override the policy that prevents the deployment of the `java-app` due to critical vulnerabilities so that we can access it.

```
kubectl label namespaces java-app tigera-admission-controller-
kubectl apply -f apps/java-app.yaml
```

