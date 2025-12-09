# Demo Commands – OPS410 Project 2 (AKS Autoscaling)

## Check cluster and basic resources

```bash
kubectl get nodes
kubectl get pods
kubectl get svc
kubectl get hpa
Watch pods and HPA during autoscaling
bash
Copy code
# Tab 1 – watch pods
kubectl get pods -w

# Tab 2 – watch HPA metrics
kubectl get hpa -w
Run load test (Tab 3)
bash
Copy code
./hey_linux_amd64 -z 1m -c 50 http://<external-ip>/
Replace <external-ip> with your LoadBalancer EXTERNAL-IP
