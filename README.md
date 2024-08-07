# application-demo

<img width="1440" alt="Screenshot 2024-07-29 at 14 25 43" src="https://github.com/user-attachments/assets/f5889cf8-1a82-4094-8e46-3ef87a699980">

<img width="1440" alt="Screenshot 2024-07-29 at 14 27 37" src="https://github.com/user-attachments/assets/b5793db3-ede2-481a-8a3a-e0bfd76cd795">

<img width="1440" alt="Screenshot 2024-07-29 at 14 29 07" src="https://github.com/user-attachments/assets/0badcec8-ca0d-431f-b18c-2489919daed7">

<img width="1440" alt="Screenshot 2024-07-29 at 14 28 53" src="https://github.com/user-attachments/assets/0688193d-b737-45ae-98ff-01e37f3271b8">

Istio Deployment on DigitalOcean Kubernetes
step-by-step process to deploy Istio on a DigitalOcean Kubernetes cluster, deploy a sample application, connect it with the service mesh in Sidecar mode, and deploy the Istio dashboard for monitoring.

Prerequisites

doctl (DigitalOcean command-line tool)
kubectl (Kubernetes command-line tool)
Istio

Steps

1. Set Up DigitalOcean Kubernetes Cluster
Install doctl:


curl -sL https://github.com/digitalocean/doctl/releases/download/v1.60.0/doctl-1.60.0-linux-amd64.tar.gz | tar -xzv
sudo mv doctl /usr/local/bin
Authenticate doctl:


doctl auth init --access-token dop_v1_87453f9b703904bdbfe020ed5eda5315831ea4997ca3fbc41a48e204cba9e910
Save Kubernetes cluster configuration:


doctl kubernetes cluster kubeconfig save 4af97b9b-0d2c-4ab1-994f-4dff9de57459
Verify your kubectl context:


kubectl config get-contexts
kubectl get nodes

2. Install Istio
Download Istio:

curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
Install Istio on your cluster:


istioctl install --set profile=demo -y
Enable Istio sidecar injection for the default namespace:


kubectl label namespace default istio-injection=enabled

3. Deploy the Sample Application
Deploy the Bookinfo sample application:


kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
Verify the services and pods are deployed:


kubectl get services
kubectl get pods

4. Expose the Application using Istio Ingress Gateway
Apply the Gateway and VirtualService configuration:


kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
Verify the Gateway and VirtualService are created:


kubectl get gateway
kubectl get virtualservice
Get the external IP of the Ingress Gateway:


kubectl get svc istio-ingressgateway -n istio-system
Set the Gateway URL environment variable:


export GATEWAY_URL=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo "http://$GATEWAY_URL/productpage"
Access the application:
Open a web browser and navigate to the URL printed in the previous step, e.g., http://<EXTERNAL-IP>/productpage.

5. Deploy the Istio Dashboard
kubectl apply -f samples/addons
get istioctl dashboard kiali

to access application Externally
kubectl get svc istio-ingressgateway -n istio-system

http://<EXTERNAL-IP>/productpage

export GATEWAY_URL=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo "http://$GATEWAY_URL/productpage"

to access application internally & dashboard kiali 
http://localhost:8080
http://localhost:20001/kiali



Paths to YAML Files

Bookinfo Application YAML: istio-1.22.3/samples/bookinfo/platform/kube/bookinfo.yaml

Bookinfo Gateway YAML: istio-1.22.3/samples/bookinfo/networking/bookinfo-gateway.yaml

Istio Add-ons: istio-1.22.3/samples/addons

