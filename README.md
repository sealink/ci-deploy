# Deployment Scripts for CI

This project centralises the deployment scripts used by all the projects that
use ECR and Elastic Beanstalk.

> We use https://semaphoreci.com/ although it can work with other CI as long as
> the required environment variables are set and `aws` is authenticated.

1. Check if the commit is tagged for release.
1. If the Docker image doesn't exist in ECR, build it and push it.
1. If the application version doesn't exist in Elastic Beanstalk, create it.
1. Finally trigger the Elastic Beanstalk deployment.
1. If the tag name starts with `base-`, the image will be built and pushed but
   not deployed.
