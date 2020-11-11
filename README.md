# andes-wordpress
Testing ansible with kubernetes and amazon eks!


eks-example
Referenced from book https://www.ansibleforkubernetes.com/

## Set up AWS Cli and kubernetes config

This is to allow communication between local kubectl command to create/modify/delete aws resources directly on local IDE.

1. Install all required dependencies (openshift, boto, boto3, ansible-lint)
```bash
$ pip install requirements.txt
```
2. Install aws cli
```
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html
```
3. Configure cli (aws configure) --> use default profile
    to specify a profile (
```
$ aws configure --profile <name>
```
4. Set virtualenv ansible_python_interpreter path for Ansible inventory file 
e.g. 
```
[localhost]
127.0.0.1 ansible_connection=local ansible_python_interpreter=/Users/andes/.virtualenvs/ansible/bin/python
```

5. Check syntax and linting AWS cloudformation yml files
```bash
$ aws cloudformation validate-template file://<yml file>
```
6. Install aws-iam-authenticator to let kubectl to connect eks cluster
```
https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
```

OR use built in aws get token to configure $KUBECONFIG
```
https://awscli.amazonaws.com/v2/documentation/api/latest/reference/eks/get-token.html
```

OR just do the following (one aws cli command only!!)
```
https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html
```
```bash
$ aws eks --region <region-code> update-kubeconfig --name <cluster_name> --kubeconfig ~/.kube/eks-example
```
(this create a optional kubeconfig in specified path)

7. Make current kubectl to use remote Amazon EKS cluster context
```bash
export KUBECONFIG=~/.kube/eks-example
```

8. Run the playbook to deploy aws resources!
```bash
ansible-playbook -i inventory main.yml
```

Yet to try github actions to create Continous delivery !!!! (Update with daily cron job....)

9. Issue a SSL certificate
https://eu-west-2.console.aws.amazon.com/acm/

aws acm request-certificate --domain-name www.example.com --validation-method DNS

Idempotency call
aws acm request-certificate --domain-name www.example.com --validation-method DNS --idempotency-token 91adc45q

OR using aws cli 


10. Apply certficate to wordpress.yml
aws_ssl_cert_arn: "arn:aws:acm:eu-central-1:some-account-id:certificate/some-cert-id"
https://kubernetes-on-aws.readthedocs.io/en/latest/user-guide/tls-termination.html


## Run playbooks
1. Create a new EKS cluster, VPC, worker nodes    
```bash
$ ansible-playbook -i cloudformation/inventory cloudformation/deploy_test.yml
```
2. Export kubeconfig
```bash
$ aws eks --region eu-west-2 update-kubeconfig --name eks-andes --kubeconfig ~/.kube/eks-andes
$ export KUBECONFIG=~/.kube/eks-andes
```
3. Deploy ELB, wordpress, mysql services, pods
```bash
$ ansible-playbook -i inventory deploy_test.yml
```

## Delete resources

1. Delete kubernetes services
```bash
$ ansible-playbook -i inventory delete.yml
```

2. Delete aws resources (eks, storage, and vpc)
```bash
$ ansible-playbook -i cloudformation/inventory cloudformation/delete.yml
```