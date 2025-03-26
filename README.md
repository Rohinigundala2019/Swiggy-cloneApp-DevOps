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


**CICD PIPLINE EXPLANATION**

This Jenkins pipeline is designed for continuous integration and delivery (CI/CD) for an application named "Swiggy." It involves multiple stages for code quality analysis, dependency scanning, Docker build, vulnerability checks, and deployment. Here's a detailed breakdown of each stage in the pipeline:

### 1. **Agent Declaration**
   - `agent any`: This means the pipeline can run on any available agent or node. Jenkins will use any worker node that's free to execute the pipeline.

### 2. **Tools Declaration**
   - `jdk 'jdk17'`: Declares the use of Java Development Kit (JDK) version 17 for the pipeline.
   - `nodejs 'node23'`: Declares the use of Node.js version 23 for executing Node.js-related tasks (like `npm install`).
   - **Environment Variable**: 
     - `SCANNER_HOME=tool 'sonar-scanner'`: This sets up an environment variable `SCANNER_HOME` which points to the SonarQube Scanner installation directory.

### 3. **Stages**
   The pipeline is divided into multiple stages, each performing a distinct task:

---

### 4. **Stage: `clean workspace`**
   - This stage is used to clean the workspace before starting any new job. It ensures there are no leftover files from previous builds that might affect the current build.
   - `cleanWs()`: Cleans the workspace (removes files from the previous builds).

---

### 5. **Stage: `Checkout from Git`**
   - This stage is responsible for fetching the source code from the Git repository where the project is stored.
   - `git 'https://github.com/Rohinigundala2019/Swiggy-cloneApp-DevOps.git'`: This command clones the repository from GitHub. The pipeline fetches the latest version of the project to work with.

---

### 6. **Stage: `SonarQube Analysis`**
   - This stage performs a static code analysis using **SonarQube** to check the quality of the code, identify bugs, vulnerabilities, and code smells.
   - `withSonarQubeEnv('sonar-server')`: This command sets up the SonarQube environment using the server configuration provided in the Jenkins instance. It points to a configured SonarQube server.
   - `sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Swiggy -Dsonar.projectKey=Swiggy '''`: This runs the SonarQube scanner on the code. It uses the `sonar-scanner` tool to analyze the project with the specified project name (`Swiggy`) and project key (`Swiggy`).

---

### 7. **Stage: `quality gate`**
   - This stage waits for the result of the **SonarQube Quality Gate** to determine if the code meets the defined quality standards.
   - `waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'`: The `waitForQualityGate` step checks the quality gate status from SonarQube. If the quality gate fails (i.e., the code does not meet quality standards), the pipeline can be configured to abort, but in this case, it's not set to abort automatically (`abortPipeline: false`).

---

### 8. **Stage: `Install Dependencies`**
   - This stage installs the required dependencies for the Node.js application using `npm`.
   - `sh "npm install"`: This runs the command `npm install`, which reads the `package.json` file and installs the necessary dependencies for the application to run.

---

### 9. **Stage: `OWASP Dependency-Check (FS Scan)`**
   - This stage runs a **dependency check** to identify known vulnerabilities in the project’s dependencies (JavaScript, Node.js modules, etc.) using the **OWASP Dependency-Check** tool.
   - `dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'`: This command scans the project directory (`--scan ./`) for dependencies and checks for vulnerabilities.
     - The flags `--disableYarnAudit` and `--disableNodeAudit` are used to disable certain audit checks.
   - `dependencyCheckPublisher pattern: '**/dependency-check-report.xml'`: After the scan, the generated report is published to Jenkins, where the results can be viewed.

---

### 10. **Stage: `TRIVY FS Scan`**
   - This stage performs a **file system scan** for vulnerabilities using **Trivy**, a vulnerability scanner for containers and file systems.
   - `sh "trivy fs . > trivyfs.txt"`: The `trivy fs` command scans the entire file system of the repository (`.` indicates the current directory), looking for vulnerabilities in files (such as dependencies and configurations). The results are saved in `trivyfs.txt`.

---

### 11. **Stage: `Docker Build & Push`**
   - This stage builds a Docker image for the application and pushes it to a Docker registry (e.g., Docker Hub).
   - `withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker')`: This step logs into Docker using the credentials stored in Jenkins (referenced by `docker-creds`).
   - `sh "docker build -t swiggy ."`: This builds a Docker image from the current directory (`.`) and tags it as `swiggy`.
   - `sh "docker tag swiggy rohinigundala2024/swiggy:latest"`: This tags the built image with the name `rohinigundala2024/swiggy:latest` for pushing to a Docker registry.
   - `sh "docker push rohinigundala2024/swiggy:latest"`: This command pushes the tagged Docker image to Docker Hub under the repository `rohinigundala2024/swiggy`.

---

### 12. **Stage: `TRIVY Image Scan`**
   - This stage scans the Docker image that was just built using **Trivy** for vulnerabilities.
   - `sh "trivy image rohinigundala2024/swiggy:latest > trivy.txt"`: The `trivy image` command scans the Docker image `rohinigundala2024/swiggy:latest` for vulnerabilities and writes the results to `trivy.txt`.

---

### 13. **Stage: `Deploy to Container`**
   - This final stage deploys the Docker container to run the application.
   - `sh 'docker run -d --name swiggy -p 3000:3000 rohinigundala2024/swiggy:latest'`: This command runs the Docker container in detached mode (`-d`), names it `swiggy`, and maps port 3000 of the container to port 3000 of the host. This is typically used for web applications to expose the app on the specified port.

---

### Summary
In summary, this pipeline performs:
1. **Code checkout** from a Git repository.
2. **SonarQube analysis** for static code quality checks.
3. **Dependency scanning** (OWASP Dependency-Check and Trivy) to identify vulnerabilities in dependencies and the file system.
4. **Docker build & push** to create and push a Docker image to Docker Hub.
5. **Image vulnerability scanning** using Trivy to ensure the Docker image has no known vulnerabilities.
6. **Deployment** of the Docker container to a running instance.

This pipeline ensures the code is properly tested, secure, and deployed with proper quality gates and security checks at each step.


**ACCESSING THE APPLICATION**

Deployed theApplication using CICD pipline, Now able to Access the Application From Public IP Address

<img width="874" alt="image" src="https://github.com/user-attachments/assets/634b7905-dbd1-44f6-871a-a50d21960d13" />

