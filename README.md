# kafka-connect-fargate
boilerplate of docker compose deployment to fargare

## Prerequisuites
1. Follow the guide in order to install awscli v1 - https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html
2. Follow the guide in order to install esc-cli - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html

## Deployment
1. Execute command in order to create IAM role - `aws iam --region eu-central-1 create-role --role-name ecsTaskExecutionRole --assume-role-policy-document file://task-execution-assume-role.json`
2. Attach a policy to the role - `aws iam --region eu-central-1 attach-role-policy --role-name ecsTaskExecutionRole --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy`
3. Create a cluster configuration, which defines the AWS region to use, resource creation prefixes, and the cluster name to use with the Amazon ECS CLI - `ecs-cli configure --cluster test --default-launch-type FARGATE --config-name test-config --region eu-central-1`
4. Create a CLI profile using your access key and secret key - `ecs-cli configure profile --access-key AWS_ACCESS_KEY_ID --secret-key AWS_SECRET_ACCESS_KEY --profile-name test-profile`
5. Create a cluster and wright down VPC ID and Subnets - `ecs-cli up --cluster-config test-config --ecs-profile test-profile`
6. Using the AWS CLI, retrieve the default security group ID for the VPC. Use the VPC ID from the previous output `aws ec2 describe-security-groups --filters Name=vpc-id,Values=vpc-05b06be99632813ac --region eu-central-1`
7. Update `ecs-params.yml` file with Subnets and Security Group details
8. Deploy container - `ecs-cli compose --project-name tutorial service up --create-log-groups --cluster-config test-config --ecs-profile test-profile`

## OPS

1. View the Running Containers on a Cluster - `ecs-cli compose --project-name tutorial service ps --cluster-config test-config --ecs-profile test-profile`
2. View the Container Logs - `ecs-cli logs --task-id <task id from the command above> --follow --cluster-config test-config --ecs-profile test-profile`

## Delete service and cluster

1. Stop and delete service - `ecs-cli compose --project-name tutorial service down --cluster-config test-config --ecs-profile test-profile`
2. Delete ECS cluster - `ecs-cli down --force --cluster-config test-config --ecs-profile test-profile`