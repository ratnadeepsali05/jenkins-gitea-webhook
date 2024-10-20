# Jenkins
1) Create two  ubuntu instances, one for jenkins and another for Gitea.
   ![image](https://github.com/user-attachments/assets/f807315d-f1ab-40ac-aeb0-21be69c21320)
   make sure you add these ports for  launching jenkins (port 8080 must for jenkins)
    
![image](https://github.com/user-attachments/assets/4f283199-6eac-474e-9d33-8e22d29743d3)

add below ports for Gitea instance (port 3000 must for gitea)
![image](https://github.com/user-attachments/assets/0a124ff4-6211-47e0-befd-8494929487c6)


2)connect the gitea instance.
On gitea instance execute the folllowing steps.
![image](https://github.com/user-attachments/assets/6dbf7f60-487c-4475-9825-46494ac38ec2)
![image](https://github.com/user-attachments/assets/88ee4522-c64b-4692-968c-47321c6e0f7b)
![image](https://github.com/user-attachments/assets/0a4d4f49-8a0d-494b-9d65-df1f7d7d9193)
where 16 is the version here , edit it to the version that you have installed.

![image](https://github.com/user-attachments/assets/3cfbaa35-efdd-4a30-84cb-e5bd1c16e180)
exit the postgresql DB with \q

![image](https://github.com/user-attachments/assets/0ee64a73-b5fb-4d1c-9ecd-b55a17e257eb)
Add the following line as the first line of pg_hba.conf . It allows access to all databases for all users with an encrypted password:

# TYPE DATABASE USER CIDR-ADDRESS  METHOD
host  all  all 0.0.0.0/0 scram-sha-256

to allow the connection from outside pgAdmin , Add or edit the following line in your postgresql.conf :

```
listen_addresses = '*'
```

Restart the service

service postgresql restart (try to use nano editor rather than vim editor ) and comment off the listen address .
![image](https://github.com/user-attachments/assets/4eaa93e7-7e8c-4a94-909d-e37a04667e33)


-> in nano editor ctr+O --> ctrl+C -->ctrl+X----> save modified buffer yes +enter 

2)steps to install Gitea :-----

Installing Gitea--->
Step 1 — Update the APT package cache, upgrade the already installed software and install Git:

```
sudo apt update && sudo apt upgrade -y && sudo apt install git -y
```

Step 2 — Download the Gitea Binary and make it executable:

```
wget -O gitea https://dl.gitea.com/gitea/1.21/gitea-1.21-linux-amd64
chmod +x gitea
```
Step 3 — Add the user that will run the Gitea application:

```
sudo adduser --system --shell /bin/bash --gecos 'Git Version Control' --group --disabled-password --home /home/git git
```
![alt text](image.png)
  


Step 4 — Create the folder structure that is used by Gitea to store data:

```
sudo mkdir -p /var/lib/gitea/custom
sudo mkdir -p /var/lib/gitea/data
sudo mkdir -p /var/lib/gitea/log
sudo chown -R git:git /var/lib/gitea/
sudo chmod -R 750 /var/lib/gitea/
sudo mkdir /etc/gitea
sudo chown root:git /etc/gitea
sudo chmod 770 /etc/gitea
```

Step 5 — Set the working directory of Gitea:

```
export GITEA_WORK_DIR=/var/lib/gitea/
```

Step 6 — Copy the Gitea binary file to /usr/local/bin to make it available system-wide:

```
sudo cp gitea /usr/local/bin/gitea
```

Run Gitea as service
Step 1 — Create a systemd service for Gitea

```
sudo vim /etc/systemd/system/gitea.service
```

Step 2 — Copy the following content into the service file:

```
[Unit]
    Description=Gitea (Git with a cup of tea)
       After=syslog.target 
        After=network.target

[Service]

    RestartSec=2s
    Type=simple
    User=git
    Group=git
     WorkingDirectory=/var/lib/gitea/
    ExecStart=/usr/local/bin/gitea web -c /etc/gitea/app.ini
     Restart=always
     Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/var/lib/gitea

[Install]
     WantedBy=multi-user.target
```
Step 3 — Enable the service and start Gitea at system boot:

![image](https://github.com/user-attachments/assets/4aa13856-4b35-41ec-b578-fcf196a34c57)

```
sudo systemctl enable gitea.service
sudo systemctl start gitea.service
```

Step 4 — In a web browser go to http://your_instance_ip:3000 to access the Gitea application


![image](https://github.com/user-attachments/assets/d276c155-3448-4524-9c02-9e4d8b51ca1d)
![image](https://github.com/user-attachments/assets/c85a657f-845d-455b-bde0-cd3fd669d3b7)

click on install gitea


password for gitea --> gitea.

![image](https://github.com/user-attachments/assets/7852b1cb-924f-428d-a3bd-a5f065503e05)
register for gitea

![image](https://github.com/user-attachments/assets/8f973d27-d9bf-4bb7-9004-becc3e96e909)



3)connect the instance of jenkins and install jenkins in it with java dependency.

![image](https://github.com/user-attachments/assets/e61fe633-980b-4873-97ff-fc57342e42f0)

![image](https://github.com/user-attachments/assets/cea10bc2-d84b-4609-8e5a-c06494813ca1)

4) go to dashboard ----> manage jenkins
  manage plugins---> available plugins -->intsall gitea .


5) create an repo  in gitea . enter in that repo then go to the settings ----> add webhooks.--> select Gitea.
   
![image](https://github.com/user-attachments/assets/719a8247-8d4b-4c4a-a7a4-1360b8623bbd)

6)add the target URL as --->
```
http://65.1.168.38:8080/gitea-webhook/post 
```

(<the ip of  jenkins instance>:8080/gitea-webhook/post

HTTP method --->POST .
POST CONTENT TYPE --->application/json.

then ,you make click on test delivery to check whether webhooks has been created or not.
![image](https://github.com/user-attachments/assets/01a2378b-3b38-48ae-a799-9e0bdcda163f)

7)inside the gitea repo create or upload new file 
  add the following script in it 

```
  pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building  the jenkinsfile.'
                // Add your build commands here
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // Add your test commands here
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // Add your deployment commands here
            }
        }
    }
}
```

8)![image](https://github.com/user-attachments/assets/867d875d-a104-4ae2-9637-dcc06209d036)

![image](https://github.com/user-attachments/assets/2f5b2cd9-367c-47f7-a00b-fb9c4adde758)

![image](https://github.com/user-attachments/assets/1f59220c-d0c3-4364-ad1c-13dc600eb1bd)

![image](https://github.com/user-attachments/assets/ec2f4a07-b3a4-43fb-8e59-8d4641f52364)

![image](https://github.com/user-attachments/assets/c49f1445-0caf-4bc9-ac40-783dbf016d77)

![image](https://github.com/user-attachments/assets/a2ca9639-6ff6-46a3-a154-3fafb7bd8785)

![image](https://github.com/user-attachments/assets/ed3a310c-bfee-4895-a58b-5db6a5491327)

then save the changes

![image](https://github.com/user-attachments/assets/41e9a407-c165-47e4-9d92-450b3e956e10)

![image](https://github.com/user-attachments/assets/e23d346f-0323-4b52-9483-d1614b5c62aa)

![image](https://github.com/user-attachments/assets/a71fcb91-29aa-4fed-ba76-4c76f6334d73)












8)go to the jenkins dashboard click on build now .
![image](https://github.com/user-attachments/assets/028a3379-ae33-4d9f-98ac-374e822af526)

![image](https://github.com/user-attachments/assets/831104f0-5fc4-4f15-b2fb-b0bf4a62cb99)















