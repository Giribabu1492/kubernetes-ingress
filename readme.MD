# ingress controller

REGION_CODE=us-east-1
CLUSTER_NAME=expense
ACC_ID=805160322688


### PERMISSIONS   one platform to other from eks to aws 

1 .OIDC provider 
...
eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve
...  
* IAM Policy
...
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.13.4/docs/install/iam_policy.json
...
* Create IAM role 
...
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
...
* Provide access to eks through IAM policy 
...
eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME  \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::$ACC_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve
...


### INSTALL DRIVERS 
...
* Add the EKS chart repo to Helm
...
helm repo add eks https://aws.github.io/eks-charts
...
* Installing aws load balancer 
...
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
...


eksctl create iamserviceaccount \
--cluster=expense \
--namespace=expense \
--name=expense-mysql-secret \
--attach-policy-arn=arn:aws:iam::805160322688:policy/ExpenseMySQLSecretRead \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve