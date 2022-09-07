pipeline {
    agent any
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
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}