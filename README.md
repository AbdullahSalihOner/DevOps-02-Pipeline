# DevOps-02-Pipeline

Welcome to **DevOps-02-Pipeline**! This project demonstrates the creation and management of a CI/CD pipeline using DevOps practices. The aim is to automate the build, test, and deployment processes to streamline software delivery, improve code quality, and increase deployment frequency.

In this project, we cover essential concepts and tools to set up a robust and scalable pipeline, allowing for continuous integration and continuous delivery (CI/CD) in a cloud-native environment.

## Key Objectives

- **Automated Builds**: Set up a pipeline that automatically triggers builds on code changes.
- **Automated Testing**: Integrate testing steps to ensure code quality and stability at each stage.
- **Continuous Integration**: Ensure seamless integration of new code with automated validation and feedback.
- **Continuous Delivery**: Automate deployment to staging and production environments for faster delivery cycles.

---

This project provides a hands-on approach to setting up a DevOps pipeline with practical examples, making it easier for developers and DevOps engineers to manage, scale, and maintain applications in a dynamic environment.

## Tools and Technologies

This project leverages several essential DevOps tools and technologies to set up and manage a CI/CD pipeline:

- **Jenkins**: Automates the CI/CD pipeline, handling build, test, and deployment stages.
- **Java**: Provides the runtime environment required to build and run applications.
- **Git & GitHub**: Version control system and code repository, respectively, used to manage source code.
- **Maven**: A build automation tool primarily used for Java projects, managing dependencies and build processes.
- **Docker & DockerHub**: Containerization platform and image registry to package, deploy, and manage applications as containers.
- **Kubernetes**: An orchestration platform for deploying, scaling, and managing containerized applications.

---

## Jenkins Setup

To set up Jenkins locally, follow these steps:

1. **Navigate to the Jenkins Directory**: Open your command line and move to the Jenkins installation directory.

   ```bash
   cd D:\DevOps\Jenkins
   java -jar jenkins.war --httpPort=9999
   ```

### Adding Maven in Jenkins

To automate the build process, we need to configure Maven within Jenkins. Follow these steps to add Maven (version 3.9.6) as a tool in Jenkins:

1. **Open Jenkins**: Access your Jenkins instance in your browser at [http://localhost:9999](http://localhost:9999).

2. **Navigate to Global Tool Configuration**:
   - From the Jenkins dashboard, go to **Manage Jenkins** > **Global Tool Configuration**.

3. **Add Maven**:
   - Scroll down to the **Maven** section.
   - Click **Add Maven** to create a new Maven installation.
   - Set the **Name** (e.g., `Maven-3.9.6`).
   - Under **Install automatically**, select **Version 3.9.6** from the list. Jenkins will handle the installation, or you can manually specify a local Maven installation if needed.

4. **Save**:
   - Click **Save** to save the configuration.

Jenkins is now configured to use Maven 3.9.6, allowing you to automate the build process for Java applications within the pipeline.

---

### Setting Up Credentials and Docker in Jenkins

In order to automate pushing images to DockerHub and pulling code from GitHub, we need to set up credentials in Jenkins. Additionally, Docker must be configured as a tool to manage containerization tasks.

#### Adding DockerHub and GitHub Credentials

1. **Open Jenkins**: Access your Jenkins instance at [http://localhost:9999](http://localhost:9999).

2. **Navigate to Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials**.
   - Select the appropriate domain (or create a new domain if necessary).

3. **Add DockerHub Credentials**:
   - Click **Add Credentials**.
   - Set the **Kind** to **Username with password**.
   - Enter your **DockerHub username** and **password**.
   - For **ID** or **Description**, use something descriptive like `dockerhub-credentials` for easy identification.
   - Click **OK** to save.

4. **Add GitHub Credentials**:
   - Click **Add Credentials** again.
   - Set the **Kind** to **Username with password** or **Secret text** (if using a personal access token).
   - Enter your **GitHub username** and **password** (or **token** for **Secret text**).
   - Use an **ID** like `github-credentials` for easy identification.
   - Click **OK** to save.

#### Adding Docker as a Tool

1. **Navigate to Global Tool Configuration**:
   - From the Jenkins dashboard, go to **Manage Jenkins** > **Global Tool Configuration**.

2. **Add Docker**:
   - Scroll down to the **Docker** section.
   - Click **Add Docker** to create a new Docker installation.
   - Set the **Name** (e.g., `Docker-latest`).
   - Under **Install automatically**, choose **Latest** to allow Jenkins to handle Docker installations and updates.

3. **Save Configuration**:
   - Click **Save** to store these changes.

With Docker and credentials configured in Jenkins, you’re now ready to automate tasks involving Docker containers and integration with GitHub repositories.

---


### Jenkins Pipeline for Maven Build and Docker Image Creation

This section describes a Jenkins Pipeline script that performs two main tasks:
1. **Build the Maven Project**: Compiles the project and packages it using Maven.
2. **Create a Docker Image**: Builds a Docker image from the project, tagging it with the `latest` tag.

#### Pipeline Script

Below is the Jenkins Pipeline script. This script assumes Jenkins has been configured with `Maven3` as a tool, and Docker is installed and available on the Jenkins agent.

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven3'  // Uses the Maven tool configured in Jenkins
    }

    stages {

        stage('Build Maven') {
            steps {
                // Check out code from GitHub repository
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/AbdullahSalihOner/DevOps-02-Pipeline']]
                )

                // Build and package the application with Maven
                // For Unix/Linux, use: sh 'mvn clean install'
                bat 'mvn clean install'  // For Windows, use 'bat'
            }
        }
        
        stage('Docker Image') {
            steps {
                // Build Docker image and tag it
                // For Unix/Linux, use: sh 'docker build -t asoner01/my-application:latest .'
                bat 'docker build -t asoner01/my-application:latest .'  // For Windows, use 'bat'
            }
        }

    }
}
```

#### DockerHub Push Stage

To make the Docker image available publicly, we’ve added a step to push it to DockerHub. Make sure that DockerHub credentials have been configured in Jenkins as described earlier.

Update the Jenkins Pipeline script to include the DockerHub push step:

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Build Maven') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/AbdullahSalihOner/DevOps-02-Pipeline']]
                )
                bat 'mvn clean install'
            }
        }
        
        stage('Docker Image') {
            steps {
                bat 'docker build -t asoner01/my-application:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        bat 'docker push asoner01/my-application:latest'
                    }
                }
            }
        }
    }
}
```

### Deploying to Kubernetes

To automate the deployment of the Docker image to a Kubernetes cluster, we configured Jenkins to use the Kubernetes plugin. This allows us to deploy the application directly from Jenkins.

#### Jenkins Pipeline Update for Kubernetes Deployment

In this stage, we use the `kubernetesDeploy` step to deploy our application to Kubernetes using a YAML configuration file (`deployment-service.yml`).

Here’s the updated Jenkins Pipeline script:

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {

        stage('Build Maven') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/AbdullahSalihOner/DevOps-02-Pipeline']]
                )
                bat 'mvn clean install'
            }
        }
        
        stage('Docker Image') {
            steps {
                bat 'docker build -t asoner01/my-application:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        bat 'docker push asoner01/my-application:latest'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                kubernetesDeploy(configs: 'deployment-service.yml', kubeconfigId: 'kubernetes')
            }
        }

    }
}
```

### Monitoring the Kubernetes Deployment with Minikube

After deploying the application to a Kubernetes cluster using Jenkins, we can use **Minikube** to monitor and manage the application locally. Minikube provides a convenient way to run Kubernetes on your local machine, enabling you to view the status of pods, services, and deployments.

#### Accessing the Minikube Dashboard

To launch the Minikube dashboard and monitor the deployment, follow these steps:

1. **Start Minikube** (if it’s not already running):

   ```bash
   minikube start
   minikube dashboard
    ```
   



   
