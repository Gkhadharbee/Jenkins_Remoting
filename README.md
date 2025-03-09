# <p align="">Jenkins Remoting Layer
## <p align="">About the project</p>
   This project establish a communication channel between the controller(master) and agents(Slaves) Nodes.
It is responsible for transmitting data, executing commands, and maintaining a stable connection between different components in a Jenkins setup.

## Overview  
* Remoting is a library used by Jenkins to establish a communication channel between the controller and agents.
* It operates over various transport protocols, such as JNLP, SSH, WebSockets, and direct TCP.
* Provides serialization and deserialization of Java objects across nodes.
* Used for running builds and executing commands on remote agents.

## Pre-requisites
* Understanding about the Jenkins Remoting
* Jenkins installation
* Jenkins Master Node & Agent or Slave Node
* Plugins installation
* Verify Agent Connectivity to the Controller
* Pipeline Job to use remote agents

## <p align="">Steps</p>

### <p align="">Step1:</p>

### <p align="">Understand Jenkins Remoting</p>
Jenkins Remoting is a library that enables communication between Jenkins master (controller) and agents (nodes). It allows executing build tasks on remote machines.

### <p align="">Step2:</p>

#### <p align="">Install and Configure Jenkins</p>
Install Jenkins
1. Ensure that your software packages are up to date on your instance by using the following command to perform a quick software update:
```bash
sudo yum update –y
```
2. Add the Jenkins repo using the following command:
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
```
3. Import a key file from Jenkins-CI to enable installation from the package:
```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
```
4. Install Java (Amazon Linux 2023):
```bash
sudo yum install java-17-amazon-corretto -y
```
5. Install Jenkins:
```bash
sudo yum install jenkins -y
```
6. Enable the Jenkins service to start at boot:
```bash
sudo systemctl enable jenkins
```
7. Start Jenkins as a service:
```bash
sudo systemctl start jenkins
```
8. You can check the status of the Jenkins service using the command:
```bash
sudo systemctl status jenkins
```
9. Start Jenkins and set up an admin user.

![Screenshot 2025-02-27 153044](https://github.com/user-attachments/assets/a84b4899-feaf-4e90-b75f-30b72b65a06e)

![Screenshot 2025-02-27 153112](https://github.com/user-attachments/assets/a4058795-57f0-4c42-8e5e-f7af2d36b61d)

![Screenshot 2025-02-27 153322](https://github.com/user-attachments/assets/2bc78408-3c9f-4106-9ebf-dc707240e4de)

10. Install Required Plugins.
11. Go to Manage Jenkins → Manage Plugins.
12. Install the following:
* SSH Slaves Plugin (if using SSH-based agents).
* JNLP (Java Web Start) Agent Plugin (for remoting-based agents), (You don't need a separate plugin as JNLP is a core feature of Jenkins itself)

### <p align="">Step3:</p>

#### <p align="">Set Up Jenkins Agent (Node)</p>
1. Using JNLP (Java Web Start)
2. Go to Manage Jenkins → Manage Nodes.
3. Click on New Node, provide a name, and select Permanent Agent.

![Screenshot 2025-02-26 120407](https://github.com/user-attachments/assets/cebb0450-1100-4308-8f21-954ca56d01c5)

Configure:
* Remote Root Directory (e.g., /home/jenkins/ or C:\Jenkins\).
* Labels (optional).
* Usage: Keep as “Use this node as much as possible”.
* Launch method: Choose “Launch agent by connecting it to the master”.
* Save the node configuration.

![Screenshot 2025-02-26 120442](https://github.com/user-attachments/assets/8afef8e3-a2bc-4c84-9f91-5ef5434526b9)

### <p align="">Step4:</p>

#### <p align="">Download and Start the Jenkins Agent</p>
On the agent machine, open a terminal.
1. Download the agent.jar:

```bash
wget http://<JENKINS_URL>:8080/jnlpJars/agent.jar
```

2. Run the agent using the following command:

```bash
java -jar agent.jar -jnlpUrl http://<JENKINS_URL>:8080/computer/<NODE_NAME>/slave-agent.jnlp -secret <SECRET_KEY> -workDir "<WORK_DIRECTORY>"
```

3. Replace <JENKINS_URL>, <NODE_NAME>, and <SECRET_KEY> with actual values.
4. On master node, You can find the secret key under Manage Nodes → Select the Node (or) you can use that command which is in the below image to run the agent.

![Screenshot 2025-02-27 154250](https://github.com/user-attachments/assets/a267be20-7a2d-4752-bea9-325fe06b506b)

### <p align="">Step5:</p>

#### <p align="">Verify Agent Connectivity</p>
1. Go to Manage Jenkins → Manage Nodes.
2. The node should be in Connected status.

![Screenshot 2025-02-26 114909](https://github.com/user-attachments/assets/f78fb817-8981-4075-82a0-5c92a7d861fb)

3. Run a test job to verify it executes on the agent.

### <p align="">Step6:</p> 

#### <p align="">Configure Jenkins Pipeline to Use Remote Agents</p>
1. Create a new Pipeline Job in Jenkins.
2. Use the following pipeline script:

```bash
pipeline {
    agent {
        label 'remote-agent'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running on a remote agent!'
                sh 'hostname'
            }
        }
    }
}
```
3. Save and run the pipeline.

### <p align="">Step7:</p>

#### <p align="">Secure Jenkins Remoting</p>
* Use authentication with Jenkins credentials.
* Set up firewalls to allow only necessary traffic.
* Use SSH tunnels or VPNs for secure communication.
* Disable the remoting protocol when not in use (Manage Jenkins → Security Settings).
