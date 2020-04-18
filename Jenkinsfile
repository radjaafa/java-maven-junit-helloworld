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
        
        stage('SonarQube') {
            environment {
                scannerHome = tool 'SonarQubeAnalysis'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
