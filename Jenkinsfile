pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            label 'aws-slave'
            args  '-v /root/.m2:/root/.m2'
    }
}
    options {
        skipStagesAfterUnstable()
    }
    stages {
      /*  stage('Clean') {
            steps{
                cleanWs()
            }
        } */

         stage('Build') { 
             steps {
                echo '-------Build Started mf--------'
                git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
                sh 'mvn clean package sonar:sonar' 
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
                timeout(time: 5, unit: 'MINUTES') {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK'){
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                   
                }
            }
        }
    }
    post {
        success {
             archiveArtifacts artifacts: '**/*.jar',
             fingerprint:true 
            }
         }
}
