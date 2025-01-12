CI/CD tools can help a team automate their development, deployment, and testing. Some tools specifically handle the integration (CI) side, some manage development and deployment (CD), while others specialize in continuous testing or related functions.

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
  
  Put the right version that contains the changes **from all branches** (corresponding to each application) **to all values.yaml files** (corresponding to each application) and **push to the branch created from the prod**.
  
  ![[Pasted image 20240427175212.png]]

We define the environment variables of the kubernetes in the values.yaml file.
- ![[Pasted image 20240429140156.png]]
-------------------------------

In the "script" section of the gitlab-ci file, you can write python commands like 'cd' for changing the work directory. Workdir is always addressed form the general gitlab-ci file for the whole project. (where the pipeline is running from)

-------------------
View the log and container terminal of a pod in argocd:

![[Pasted image 20240504165125.png]]

------------------
![[Pasted image 20240504195855.png]]this section is used for debbuging or testing the pipeline

----------------------
![[Pasted image 20240504200033.png]]This specifies the tag we give to the application's new version. 

![[Pasted image 20240504200119.png]]this is the automatic tag and will be added automatically to the 'version' part of the values.yaml file, if this tag is specified in the deployment stage of the pipeline code.

----------------
This stage is equivalent to pushing the application's new version to the git-ops project. It is automated.

![[Pasted image 20240504200422.png]]

----

نکته بسیار مهم:

اون ورژنی که "دستی" یا بطور اتوماتیک با CD pipeline میزدیم برای tag ایمیج یک اپلیکیشن (که داخل values.yaml بود) برای زمانی هست که app رو خودمون نوشتیم (مث cdn) و یک app اماده مثل vector نیست که صرفا بخایم value هاشو تغییر بدیم و هر بار با عوض شدن کد اپلیکیشن باید image اش هم عوض بشه.

اگر یک app شخصی نباشه اون ورژن براش ثابته در اغلب موارد - چون ما کدشو تغییر نمیدیم.

-  اگر برنامه شخصی نباشه باید ریپازیتوری چارت هاشو به helm خودمون اضافه کنیم و بعد توی dependency چارت از ادرس helm اش استفاده کنیم.
- اگر شخصی باشه chart اش نمیدونم چجوری ساخته میشه تو helm که ما اینجوری استفاده کنیم.

![[Pasted image 20250112182936.png]]

----

به کمک helm و بدون نیاز به argo هم میشه با دستور helm create اپلیکیشن رو از روی chart و value ساخت و بالا اورد. argo یه ابزار کمکی هست که automate شده تر هست و UI خوبی میده.

مثلا ارگو کمک میکنه اگه ورژن ها و مقادیر values عوض شد اپلیکیشن با مقادیر update شده ساخته بشه.

-------

