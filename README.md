# andes-wordpress
Testing ansible with kubernetes and amazon eks!


eks-example
Referenced from book https://www.ansibleforkubernetes.com/


1. pip install requirements.txt
2. install aws cli
(https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)
3. configure cli (aws configure) --> use default profile
    to specify a profile (aws configure --profile <name>)
4. inventory set virtualenv ansible_python_interpreter path
e.g. 

[localhost]
127.0.0.1 ansible_connection=local ansible_python_interpreter=/Users/andes/.virtualenvs/ansible/bin/python


5. Linting yml using aws cloudformation validate-template file://<yml file>

6. Install aws-iam-authenticator to let kubectl to connect eks cluster
https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html

or use built in aws get token to configure $KUBECONFIG
https://awscli.amazonaws.com/v2/documentation/api/latest/reference/eks/get-token.html

https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html

aws eks --region <region-code> update-kubeconfig --name <cluster_name> --kubeconfig ~/.kube/eks-example
(this create a optional kubeconfig in specified path)

7. export KUBECONFIG=~/.kube/eks-example

8. ansible-playbook -i inventory main.yml


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
