pipeline {
  agent any
  environment {
      DOCKERCREDS_USR = 'dry1995' //use the credentials just created in this stage
}
  stages {
    stage('pull code') {
      steps{
      git branch: 'pipeline-editor', credentialsId: 'GitHub', url: 'git@github.com:JohnDing1995/jenkins-katas.git'
      stash excludes: '.git/', name: 'code'
      }
    }
    stage('Say Hello') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }
    // some block
            stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          options { skipDefaultCheckout(true) }
          steps {
            unstash 'code'
            sh './ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash 'bin'
          }
        }
        stage('test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }
          }
          options { skipDefaultCheckout(true) }
          steps {
            unstash 'code'
            sh './ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }

        }
        
      }
      
    }
stage ('Push to Dockerhub'){
      options {
        skipDefaultCheckout(true)
      }
        environment {
      DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
}
    steps {
          unstash 'bin' //unstash the bin code
          sh 'ci/build-docker.sh'
          sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
          sh 'ci/push-docker.sh'
    }
      }

  }
  post {
        always {

            deleteDir() /* clean up our workspace */
        }
  }
  
}