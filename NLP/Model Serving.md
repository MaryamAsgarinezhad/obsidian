
- The basic meaning of model serving is to **host machine-learning models** (on the cloud or on premises) and to make their functions available via API so that applications can incorporate AI into their systems. Model serving is crucial, as a business cannot offer AI products to a large user base without making its product accessible.
- Any machine-learning application ends with a deployed model.  Some require simple deployments, yet others involve more complex pipelines. Companies like Amazon, Microsoft, Google, and IBM provide tools to make it easier to deploy machine-learning models as web services. Moreover, advanced tools can automate tedious workflows to build your machine-learning model services.


Model serving, at a minimum, makes machine-learning models available via API. A production-grade API has the following extra functions:

- **Access points (endpoints)**: An endpoint is a URL that allows applications to communicate with the target service via HTTPS protocol.
- **Traffic management**: Requests at an endpoint go through various routes, depending on the destination service. Traffic management may also deploy a load-balancing feature to process requests concurrently.
- **Pre- and post-processing requests**: A service may need to transform request messages into the format suitable for the target model and convert response messages into the format required by client applications. Often, serverless functions can handle such transformations.
- **Monitor model drifts**: We must monitor how each machine-learning model performs and detect when the performance deteriorates and requires retraining.


There are various ML serving tools for deploying machine-learning models in secure environments at scale, including:

- [TensorFlow Serving](https://www.iguazio.com/blog/introduction-to-tf-serving/) covers a wide range of production requirements, from creating an endpoint to performing real-time model serving at scale.
- [Amazon’s ML API](https://aws.amazon.com/machine-learning/) provides purpose-built AI services for handling common business problems, such as intelligent chatbots for contact centers and personalized customer recommendations.
- [Amazon SageMaker](https://aws.amazon.com/sagemaker/) makes it easy to set up endpoints using a Jupyter notebook-style environment called Amazon SageMaker Studio.
- [Azure Machine Learning](https://azure.microsoft.com/en-us/services/machine-learning/) supports enterprises building and deploying machine-learning products at scale using the Azure platform.
- [Google’s Prediction API](https://cloud.google.com/ai-platform/training/docs/reference/rest) eases machine-learning model implementation, as it automatically learns from a user’s training data and generates an AI model.
- [IBM Watson](https://www.ibm.com/watson/products-services) offers pre-built applications for natural lan


## Model Serving with Iguazio

Iguazio’s [MLOps platform](https://www.iguazio.com/mlops/) can ease the management of model serving pipelines. The platform ticks all the boxes when it comes to the major features required for model serving, complete with open-source tools like Iguazio’s [MLRun](https://www.iguazio.com/open-source/mlrun/) and [Nuclio](https://www.iguazio.com/open-source/nuclio/).

MLRun is an MLOps orchestration tool for deploying machine-learning models in multiple containers. It can automate model training and validation and supports a real-time deployment pipeline. Its feature store can handle pre- and post-processing of requests. You can cut time to production by automating MLOps.

MLRun leverages Nuclio ,which is a serverless function platform, for deploying machine-learning models, making it easy to handle on-demand resource utilization and auto-scaling. It also integrates well with streaming engine (e.g. kafka, kinesis) so the function that runs the model runs it on live events. Moreover, it supports multiple worker processes ingesting data concurrently in real time.  
MLrun provides a simple way for converting a machine-learning model to a deployable function by using an easy to use python SDK that can be run from your Jupyter notebook.