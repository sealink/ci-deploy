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
    1. If the tag name starts with `base-`, the image will be built and pushed
       but not deployed.
1. For ECS:
    1. Create the task revision.
    1. Update the service with the revision.

## Environment variables

### Required variables:

```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION

APP_ECR_IMAGE
APP_ECR_REGION

# These 2 are required if you have "base-*" tags, see above.
BASE_ECR_IMAGE
BASE_ECR_REGION

CI_DEPLOY_TYPE # Either eb or ecs
```

If you're using Elastic Beanstalk:

```
EB_APP_NAME
EB_ENV_NAME
```

If you're using ECS:

```
ECS_CLUSTER
ECS_FAMILY
ECS_SERVICE
```

### Optional variables

You may override these if you know what you're doing.

```
SEMAPHORE_PROJECT_DIR
TAG
JQ_PATH
```
