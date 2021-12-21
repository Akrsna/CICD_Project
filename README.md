**Executing the Ansible Playbook by Jenkins Job to deploy the Java application on AWS EC2 instance**

**AWS EC2 instances** : 1) Jenkins Master (t2 micro 1 core CPU &amp; GB RAM)

2)Jenkins Slave (t2 micro 1 core CPU &amp; GB RAM)

3) Ansible node (Production Server) (t2 micro 1 core CPU &amp; GB RAM)

4) Jfrog or Artifactory Server (t2 large 4 core CPU &amp; 8 GB RAM)

**Jenkins Jobs**

**Job1** : Cloning the java project from git, generate and upload the antifactory (war file) to Jfrog

**Job2** : Executing the ansible play book by Jenkins on to the nodes (production servers)

**Setting up of Jenkins Master:**

- Launch an EC2 instance of t2 micro 1 core CPU &amp; GB RAM
- Connect to the instance through SSH where there is the PEM file.
- Update the server with root user – _apt-get update_
- Install Java - _apt-get install openjdk-8-jdk_
- Install Maven - _apt-get install maven_
- Install Git - _apt-get install git_
- Install Jenkins by using the following commands (reference from Digital Ocean )
- First, add the repository key to the system:

_# wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -_

- When the key is added, the system will return OK. Next, append the Debian package repository address to the server&#39;s sources.list:

_# sudo sh -c &#39;echo deb http://pkg.jenkins.io/debian-stable binary/ \&gt;/etc/apt/sources.list.d/jenkins.list&#39;_

- When both of these are in place, run update so that apt will use the new repository:

# sudo apt-get update

- Finally, install Jenkins and its dependencies:

# apt-get install Jenkins

- Create Jenkins user

#adduser Jenkins

- Make Jenkins user as a sudoer

#visudo

under root type as below

Jenkins ALL=(ALL) NOPASSWD:ALL

- Go to # vi /etc/ssh/sshd\_config

Modify the Password authentication to yes from no

- Restart ssh services

# service ssh restart

**Setting up of Jenkins Slave cum Ansible Master:**

- Launch an EC2 instance of t2 micro 1 core CPU &amp; GB RAM
- Connect to the instance through SSH where there is the PEM file.
- Update the server with root user – _apt-get update_
- Install Java - _apt-get install openjdk-8-jdk_
- Install Maven - _apt-get install maven_
- Install Git - _apt-get install git_
- Create Jenkins user

#adduser Jenkins

- Make Jenkins user as a sudoer

#visudo

under root type as below

Jenkins ALL=(ALL) NOPASSWD:ALL

- Go to # vi /etc/ssh/sshd\_config

Modify the Password authentication to yes from no

- Restart ssh services

# service ssh restart

- Install Ansible

Add the project&#39;s PPA (personal package archive) to your system. We can add the Ansible PPA by typing the following command:

# sudo apt-add-repository ppa:ansible/ansible

Press ENTER to accept the PPA addition.

Next, we need to refresh our system&#39;s package index so that it is aware of the packages available in the PPA. Afterwards, we can install the software:

# apt-get update

# apt-get install ansible

- Clone the ansible role and playbook from the GIT as Jenkins user

$ git clone [https://github.com/Akrsna/myAnsibleRepo.git](https://github.com/Akrsna/myAnsibleRepo.git)


- Here I am automating the manual steps of setting up of the tomcat 8 on ubuntu by using the ansible role (Infrastructure as a code)
- The role is in [mytomcat8onUbuntu16](https://github.com/Akrsna/myAnsibleRepo/tree/main/mytomcat8onUbuntu16) and playbook which executes this role is [mytomsetup.yml](https://github.com/Akrsna/myAnsibleRepo/blob/main/mytomsetup.yml)
- Here need to provide the hosts file where the node details will be given.

From Jenkins Master

- As a Jenkins user

# su Jenkins

$ssh-keygen

$ssh-copy-id \&lt;Private IP of Jenkins Slave\&gt;

- Give the password of the Jenkins user

$ssh \&lt;Private IP of Jenkins Slave\&gt;

- Now we have been successfully logged into slave from master through SSH successfully.

**Setting up of Ansible Node/Production Server:**

- Launch an EC2 instance of t2 micro 1 core CPU &amp; GB RAM
- Connect to the instance through SSH where there is the PEM file.
- Update the server with root user – _apt-get update_
- Create Jenkins user

#adduser Jenkins

- Make Jenkins user as a sudoer

#visudo

under root type as below

Jenkins ALL=(ALL) NOPASSWD:ALL

- Go to # vi /etc/ssh/sshd\_config

Modify the Password authentication to yes from no

- Restart ssh services

# service ssh restart

From Ansible Master

- As a Jenkins user

# su Jenkins

$ssh-keygen

$ssh-copy-id \&lt;Private IP of Ansible Node\&gt;

- Give the password of the Jenkins user

$ssh \&lt;Private IP of Ansible Node \&gt;

- Now we have been successfully logged into node from Ansible master through SSH successfully.

**Installing Jforg Artifactory on Ubuntu 16.04 (Jfrog Server)**

**Step 1: Prepare Ubuntu**

We must first update the server run the commands below:

_# apt-get update_

**Step 2: Install OpenJDK 8**

To properly use JFrog Artifactory, you will need Java installed.

_# apt-get install openjdk-8-jdk_

Now that you have updated and installed Java, continue below to installing JFrog.

**Step 3: Download and Install JFrog Artifactory**

The quickest and easiest way to get JFrog installed is via a APT repository. Installing JFrog from the repository will allow you to always get the latest updates as they&#39;re released.

To install, follow the steps below:

First, install wget and other packages if you don&#39;t already have them installed.

_# sudo apt install wget software-properties-common_

Then run the commands below to add the repository key and file to Ubuntu.

_#wget -qO - https://api.bintray.com/orgs/jfrog/keys/gpg/public.key | apt-key add -_

_#sudo add-apt-repository &quot;deb [arch=amd64] https://jfrog.bintray.com/artifactory-debs $(lsb\_release -cs) main&quot;_

When you&#39;re done, run the commands below to update Ubuntu and install JFrog Artifactory.

_#apt-get update_

_#sudo apt install jfrog-artifactory-oss_

After installing, the commands below can be used to stop, start and enable JFrog&#39;s services.

_#systemctl stop artifactory.service_

_#systemctl start artifactory.service_

_#systemctl enable artifactory.service_

To check the service status, run the commands below:

_#systemctl status artifactory.service_

It should output similar lines as shown below:

_artifactory.service - Artifactory service_

_Loaded: loaded (/lib/systemd/system/artifactory.service; enabled; vendor preset: enabled)_

_Active: active (running) since Tue 2021-12-21 07:01:22 UTC; 18s ago_

_Process: 9921 ExecStart=/opt/jfrog/artifactory/app/bin/artifactoryManage.sh start (code=exited, status=0/SUCCESS)_

_Main PID: 12539 (java)_

_Tasks: 0_

_Memory: 276.0K_

_CPU: 3.263s_

_CGroup: /system.slice/artifactory.service_

‣ _12539 /opt/jfrog/artifactory/app/third-party/java/bin/java -Djava.util.logging.config.file=/opt/jfrog/artifactory_

_Dec 21 07:01:21 ip-172-31-25-198 artifactoryManage.sh[9921]: Starting frontend..._

_Dec 21 07:01:21 ip-172-31-25-198 artifactoryManage.sh[9921]: frontend not running. Proceed to start it up._

_Dec 21 07:01:21 ip-172-31-25-198 artifactoryManage.sh[9921]: frontend started. PID: 13257_

_Dec 21 07:01:21 ip-172-31-25-198 su[13259]: Successful su for artifactory by root_

_Dec 21 07:01:21 ip-172-31-25-198 su[13259]: + ??? root:artifactory_

_Dec 21 07:01:21 ip-172-31-25-198 su[13259]: pam\_unix(su:session): session opened for user artifactory by (uid=0)_

_Dec 21 07:01:22 ip-172-31-25-198 artifactoryManage.sh[9921]: Starting observability..._

_Dec 21 07:01:22 ip-172-31-25-198 artifactoryManage.sh[9921]: observability not running. Proceed to start it up._

_Dec 21 07:01:22 ip-172-31-25-198 artifactoryManage.sh[9921]: observability started. PID: 13384_

_Dec 21 07:01:22 ip-172-31-25-198 systemd[1]: Started Artifactory service._

_lines 1-21/21 (END)_

**Step 4: Access Artifactory Portal**

When you&#39;re done installing, open your web browser and browser to the server&#39;s hostname or IP address as shown below:

http://PublicIP:8082/artifactory/

Login with Username:  admin and Password: password

You&#39;ll be prompted to change the temporary password above. When you do, you&#39;ll able to start setting up your environment.

Create the repository under New local repository – MyTestRepo

![](RackMultipart20211221-4-1baevu5_html_eada4c6fbf889daf.png)

**Logging into Jenkins**

- In the browser give URL of Jenkins Master as \&lt;PublicIP:8080\&gt;
- Give the password from the server in the given location

# cat **/var/lib/jenkins/secrets/initialAdminPassword**

- Click on the install suggested plugins
- Create Admin user and provide all the details
- We will land on the Jenkins Dashboard.

![](RackMultipart20211221-4-1baevu5_html_12b9bfe8fcf20699.png)

![](RackMultipart20211221-4-1baevu5_html_7dd808f8729019af.png)

Configuring the Jenkins slave in Jenkins

- Go to Manage Jenkins and click on Manage Nodes and Clouds
- Give the node name as mynode01 and select Permanent Agent. OK
- Remote root directory - /home/Jenkins
- Labels - mynode01
- Usage -Use this node as much as possible
- Launch method – Launch agents via SSH
- Under Host – give the private IP of the Jenkins Slave
- Credentials – Jenkins/password
- Save

![](RackMultipart20211221-4-1baevu5_html_261f401920ada00d.png)

Configuring the required Plugins (Artifactory, Ansible) in Jenkins

- Go to Manage Jenkins and click on Manage Plugins
- In Available search for Artifactory and check that
- Click on Install without restart and similar steps for Ansible plugin.
- After installation of plugin check restart Jenkins where there are no jobs running
- After restarting login again into the Jenkins.

Configuring the Jfrog in Jenkins

- Go to Manage Jenkins and click on Configure
- Search for Jfrog
- Check Use the Credentials Plugin
- Add JFrog Platform Instances like instance ID – username (admin), JFrog Platform URL\&lt;jfrog server IP:8082\&gt;, JFrog Artifactory URL \&lt;jfrog server IP:8082/artifactory\&gt;
- Give the credentials of Jfrog Server.
- Test the connection : if the connection is correct the result will be found JFrog Artifactory URL
- Save

![](RackMultipart20211221-4-1baevu5_html_ce3029143a4d8388.png)

**Creating Jenkins Job1:**

- Click on new item provide the name of the job MyJavaProject and select FreeStyle
- Under my MyJavaProject – click on Configure

- Check the Restrict where this project can be run and give the node label as mynode01

![](RackMultipart20211221-4-1baevu5_html_90d1cd8cf5dc4140.png)

- In Source Code Management – check on GIT
- Go to Git and open the java application source code repository.
- Under Code copy the URL for cloning the project from GIT
- Give the Repository URL - [https://github.com/Akrsna/SampleJavaProject.git](https://github.com/Akrsna/SampleJavaProject.git)

![](RackMultipart20211221-4-1baevu5_html_9a17926e46cee166.png)

- In Build trigger – we can give the schedule when the job should run by using the CRON expression.

![](RackMultipart20211221-4-1baevu5_html_f106d607a30ad9f0.png)

- Build Environment – check the Delete workspace before build starts.

![](RackMultipart20211221-4-1baevu5_html_2c85413aa56dd332.png)

- Check Maven3-Artifactory Integration
- Select the Artifactory server from the dropdown as we already configured jfrog in Jenkins
- Select the created repository from the Jfrog.
- Check Deploy artifacts to Artifactory and Include Patterns as **\*/\*.war**
- So that only the war file will be sent to the Jfrog repository.

![](RackMultipart20211221-4-1baevu5_html_85f5576d96ad061b.png)

![](RackMultipart20211221-4-1baevu5_html_47ea545f7809ca3f.png)

- Under Build – give Maven name, root pom as pom.xml and goals as clean install.
- Save

![](RackMultipart20211221-4-1baevu5_html_e34fd19c702dc478.png)

Creating Jenkins Job2:

- Click on new item provide the name of the job myAnsibleJob and select FreeStyle
- Under my MyJavaProject – click on Configure
- Check the Restrict where this project can be run and give the node label as mynode01
- Under build – Invoke ansible playbook
- Provide the host list path and also the playbook path respectively.
- Save.

![](RackMultipart20211221-4-1baevu5_html_98edea514d6094d5.png)

Executing the builds:

- Select the MyJavaProject and click on Post Build Actions – under Build other projects give the myAnsibleJob.
- Check on Trigger only if the build is stable.
- Save

![](RackMultipart20211221-4-1baevu5_html_ef2120f86f99959a.png)

- Go to MyJavaProject and click on Build now
- The job is triggered and we can see the running of the job by clicking on Console output.
- After successful completion of the first job the second job gets retriggered automatically and the java war file gets deployed on to the ansible node.
- To verify that in the browser \&lt;node IP address:8080\&gt;

The Apache Tomcat/8.5.5 page gets opened and click on Manage App.

![](RackMultipart20211221-4-1baevu5_html_89448473c1581e2d.png)

- Click on the Manager App there we can see the application name and click on it.
- Now we will be able to see the deployed application is up and running by Apache tomcat.
# CICD_Project
