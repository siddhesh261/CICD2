# Project-05: Build a Docker image for Python flask App using Jenkins pipeline

# GitHub >> Jenkins >> Docker >> DockerHub

## Prerequisites

- [AWS Account](https://aws.amazon.com/free/)
- IDE

  - [Visual Studio Code](https://code.visualstudio.com/download)
- Install VS Code Extensions (Terraform, terraform-lint)
- [Git](https://git-scm.com/downloads)
- [Python] (https://www.python.org/downloads/)
- [pip] Package Manager(https://pip.pypa.io/en/stable/installation/#get-pip-py)
- [Docker Hub Account](https://hub.docker.com/signup)

## Step-01: Develop and Test Application Code (on local system)

### 1.1 Develop `Application code` + `Dockerfile` + `Jenkinsfile`

- You may clone the sample Python Flask application from this repo:

```
git clone https://github.com/kbindesh/sample-python-flask-app.git
```

**Note**: Update the Docker Hub account details as per your account in Jenkinsfile.

- **Install Flask module**

```
pip install Flask
```

### 1.2 Test the Application locally

- Open the application in Visual Studio Code editor.

- In termial, run below command to run it locally:

```
python3 app.py

OR

python app.py
```

### 1.3 Create a new remote repository in Github Account (SCM)

- Create a new repo with atleast three branches, namely:
  - Development
  - Testing
  - Production

### 1.4 Check-in the code to SCM (Github)

- Create a new Github repository.
- Check-in the code on **feature branch**.
- Create a PR to merge the changes on Development/Integration branch.
- Approve the PR request and verify the changes on Development branch.

## Step-02: Setup the Jenkins Server (EC2 Instance/VM/PM)

### 2.1 Provision an EC2 Instance

- **AMI**: Amazon Linux 2 (you can have any other OS)
- **Instance Type**: t2.micro
- **VPC & Subnet**: Default
- **Public IP**: Enabled
- **Security Group**: Allow Ingress traffic on SSH(22) and 8080
- **Storage**: Root Volume(at least 10GB)

### 2.2 Install and Configure `Jenkins`

- Connect to Jenkins server
- Configure Jenkins **On Amazon Linux 2 EC2 Instance**

```
sudo yum install -y java-17-amazon-corretto.x86_64
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum -y upgrade
sudo yum install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

- **On other platforms**

- Refer to this link: https://www.jenkins.io/doc/book/installing/

### 2.3 Install & Configure `Docker` on Jenkins server

- Execute below commands to install docker:

```
sudo amazon-linux-extras install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo chkconfig docker on
```

### 2.4 Download and Install `Git`

- On Amazon Linux 2/RHEL/CentOS

```
sudo yum install -y git
```

- On other OS platforms, refer to this link: https://git-scm.com/downloads

### 2.5 Install `Python`

- By default, Amazon Linux 2 comes with Python pre-installed.
- In order to check if the python is present on your machine or not, simply check the python version:

```
python --version

OR

python3 --version
```

- In case you do not find any existing python version on the system, to install python (say Python 3.7 version)

```
sudo yum install python37
```

- You may download the latest version of Python from this link: https://www.python.org/downloads/

### 2.6 Install `pip` Package Manager

- To check if `pip` package manage already exists on your system:

```
pip --version
```

- In case if pip is not present on your machine, download the installation script from https://pypa.io

```
sudo curl -O https://bootstrap.pypa.io/get-pip.py
```

- Execute the downloaded script to install latest version of pip package manager:

```
sudo python3 get-pip.py --user
```

### 2.7 Create a new Github Webhook for triggering Jenkins jobs

- Navigate to the Github repo created in **Step# 1.3**
- Go to **Setting** tab >> **Webhooks** >> **Add Webhook**
- **Payload URL**: https://<your_jenkins_server_public_ip_or_dns>:8080/github-webhook/
- **Content type**: application/json
- **Which events would you like to trigger this webhook?** : Let me select individual events >> Select **Pull requests** and **Pushes** checkboxes
- Check the **Active** checkbox >> **Create Webhook**

## Step-03: Store Docker Hub (or any other registry) credentials in Jenkins

- Access your Jenkins server's dashboard: http://jenkins_server_public_ip_or_dns:8080
- From left-side panel, select **Manage Jenkins** >> **Credentials**
- Select scope as **System** >> Under credentials section, click on **Systems** >> **Global Credentials (Unrestricted)** >> **New Credential**

```
# Credential (in this lab assuming dockerhub as registry)

Kind: Username and Password
Scope: Global
ID: docker_creds
Username: <YOUR_DOCKER_HUB_USERNAME>
Password: <YOUR_DOCKER_HUB_PASSWORD>
```

## Step-04: Elevate the permissions of Jenkins user (on Jenkins Server)

```
# Add the jenkins user to docker group
sudo usermod -aG docker jenkins

# Restart the the system or restart the jenkins service
sudo shutdown -r now

OR

sudo service jenkins restart

```

## Step-05: Create a Jenkins build job for packaging and shipping Application to DockerHub

- Access your Jenkins server's dashboard: http://jenkins_server_public_ip_or_dns:8080
- From left-side panel, select **New item**.
- Jenkins **Item Name**: say _build-docker-img-job-01_
- **Type**: Pipeline
- Under **Build Triggers** section, check **GitHub hook trigger for GITScm polling** option.
- Under **Pipeline** section:

  - **Definition**: Pipeline script from SCM
  - **SCM**: Git
  - **Repository URL**: <YOUR_GITHUB_REPOSITORY_URL>
  - **Branches to build**: <GITHUB_BRANCH_NAME>
  - **Script Path**: Jenkinsfile

## Step-06: Trigger the Jenkins job automatically

- As the github webhook is in place, mere code push or PR merge on main branch will trigger the Jenkins Job.
- From your local system repo, check-in a small change to your github repo.

## Step-07: Verify the created Docker Image in Docker Hub

- Navigate to your dockerhub account, and verify if the docker image got created successfully.
