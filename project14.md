#  PROJECT 14 

## EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

**IMPORTANT NOTICE** – This project has some initial theoretical concepts that must be well understood before moving on to the practical part. Please read it carefully as many times as needed to completely digest the most important and fundamental DevOps concepts. To successfully implement this project, it is crucial to grasp the importance of the entire CI/CD process, roles of each tool and success metrics – so we encourage you to thoroughly study the following theory until you feel comfortable explaining all the concepts (e.g., to your new junior colleague or during a job interview).


In previous projects, you have been deploying a tooling website directly into the `MARKDOWN_HASH40d1b2d83998fabacb726e5bc3d22129MARKDOWNHASH`directory on dev servers. Well, even though that worked fine, and we were able to access the website, it is not the best way to do it. Real world web application code written on [Java](https://en.wikipedia.org/wiki/Java(programming_language)), .[NET](https://en.wikipedia.org/wiki/.NET_Framework) or other compiled programming languages require a build stage to create an executable file. The executable file (e.g., `jar`file in case of `Java`) contains all the codes embedded, and the necessary library dependencies, which the application needs to run and work successfully.

Some other programs written languages such as **PHP, JavaScript or Python** work directly without being built into an executable file – these languages are called **interpreted**. That is why we could easily deploy the entire code from git into *var/www/html* and immediately the webserver was able to render the pages in a browser. However, it is not optimal to download code directly from Git onto our servers. There is a smarter way to package the entire application code, and track release versions. We can package the entire code and all its dependencies into an archive such as *.tar.gz* or *.zip*, so that it can be easily unpacked on a respective environment’s servers.
For a better understanding of the difference between **compiled** vs **interpreted** programming languages – read  [this short article](https://www.freecodecamp.org/news/compiled-versus-interpreted-languages/)

In this project, you will understand and get hands on experience around the entire concept around CI/CD from applications perspective. To fully gain real expertise around this idea, it is best to see it in action across different programming languages and from the platform perspective too. From the application perspective, we will be focusing on PHP here; there are more projects ahead that are based on Java, Node.js, .Net and Python. By the time you start working on Terraform, Docker and Kubernetes projects, you will get to see the platform perspective of CI/CD in action.


### First install Jenkins 
since i am using Fedora i will the following steps: 
```
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum upgrade
# Add required dependencies for the jenkins package
sudo yum install java-11-openjdk
sudo yum install jenkins
sudo systemctl daemon-reload
```
following the [steps](https://www.jenkins.io/doc/book/installing/linux/) for installation of Jenkins 

Next we will install Java dependencies 

```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
###  INSTALL JAVA
```
sudo yum install java-11-openjdk-devel -y
```
#### open the bash profile 
```
vi .bash_profile
```
#### paste the below in the bash profile  -
```
 export JAVA_HOME=$(dirname (readlink (which javac)))))
 export PATH=$PATH:$JAVA_HOME/bin 
 export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```
start Jenkins service and Enabled 
```
systemctl start Jenkins
systemctl1 enable jenkins

sudo systemctl daemon-reload
```
![](/jenkins%20status.PNG)

use this command to retrieve user password
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
once Jenkins has been installed navigate to 

```
http://<my public ip>:8080

```

### Configuring Ansible For Jenkins Deployment

In previous projects, you have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible from Jenkins UI.

To do this,

1. Navigate to Jenkins URL

2. Install & Open Blue Ocean Jenkins Plugin

3. Create a new pipeline
![](/Jenkins-Create-Pipeline.png)
4. Select GitHub

![](/Jenkins-Select-Github.png)
5. Connect Jenkins with GitHub

![](/Jenkins-Create-Access-Token-To-Github.png)

6. Login to GitHub & Generate an Access Tokenhttps://www.darey.io/wp-content/uploads/2021/07/Jenkins-Create-Access-Token-To-Github.png

![](/Jenkins-Github-Access-Token.png)


![](/Jenkins-Github-Generate-Token.png)

7. Copy Access Token
![](/Jenkins-Copy-Token.png)

8. Paste the token and connect
![](/JEnkins-Paste-Token-And-Connect.png)

9. Create a new Pipeline
![](/Create-Pipeline.png)

At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console

![](/Jenkins-Exit-Blue-Ocean.png)

Here is our newly created pipeline. It takes the name of your GitHub repository.

![](/Jenkins-Ansible-Pipeline.png)

### Let us create our `Jenkinsfile`
Inside the Ansible project, create a new directory deploy and start a new file Jenkinsfile inside the directory.

![](/folder%20structure.PNG)

Add the code snippet below to start building the `Jenkinsfile` gradually. This pipeline currently has just one stage called `Build` and the only thing we are doing is using the `shell script` module to echo `Building Stage`

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

Now go back into the Ansible pipeline in Jenkins, and select configure

![](/Jenkins-Select-Configure.png)

Scroll down to `Build Configuration` section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`
![](/Jenkinsfile-Location.png)

Back to the pipeline again, this time click "Build now"

![](/Jenkins-Build-Now.png)

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean interface.

1. Click on Blue Ocean

![](/Jenkins-Click-Blue-Ocean.png)


2. Select your project

3. Click on the play button against the branch

![](/Jenkins-Ansible-Blue-Ocean-Start-Pipeline.png)

Notice that this pipeline is a multibranch one. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.
Let us see this in action.

1. Create a new git branch and name it `feature/jenkinspipeline-stages`
2. Currently we only have the Build stage. Let us add another stage called `Test`. Paste the code snippet below and push the new changes to GitHub.
```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}

```
4. To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

1. Click on the "Administration" button

![](/Jenkins-Ansible-Administration-Button.png)


2. Navigate to the Ansible project and click on "Scan repository now"

![](/Jenkins-Scan-Repository-Now.png)

3. Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

![](/Jenkins-Discover-New-Branch.png)

4. In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.
![](/Jenkins-Test-Stage-Blue-Ocean.png)

### A QUICK TASK FOR me!

```
1. Create a pull request to merge the latest code into the main branch
2. After merging the PR, go back into your terminal and switch into the main branch.
3. Pull the latest change.
4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)
   1. Package 
   2. Deploy 
   3. Clean up
5. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch
6. Eventually, your main branch should have a successful pipeline like this in blue ocea
```
![](/Jenkins-Complete-Initial-Pipeline.png)




```
git checkout -b feature/jenkinspipeline-stages
```



check which branch you are on using this   command
```

git checkout -a
```

## RUNNING ANSIBLE PLAYBOOK FROM JENKINS

Now that you have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

1. Installing Ansible on Jenkins
```
sudo yum install Jenkins
```
next we will install jenkins dependandies 
## FOR PYTHON 
```
yum install python3 python3-pip wget unzip git -y
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade pip
python3 -m pip install PyMySQL
python3 -m pip install mysql-connector-python
python3 -m pip install psycopg2==2.7.5 --ignore-installed
```
## FOR ROLES 

For Mysql Database
```
ansible-galaxy collection install community.mysql
```

For Postgresql Database

```
ansible-galaxy collection install community.postgresql
```
2. Installing Ansible plugin in Jenkins UI

3. Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully)

You can [watch a 10 minutes video here](https://www.youtube.com/watch?v=PRpEbFZi7nI&feature=youtu.be) to guide you through the entire setup


**Note:** Ensure that Ansible runs against the Dev environment successfully.

**Possible errors to watch out for:**