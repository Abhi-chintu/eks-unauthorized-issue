# eks-unauthorized-issue

Consider Admin have created the eksCluster using eksctl command. So once you login to bastion host as a IAM user and setup **aws configure** with your credentials. And now if you trigger **kubectl get nodes** it will throw **error: You must be logged in to the server (Unauthorized)**

Follow the below steps to solve the issue
------------------------------------------
    aws sts get-caller-identity

