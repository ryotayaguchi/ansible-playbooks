# How to use

## Prerequisites
- AWS default VPC
   - VPC name is `default`
   - CIDR is `172.31.0.0/16`
   - All subnets are public subnets
- AWS EC2 instance for Ansible
   - Amazon Linux 2023 or something similar
   - Running the default VPC
   - IAM role is attached with the below permission
      - AmazonEC2FullAccess
      - AmazonRoute53FullAccess
      - AmazonSSMFullAccess

## Ansible setup
```bash
sudo dnf install -y git,python3-pip
pip install -U pip,ansible,boto3

git clone git@github.com:ryotayaguchi/ansible-playbooks.git
ssh-keygen -t ed25519 -f ~/.ssh/stag01.pem
```

## Deploy ALB and EC2 instances
```bash
ansible-playbook route53.yml -e env=stag01 -t create
ansible-playbook ec2-alb.yml -e env=stag01 -t create
ansible-playbook ec2-ins.yml -e env=stag01 -t key,create -e version=1.24
```

## Rolling replacement of EC2 instances
```bash
for T in delete create stop; do ansible-playbook ec2-ins.yml -e env=stag01 -t ${T} -e version=1.25; done
```

## Cleanup
```bash
ansible-playbook ec2-ins.yml -e env=stag01 -t delete,cleanup
ansible-playbook ec2-alb.yml -e env=stag01 -t delete
ansible-playbook route53.yml -e env=stag01 -t delete
```
