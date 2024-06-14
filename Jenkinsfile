pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Sagmaddy/Insurance/', branch: 'master'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t sagmaddy/insuranceproject:v1 .'
                    sh 'docker images'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Ensure Jenkins has sudo privileges to run docker
                    sh 'sudo docker run -itd -p 8083:8081 sagmaddy/insuranceproject:v1'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhubpass', variable: 'dockerhubpass')]) {
                    sh 'docker login -u sagmaddy -p ${dockerhubpass}'
                }
                sh 'docker push sagmaddy/insuranceproject:v1'
            }
        }

        stage('Deploy on Ansible') {
            steps {
                ansiblePlaybook become: true, credentialsId: 'ansible', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', vaultTmpPath: ''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}




