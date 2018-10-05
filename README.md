# updoc-cd
Do not get the client_id and client_secret. terraform is not work for now.

manual steps
Create VM
1. create resource group
2. create resource from CentOS 7 -LVM - Rogue Wave Software
    a. use ssh from cat id_rsa.pub 
    b. 1 cpu and 1G Ram
3. add inbound port 22 and 80
4. ssh tester@<public IP> 
5. install docker 
    a. curl -sSL https://get.docker.com/ | sh
    b. sudo usermod -aG docker tester
    c. sudo systemctl enable docker
    d. sudo systemctl start docker
    e. mkdir ~/jenkins
    f. cd ~/jenkins
    h. sudo docker pull jenkins/jenkins:lts
    g. sudo docker run -d --name jenkins -p 80:8080 -p 50000:50000 -v $(pwd):/var/jenkins_home --restart always jenkins/jenkins:lts
6. setup jenkins
    a. copy and paste key at first up
    b. install blue ocean plugin
    c. go to github and get the token that has repos and user:email right
    d. create pipeline 