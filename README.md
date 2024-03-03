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
helm upgrade --install kube-prom prometheus-community/kube-prometheus-stack -f values.yaml --install --debug 
```
Step 4: Add Aqua Security Helm Chart Repository

```
helm repo add aqua https://aquasecurity.github.io/helm-charts/
helm repo update
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
Enter dashboard ID 16742 and click on "Load".

![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/68f13f6d-2ff2-4703-a019-f2e4dee9e937)
Navigate to "Dashboards" in Grafana.
Click on "New" and select "Import".
Enter dashboard ID 16652 and click on "Load".
![image](https://github.com/vijaybiradar/Trivy-Operator-in-Kubernetes-Cluster./assets/38376802/fbed4ebe-9da8-4e4b-ba0d-6f3cc4c4ba27)



8.Explore Trivy Operator metrics through Prometheus to gain insights into image vulnerabilities and enhance Kubernetes cluster security.:

prom query:sum(trivy_image_vulnerabilities)
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
