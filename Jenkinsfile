pipeline {
    agent any
    environment {

        PROJECT_ID = "ca-task-361717"
        CLUSTER_NAME = "catask-cluster"
        LOCATION = "us-central1"
        CREDENTIALS_ID = "ca-task"
    }
    tools{
        maven 'MAVEN'
    }
    stages {
        stage('Build Maven') {
            steps {
                echo 'Building Maven Project ...'
                /* groovylint-disable-next-line LineLength */
                checkout([$class: 'GitSCM', branches: [[name: '*/dev-gh']], extensions: [], userRemoteConfigs: [[credentialsId: '10315534-2cff-461c-a52a-cf9d7e25a4ab', url: 'https://github.com/ahmedghilman/docker-javaspring.git']]])
                sh 'mvn -Dmaven.test.skip clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing Maven Project ...'
                sh 'mvn test'
            }
        }
        stage('Build Image') {
            steps {
                echo 'Building Docker Image ..'
                /* groovylint-disable-next-line GStringExpressionWithinString */
                sh  "docker build -t gahmed/catask-app:${env.BUILD_ID} ."
            }
        }
        stage('Push Docker Image To DockerHub') {
            steps {
                echo 'Pushing Docker Image to Dockerhub....'
                /* groovylint-disable-next-line GStringExpressionWithinString */
                sh "docker tag gahmed/catask-app:${env.BUILD_ID}  gahmed/catask-app:${env.BUILD_ID}"
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubpwd')]) {
                    // some block
                    /* groovylint-disable-next-line GStringExpressionWithinString */
                    sh 'docker login -u gahmed -p ${dockerhubpwd}'
                    sh "docker push gahmed/catask-app:${env.BUILD_ID}"
                }
            }
        }
        stage('Deploy To GKE') {
            steps {
                echo "Deployment started ..."
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', \
                  projectId: env.PROJECT_ID, \
                  clusterName: env.CLUSTER_NAME, \
                  location: env.LOCATION, \
                  manifestPattern: 'deployment.yaml', \
                  credentialsId: env.CREDENTIALS_ID, \
                  verifyDeployments: true])
            }
        }
    }
}
