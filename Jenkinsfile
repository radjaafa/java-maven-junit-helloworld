pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            label 'aws-slave'
            args  '-v /root/.m2:/root/.m2'
    }
}
    stages {
      /*  stage('Clean') {
            steps{
                cleanWs()
            }
        } */

         stage("build & SonarQube analysis") {
            steps {
              withSonarQubeEnv('My SonarQube Server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
         }
        
         stage('Test') {
             steps {
                sh 'mvn test'
            }
            post {
                always{
                    junit 'target/surefire-reports/*.xml'
                }
            }
                 
        }

        /*stage ('Download arts') {
             steps {
                 
                 sh 'echo "artifact file" > generatedfile.txt'

            }
        } */

        /*stage ('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'           
            }
        }  */  
                
        stage("Quality Gate") {
            steps {
                echo '---------Quality Gate--------'
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
            }
                 }
        }
     post {
        always {
             archiveArtifacts artifacts: '**/*.jar',
             fingerprint:true 
            }
         }
    }
}
