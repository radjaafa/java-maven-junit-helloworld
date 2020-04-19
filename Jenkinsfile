pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {

         stage('Build') { 
             steps {
                echo '-------Build Started mf--------'
                git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
                sh 'mvn -B -DskipTests clean package' 
            }
        }
         stage('Test') {
             steps {
                sh 'mvn test'
            }
             post {
                always {
                    junit 'target/surefire-reports/*.xml'
                 }
            }
        }
   
        stage('SCM') {
            steps{
            git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
        }
    }


        stage('SonarQube Analysis'){  
            steps {
                withSonarQubeEnv(installationName:'sonarqube') { 
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar'
                }
            }
        }
        stage ('Download arts') {
            steps {
                 sh'mkdir testart'
                 sh 'echo "artifact file" > generatedfile.zip'

            }
        }
            
     post {
         always {
             archiveArtifacts artifacts: '**/*',
             onlyIfSuccessful: true
            }
         }
           

        stage("Quality Gate") {
            steps {
                echo '---------Quality Gate--------'
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
            
        
    
    
