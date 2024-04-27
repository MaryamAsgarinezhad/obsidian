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

There are two approaches, same as the build stage (the "only"  and "image_tag" should change, **depending on the corresponding build stage**)
