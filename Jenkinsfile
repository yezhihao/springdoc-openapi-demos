def agentWorkspace
def pushToRegitry = {
  dockerImage.push()
  dockerImage.push("${env.BUILD_NUMBER}")
}
pipeline {
  agent any
  stages {
    stage('do everything in docker') {
      agent {
        docker {
          image 'gradle:6.5.1-jdk8'
          args '-v $HOME/.gradle:/root/.gradle'
        }
      }
      stages {
        stage('Test') {
          steps {
            sh './gradlew clean'
          }
        }
        stage('Build') {
          steps { 
            sh agentWorkspace = ${WORKSPACE}
            sh './gradlew :springdoc-openapi-spring-boot-2-webmvc:build'
            sh "mkdir -p target"
            sh "cp -R springdoc-openapi-spring-boot-2-webmvc/Dockerfile target/"
            sh "cp -R springdoc-openapi-spring-boot-2-webmvc/build/libs* target/"
          }
        }
      }
    }
    stage('build docker') {
      steps {
        script {
          echo "${agentWorkspace}" 
          dockerImage = docker.build('bnasslahsen/springdoc-openapi-spring-boot-2-webmvc', '${agentWorkspace}/target')
        }
      }
    }
    stage('Deploy Image') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'docker-login', pushToRegitry)
        }
      }
    }
  }
}
