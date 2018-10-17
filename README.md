# updoc-cd
Do not get the client_id and client_secret. terraform is not work for now.

manual steps
Create VM
1. create resource group
2. create resource from CentOS 7 -LVM - Rogue Wave Software
    a. use ssh from cat id_rsa.pub 
    b. 1 cpu and 1G Ram
    c. add 30G hard disk that will be used by jenkins to avoid not space
3. add inbound port 22 and 80
4. ssh tester@<public IP> 
5. partition, format and mount 
    to find folder size --> df -hT
    to find attached new disk, it should only sdX, but without number sdX1, sdX2 --> dmesg | grep sd
    to partition, format and mount to ~/jenkins-data 
            --> fdisk /dev/sdX 
            --> sudo mkfs -t ext4 /dev/sdcX1

            ---follw https://docs.microsoft.com/en-us/azure/virtual-machines/linux/attach-disk-portal
    to set ownership sudo chown tester:tester ~/jenkins-data
6. install docker 
    a. curl -sSL https://get.docker.com/ | sh
    b. sudo usermod -aG docker tester
    c. sudo systemctl enable docker
    d. sudo systemctl start docker
    e. --sudo systemctl disable firewalld
    e. --sudo systemctl stop firewalld
    e. mkdir ~/jenkins-data
    f. cd ~/jenkins-data    
    h. sudo docker pull jenkins/jenkins:lts
    g. sudo docker run -d --name jenkins -p 80:8080 -p 50000:50000 -v $(pwd):/var/jenkins_home --restart always jenkins/jenkins:lts
7. setup jenkins
    a. copy and paste key at first up
    b. install blue ocean plugin
    c. go to github and get the token that has repos and user:email right
    d. create pipeline 

Create slave1 VM
1. install docker 
    a. sudo curl -sSL https://get.docker.com/ | sh
    b. sudo usermod -aG docker tester
    c. sudo systemctl enable docker
    d. sudo systemctl start docker
2. install openJava8
   sudo yum install java-1.8.0-openjdk -y
3. download Jenkins Swarm Client
    a. sudo mkdir -p /usr/local/jenkins
    b. cd /usr/local/jenkins
    c. sudo wget https://repo.jenkins-ci.org/releases/org/jenkins-ci/plugins/swarm-client/3.7/swarm-client-3.7.jar
    d. sudo touch swarm.sh
    e. sudo chmod +x swarm.sh
4. edit swarm.sh file by adding below
        #!/bin/bash
        cd $(dirname $0)

        JENKINS_IP="10.0.0.4"
        USERNAME="admin"
        PASSWORD=""

        java -jar swarm-client-3.7.jar -name "$(hostname)" -executors 8 -labels docker -master "http://$JENKINS_IP:8080" -username "$USERNAME" -password "$PASSWORD" -fsroot /tmp
5. edit the file /etc/systemd/system/jenkins.service by adding below
        [Unit]
        Description=Jenkins
        After=network.target

        [Service]
        User=tester
        Restart=always
        Type=simple
        ExecStart=/usr/local/jenkins/swarm.sh

        [Install]
        WantedBy=multi-user.target
6. sudo systemctl enable jenkins
7. sudo systemctl start jenkins
8. install git 

Create WebHook from github
1. get jenkins IP
2. login github and select repository
3. create webhook 