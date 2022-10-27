+++
author = ""
bg_image = ""
categories = []
date = ""
description = ""
draft = true
image = ""
tags = []
title = ""
type = "post"

+++
---
title: MLOps with CI/CD
date: 2022-10-26 22:00:00 +0000
image: "/images/sm-overview.png"

---
+++
author = "Kyrie"
bg_image = ""
categories = ["GitHub", "Git", "MLOps", "Data Science", "Data Engineering"]
date = 2022-10-25T22:00:00Z
description = "Train, deploy, and host your models on AWS."
image = "/images/mlops-loop-en.jpg"
tags = []
title = "MLOps CI/CD"
type = "post"

+++
# MLOps with CI/CD

**A hands-on lab to train, deploy, and host your models on AWS.**
[![Netlify Status](https://api.netlify.com/api/v1/badges/50131926-bd9b-4c67-96ab-643947664611/deploy-status)](https://app.netlify.com/sites/smartworkz-site/deploys)

* We will download code from an S3 bucket to use throughout this workshop.
* It contains image classification (MNIST) code using ConvNets based on [PyTorch examples,](https://github.com/pytorch/examples) and a CloudFormation stack.

![](/images/minst.png)

* You will push this code to your GitHub repository as an initial step to create CI/CD pipeline.

## 1. Prerequisites

* _Open and log in to your_ [_AWS account_](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fus-east-1.console.aws.amazon.com%2Fconsole%2Fhome%3FhashArgs%3D%2523%26isauthcode%3Dtrue%26nc2%3Dh_ct%26region%3Dus-east-1%26skipRegion%3Dtrue%26src%3Dheader-signin%26state%3DhashArgsFromTB_us-east-1_69d8e4b4ab0e37a6&client_id=arn%3Aaws%3Asignin%3A%3A%3Aconsole%2Fcanvas&forceMobileApp=0&code_challenge=uRVQqhamdm60rHPKKXydxDu4E3wcQICknuXn1V8seVo&code_challenge_method=SHA-256 "_blank") _(eu-west-1)_
* _Open and log in to your_ [_GitHub account_](https://github.com/)
* If not already done, _Install_ [_Visual Studio Code_](https://code.visualstudio.com/download) _(VSC)_
* If not already done, _Install_ [_Git Bash_](https://git-scm.com/downloads)
* _(Optional) Configure Git Bash as the default terminal for VSC_
  1. Click View, Terminal
  2. After the Terminal appears, press the F1 key
  3. Type the following, Terminal: Select Default Profile
  4. Select from the dropdown, Git Bash
* **Either,** clone the GitHub repository in the local Git repository

```shell
git clone https://github.com/smartworkz-kyriacos/mlops-sagemaker-ci-cd.git
```

* **Or**, _download the_ [_code_]()_, and unzip it in the local Git repository folder path_.
* Then _`cmd` from File Explorer in the path field_

1. Using File Explorer navigate to the local Git Repository

![](/images/Git-Repository.png)

1. In the path field type cmd and press the Enter key

![](/images/cmd.png)

* A cmd window opens in the repository path.

![](/images/Win_Cmd-Prompt.png)

* _Type `code .` in the cmd window prompt with the path_

![](/images/code.png)

* VSC opens automatically.

![](/images/vsc.png)

* _Open_ your GitHub account Repositories page

![](/images/Full-Screen.png)

Make sure that the  Git Bash terminal is in VSC (arrange it side-by-side with the  GitHub page).

![](/images/Git-Bash-VSC.png)

Run the following commands:

```shell
#Configure global settings

git config --global user.name "Name - Company"
git config --global user.email "your@email.nl"
git config --global push.default matching
git config --global alias.co checkout
git config --global credential.helper cache

#Check

git config --global user.name
git config --global user.email

#Initialize

git init
git status
git add .
git commit -m "MLOPs code remote upload from the local repository"
```

```shell
#Push to the main branch*

git push
```

* _Create a GitHub  Personal Access Token (PAT)_

1. In the upper-right corner of any page, click your profile photo, then click **Settings**.

   ![Settings icon in the user bar](https://docs.github.com/assets/cb-34573/images/help/settings/userbar-account-settings.png)
2. In the left sidebar, click **Developer settings**.

   ![Developer settings](https://docs.github.com/assets/cb-6064/images/help/settings/developer-settings.png)
3. In the left sidebar, click **Personal access tokens**.

   ![Personal access tokens](https://docs.github.com/assets/cb-7169/images/help/settings/personal_access_tokens_tab.png)
4. Click **Generate new token**.

   ![Generate new token button](https://docs.github.com/assets/cb-6922/images/help/settings/generate_new_token.png)
5. Give your token a descriptive name.

   ![Token description field](https://docs.github.com/assets/cb-3880/images/help/settings/token_description.png)
6. To give your token an expiration, select the **Expiration** drop-down menu, then click a default or use the calendar picker.![Token expiration field](https://docs.github.com/assets/cb-39847/images/help/settings/token_expiration.png)
7. Select the scopes or permissions, you'd like to grant this token. To use your token to access repositories from the command line, select **repo**.

   ![Selecting token scopes](https://docs.github.com/assets/cb-43299/images/help/settings/token_scopes.gif)
8. Click **Generate token**.

   ![Generate token button](https://docs.github.com/assets/cb-10912/images/help/settings/generate_token.png)

   ![Newly created token](https://docs.github.com/assets/cb-31676/images/help/settings/personal_access_tokens_ghe.png)

   **Warning:** Treat your tokens like passwords and keep them secret. When working with the API, use tokens as environment variables instead of hardcoding them into your programs.

* Save locally e.g. in a text file. Will be used shortly to specify in the stack details later.

## 2. Create the MLOps pipeline

* _Navigate to the_ [_CloudFormation service_](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Feu-west-1.console.aws.amazon.com%2Fcloudformation%2Fhome%3Fregion%3Deu-west-1%26state%3DhashArgs%2523%252F%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fcloudformation&forceMobileApp=0&code_challenge=2wvOgJKH4gI44qKCepanh-y6BA4SLHzP7S2hxNBhcJo&code_challenge_method=SHA-256)
* _Select Create stack_

![](/images/01_cloudformation.png)

* _Select Upload template file and upload the YAML file from the infrastructure folder called `infra/pipeline.yml`_

![](/images/02_cloudformation_create_stack.png)

* _Specify the stack details. These include:_
  * **Stack name:** mlpipeline
  * **Email:** {Your email} (to receive SNS notification)
  * **GitHub Token:** {previously generated and saved locally}
  * **GitHub User:** {Your user id}
  * **GitHub Repository:** mlops-sagemaker-ci-cd
  * **Branch:** master (main)

![](/images/mlops_03_cloudformation_specify_stack.png)

* _Click Next in the Configure stack options page_

![](/images/04_cloudformation_configure_stack.png)

* _Acknowledge that CloudFormation might create IAM resources with custom names and click Create stack_
* _You will see the stack creation which should be complete within some minutes._

![](/images/06_cloudformation_create_in_progress.png)

## 3. Run the MLOps pipeline

This is how your pipeline looks now:

<img src="images\\mlops_pipeline.png" style="zoom:50%;" />

Now that you created CI/CD pipeline, it's time to start experimenting with it.

* _Navigate to the CodePipeline service_

<img src="images/console-pipeline.png" style="zoom:100%;" />

* _Select your created pipeline_

![](/images/pipeline-green.png)

* 

## The steps include:

* **Source:** pulls code every time submit changes. Can be triggered manually by clicking on the **Release change** button.
* **Build_and_train:** executes the `source\training.py` script. This downloads the data uploads to an S3 bucket creates a training job and deploys the model
* **Test_Model:** executes the `source\test.py`  script that performs a basic test of the deployed model

We will now make changes to this code in order to improve the model. The goal is to show you how you can focus on model implementation, and have CodePipeline perform training steps automatically every time you push changes to the GitHub repo.

## 4. Use GPU and Spot instances

* _Modify `instance_type = "ml.p3.2xlarge"` in the `source\training.py` script_
* _In the `source\training.py` script uncomment these lines:_
  * `use_spot_instances = True	# Use a spot instance`
  * `max_run = 300 			# Max training time`
  * `max_wait = 600 			# Max training time + spot waiting time`
* After making these changes your PyTorch estimator should be like this:

```shell
estimator = PyTorch(
  entry_point="code/mnist.py",
  role=role,
  framework_version="1.4.0",
  instance_count=2,
  instance_type="ml.p3.2xlarge",
  py_version="py3",
  use_spot_instances=True,  # Use a spot instance
  max_run=300,  # Max training time
  max_wait=600,  # Max training time + spot waiting time
  hyperparameters={"epochs": 14, "backend": "gloo"},
)
```

* _Commit and push changes to your GitHub repository_. At the Git Bash run the following commands:

```shell
git status
git add .`
git commit -m "MLOPs code remote upload from the local repository"
git push
```

* _Navigate to SageMaker Training jobs._

![](/images/sagemaker-console.png)

* _Check to see Manage Spot Training Savings_

![](/images/spot-cost.png)

## 5. Add the training job dependencies

* _In the `source\training.py` script uncomment the following line `source_dir = "code`_
* _In the `source\training.py` script update entry_point to `entry_point="mnist.py"`_
* This line will tell SageMaker to first install defined dependencies from `code/requirements.txt`, and then to upload all code inside of this folder to your container.

  Your estimator should now look like this

  ```shell
  estimator = PyTorch(
    entry_point="mnist.py",
    source_dir="code",
    role=role,
    framework_version="1.4.0",
    instance_count=2,
    instance_type="ml.p3.2xlarge",
    py_version="py3",
    use_spot_instances=True,  # Use a spot instance
    max_run=300,  # Max training time
    max_wait=600,  # Max training time + spot waiting time
    hyperparameters={"epochs": 14, "backend": "gloo"},
  )
  ```

In order to do training with your new code, you should just commit and push changes to your GitHub repo as you did before in step 4!

Now after some minutes, in the AWS console inside SageMaker and section [Training jobs ](https://eu-west-1.console.aws.amazon.com/sagemaker/home?region=eu-west-1#/jobs) you will see the new job being executed.

## 6. Trigger training job from the local Git repository

In this section, you will trigger training jobs from your local machine without the need to commit and push every time.

* _Use or Create_ [_AWS Access keys_](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)
* _Install_ [_AWS CLI_](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* [_Set up_](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) _your AWS CLI_

```shell
 aws configure
    AWS Access Key ID [None]: enter your AWS Access Key ID
    AWS Secret Access Key [None]: enter your AWS Secret Access Key
    Default region name [None]: eu-west-1
    Default output format [None]: json
```

* _Create a virtual environment inside your project_

```shell
    cd source
    python3 -m venv venv
    source venv/bin/activate
```

* _Install required dependencies_

  pip install -r requirements.txt
* _Navigate to_ [_CloudFormation service stacks_](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Feu-west-1.console.aws.amazon.com%2Fcloudformation%2Fhome%3Fregion%3Deu-west-1%26state%3DhashArgs%2523%252Fstacks%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fcloudformation&forceMobileApp=0&code_challenge=URwODm8u3qJ-reT4-VjJK0HUS2_il02O30dEoJR9G4w&code_challenge_method=SHA-256)
* _Select the stack created earlier and go to the output section_

<img src="images\\output.png"  />

* _Copy the ExampleLocalCommand_

```shell
python training.py arn\:aws\:iam::xxxxxxx\:role/mlops-sagemaker-role bucket-name MODEL-NAME VERSION
```

* _In the command line replace MODEL-NAME and VERSION and execute_
* _Navigate to SageMaker Training jobs, check to see Manage Spot Training Savings_

![](/images/spot-cost-2.png)

## 7. Deploy with Lambda function

Now that we have a working SageMaker endpoint, we can integrate it with other AWS services. In this lab, you will create **API Gateway** and **Lambda function**.

This architecture will enable us to quickly test our endpoint through a simple `HTTP POST` request.

![](/images/mlops_lambda.png)

* _Install Chalice_

  Go to the `lambda` folder and install `chalice`

  pip install -r requirements-dev.txt

or run

    pip install chalice==1.20.0

* _Navigate to your_ [_SageMaker Endpoint_](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Feu-west-1.console.aws.amazon.com%2Fsagemaker%2Fhome%3Fregion%3Deu-west-1%26state%3DhashArgs%2523%252Fendpoints%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fsagemaker&forceMobileApp=0&code_challenge=MSKAxc0JJQj_CobJpfJex4TeYhURJarE2euJ8d6Jfvg&code_challenge_method=SHA-256) _in the AWS console and copy the name of the endpoint_

![](/images/mlops_endpoint.png)

* _In the lambda\\.chalice\\config.json update the value of the ENDPOINT_NAME environment variable with the name of your SageMaker endpoint_

  {
  "version": "2.0",
  "app_name": "predictor",
  "autogen_policy": false,
  "automatic_layer": true,
  "environment_variables": {
  "ENDPOINT_NAME": "name-of-your-sagemaker-endpoint"
  },
  "stages": {
  "dev": {
  "api_gateway_stage": "api"
  }
  }
  }
* _Deploy the Lambda function_

Let's now deploy this Lambda by running

    chalice deploy --stage dev

Make sure to run this command from the `lambda` folder. If your deployment times out due to your connection, please add `--connection-timeout 360` to your command.

Our Lambda function expects to receive an image in the request body. It then reshapes this image so it can be sent to our trained model. Finally, it receives response from the SageMaker endpoint and returns it to requester.

As we are exposing this Lambda function through REST API `@app.route("/", methods=["POST"])`, Chalice will deploy it behind the API Gateway that will route the incoming traffic to it.

* _Trigger your Lambda_

Now you can trigger this Lambda function by running included bash script

    bash post.sh

This script will download an image, and send a `POST` request to your Lambda. The response will contain probabilities for this image and prediction made by the deployed model.

    {
       "response" : {
          "Probabilities:" : "[[-3.10787258e+01 -1.61031952e+02 -2.43714166e+00 -2.35641022e+01\n  -1.84978195e+02 -9.14689526e-02 -5.73226471e+01 -8.57289124e+01\n  -7.99111023e+01 -9.30446320e+01]]",
          "This is your number:" : "5"
       }
    }