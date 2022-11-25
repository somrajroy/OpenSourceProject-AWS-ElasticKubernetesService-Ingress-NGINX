AWS EKS Ingress with NGINX Ingress Controller & eksctl <br/><br/>
* Kubernetes supports a high-level abstraction called Ingress, which allows host- or URL-based routing. An Ingress is a core concept of Kubernetes. It is always implemented by a third party proxy; these implementations are known as ingress controllers. An ingress controller is responsible for reading the ingress resource information and processing that data accordingly. Different ingress controllers have extended the specification in different ways to support additional use cases.<br/>
* Ingress can route traffic to multiple services inside the cluster <br/>
![image](https://user-images.githubusercontent.com/92582005/202991943-e65e2453-760d-4bd5-bd8c-86ab19de6166.png) <br/>
![image](https://user-images.githubusercontent.com/92582005/202992080-7568c0b1-4abf-4f8b-a20d-ec11c49f46d7.png) <br/>
* Clone the repository and navigate to the folder lab-05 <br/>
* Open CLI in administrator mode and login <br/>
  $ aws configure <br/>
* Create an EKS cluster <br/>
  $ eksctl create cluster --name k8sdemo --version 1.23 --region us-west-2 --nodegroup-name k8snodes --node-type t3.medium --nodes 2 <br/>
* Switch context <br/>
  $ aws eks --region us-west-2 update-kubeconfig --name k8sdemo <br/>
* Refer below commands for verification of contexts.
  $ kubectl config view <br/>
  $ kubectl config current-context (output should be k8sdemo).<br/>
  $ kubectl config get contexts <br/>
  $ kubectl config use-context <<-context name->> <br/>
* Check nodes and pods in the EKS cluster.<br/>
  $ kubectl get nodes -o wide <br/>
  $ kubectl get pods <br/>
* Install Helm and update the repo. Then install the NGINX inngress controller without customizing the defaults.<br/>
  $ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx <br/>
  $ helm repo update <br/>
  $ helm install nginx-ingress ingress-nginx/ingress-nginx --create-namespace --namespace ingress-basic --set controller.replicaCount=2 --set   controller.nodeSelector."kubernetes.io/os"=linux --set defaultBackend.nodeSelector."kubernetes.io/os"=linux <br/>
* Verify NGINX controller installation <br/>
  $ kubectl get pods -n ingress-basic -l app.kubernetes.io/name=ingress-nginx --watch <br/>
* Inspect the ingress controller Service & pods <br/>
  $ kubectl get svc -n ingress-basic <br/>
    ![image](https://user-images.githubusercontent.com/92582005/202901389-74dac10d-f48a-42bf-827d-051c5cdfef9d.png) <br/>
  $ kubectl get pods -n ingress-basic -l app.kubernetes.io/name=ingress-nginx <br/>
    ![image](https://user-images.githubusercontent.com/92582005/202901432-f2a989c9-9929-4f19-83b2-d9c00fbe65c7.png) <br/>
* Deploy the applications (pods and ClusterIP services) <br/>
  $ kubectl apply -f cats.yaml <br/>
  $ kubectl apply -f dogs.yaml <br/>
  $ kubectl apply -f birds.yaml <br/>
* Create the ingress resource <br/>
  $ kubectl apply -f ingress.yaml <br/>
* List existing pods & services <br/>
  $ kubectl get pods <br/>
  $ kubectl get svc <br/>
* List existing ingress <br/>
  $ kubectl get ingress <br/>
* Access the application - Get the ingress controller External IP (type LoadBalancer). AWS gives the DNS name and not the IP and it will come under "External IP" column. It can be also found in EC2 console under "Load Balancers". <br/>
  $ kubectl get svc -n ingress-basic
  ![image](https://user-images.githubusercontent.com/92582005/202900773-90d48063-fe27-48ef-b5d3-3ee7f5887397.png) <br/>
* Browse to the cats, dogs and birds service <br/>
  $ http://<<-DNS name as given under external ip column->>/cats <br/>
  $ http://<<-DNS name as given under external ip column->>/dogs <br/>
  $ http://<<-DNS name as given under external ip column->>/birds <br/>
* Clean up resources <br/>
  $ kubectl get all <br/>
  $ kubectl delete all --all <br/>
  $ kubectl delete ingress --all <br/>
  $ kubectl delete all --all -n ingress-basic <br/>
  $ kubectl delete namespace ingress-basic <br/>
  $ kubectl delete ingressclass nginx <br/>
* List existing resources <br/>
  $ kubectl get all <br/>
* Clean up AWS enviornment <br/>
  $ eksctl delete cluster --name k8sdemo <br/><br/>
  $ kubectl get -A ValidatingWebhookConfiguration <br/>
  $ kubectl delete ValidatingWebhookConfiguration nginx-ingress-ingress-nginx-admission <br/>
#### Further references :
* [What is Amazon EKS?](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)<br/>
* [How do I provide external access to multiple Kubernetes services in my Amazon EKS cluster?](https://aws.amazon.com/premiumsupport/knowledge-center/eks-access-kubernetes-services/)<br/>
* [Introducing the AWS Load Balancer Controller](https://aws.amazon.com/blogs/containers/introducing-aws-load-balancer-controller/)<br/>
* [How to Set Up Ingress Controller in AWS EKS](https://towardsdatascience.com/how-to-set-up-ingress-controller-in-aws-eks-d745d9107307)<br/>
* [How do I set up an ALB using the AWS Load Balancer Controller on an EC2 node group in EKS?](https://www.youtube.com/watch?v=TUS8KWaGKco)<br/>
* [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)<br/>
* [Using a Network Load Balancer with the NGINX Ingress Controller on Amazon EKS](https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/)<br/>
* [How do I configure the NGINX Ingress Controller to increase the client request body, activate CORS to allow additional headers, and use WebSocket to work with Amazon EKS?](https://aws.amazon.com/premiumsupport/knowledge-center/eks-configure-nginx-ingress-controller/)<br/>
* [NGINX Ingress Controller for Amazon EKS](https://www.qloudx.com/nginx-ingress-controller-for-amazon-eks-frequently-used-annotations/)<br/>
  
  

