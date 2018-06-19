
# Pre-Requisites for the IBM Watson studio Dlaas Tutorial
 
### Please make sure you satisfy the following list of pre- requisites before you attend DlaaS Tutorial.
#### Checklist:
1. Access to internal IBM Cloud Account

2. Python dependency

3. Install the IBM Cloud developer tools

4. Access your IBM Cloud account using cli.

                       4.1. Get your API Key
                       
                       4.2  Login using API Key
                       
5. Target correct Org, Space and Endpoint for the tutorial.

6. Troubleshooting.


 
### 1. Access to the internal IBM Cloud account
please verify that you have access to Internal IBM cloud account.

Login to [https://console.bluemix.net/](https://console.bluemix.net/) 

<img src="img/login.png" >

Click on the profile avatar in the top-right-corner for the webpage.

Under the account, you should see a dropdown box, with a list of all the IBM cloud accounts you have access to.

Select the AI Lab account by choosing 1589313—IBM account. 

<img src="img/i2.png" >

 
(Notice that sometimes the account is just listed as ‘IBM’.)

Verify the account access by checking that the CLOUD FOUNDRY ORG is ‘MITIBMWatsonAiLab’

<img src="img/i1.png">

If you do not have MITIBMAiLab under Cloud Foundry Org, stop here and contact Noor Fairoza via slack or email.

### 2. Python
Make sure that Python version 3.6 and pip 10 is installed. 
Open a terminal and use the commands below in your command line interface to check.

 <img src="img/python.PNG">
 
If either python >3.6 and/or pip 10 are not installed, please follow link [https://www.python.org/downloads/](https://www.python.org/downloads/) to do so.
 
### 3. Install the IBM Cloud developer tools
The "Bluemix CLI" tools let you interact with IBM Cloud Services.
Install these tools as follows: 
 open  a terminal and use the command below:
3.1 Install bluemix CLI

#### Mac OS: 

`curl -fsSL https://clis.ng.bluemix.net/install/osx | sh`

#### Linux: 

`curl -fsSL https://clis.ng.bluemix.net/install/linux | sh`
 
#### Windows 10: 

Open Windows PowerShell by right-clicking the PowerShell icon and selecting "Run as Administrator" and run the command below.

`Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')`
 
#### 3.2 Install Machine learning plugin

`bx plugin install machine-learning -r Bluemix`

#### 3.3 Install  Watson ML Client   library and plugin  for the IBM  Cloud CLI

`pip install watson-machine-learning-client`
 
 
### 4. Access your IBM Cloud account using CLI.

####  4.1 . Get your API Key
login to [https://console.bluemix.net/iam/#/apikeys](https://console.bluemix.net/iam/#/apikeys) from your browser

Click create button. Give a name and description  and create a platform api key.

 <img src="img/apikey.png">

Click `show`. Copy and save your key at a secure location.

`Note`:- you can see your api only once. If you lose your key, you will need to recreate it.
 
#### 4.2 . Login using your IBM Cloud API Key

Open your Command line interface

Use the following command to login

`bx login –apikey <yourapikeyhere>`

<img src="img/apikeylogin.png">
 
### 5. Target correct  Region, Resource group, Org and Space.

Use the "bx target" command to target the correct Cloud parameters.

`bx target -r us-south -g MITIBMWatsonAiLab   -o MITIBMWatsonAiLab -s dev`
 
  <img src="img/v1.png">

 


### Troubleshooting:

#### Windows Machine:
If you have a windows machine, you can use git bash to run the following commands.
You can download git from https://git-scm.com/downloads. git bash come bundled with git.

 <img src="img/gitbash.png">

#### Python Compatibility issues
If you have any concerns about python version compatibility, we suggest you use Anaconda.

You can download and installation instructions of Anaconda from below links

Mac OS users, [https://conda.io/docs/user-guide/install/macos.html](https://conda.io/docs/user-guide/install/macos.html
)

Windows users, [https://conda.io/docs/user-guide/install/windows.html](https://conda.io/docs/user-guide/install/windows.html)

Choose the different version of python

First, make sure you have the latest version of conda by running

conda update conda

Then run

`conda install python=3.5`

or any version you need

You can visit this link for reference [https://conda.io/docs/user-guide/tasks/manage-python.html](https://conda.io/docs/user-guide/tasks/manage-python.html)
 
`Note for Windows users: - `
 please make sure that the python executable path is set in the environment variable `‘path’`.
 
Visit to `system properties`. 
Click on `environment variable`s.
Click on `‘path’` variable and click on `edit`.
Verify if the path of your python executable is preset in the list. If not, clock on ‘`New`’ button and 

add the path. Click `OK`.

<img src="img/winenv.png">
 
`Note`: - You will need to add the path of the folder that contains python.exe ( version >3).

Bluemix CLI installation help.
you can follow the installation steps from here.
[https://console.bluemix.net/docs/cli/index.html#overview] (https://goo.gl/FNwibJ)
[https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install)

#### Issues with aws commands:
Install Latest AWS CLI (For object storage)
`curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
./awscli-bundle/install -b ~/bin/aws`
(OR)
`pip install awscli`
