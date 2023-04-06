# Implementing observability to secure containers and Kubernetes for EKS

Attend this in-depth, hands-on security workshop with Calico and AWS experts to get insights into Kubernetes cluster traffic, workload interactions, and security policy enforcement. The 90-minute interactive lab comes with your own provisioned Calico Cloud environment and a sample application. This workshop is designed to provide hands-on experience for you to:

## Agenda 

- View service-to-service communication to assess security risk from external threats
- Analyze and fix security policy gaps within the cluster
- Detect and prevent anomalous behaviors such as attempts to access restricted URLs
- Implement security policies in runtime to mitigate risk based on vulnerabilities found in build time

## Notes

### Deploy Workshop Content

```bash
kubectl apply -R -f workshops/001 
```

### Browse Demo App

```bash
kubectl port-forward -n dmz service/frontend 9000:80
```

### Enable L7 Logging for Services

```bash
kubectl annotate svc frontend -n dmz projectcalico.org/l7-logging=true
kubectl annotate svc checkoutservice -n trusted projectcalico.org/l7-logging=true
kubectl annotate svc emailservice -n trusted projectcalico.org/l7-logging=true
kubectl annotate svc productcatalogservice -n trusted projectcalico.org/l7-logging=true
kubectl annotate svc recommendationservice -n trusted projectcalico.org/l7-logging=true
kubectl annotate svc adservice -n trusted projectcalico.org/l7-logging=true
kubectl annotate svc cartservice -n trusted projectcalico.org/l7-logging=true
```

### Interactive access to Pod

```bash
kubectl exec -it netshoot -- bash
```

### Curl AWS Metadata service

```bash
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/
```
