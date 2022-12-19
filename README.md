# Docker Build Args

This example showing Docker build with `--build-args` and CircleCI usage orb ecr integration.

## Requirements

### AWS

Create two ECR private repositories, for example: node-base and node-deploy

Create IAM SA account with policy inline:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ecr:*"
            ],
            "Resource": [
                "arn:aws:ecr:us-east-1:ACCOUNT_ID:repository/node-base",
                "arn:aws:ecr:us-east-1:ACCOUNT_ID:repository/node-deploy"
            ]
        }
    ]
}
```

### CircleCI

Configure project with context and environment variables correctly:

| Name                  | Value                  |
|:----------------------|:---------------------- |
| Context               |  myContext             |
| AWS_ACCESS_KEY_ID     |  AWS_ACCESS_KEY_ID     |
| AWS_SECRET_ACCESS_KEY |  AWS_SECRET_ACCESS_KEY |
| AWS_ECR_REGISTRY_ID   |  AWS_ACCOUNT_ID        |
| AWS_REGION            |  us-east-1             |

### Docker

To initial tests, you must build and push image in node-base.

```sh
$ aws ecr get-login-password --region us-east-1 --profile sa-ci-ecr | docker login --username AWS --password-stdin AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com
$ docker build -t node-base:latest -f dockerfile.base .
$ docker tag node-base:latest AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/node-base:latest
$ docker push AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/node-base:latest
```

* The profile usage is result create IAM SA account.

## Usage

Change `dockerfile.deploy` and push to running workflow in CircleCI
