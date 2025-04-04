<div align='center'>
  <h1>Design an End-To-End LLM Big Data Pipeline for Batch Training and Real-Time Inference</h1>
</div>

# Table of Contents

- [Table of Contents](#table-of-contents)
- [Functional Requirements](#functional-requirements)
- [Non-Functional Requirements](#non-functional-requirements)
- [Infrastructure](#infrastructure)
- [Services](#services)
- [AWS Workflow](#aws-workflow)

# Functional Requirements

- The system should be able to train the model on a large dataset with parallel (multi-thread) processing.
- The system should be able to load the model fast, as well as having high throughput and low-latency inference requests.

# Non-Functional Requirements

- The system's infrastructure should be able to scale with increasing dataset size and neural network parameters (weights).
- The system should be fault-tolerant.

# Infrastructure

1. `Scalability`:

- AWS S3 scales automatically.
- AWS SageMaker can be set to [autoscale](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html) to automatically adjust the number of EC2 instances according to workload.

2. `Availability and Fault Tolerance`:

- For availability, package the model with TensorFlow Lite or TensorFlow Serving and deploy it with SageMaker. Use API Gateway to expose the model as a real-time endpoint and keep the model always ready for inference.
- For fault tolerance, save training checkpoints periodically to S3. If a training job is interrupted, it can resume from the latest checkpoint instead of restarting.

1. `Fast Model Loading and Low-Latency Inference Requests:`

- Amazon SageMaker Inference uses the [Fast Model Loader](https://aws.amazon.com/blogs/machine-learning/introducing-fast-model-loader-in-sagemaker-inference-accelerate-autoscaling-for-your-large-language-models-llms-part-1/) to enable faster model loading while reducing inference time.

1. `Backend API:`

- The API for client-server communication can be either [RESTful API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/restfull_api.md), [GraphQL API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/backend/grahql.md), or [gRPC](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/backend/gRPC.md).

# Services

1. `Web App Frontend`:

- Can be implemented with React.js.
- Frontend Hosting: deploy and host the frontend (static part) with built-in SSL/TLS certificate support using [AWS Amplify](https://github.com/camponogaraviera/aws/blob/dev/services/aws_frontend.md#aws-amplify).

2. `Web App Backend API`:

- Build a RESTful API using [AWS API Gateway](https://github.com/camponogaraviera/aws/blob/dev/services/aws_networking.md#amazon-api-gateway) and [Lambda](https://github.com/camponogaraviera/aws/blob/dev/services/aws_compute_deployment.md#aws-lambda) for lightweight inference requests. Use Lambda to define the backend logic that will respond to events such as API requests passing through the API Gateway.
- Build a RESTful API using AWS [Elastic Container Service](https://aws.amazon.com/ecs/) for high-volume inference requests.
- Backend Hosting: host on [EC2](https://github.com/camponogaraviera/aws/blob/dev/services/aws_compute_deployment.md#amazon-elastic-compute-cloud-ec2), [Beanstalk](https://github.com/camponogaraviera/aws/blob/dev/services/aws_compute_deployment.md#aws-elastic-beanstalk), or [Fargate](https://github.com/camponogaraviera/aws/blob/dev/services/aws_compute_deployment.md#aws-fargate) to handle complex workflows and a more robust inference server.

1. `Batch Training Pipeline`:

- Data storage: [AWS S3](https://github.com/camponogaraviera/aws/blob/dev/services/aws_storage.md#amazon-s3) to store the raw training dataset and model metadata.
- Data preprocessing: [SageMaker Processing](https://sagemaker.readthedocs.io/en/stable/amazon_sagemaker_processing.html).
- Model training: [SageMaker Training Job](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_CreateTrainingJob.html) using distributed training on GPU clusters (e.g., EC2 p4d or EC2 g5 instances).
- Model checkpoint: AWS S3.

1. `Model Deployment`:

- Model Registry: [SageMaker Model Registry](https://docs.aws.amazon.com/sagemaker/latest/dg/model-registry.html).
- AI Model Hosting: deploy the trained model to an endpoint using [AWS Sagemaker hosting services](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints-options.html).

1. `Real-Time Inference`:

- Model inference: [SageMaker Batch Transform Jobs](https://docs.aws.amazon.com/sagemaker/latest/dg/batch-transform.html) or AWS Inferentia-based [EC2 Inf1 Instances](https://aws.amazon.com/ec2/instance-types/inf1/).

Ps: The backend infrastructure (e.g., SageMaker endpoints, API Gateway, and Lambda functions) is hosted in the AWS Region of your choice. One need to choose EC2 instance types when creating SageMaker endpoints.

# AWS Workflow

1. User visits https://yourcustomdomain.com
2. Types a query in the text box and clicks "Submit."
3. The frontend sends the query to API Gateway.
4. API Gateway triggers a Lambda function (or forwards directly) to query the SageMaker endpoint.
5. SageMaker processes the input and returns a response.
6. The response is displayed on the webpage.
