 Steps involved:
1. Set up prerequisites: Install awscli, eksctl, kubectl, helm 
2. Installed EKS with Fargate.
     ekctl create cluster --name <cluster-name> --region <region-name> --fargate
3. Acquired or updated the KubeConfig file for CLI access to cluster resources.
     aws eks update-kubeconfig --name <cluster-name> --region <region-name>
4. For deploying the pods other then the default namespaces we need to create the new namespace for this deploy the new fargate profile and allow the pods to run on the new namespace.
  Create the custom fargate profile allowing the namespace created to deploy the pods in that.
     eksctl create fargateprofile --cluster demo-cluster --region us-east-1 --name alb-sample-app --namespace game-2048
5. Deployed the 2048 app as a K8s Pod, created a corresponding service, and set up an Ingress resource for traffic routing.
     kubectl apply -f <file.yaml>
6. Established an IAM OIDC Provider to grant ALB Controller access to the Application Load Balancer.
7. Implemented the Ingress Controller to automate ALB Controller creation.
8. Created a service account with an IAM Role and Policy for Load Balancer access.
     eksctl create iamserviceaccount --cluster=demo-cluster --namespace=game-2048 --name=aws-load-balancer-controller --role-name <role-name> --attach-policy-arn=<policy-arn> --approve
9. Deployed ALB Controller using Helm Charts, interfacing with the service account.
    helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n game-2048 \
    --set clusterName=<cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=<serviceaccount-name> \
    --set region=<region-name> \
    --set vpcId=<vpc-id>
10. Accessed the deployed 2048 application with the DNS URL of the Load Balancer in the public subnet.
