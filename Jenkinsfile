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
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE_VERSION}"
        CD_TRIGGER_TOKEN = 'gitops-token'
        JENKINS_API_TOKEN = 'JENKINS_API_TOKEN'
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
              sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar -Dsonar.host.url=http://172.31.32.55:9000 -Dsonar.token=$SONAR_TOKEN'
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
            docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
            docker_image.push("${IMAGE_TAG}")
            docker_image.push('latest')
          }
        }
      }
        }

        stage('Trivy Scan') {
      steps {
        script {
          sh "docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image ${IMAGE_NAME}:latest --timeout 5m --no-progress --security-checks vuln --exit-code 0 --severity HIGH,CRITICAL"
        }
      }
        }

        stage('Trigger CD Pipeline') {
      steps {
        script {
          echo "Triggering CD pipeline with IMAGE_TAG = ${APP_NAME}:${RELEASE_VERSION}"
          build job: 'gitops-complete-pipeline',
                        parameters: [string(name: 'IMAGE_TAG', value: "${APP_NAME}:${RELEASE_VERSION}")],
                        wait: false
        }
      }
        }
    }

    post {
        always {
      cleanWs()
        }
    }
}
