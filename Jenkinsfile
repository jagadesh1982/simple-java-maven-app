pipeline {

  agent none 

  environment {
    registry = "docker.io/jagadesh1982"
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
       
       stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }  
 

  }

}
