pipeline {
    agent any

    tools{
        jdk 'JDK17'
        maven 'Maven3'
    }

    stages {


        stage('Build Maven') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AbdullahSalihOner/DevOps-02-Pipeline']])

                // sh 'mvn clean install'
                  bat 'mvn clean install'
            }
        }


        stage('Unit Test') {
            steps {
                // sh 'mvn test'
                  bat 'mvn test'

                // sh 'echo Unit Test'
                // bat 'echo Unit Test'
            }
        }


        stage('Docker Image') {
           steps {
               //  sh 'docker build  -t asoner01/my-application:latest  .'
                  bat 'docker build  -t asoner01/my-application:latest  .'
           }
        }


        stage('Docker Image to DockerHub') {
            steps {
                script{
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {

                        //  sh 'echo docker login -u asoner01 -p DOCKERHUB_TOKEN'
                        // bat 'echo docker login -u asoner01 -p DOCKERHUB_TOKEN'

                        // sh 'echo docker login -u asoner01 -p ${dockerhub}'
                          bat 'echo docker login -u asoner01 -p ${dockerhub}'

                        // sh 'docker image push  asoner01/my-application:latest'
                           bat 'docker image push  asoner01/my-application:latest'
                    }
                }
            }
        }


        stage('Deploy to Kubernetes'){
            steps{
                kubernetesDeploy (configs: 'deployment-service.yml', kubeconfigId: 'kubernetes')
            }
        }


       stage('Docker Image to Clean') {
           steps {
               //   sh 'docker rmi asoner01/my-application:latest'
               //  bat 'docker rmi asoner01/my-application:latest'

               // sh 'docker image prune -f'
                bat 'docker image prune -f'
           }
       }


    }
}