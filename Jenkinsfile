pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = 'register-app-pipeline'
        RELEASE_VERSION = '1.0.0'
        DOCKER_USER = 'abuchijoe'
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "$DOCKER_USER/${APP_NAME}:${RELEASE_VERSION}"
        IMAGE_TAG = "$DOCKER_USER/${APP_NAME}:${RELEASE_VERSION}"
    }
    stages {
        stage('Cleanup workspace') {
      steps {
        cleanWs()
      }
        }

        stage('Checkout from SCM') {
      steps {
        git branch: 'main', url: 'https://github.com/Joseph-peemi/Terraform-Pipeline-Production.git'
      }
        }

        stage('Build Application') {
      steps {
        sh 'mvn clean package'
      }
        }

        stage('Test Application') {
      steps {
        sh 'mvn test'
      }
        }
        stage('SonarQube Analysis') {
      steps {
        script {
          withSonarQubeEnv('sonarqube-server') {
            withCredentials([string(credentialsId: 'Jenkins-sonarqube-token', variable: 'SONAR_TOKEN')]) {
              sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar -Dsonar.host.url=http://172.31.24.200:9000 -Dsonar.token=$SONAR_TOKEN'
            }
          }
        }
      }
        }
        stage('Quality Gate') {
      steps {
        script {
          waitForQualityGate abortPipeline: false, credentialsId: 'Jenkins-sonarqube-token', sonarQubeEnvName: 'sonarqube-server'
        }
      }
        }
        stage('Build & Push Docker Image') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', DOCKER_PASS) {
            docker_image = docker.build(IMAGE_NAME)
            docker_image.push("${RELEASE_VERSION}")
            docker_image.push('latest')
          }
        }
      }
        }
    }
}
