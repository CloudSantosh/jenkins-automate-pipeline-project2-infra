## ++++ JENKINS INSTALLATION ++++

## Pre-Requisites

### Jenkins Server Details:

```
Operating System     : Ubuntu
Hostname             : jenkins-ansible
RAM                  : 2 GB
CPU                  : 1 Core
EC2 Instance         : t2.small
```

#### Update repository of ubuntu

```
sudo -i
sudo apt-get update
```

### Change hostname

```
hostname
hostnamectl set-hostname jenkins-ansible
bash
hostname
```

### Install Java

```
java -version
apt-get install openjdk-11-jdk
java -version
```

### Install Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install jenkins=2.361.3 -y
```

### Service start

```
systemctl start jenkins
```

### Service enable & check status

```
systemctl enable jenkins
systemctl status jenkins
```

### Check 8080 port is used or not

```
netstat -plant | grep 8080
```

### Check version & Open jenkins on browser

```
jenkins --version

URL:   http://<jenkins_server_ip>:8080
```

### Get Jenkins Administrator password using this command

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

<br/>
<br/>
<br/>
<br/>

## ++++ ANSIBLE INSTALLATION ++++

### Add Ansible repository

```
sudo apt-add-repository ppa:ansible/ansible
```

### Now fetch latest update & install Ansible

```
sudo apt update
sudo apt-get install ansible -y
```

### Now check Ansible version

```
ansible --version
```

<br/>
<br/>
<br/>
<br/>

## ++++ MAVEN INSTALLATION ++++

### Check version before install

```
mvn --version
```

### Change dir to /opt and download maven

```
cd /opt/
ls
wget https://dlcdn.apache.org/maven/maven-3/3.9.1/binaries/apache-maven-3.9.1-bin.zip
apt-get install unzip -y
unzip apache-maven-3.9.1-bin.zip
ls
rm -rf apache-maven-3.9.1-bin.zip
ls
```

### Configure maven home path

```
vim ~/.bashrc

## Add end of the file & save it.
export M2_HOME=/opt/apache-maven-3.9.1
export PATH=$PATH:$M2_HOME/bin

source ~/.bashrc
```

### Check version again now

```
mvn --version
mvn --help
```

# [Back](https://github.com/CloudSantosh/jenkins-automate-pipeline-project2-application#configure-all-below-pre-requisites-for-project)

## SONARQUBE INSTALLATION

## Pre-Requisites

### Jenkins Server Details:

```
Operating System     : Ubuntu
Hostname             : sonarqube
RAM                  : 2 GB
CPU                  : 1 Core
EC2 Instance         : t2.small
```

#### Update repository of ubuntu

```
## Download URL:  https://www.sonarsource.com/products/sonarqube/downloads/

sudo -i
sudo apt update
```

### Change time hostname

```
hostnamectl set-hostname sonarqube
bash
```

### Install Java

```
java -version
apt-get install openjdk-17-jdk -y       ## For sonarqube-10.0.0.68432.zip
apt-get install openjdk-11-jdk -y       ## For sonarqube-8.9.2.46101.zip
java -version
```

### Install Sonarqube

```
cd /opt/
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.0.0.68432.zip
OR
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.2.46101.zip
apt install unzip -y
unzip sonarqube-10.0.0.68432.zip
ls
rm -rf sonarqube-10.0.0.68432.zip
mv sonarqube-10.0.0.68432 sonarqube
ls
```

### Create sonar user

```
useradd -d /opt/sonarqube sonar
cat /etc/passwd | grep sonar
ls -ld /opt/sonarqube
chown -R sonar:sonar /opt/sonarqube
ls -ld /opt/sonarqube
```

### Create custom service for sonar

```
cat >> /etc/systemd/system/sonarqube.service <<EOL
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
User=sonar
Group=sonar
PermissionsStartOnly=true
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=4096
TimeoutStartSec=5
Restart=always

[Install]
WantedBy=multi-user.target
EOL


ls -l /etc/systemd/system/sonarqube.service
```

### Open port 9000 from firewalld OR security group

```
9000
```

### Service start

```
systemctl start sonarqube.service
```

### Service enable & check status

```
systemctl enable sonarqube.service
systemctl status sonarqube.service
```

### Check 9000 port is used or not

```
apt install net-tools
netstat -plant | grep 9000
```

### Open sonarqube on browser

```
URL:   http://<sonarqube_ip>:9000

U: admin
P: admin

New Pass: admin@123
```

# [Back](https://github.com/CloudSantosh/jenkins-automate-pipeline-project2-application#configure-all-below-pre-requisites-for-project)

# KUBERNETES CLUSTER INSTALLATION

## Pre-Requisites

### Kubernetes Cluster Server Details:

```
Operating System     : Ubuntu
Hostname             : k8-master
RAM                  : 2 GB
CPU                  : 2 Core
EC2 Instance         : t3a.small
```

## ON MASTER NODE

### Switch to root user & Update repository of ubuntu

```
sudo -i
sudo apt update
```

### Start by disabling the swap memory

```
sudo swapoff -a
sed -i 's/^\(.*swap.*\)$/#\1/' /etc/fstab
```

### Change time hostname

```
hostname
sudo hostnamectl set-hostname k8-master
bash
hostname
```

### Install Docker with the command

```
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

### Add Docker’s official GPG key

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### Add Docker Repo

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

### Install the latest version of Docker Engine and containerd

```
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

### Check the installation (and version) by entering the following

```
docker --version
```

### The product_uuid can be checked by using the command

```
sudo cat /sys/class/dmi/id/product_uuid
```

### Set Docker to launch at boot by entering the following

```
sudo systemctl enable docker
```

### Verify Docker is running

```
sudo systemctl status docker
```

### Add Kubernetes Repo

```
{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
}
```

### Install kubeadm kubelet kubectl

```
apt update && apt-get install -y kubelet=1.21* kubeadm=1.21* kubectl=1.21*                 ## For 1.21 version
sudo apt-mark hold kubelet kubeadm kubectl
```

### Verify the installation with kubeadm

```
kubeadm version
kubectl version --short
```

### Initialize Kubernetes on Master Node

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

### Enter the following to create a directory for the cluster: To start using your cluster, you need to run the following as a regular user

```
sudo mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Now check to see if the kubectl command is activated

```
kubectl get nodes

NAME          STATUS   ROLES    AGE    VERSION
master-node   NOtReady    master   8m3s   v1.18.5
```

### Deploy Pod Network to Cluster

```
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### Verify that everything is running and communicating

```
kubectl get pod --all-namespaces
```

### Cross check your cluster is running status

```
kubectl get nodes
```

### Remove taint from k8-master node

```
kubectl taint nodes k8-master node-role.kubernetes.io/control-plane:NoSchedule-            ## v1.27.1
OR
kubectl taint nodes k8-master node-role.kubernetes.io/master:NoSchedule-                   ## v1.21.14

```

</br>

# KUBERNETES CLUSTER TESTING

### Check pod status

```
kubectl get pod
```

### Create testing.yml and insert below content

vim testing.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: testing
spec:
  containers:
  - name: testing
    image: nginx
```

### Check pod status

```
kubectl apply -f testing.yaml
```

### Check pod status

```
kubectl get pod
```

### After check delete the pod

```
kubectl delete -f testing.yaml
```

# [Back](https://github.com/CloudSantosh/jenkins-automate-pipeline-project2-application#configure-all-below-pre-requisites-for-project)
