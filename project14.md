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

