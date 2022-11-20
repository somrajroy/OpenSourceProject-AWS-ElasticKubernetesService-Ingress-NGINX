AWS EKS Ingress with NGINX Ingress Controller<br/><br/>
* Create an EKS cluster
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
  $ kubectl get pods -n ingress-basic -l app.kubernetes.io/name=ingress-nginx <br/>
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
  $ http://<<-DNS name as given under external ip column->>/cats
  $ http://<<-DNS name as given under external ip column->>/dogs
  $ http://<<-DNS name as given under external ip column->>/birds
* Clean up resources <br/>
  $ kubectl get all <br/>
  $ kubectl delete all --all <br/>
  $ kubectl delete ingress --all <br/>
  $ kubectl delete all --all -n ingress-basic <br/>
  $ kubectl delete namespace ingress-basic <br/>
* List existing resources <br/>
  $ kubectl get all <br/>
* Clean up AWS enviornment <br/>
  $ eksctl delete cluster --name k8sdemo <br/><br/>
#### Further references :
* [How do I provide external access to multiple Kubernetes services in my Amazon EKS cluster?](https://aws.amazon.com/premiumsupport/knowledge-center/eks-access-kubernetes-services/)<br/>
* [Introducing the AWS Load Balancer Controller](https://aws.amazon.com/blogs/containers/introducing-aws-load-balancer-controller/)<br/>
* [How to Set Up Ingress Controller in AWS EKS](https://towardsdatascience.com/how-to-set-up-ingress-controller-in-aws-eks-d745d9107307)<br/>
* [How do I set up an ALB using the AWS Load Balancer Controller on an EC2 node group in EKS?](https://www.youtube.com/watch?v=TUS8KWaGKco)<br/>
  
  

