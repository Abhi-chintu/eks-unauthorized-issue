# eks-unauthorized-issue

Consider Admin have created the eksCluster using eksctl command. So once you login to bastion host as a IAM user and setup **aws configure** with your credentials. And now if you trigger **kubectl get nodes** it will throw **error: You must be logged in to the server (Unauthorized)**

Follow the below steps to solve the issue
------------------------------------------
    aws eks --region ap-south update-kubeconfig --name <clustername>
    aws sts get-caller-identity

With the above command check which role the cluster is assuming. If it is assuming the IAM user role, then we need to edit the trust relationship of the role which the ekscluster is using. 

Goto Eks dashboard --> access --> click on the role --> edit the trust relationship --> 

    {
                "Sid": "Statement1",
                "Effect": "Allow",
                "Principal": {
                    "AWS": "arn:aws:iam::<account-number>:user/<IAM-user-name>"
                },
                "Action": "sts:AssumeRole"
     }

Assume the specific IAM role and create the temporary credentials

    aws sts assume-role \
    --role-arn arn:aws:iam::<account-number>:role/<eks-IAM-role> \
    --role-session-name eks-access

By giving the above command, we get temporary credentials they can be used to make AWS API requests as if you were the assumed role. 

    eval $(aws sts assume-role --role-arn arn:aws:iam::396202605633:role/Admin-eks --role-session-name test | jq -r '.Credentials | "export AWS_ACCESS_KEY_ID=\(.AccessKeyId)\nexport AWS_SECRET_ACCESS_KEY=\(.SecretAccessKey)\nexport AWS_SESSION_TOKEN=\(.SessionToken)\n"')

The eval command you provided uses the AWS CLI and jq to assume the "Admin-eks" role and export the temporary credentials as environment variables. This is a common approach to dynamically set AWS credentials for a specific role in the current shell session.

Now update the kubeconfig file and try to give **kubectl get nodes**
