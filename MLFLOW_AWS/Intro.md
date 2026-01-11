## Experiments will be tracked by MLFLOW which will be hosted in AWS cloud
## We are using AWS EC2 Instance
- How entire Data Science project can be deployed in AWS Cloud by using various MLOPS tool.
- Experiements will be tracked by MLFLOW and they will be hosted in the AWS EC2 instance.
- Deploy in AWS and tracking in DagsHub(For data science project)
- boto3 -> a library very important if u want to work with AWS S3 bucket bcoz all these experiment tracking which u will do will get saved in the S3 bucket. This S3 bucket will be available in the AWS , with the S3 bucket we will track the entire MLFLOW experiments.
- MLFLOW will pick the information from the S3 bucket.
- AWS S3 if u want to connect u have to use boto3.

- Main idea over here is to make sure that we try to track all the experiments in the AWS cloud.
- We are not hosting the entire data science application , only the MLFLOW tracking will happen in the AWS EC2 instance.
- Further applications we will also deploy in the AWS EC2 instance.
- Entire tracking will be happening in the AWS EC2 instance.
