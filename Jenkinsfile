pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    mvn -B  install -DskipTests'''
      }
    }
        }
        stage('Static Analysis') {
          steps {
            echo '...run SonarQube or other SAST tools here'
          }
        }
        stage('Build Container') {
          steps {
            sh '''#!/bin/bash
cd webgoat-server
whoami
pwd
echo $PATH
docker build --no-cache -t webgoat/webgoat-8.0:latest .
docker tag webgoat/webgoat-8.0:latest hub.docker.com/webgoat/webgoat-8.0:latest
docker push hub.docker.com/repository/docker/bilalk88/webgoat/webgoat-8.0:latest
'''
          }
        }
      }
    }
    stage('Test Container') {
      parallel {
        stage('Anchore OS Scan') {
          steps {
            sh 'echo "nexus:5000/webgoat/webgoat-8.0 ${WORKSPACE}/webgoat-server/Dockerfile" > anchore_images'
            anchore 'anchore_images'
          }
        }
        }
      }
    }
    stage('Publish Container') {
      when {
        branch 'master'
      }
      steps {
        sh '''
                    docker tag webgoat/webgoat-8.0 hub.docker.com/webgoat/webgoat-8.0:8.0
                    docker push hub.docker.com/repository/docker/bilalk88/webgoat/webgoat-8.0
                '''
      }
    }
  }
  tools {
    maven 'Maven'
  }
}
