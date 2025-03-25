# 🚀 **Swiggy Clone App Deployment explanation step-by-stept**

**Tools & Services Used:**

1. Terraform For EC2 instance and Security-Group Creation
2. Github
3. Jenkins
4. Sonarqube
5. Owasp
6. Trivy
7. Docker&Dockerhub


**Explanation**

After setting up the EC2 instance with Terraform open the ports 22,443,80,8080,9000.

Using the Script Please install the below
1.Jdk
2.Docker
3.Jenkins
4.Sonarqube using Docker

Once all the installations are completed try to Access the Jenkins on port 8080 and Sonarqube on port 9000

**CICD setup:**

We need to Install the Plugins inorder setup this pipeline, Install the plugins as per below screenshots

![image](https://github.com/user-attachments/assets/71fe7696-dad6-4149-8f24-b0119896fcd1)

Once the Above is done, please navigate to Manage Jenkins->Tools

<img width="836" alt="image" src="https://github.com/user-attachments/assets/28d4f3ec-8d76-4b79-a4a8-718c43e1903f" />


<img width="847" alt="image" src="https://github.com/user-attachments/assets/9be3b4cc-6385-427d-89c7-ac8911dba477" />


<img width="838" alt="image" src="https://github.com/user-attachments/assets/a6218fbd-1b2d-40dd-97df-b09203e57e6f" />


<img width="855" alt="image" src="https://github.com/user-attachments/assets/a4b5ae5f-2bcf-4997-9319-41228d77f67c" />


Navigate to the Manage Jenkins->System

Here based on the below screenshot configure the Sonarqube server

<img width="836" alt="image" src="https://github.com/user-attachments/assets/a12cccd0-efee-42c9-b6ed-cf3bc31ae270" />

Navigate to the Manage Jenkins-> Credentials, Here you have to add the crdentials for Dockerhub which is needed to push the image that is build by using the Pipeline and the the token which we are creating on the Sonarqube which is used for the Authentication purpose.



<img width="662" alt="image" src="https://github.com/user-attachments/assets/c30adc03-aba1-490d-8272-b4eafae67c3d" />



**** Changes to be done from the Sonarqube Server ****

Navigate to Administration -> Configuration ->Webhooks, Create a webhook As per the below Screenshot, 

<img width="351" alt="image" src="https://github.com/user-attachments/assets/f2b5a0e5-7950-4dbd-ac7f-abab54619b51" />


Now, Navigate to Administration->Security->Users create a Token as per the below screenshot
   

<img width="735" alt="image" src="https://github.com/user-attachments/assets/b685cdba-3ab9-435c-862f-5f31888c888b" />


Then proceed for creating the Pipeline using the pipeline script available in the Repository



**** What is a Webhook in SonarQube? ****

A webhook in SonarQube is a way for the SonarQube server to notify external systems (like CI/CD pipelines, monitoring tools, or messaging services) when specific events occur in a project, such as:

When an analysis is finished.

When a Quality Gate status changes (e.g., Passed, Failed).

When a new issue is raised or an existing issue is updated.

In essence, a webhook allows SonarQube to send a payload (data) to an external URL (such as a listener or a service) whenever certain events happen within SonarQube.
