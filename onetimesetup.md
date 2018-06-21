## Step 0: [Pre-requisites](https://github.com/mypublicorg/pytorch-cifar10-in-ibm-cloud/blob/master/pre-req.md)

Please make sure you have completed the prequisites before starting this tutorial.
You can get the prequisites [here](https://github.com/mypublicorg/pytorch-cifar10-in-ibm-cloud/blob/master/pre-req.md)


## Step 1: Login to IBM Cloud and target the correct area 

### Step 1.1 Login Using API Key:   
Login to your IBM Cloud account using the apiKey (see [pre-requisite document](https://github.com/mypublicorg/pytorch-cifar10-in-ibm-cloud/blob/master/pre-req.md) for details)

```
$ bx login --apikey <your_api_key>
```

## Step 1.2 Target correct Region, Resource group, Org and Space.
Use the "bx target" command to target the correct Cloud parameters.
```
$ bx target -r us-south -g MITIBMWatsonAiLab -o MITIBMWatsonAiLab -s dev
```

## Step 2: Create a Watson ML Service Instance
Below `pm-20` is the name of the Watson ML Service. 
You create a new instance and give it a name, e.g., `myuseridMLinstance1`. Below, replace  <CLI_WML_Instance> by your chosen name.
```
$ bx service create pm-20 standard <CLI_WML_Instance>
```

#### 2.1. Create an Access key for accessing your Watson ML service instance
Below, replace <key_CLI_WML_Instance>  by a key name of your choice.

```
$ bx service key-create <CLI_WML_Instance> <key_CLI_WML_Instance>
```
#### 2.2 Retrieve credentials:
```
$ instance_id=`bx service key-show CLI_WML_Instance key_CLI_WML_Instance | grep "instance_id"| awk -F": " '{print $2}'| cut -d'"' -f2`
$ username=`bx service key-show CLI_WML_Instance key_CLI_WML_Instance | grep "username"| awk -F": " '{print $2}'| cut -d'"' -f2`
$ password=`bx service key-show CLI_WML_Instance key_CLI_WML_Instance | grep "password"| awk -F": " '{print $2}'| cut -d'"' -f2`

$ echo ""; echo "ML Instance Credentials:"; echo "instance_id: $instance_id"; echo "username: $username "; echo "password: $password"; echo ""
```

#### 2.2 Set up Environment Variables:
```
$ export ML_INSTANCE=$instance_id
$ export ML_USERNAME=$username
$ export ML_PASSWORD=$password
```

## Step 3: Create a bucket in the Cloud Object Storage (COS) to store data

A [bucket](https://datascience.ibm.com/docs/content/analyze-data/ml_dlaas_object_store.html) is a huge "folder" 
in the COS. 
You use the bucket to put and get any file or folder (e.g., your datasets).

#### 3.1. Create a cloud storage instance:

Lets create a personal cloud storage instance to hold your bucket(s) and name the instance <my_COS_instance>.
The `service-instance-create` command below creates the COS instance, and the `service-instance` command retrieves its attributes.

```
$ bx resource service-instance-create <my_COS_instance> cloud-object-storage standard global
$ bx resource service-instance <my_COS_instance>

```

#### 3.2. Get security credentials:

Now create and get the credentials to access `my_COS_instance`.
Give a name to your credentials (replace `<my_COS_key>` below).

Create key, store it and print it:

```
$ bx resource service-key-create <my_COS_key> Writer --instance-name <my_COS_instance> --parameters '{"HMAC":true}' > /dev/null 2>&1
$ access_key_id=`bx resource service-key <my_COS_key> | grep "access_key_id"| cut -d\:  -f2`
$ secret_access_key=`bx resource service-key <my_COS_key> | grep "secret_access_key"| cut -d\:  -f2`
$ echo ""; echo "COS Credentials:"; echo "access_key_id: $access_key_id"; echo "secret_access_key: - $secret_access_key"; echo ""
```
Save your keys to shell variables. (write down the keys as you'll need them again later to access your resources.)
```
export MY_BUCKET_KEY=$access_key_id
export MY_BUCKET_SECRET_KEY=$secret_access_key
```

#### 3.3 Create and configure your aws profile.
Use the `aws` tool to add `access_key_id` and `secret_access_key` to a aws-profile,
and give a name to your aws-profile (Replace <my_aws_profile> below). 

```
$ aws configure --profile <my_aws_profile>
```
The above command will ask for your `access_key_id` and `secret_access_key`.
Press enter for all other fields requested. [none]

#### 3.4. Create an alias to simplify the invocation of the aws command:

First, lets create an alias for repeating parts of the command (to avoid typing too much).

Mac OS users:
```
$ alias bxaws='aws --profile <my_aws_profile> --endpoint-url=http://s3-api.us-geo.objectstorage.softlayer.net'
```

Windows OS users:
```
doskey bxaws=aws --profile <my_aws_profile> --endpoint-url=http://s3-api.us-geo.objectstorage.softlayer.net $*
```

#### 3.5. Create a bucket:

Now, lets make a bucket and name it something unique! Buckets are named globally, which means that only one IBM Cloud account can have a bucket with a particular name. 
**NB: the bucket names may not contain upper-case, underscores, dashes, periods, etc. Just use simple text, and add your userid as part of the bucket name.  
```
$ bxaws s3api create-bucket --bucket <your-bucket-name>
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
For example, lets get the cifar10 dataset and do a little trainning.
You can get this dataset from the Internet, e.g., by doing:
```
mkdir cifar10
cd cifar10
wget https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
tar xvf cifar-10-python.tar.gz
rm cifar-10-python.tar.gz
```
or

Download all the data from this [link](https://ibm.box.com/s/5ss0adenqf4dow9bqynb687cuu1oh79q)


### Step 1: Upload the dataset to your bucket:
We assign the name of the bucket to a shell variable
```
$ bucket_name=<your_bucket_name>
$ bxaws s3 cp cifar10/  s3://$bucket_name/cifar10 --recursive
```
(optional) Verify that the data was successfully uploaded using this comand.

```
$ bxaws  s3 ls s3://$bucket_name/cifar10
```


### Step 2: Edit your manifest file, e.g., `pytorch-cifar.yml`

This yaml file should hold all the information needed for executing the job, including what bucket, ml framework, and computing instance to use.


#### 2.1. Copy the template manifest:

Make a copy from 
Download the [template](https://github.com/mypublicorg/pytorch-cifar10-in-ibm-cloud/blob/master/pytorch-cifar-template.yml)
and then make a copy of it to be edited.

```
$ cp pytorch-cifar-template.yml my-pytorch-cifar.yml
```
#### 2.2. Edit the configuration file:

Edit `my-pytorch-cifar.yml`:
Add your author info and replace the values of `aws_access_key_id`, `aws_secret_access_key`, and `bucket` 
with your storage instance credentials and bucket name.
This should be done for both the data input reference (`training_data_reference`) 
and the output reference (`training_results_reference`). 
Notice that you may use the same bucket for both input and output, but this is not required.

```yaml
model_definition:
  framework:
#framework name and version (supported list of frameworks available at 'bx ml list frameworks')
    name: pytorch
    version: 0.4
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
      --checkpoint_path ${RESULT_DIR} --epochs 5
```

This command will execute `main.py`, which starts a training run.  
Since no model is specified, it will use the default model, `vgg16`, 
for 5 epochs using the dataset that we uploaded to the bucket. 

### Step 3: Send code to run on Watson Studio!

#### 3.1. Zip all the code and models into a .zip file:
```
$ zip model.zip main.py models/*
```

#### 3.2. Send your code and manifest to IBM Watson Studio:
```
$ bx ml train model.zip pytorch-cifar.yml
```

<img src="img/startrun.jpg" width=90%>

That's it! The command should generate a training ID for you, meaning our model has started training on Watson!

### Step 4: Monitor the training

#### We can check the status of all training using the command:
```
$ bx ml list training-runs
```
<img src="img/listruns.jpg" width=100%>

#### Continuously monitor a training run by using the `bx ml monitor` command:
```
$ bx ml monitor training-runs < trainingID >
```
<img src="img/monitorruns.jpg" width=100%>

As training proceeds, you should see results from the training process being copied to the results bucket specified 
in `training_results_references.target bucket`.

<img src="img/cloudstorage.jpg" width=100%>

You can also inspect the status of training by downloading and viewing the training log file which has been copied to the results bucket. (This can be useful for debugging).

#### To do this, run:
```
$ bxaws s3 cp s3://my_bucket/ < trainingID>/learner-1/training-log.txt -
```

### Additional Information on Deep Learning in IBM Cloud

- [Deep Learning in IBM Studio](https://www.ibm.com/cloud/deep-learning)
- [Deep Learning Documentation](https://dataplatform.ibm.com/docs/content/analyze-data/ml_dlaas_working_with_new_models.html)



## Enjoy
Content derived from material provided by Kaouta el Maghraoui (IBM Research), German Goldszmidt (IBM WCP),
Hendryk Strobelt (IBM Research).

### Other useful commands

#### Download Trained model files
```
$ bxaws s3 cp s3://$bucket_name/<training run ID> ./trainedmodel --recursive
ls ./trainedmodel 
```

#### List the buckets
```
$ bxaws  s3api list-buckets 
```

#### List the contents of your bucket
```
$ bxaws s3 ls s3://$bucket_name/
```

#### Query status of the job
```
$ bx ml show training-runs <training run ID>
```

#### View log files 
```
$ bxaws s3 ls s3://$bucket_name/<training_id>/learner-1/
```

#### Delete files from your bucket
```
$ bxaws s3 rm s3://$bucket_name/fileOrDirectoryName  --recursive
``` 
