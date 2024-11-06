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



   
