# MITIBMCloud

## How can I run code on IBM Cloud [Watson Studio](https://www.ibm.com/cloud/watson-studio)?

Here are the setup steps to to use the Machine Learing (ML) service.
**Note: These steps use a Command Line Interface (CLI). There is an alternative browser used interface** 

### Steps for one time Setup:

Step 0. Confirm that you have an IBM Cloud userid
Step 1. Download [CLI tools](https://console.bluemix.net/docs/cli/index.html#overview) to access and manage resources in the IBM Cloud
      
      1.1 Download and Install the IBM Cloud CLI
      1.2 Install `awscli`
      1.3 Install `bx` machine-learning plugin
      
Step 2. Login thru the CLI to your IBM Cloud account
      2.1 Login using password
      2.2 Login Using API Key
            a.Get an Api-key
            b.login
            
Step 3. Configure your IBM Cloud account. 
     3.1 Use an existing org
     3.2 Use an existing space
     3.3 Target correct org and space
          Note for MIT IBM Watson AI lab users
     
Step 4. Create a Watson ML Instance
    4.1 Setup a Watson ML Instance : Create WML Access key
    4.2 Set up Environment variables
    
Step 5. Define a [Cloud Object Storage](https://www.ibm.com/cloud/object-storage/faq) Instance to store your data.
    5.1. Create a cloud storage instance
    5.2. Get security credentials
    5.3 Create and configure your aws profile
    5.4. Create an alias
    5.5. Create a bucket
 
 ### Steps for the Demo:
 Step 0: Get a dataset
 
 Step 1: Upload your dataset to the bucket
 
 Step 2: Edit your manifest file
    2.1. Copy the template manifest
    2.2. Edit the configuration file
    
 Step 3: Send code to run on Watson Studio!
    3.1. Zip all the code and models into a .zip file
    3.2. Send your code and manifest to IBM Watson Studio
    
 Step 4: Monitor the training
 
 ### Additional Information on Deep Learning in IBM Cloud
 ### Other useful commands
 
 
## Step 0: Confirm that you have a valid account on IBM Cloud. 

Goto [https://console.bluemix.net/](https://console.bluemix.net/) and login
(If you are part of the IBM-MIT AI lab, but do NOT have a valid account, please contact noor.fairoza@ibm.com)
#### MIT-IBM Watson AI Lab users:
##### Verify that you have access to `1589313—IBM` account:
Click on the profile avatar in the right-top-corner.
Under the account you can switch to AI Lab account by choosing 1589313—IBM account.
<img src="img/i2.png" >
Verify your CLOUD FOUNDRY ORG in the Dashboard, it should be `MITIBMWatsonAiLab`
<img src="img/i1.png">

Please contact `noor.fairoza@ibm.com` if you are d NOT have access to `1589313—IBM` account

## Step 1: Install CLI tools in your local machine (laptop) to remotely access your Cloud resources

#### 1.1. Download and Install the IBM Cloud CLI: `bx`
'bx' allows you to start and manage resources (e.g., applications, containers, services, ...) in the IBM cloud. 

Download [bx CLI](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install)
and install it, following the instructions for your local machine operating system (OSX, Linux or Windows)
`curl -fsSL https://clis.ng.bluemix.net/install/osx | sh`
or
`curl -fsSL https://clis.ng.bluemix.net/install/linux | sh`


#### 1.2. Install `awscli` using [pip](https://pypi.org/project/pip/)

The [aws CLI](https://docs.aws.amazon.com/cli/latest/userguide/) lets you setup and upload data to your buckets. (Will get to this later)

Note: Please install latest version of awscli
```
pip install awscli
```
or
```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
./awscli-bundle/install -b ~/bin/aws
```
#### 1.3. Install `bx` machine-learning plugin

The `machine-learning` plugin for `bx` lets you start, view, and stop your Machine Learning jobs on Watson.

```
bx plugin install machine-learning
```

## Step 2. Login to IBM Cloud account:
##### 2.1. Login using password 

Now we will create data and service resources in the IBM Cloud. First we login.
```
bx login
```
IBM employees can use
```
bx login --sso
```

or

##### 2.2 Login Using API Key:   Get an Api-key:
Goto [https://console.bluemix.net/](https://console.bluemix.net/) and login
click here: [https://console.bluemix.net/iam/#/apikeys](https://console.bluemix.net/iam/#/apikeys)
or
Click on Manage --> Security --> Platform API Keys

Create an API key and save it somewhere secure. 

Note: Please do NOT share this key with anyone. 

<img src="img/i3.png">

Login:

Now we will create data and service resources in the IBM Cloud. First we login. (Use your API key from above)
```
bx login --apikey <your_api_key>
```



## 3. Configure your account to access IBM Cloud 

In order to run jobs on Watson, you need an `organization` (also called `org`) and a `space` to hold your jobs. 
`Org` names are also globally unique. 

#### 3.1 Use an existing org
The account owner should have already created an `org` for you (and others) to share assets.
You can find out the organizations available for you with the command:<br>

```
bx account orgs
```

The command will return something like:

```
Name                Region     Account owner      Account ID                         Status   
MITIBMWatsonAiLab   us-south   ailab@us.ibm.com   5eb998dd20e3d7fc0153329e32362d64   active   
```
Select the correct `Name` and save it in a variable, i.e.,
```
org_name="MITIBMWatsonAiLab"
bx target -o $org_name
```

#### 3.2 Use an existing space
Now lets find out the name of the `space` for you under the `org`. 
```
bx account spaces
```
This will return something like:
```
Getting spaces under organization MITIBMWatsonAiLab in region us-south as myuserid@mit.edu...
OK

Name   
dev 
```
Select the correct space `Name` and save it in a variable, e.g.,
```
space_name="dev"
```
#### 3.3 Target correct org and space
```
bx target -o $org_name -s $space_name
```

#### MIT-IBM Watson AI Lab users only:
You can use the below command to target correct org, space and region for ailab projects.
`bx login -o MITIBMWatsonAiLab -a api.ng.bluemix.net -g MITIBMWatsonAiLab -s dev --apikey <yourapikey>`



## Step 4: Create a Watson ML Service Instance
Note: -  Learner Image versions that are supported by WML [https://github.ibm.com/deep-learning-platform/dlaas/wiki/Learner-Image-details](https://github.ibm.com/deep-learning-platform/dlaas/wiki/Learner-Image-details)

`bx service create pm-20 standard <your_Instance_Name>`

#### 4.1. Setup a Watson ML Instance : Create WML Access key

```
bx service key-create CLI_WML_Instance cli_key_CLI_WML_Instance
```

#### 4.2 Set up Environment variables
```
export ML_INSTANCE=`bx service key-show <your_Instance_Name> <your_Instance_Key_Name> | gawk '/instance_id/ {gsub("\"","");print $2}' 
export ML_USERNAME=`bx service key-show <your_Instance_Name>  <your_Instance_Key_Name> | gawk '/username/ {gsub("\"","");print $2}' 
export ML_PASSWORD=`bx service key-show <your_Instance_Name>  <your_Instance_Key_Name>| gawk '/password/ {gsub("\"","");print $2}' 
export ML_ENV=`bx service key-show <your_Instance_Name> <your_Instance_Key_Name>| gawk '/url/ {gsub("\"","");print $2}'
```

## Step 5: Create a bucket in the Cloud to store your data

A [bucket](https://datascience.ibm.com/docs/content/analyze-data/ml_dlaas_object_store.html) is a huge "folder" in the cloud. 
You use the bucket to put and get any file or folder (e.g., your datasets) using an api-style interface.


#### 5.1. Create a cloud storage instance:

lets create your own personal cloud storage instance to hold your bucket(s) and name the instance `my_instance`.

```
bx resource service-instance-create "my_instance" cloud-object-storage standard global
bx resource service-instance "my_instance"

```

#### 5.2. Get security credentials:

We then create and get the credentials to `my_instance` and naming it `my_cli_key` so that you can create and access your bucket.

Create key, store it and print it:

```
bx resource service-key-create "my_cli_key" Writer --instance-name "my_instance" --parameters '{"HMAC":true}' > /dev/null 2>&1
access_key_id=`bx resource service-key my_cli_key | grep "access_key_id"| cut -d\:  -f2`
secret_access_key=`bx resource service-key my_cli_key | grep "secret_access_key"| cut -d\:  -f2`
echo ""; echo "Credentials:"; echo "access_key_id - $access_key_id"; echo "secret_access_key - $secret_access_key"; echo ""
```
Save your keys.(note it down)
You'll need them again later to access your resources...
```
export MY_BUCKET_KEY = $access_key_id
export MY_BUCKET_SECRET_KEY = $secret_access_key
```

#### 5.3 Create and configure your aws profile.

Use `aws` tool to add `access_key_id` and `secret_access_key` to a profile and give any name to your profile. say, `my_profile` (leave the other fields as None).

```
aws configure --profile my_profile
```

Provide `access_key_id` and `secret_access_key` when requested. Press enter if anything else is requested. [none]

#### 5.4. Create an alias:

First, lets create an alias for repeating parts of the command.

Mac OS users:
`alias aws='aws --endpoint-url=http://s3-api.us-geo.objectstorage.softlayer.net'`

Windows OS users:
`doskey aws=aws --endpoint-url=http://s3-api.us-geo.objectstorage.softlayer.net $*`



#### 5.5. Create a bucket:

Now, lets make a bucket and name it something unique! Buckets are named globally, which means that only one IBM Cloud account can have a bucket with a particular name. **NB: the bucket names may not contain upper-case, underscores, dashes, periods, etc. Just use simple text, e.g., below we call the bucket "mybucket".  
```
bucket_name="mybucket"
aws --profile <PROFILE_NAME> s3api create-bucket --bucket <your-bucket-name>
```

## Congratulations you are done with the one-time SETUP!

## Tutorial
Now, to test that your setup is working, lets try a simple model.

Step 0: Get a dataset
Step 1: Upload your dataset to the bucket
Step 2: Edit your manifest file
Step 3: Send code to run on Watson Studio!
Step 4: Monitor the training

### Step 0. Get a dataset 
For example, lets get the cifar10 dataset and do a little trainning..
You can get this dataset from the Internet, e.g., by doing:
```
mkdir cifar10
cd cifar10
wget https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
tar xvf cifar-10-python.tar.gz
rm cifar-10-python.tar.gz
```
or

Download all the data from this link `https://ibm.box.com/s/5ss0adenqf4dow9bqynb687cuu1oh79q`


### Step 1: Upload the dataset to your bucket:

```
aws  --profile my_profile s3 cp cifar10/  s3://$bucket_name/cifar10 --recursive
```
(optional) You could verify if the data is successfully uploaded using this comand.

`aws --profile my_profile s3 ls s3://$bucket_name/cifar10`


### Step 2: Edit your manifest file, e.g., `pytorch-cifar.yml`

This yaml file should hold all the information needed for executing the job, including what bucket, ml framework, and computing instance to use.


#### 2.1. Copy the template manifest:

Get your compy from the template.

```
cp pytorch-cifar-template.yml my-pytorch-cifar.yml
```
#### 2.2. Edit the configuration file:

Edit `my-pytorch-cifar.yml`:
Add your author info and replace the values of `aws_access_key_id`, `aws_secret_access_key`, and `bucket` in `my-pytorch-cifar.yml` with your storage instance credentials and your chosen bucket name.
This should be done for both the data input reference (e.g., `training_data_reference`) and the output reference (e.g., `training_results_reference`). Notice that you may use the same bucket for both input and output, but this is not required.

```yaml
model_definition:
  framework:
#framework name and version (supported list of frameworks available at 'bx ml list frameworks')
    name: pytorch
    version: 0.3
#name of the training-run
  name: MYRUN
#Author name and email
  author:
    name: JOHN DOE
    email: JOHNDOE@MIT.EDU
  description: This is running cifar training on multiple models
  execution:
#Command to execute -- see script parameters in later section !!
    command: python3 main.py --cifar_path ${DATA_DIR}/cifar10
      --checkpoint_path ${RESULT_DIR} --epochs 10
    compute_configuration:
#Valid values for name - k80/k80x2/k80x4/p100/p100x2/v100/v100x2
      name: k80
training_data_reference:
  name: training_data_reference_name
  connection:
    endpoint_url: "https://s3-api.us-geo.objectstorage.service.networklayer.com"
    aws_access_key_id: < YOUR SAVED ACCESS KEY >
    aws_secret_access_key: < YOUR SAVED SECRET ACCESS KEY >
  source:
    bucket: < mybucketname >
  type: s3
training_results_reference:
  name: training_results_reference_name
  connection:
    endpoint_url: "https://s3-api.us-geo.objectstorage.service.networklayer.com"
    aws_access_key_id: < YOUR SAVED ACCESS KEY >
    aws_secret_access_key: < YOUR SAVED SECRET ACCESS KEY >
  target:
    bucket: < mybucketname >
  type: s3
```

Notice that under `execution` in the yaml file, we specified a command that will be executed 
when the job starts execution at the server. (make sure you give right path to data)

```
python3 main.py --cifar_path ${DATA_DIR}/cifar10
      --checkpoint_path ${RESULT_DIR} --epochs 10
```

This command will execute `main.py`, which starts a training run of a specified model. 
Since no model is specified, it will train the default model, `vgg16`, 
for 10 epochs using the dataset that we uploaded to the bucket. 

### Step 3: Send code to run on Watson Studio!

#### 3.1. Zip all the code and models into a .zip file:
```
zip model.zip main.py models/*
```

#### 3.2. Send your code and manifest to IBM Watson Studio:
```
bx ml train model.zip pytorch-cifar.yml
```

<img src="img/startrun.jpg" width=90%>

That's it! The command should generate a training ID for you, meaning our model has started training on Watson!

### Step 4: Monitor the training

#### We can check the status of training using the `bx ml list` command:
```
bx ml list training-runs
```
<img src="img/listruns.jpg" width=100%>

#### Continuously monitor a training run by using the `bx ml monitor` command:
```
bx ml monitor training-runs < trainingID >
```
<img src="img/monitorruns.jpg" width=100%>

As training proceeds, you should see results from the training process being copied to the results bucket specified in your training job `yaml` file - `training_results_references.target bucket`.

<img src="img/cloudstorage.jpg" width=100%>

You can also inspect the status of training by downloading and viewing the training log file which has been copied to the result bucket. 
This is useful in debugging errors and failed jobs.

#### To do this, we run:
```
aws --profile my_profile s3 cp s3://my_bucket/ < trainingID Rig >/learner-1/training-log.txt -
```

### Additional Information on Deep Learning in IBM Cloud

- [Deep Learning in IBM Studio](https://www.ibm.com/cloud/deep-learning)
- [Deep Learning Documentation](https://dataplatform.ibm.com/docs/content/analyze-data/ml_dlaas_working_with_new_models.html)



## Enjoy
Content derived from material provided by Kaouta el Maghraoui (IBM Research), German Goldszmidt (IBM Research).

### Other useful commands

#### Download Trained model files
` aws  --profile <PROFILE_NAME> s3 cp s3://$bucket_name/<training run ID> ./trainedmodel --recursive
ls ./trainedmodel `
 
#### List the buckets
`aws  s3api list-buckets --profile <PROFILE_NAME>`

#### List the contents of your bucket
`aws ---profile <PROFILE_NAME> s3 ls s3://$bucket_name/`

#### Query status of the job
`bx ml show training-runs <training run ID>`

#### View log files
`aws cli aws s3 ls s3://$bucket_name/<training_id>/learner-1/`

#### Delete files from your bucket
`aws --profile <PROFILE_NAME> s3 rm s3://$bucket_name/fileOrDirectoryName  --recursive` 
