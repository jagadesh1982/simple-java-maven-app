pipeline {

  agent any

  environment {
    registry = "docker.io/<user Name>/<image Name>"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }

stages {
   stage("build"){
        agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                  }
             }
       
       steps { 
                 
                sh 'mvn -B -DskipTests clean package'
                stash includes: 'target/*.jar', name: 'targetfiles'
            }     
       }
  
    stage('Test') {
    	agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                  }
             }

            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    
     stage('Building image') {
          steps{
             script {
                unstash 'targetfiles'
                sh 'ls -l -R'
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
            }
          }
        }



      stage('Deploy Image') {
          steps{
             script {
                docker.withRegistry( '', registryCredential ) {
                dockerImage.push()
             }
           }
         }
       }


   stage('Remove Unused docker image') {
      steps{
          script {
            sh "docker rmi -f $registry:$BUILD_NUMBER"
        }
      }
    }
        

  }
}
