There are two main stages we want to automate:

- **Build**: to build an image of our app and put in the artifactory.
  
Approach 1- Release a new version of your application, set the build pipeline to "tags" mode:
  ```python
  build_prod:  
  image: hamravesh.hamdocker.ir/public/darkube-cli:v1.1  
  only:  
    - tags  
  script:  
    - export IMAGE=docker.mci.dev/darkube/mci/cdn-regression-test  
    - darkube build -f Dockerfile --push -t $IMAGE:$CI_COMMIT_TAG -t $IMAGE:$CI_COMMIT_REF_SLUG  
      --workdir regression_test --build-arg CICD_REPO_USER=$CICD_REPO_USER --build-arg CICD_REPO_PASS=$CICD_REPO_PASS  
  stage: build
```

Approach 2- Push the changes to a specific branch or directory, set the build pipeline to "refs" and generate automatic tags:

```python
  build_dev:  
  image: hamravesh.hamdocker.ir/public/darkube-cli:v1.1  
  only:  
    changes:  
      - regression_test/**  
    refs:  
      - master  
  script:  
    - export IMAGE=docker.mci.dev/darkube/mci/cdn-regression-test  
    - darkube build -f Dockerfile --push -t $IMAGE:$CI_COMMIT_SHORT_SHA -t $IMAGE:$CI_COMMIT_REF_SLUG  
      --workdir regression_test --build-arg CICD_REPO_USER=$CICD_REPO_USER --build-arg CICD_REPO_PASS=$CICD_REPO_PASS  
  stage: build
```


- **Deploy**: to deploy the images on the kuber cluster.
- The **equivalant action** instead of writing the deploy pipeline is to **push "values.yaml" with the specific version** to the desired branch on gitlab.

There are two approaches, same as the build stage (the "only"  and "image_tag" should change, **depending on the corresponding build stage**)
```python
deploy_cdn_regression_dev:  
  only:  
    changes:  
      - regression_test/**  
    refs:  
      - master  
  stage: deploy  
  variables:  
    IMAGE_TAG: $CI_COMMIT_SHORT_SHA  
    CHART_YAML_PATH: values.yaml  
    ENV: dev  
    APP_NAME: "regression-cdn"  
  trigger:  
    branch: master  
    project: mse/sre/sre-ops  
    strategy: depend
```
Here "APP_NAME" specifies the name of the folder in "project" project that contains your Helm files.

- In this deployment pipeline configuration:

   `trigger:     branch: master`

The `trigger` section specifies conditions under which the pipeline job should be triggered. Specifically, `branch: master` indicates that the job should be triggered when changes are pushed to the `master` branch of the repository. This means that the deployment job will only run when new commits are made to the `master` branch.

In other words, this configuration ensures that the **deployment to the specified environment (`dev` in this case**) will only occur when changes are merged into the `master` branch, helping to maintain a controlled deployment process tied to changes in the main development branch.


 - `only` controls when a job should run based on changes within the repository, while `trigger` defines conditions for automatic job triggering based on external events, such as branch updates. They complement each other to provide fine-grained control over pipeline execution.
-----------------------------

You can specify which branch do you want to create the tag from:

![[Pasted image 20240427163403.png]]

Having multiple images in a version (multiple applications):
![[Pasted image 20240427164318.png]]

- If there is no deployment pipeline to deploy the code on "production" (or any other) namespace, you have to push the **version** you have built directly  to that branch. If you don't have access to do so, create a merge request on that branch.