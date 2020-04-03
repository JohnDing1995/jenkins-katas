pipeline {
  agent any
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
node('host') {
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
    }

  }
  post {
        always {

            deleteDir() /* clean up our workspace */
        }
}