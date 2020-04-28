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
                archiveArtifacts artifacts: '**/*.jar', fingerprint:true  
            }
        }
        
         stage('Test') {
             steps {
                sh 'mvn test'
                sh 'mvn verify'
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

        /*stage ('Deploy') {
                when{
                    expression {
                        currentBuild.result == null || currentBuild.result == 'SUCCESS'
                    }
                }
                steps{
                    sh './build.sh'
                }        
        }    */

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
             junit 'target/surefire-reports/*Test.xml'
             junit 'target/failsafe-reports/*IT.xml'
             archive 'target/*jar'
            }
         }
        stage('Push'){
             steps{
                 sshagent (credentials['jenkins']){
                     sh 'scp -o StrictHostKeyChecking=no target/*.jar jenkins@3.125.242.200'
                 }                 
             }
         }
}
