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

         stage('Build') { 
             steps {
                echo '-------Build Started mf--------'
                git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
                sh 'mvn -B -DskipTests clean package' 
                archiveArtifacts artifacts: 'target/*.jar', fingerprint:true  
            }
        }
        
         stage('Test') {
             steps {
                sh 'mvn test'
                sh 'mvn verify'
                junit 'target/surefire-reports/*Test.xml'
                junit 'target/failsafe-reports/*IT.xml'
            }
             
        }
   
        stage('SCM') {
            steps{
            git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
        }
    }

        stage('SonarQube Analysis') {  
            steps {
                withSonarQubeEnv(installationName:'SonarQube') { 
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar'
                }
            }
        }
        
        stage('Push'){
             steps{
                 sshagent (credentials['jenkinsh']){
                     sh 'scp -o StrictHostKeyChecking=no target/*.jar jenkins@3.125.242.200'
                 }                 
             }
         }

        stage("Quality Gate") {
            steps {
                echo '---------Quality Gate--------'
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

    }
    post {
        success {   
            echo 'Build finished contrary to common sense'
            }
         }
    
}
