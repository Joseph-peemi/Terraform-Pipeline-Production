pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    stages {
        stage("Cleanup workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', url: 'https://github.com/Joseph-peemi/Terraform-Pipeline-Production.git'
            }
        }

        stage("Build Application") {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }
        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv('sonarqube-server') {
                        withCredentials([string(credentialsId: 'Jenkins-sonarqube-token', variable: 'SONAR_TOKEN')]) {
                            sh 'mvn sonar:sonar -Dsonar.token=$SONAR_TOKEN'
                        }
                    }
                }
            }
        }
    }
}
