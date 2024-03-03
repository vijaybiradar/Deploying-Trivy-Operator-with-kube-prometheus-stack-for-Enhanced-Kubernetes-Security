# Deploying Trivy Operator with kube-prometheus-stack for Enhanced Kubernetes Security.

Step 1: Create a Namespace for Monitoring
```
kubectl create ns monitoring
```

Step 2: Add Prometheus Helm Chart Repository


Step 2: Add Prometheus Helm Chart Repository

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/dc1f1a9b-e2b1-4c3a-b3f1-656936c1145d)

Untar the Helm chart for prometheus-community/kube-prometheus-stack:

```
helm pull --untar prometheus-community/kube-prometheus-stack
```


Step 3: Customize and Install Kube Prometheus Stack
Edit values.yaml to customize Kube Prometheus Stack:
```
prometheus:
  prometheusSpec:
    serviceMonitorSelectorNilUsesHelmValues: true
    serviceMonitorSelector: {}
    serviceMonitorNamespaceSelector: {}
```

Install Prometheus Helm Chart:

```
helm upgrade --install kube-prom prometheus-community/kube-prometheus-stack -n monitoring -f values.yaml --install --debug 
```

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/f4eb40f5-88bb-44f9-a2cb-d52b060822f3)

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/c65769e6-4d2d-4603-98c1-457ad2d8abee)



Step 4: Add Aqua Security Helm Chart Repository

```
helm repo add aqua https://aquasecurity.github.io/helm-charts/
helm repo update
```

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/ed6853b2-6709-4d2f-a844-47f608c247c6)

untar the Helm chart for trivy-operator from the aqua/trivy-operator

```
helm pull --untar aqua/trivy-operator
```

Step 5: Customize and Install Trivy Operator
Edit values.yaml to customize Trivy Operator installation:
```
serviceMonitor:
  # enabled determines whether a serviceMonitor should be deployed
  enabled: true
trivy:
  ignoreUnfixed: true
```

Install Trivy Operator:

```
helm install trivy-operator aqua/trivy-operator -n monitoring -f values.yaml
```

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/28334278-d6e7-4211-a638-fc2147657b15)

Step 6: Access Grafana Dashboard

Port Forward Grafana Service
```
kubectl port-forward svc/prom-grafana 3000:80 -n monitoring
```
Access Grafana Dashboard
Open a web browser and go to localhost:3000.

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/6c9bcae2-a693-4e83-b02b-6e84cdc11dba)

Login with the following credentials:
Username: admin
Password: (retrieve password using the following command)
```
kubectl get secret prom-grafana -o jsonpath="{.data.admin-password}" -n monitoring | base64 --decode ; echo
```
Username: admin
Password: prom-operator


7. Import Trivy Dashboard into Grafana
Navigate to "Dashboards" in Grafana.
Click on "New" and select "Import".
Enter dashboard ID 17813 and click on "Load".

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/e55a2a88-54b1-4f74-be75-1ca6ecafc92e)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/33eed199-0a20-4cc4-81fb-5ab6b4ccfee4)


Navigate to "Dashboards" in Grafana.
Click on "New" and select "Import".
Enter dashboard ID 16652 and click on "Load".
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/fbed4ebe-9da8-4e4b-ba0d-6f3cc4c4ba27)

Navigate to "Dashboards" in Grafana.
Click on "New" and select "Import".
Enter dashboard ID 16742 and click on "Load".

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/68f13f6d-2ff2-4703-a019-f2e4dee9e937)



8.Explore Trivy Operator metrics through Prometheus to gain insights into image vulnerabilities and enhance Kubernetes cluster security.:

Total vulnerabilities found in your cluster:

sum(trivy_image_vulnerabilities)
Total misconfiguration identified in your cluster:

sum(trivy_resource_configaudits)
Exposed Secrets discovered by the Trivy Operator in your cluster:

sum(trivy_image_exposedsecrets)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/3839d47a-ff87-4550-b661-c289a26ddc44)




9. Query Reports

After Trivy Operator starts running scans, you can query the generated reports using the following commands:

```
kubectl get vulnerabilityreports --all-namespaces -o wide
kubectl get configauditreports --all-namespaces -o wide
kubectl get infraassessmentreports --all-namespaces -o wide
kubectl get rbacassessmentreports --all-namespaces -o wide
kubectl get exposedsecretreport --all-namespaces -o wide
kubectl get clustercompliancereport --all-namespaces -o wide
```

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/55490760-843d-4484-bb7b-53b9c725a9d5)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/6e0a2efa-ed0f-4101-bd92-9a0a4a492e2e)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/54bfe814-239e-4a06-a029-e26aecb1f481)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/f8287508-64dd-4905-8a80-13f28a8f1192)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/191ae33f-4c12-444b-b9de-7dcc33ed4c44)
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/8cefac58-4d9a-4f9e-9f0e-7a5569da4fff)





