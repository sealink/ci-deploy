# Deployment Scripts for CI

This project centralises the deployment scripts used by all the projects that
use ECR, ECS and Elastic Beanstalk.

> We use https://semaphoreci.com/ although it can work with other CI as long as
> the required environment variables are set and `aws` is authenticated.

1. Check if the commit is tagged for release.
1. If the Docker image doesn't exist in ECR, build it and push it.
1. For Elastic Beanstalk:
    1. If the application version doesn't exist in Elastic Beanstalk, create it.
    1. Finally trigger the Elastic Beanstalk deployment.
1. For ECS:
    1. Create the task revision.
    1. Update the service with the revision.
1. If the tag name starts with `base-`, the image will be built and pushed but
   not deployed.

## Experimental blue/green EB deployment automation

This is experimental because Elastic Beanstalk is unstable when this option is
enabled.

The additional logic is as follows.

1. Before deployment, clone the Elastic Beanstalk environment.
1. Swap the URLs before the original environment and the cloned environment.
1. Deploy to the original environment with the `AllAtOnce` option.
1. Swap back the URL.
1. Terminate the cloned environment.

## Environment variables

### Required variables:

```
AWS_ACCESS_KEY_ID=aws-access-key-id
AWS_SECRET_ACCESS_KEY=aws-secret-access-key
AWS_DEFAULT_REGION=ap-southeast-2

APP_ECR_IMAGE=dkr.ecr.ap-southeast-2.amazonaws.com/my-application
APP_ECR_REGION=ap-southeast-2

# These 2 are required if you have "base-*" tags, see above.
BASE_ECR_IMAGE=dkr.ecr.ap-southeast-2.amazonaws.com/my-application-base
BASE_ECR_REGION=ap-southeast-2

CI_DEPLOY_TYPE= # Either eb or ecs
```

If you're using Elastic Beanstalk:

```
EB_APP_NAME=MyApplication
EB_ENV_NAME=my-application
```

If you're using the automated blue/green deployment:

```
ENABLE_BLUE_GREEN_DEPLOY=1
```

If you're using ECS:

```
ECS_CLUSTER=production
ECS_FAMILY=my-application
ECS_SERVICE=my-application
```

### Optional variables

You may override these if you know what you're doing.

```
SEMAPHORE_PROJECT_DIR=/home/runner/my-application
TAG=0.1.0
JQ_PATH=/usr/bin/jq
EB_WORKER_ENV_NAME=my-application-worker
CLONE_EB_ENV_NAME_SUFFIX=-clone
```
