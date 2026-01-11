- In step 2 we are giving administrator access because we can use multiple services(this access is not given in companies).
- Above search for IAM user , in IAM under top features select users , there we will create a user (previously created users will be there).
- Click create user , in user details enter User name(eg. mlflowuser) , also enable(tick) on Provide user access to the AWS Management Console.(Here e don't want to provide so leave as it is). Go next.

- Next we have Set permissions , in permission options select - Attach policies directly and under Permission Policies select AdministratorAccess(tick) -> in AdministratorAccess we will get access to each and every services that we require. -> next.
- On next page click Create User. -> hence now mlflowuser has got created.
- When u go inside that particular user by clicking on it there is something called as Security Credentials , click on it and u will get to see something called as access keys in it -> click create access key -> then in usecase select command line interface(CLI)(we will be working with CLI for most of the usecases) , under confirmation press tick -> then next -> We will be on page where it will ask to provide Set description tag -> don't provide it (not required) -> click create access key.
- After this Retrieve access key page will be there 2 things will be there Access key and secret access key -> download the csv file from below on the same page(we can refer it on the later stages).

- Install AWS CLI (from chrome) - Search AWS cli download (Installing or updating to the latest version of the AWS CLI) for windows there is a installer -> just click on it and ur installer will get downloaded .
- To check if everything is working -> go to terminal and type : aws -> U will know everything is working fine.-> This tells that aws cli has been installed correctly.
- Then write aws configure -> this we use to configure the credentials in aws , when u press it its asking u AWS Access ID : "copy and paste access key" -> this is the first thing u have to configure. Press enter -> then it will ask AWS Secret Access Key : "copy and paste key" . Press enter . Then it will ask Default Region name [us-east-1]: (By default its us-east-1) , then press enter , Default Output Format is also [json] press enter . ----> This way my aws cli has got configured.
- By Now we have completed our 3rd step.

- AWS S3 Bucket Search it :
- Amazon Simple Storage Service (Amazon S3) is an object storage service offering industry-leading scalability, data availability, security, and performance. Millions of customers of all sizes and industries store, manage, analyze, and protect any amount of data for virtually any use case, such as data lakes, cloud-native applications, and mobile apps. With cost-effective storage classes and easy-to-use management features, you can optimize costs, organize and analyze data, and configure fine-tuned access controls to meet specific business and compliance requirements.

## Step 4:
- AWS Console me search for S3 -> right click -> open the link in new tab. -> please make ensure to check that location is us-east-1 .
- There in new tab u will be able to see that multiple buckets have been created.
- Click create bucket -> bucket name : mlflowtracking1 -> uncheck the Block all public access setting -> check on "I acknowledge that current setting might result in bucket and objects within becoming public"
- click create bucket -> bucket with name mlflowtracking1 will be created , when u go inside this bucket nothing is available there -> her ein this bucket we will load all our tracking experiments and entire mlruns folder will be getting uploaded over here
- Step 4 is done here .

## Step 5:
- Search for EC2 instance in on the profile page of urs , open it .(Right click 'open link in new tab')
- Under resources go to Instances -> then on top right there will be a button 'Launch Instances' click on it.
- On new page 'Launch an instance' enter instance name (eg. mlflow tracking) , on the same page uner 'Quick Start' select Ubuntu and AMI(Amazon Machine Image) we are using free tier one , ubuntu me instance type is t2.micro . We can select key pair also but currently leave it , under network settings enable everything. Configure storage will be 8 GB.
- Under key pair , select key pair type as RSA , Private key file format as : .pem , then click 'Create key pair' , if u have existing key pairs u can select any. key pair name also u have to enter (eg. mlflowtracking), then click 'Create key pair' -> when u do so a file mlflowtracking.pem will also get downloaded. .pem file is required when u want to connect ur EC2 instance in a different way.
- Then finally launch instance.
- Then under instances go to that particular instance(we have to add security group port:5000)
- To go to that particular instance go it instance id (click it) , under that go to 'Security' , there we will have inbound rules and outbound rules , so here we have to create another inbound rule where i add my security grp to '5000' port.
- In security click the id under security groups -> there click 'Edit Inbound Rules' -> Add rule -> there give port as '5000' , select 0.0.0.0/0 the search icon column , type column it should be Custom TCP , then save rules .
- Then we go back and select our created instance id (going into that instance) , on top right there is a button 'Connect' click it go under the header 'EC2 Instance Connect' and go below and click 'Connect'.
- Once u connect a bash prompt like a command prompt will open.
- Whatever commands we have there will make ensure that it has all the updated things .

## Run the following commands in the EC2 Machine(the command prompt which we have got after we connected)
- sudo apt update
- sudo apt install python3-pip
- sudo apt install pipenv
- sudo apt install virtualenv
- mkdir mlflow (making a directory for mlflow) - we clear screen here by using command clear
- cd mlflow (then cd mlflow)
- pipenv install mlflow
- pipenv install awscli
- pipenv install boto3
- pipenv shell

## Then set aws credentials
aws configure (Here it will ask for access key and secret access key which we have done before) also it will ask default region : us-east-1 , default output format none

## Finally
mlflow server -h 0.0.0.0 --default-artifact-root s3://mlflowtracking1 -> mlflowtracking1 is the name of the bucket we have here(which was created in s3 bucket). -> this will be ur default-artifact-root , and u will be running ur server here. Here it will be listening to the port : http://0.0.0.0:5000

- After this go back to ur instance there u will have a Public IPv4 DNS (a id will be there)
- Copy that id and put in on google like this -> copied_id:5000 -> here u are putting 5000 as ur port -> Then u will be able to see that my Mlflow should be running over here.
- Also update the above url in ur code , copied_id:5000 -> go to app.py -> place it in remote_server_uri
- Now this url is ur instance url. -> Earlier we used to give DagsHub/Localhost.
- Run app.py file , tracking will happen. (For running python app.py)
- This Experiment is basically getting tracked in my mlflow server which is basically in my EC2 instance.
- http://copied_id:5000/ -> format for entering the uri in app.py in remote_serving_uri variable.
- We can use either way if we setted mlflow tracking uri using os and env in the app.py file then also its okay or u can also run this in ur command prompt : export MLFLOW_TRACKING_URI=http://ec2-44-201-179-129.compute-1.amazonaws.com:5000/ (this is done for an example id).
- Reload AWS S3 u will be able to see some folder.
- Once ur work is done go to the instances : there go to instance state and terminate/delete the instance. This is best pracice. S3 bucket is not to be deleted. IAM user me jaake we also have to delete mlflowuser.
- If u don't delete then charges will be there.