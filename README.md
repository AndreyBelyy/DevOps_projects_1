# Jenkins CI/CD pipeline with GitHub webhook integration for Deploying Docker application on EC2 instances using the declarative pipeline.
Steps:
1. Create an AWS instance with Ubuntu OS and new key pair (#any_name#), allowing HTTP and HTTPS for inbound rules.
2. Download .pem file
3. Launch instance
4. Go to "Connect to instance" - "SSH client" -  press copy under "4. Connect to your instance using its Public DNS" (kindly refer to https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html)
5. Open the terminal and head to the folder where is downloaded .pem file
6. Paste the commant from 4 point
7. In the machine, run the command
“ssh-keygen”
This will generate public and private keys in the machine.
id_rsa – Private Key
id_rsa.pub – Public Key
8. Install jenkins (https://www.jenkins.io/doc/book/installing/linux/)
9. Install Docker "sudo apt install docker.io"
10. Add your jenkins user to the docker group "sudo usermod -aG docker jenkins" and restart the jenkins server
11. Now check if it got installed by running “jenkins --version” and “docker --version”
12. Now, we will allow ports 8080 and 8001 for this machine from a security group. We can find the security group in the VM description. We need to allow “Inbound Rule” for 8080 and 8001 from 0.0.0.0/0 and type "Custom TCP"
13. Now, Copy the Public Ip of the machine and paste it to the browser to access the Jenkins portal. As,
“54.80.182.77:8080"
14. We need an Administrator Password to unlock this. Usualy path is indicated in "Unlock Jenkins" page for example "/var/lib/Jenkins/secrets/initialAdminPassword"
15. Now Click on, “Install Suggested Plugins”
16. After plugin installation Jenkins will ask us to create the First Admin User
17. Now, we will create a CI/CD pipeline, which will fetch the code from GitHub
18. Ckick on "New Item"
19. Add name, project is "Freestyle project" then click OK
20. In Source Code Management, select Git and Add Repository URL and Credentials. (If there is not any added credential, we need to add). If the repo is open, it could be any name if it's a https link.
21. Repo URL: https://github.com/AndreyBelyy/DevOps_projects_1.git
22. In Build Step, select Execute Shell and write following command to build docker image and from Docker image we will create a container:
  docker build -t this-project-app . (When you run this command, Docker will look for a Dockerfile in the current directory (.) and use it to build an image tagged as "this-project-app". The image will be created based on the instructions and configurations specified in the Dockerfile.)
  docker run -p 8001:8001 -d this-project-app  (Docker will create a container from the "this-project-app" image, map port 8001 from the container to port 8001 on the host machine, and run the container in detached mode. The container will execute the application or process defined in the image, and you can access it on your host machine by using http://localhost:8001 (assuming the application inside the container listens on port 8001)
23. Click on build Now. And the build will be started, in the build history
24. After getting success, In the browser, search for <public_ip_of_ec2:8001>
25. Now, our goal is,
·       Whenever the developer commits their code in GitHub, after every commit, it should reflect in the live web app.
·       For that, we will use “GitScm polling”.
·       Every time, a developer made a commit, a trigger will run automatically, which will rebuild the image and run a container on your behalf as a part of automation that will run the pipeline automatically
26. Now, configure the project again, and add "Build Trigger: GitHub hook trigger for GitScm polling", Description: GitHub webhook integration
27. We need to install the “GitHub Integration” plugin from Manage Jenkins, by following the path, (Manage Jenkins > Manage Plugins > GitHub Integration)
28. Goto GitHub > Settings > SSH and GPG Keys > New SSH Key
    Title: any_name
    Key_type: Authentication Key
    Key: To get the Public key, open the “id_rsa.pub” file in EC2 instance and copy the content. (Public Key)
29. Now, we need to go to GitHub and create a new SSH and GPG Key (GitHub > Repo “DevOps_projects_1” > Settings > Webhooks)
30. Add the following details,
    Payload URL: http://<public_ip_of_ec2>:8080/github-webhook/
    Content Type: "application/json"
    Which event would you like to trigger this webhook?
     - "Just the push event."
     - "Active: True"
    Click on “Add Webhook”.
31. Save the configured project
32. Do some changes in the code and push to GitHub, this will automatically run a pipeline, and the new code will be Live
    


   
