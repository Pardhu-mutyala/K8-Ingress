# Ingress Controller
## OIDC Provider
trust and authentication provider

REGION_CODE=us-east-1
CLUSTER_NAME=robosho-cluster
ACC_ID = <your AWS ID>
...

eksctl utils associate-iam-oidc-provider \
    --region <region-code> \
    --cluster <your-cluster-name> \
    --approve
...

## DOwnload IAM Policy
...
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v3.0.0/docs/install/iam_policy.json
...
## Create IAM policy
...
Create an IAM policy named AWSLoadBalancerControllerIAMPolicy. If you downloaded a different policy, replace iam-policy with the name of the policy that you downloaded.
...
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
...
    CReate IAM role amd K8 service account

    Take note of the policy ARN that's returned.
Create an IAM role and Kubernetes ServiceAccount for the LBC. Use the ARN from the previous step.
...

## Create IAM Role and K8 ServiceAccount.Mapping between IAM role and K8 ServiceAccount
...
eksctl create iamserviceaccount \
--cluster=<cluster-name> \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region <region-code> \
--approve
...

## Install Load balancer controller drivers

...
helm repo add eks https://aws.github.io/eks-charts
...
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
...