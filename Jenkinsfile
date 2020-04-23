pipeline {
    agent {
        label "aws-slave"
        }
    
    stages {
                stage('Clean') {
            steps{
                cleanWs()
            }
        }

        

         stage('Build') { 
             steps {
                echo '-------Build Started mf--------'
                git 'https://github.com/radjaafa/java-maven-junit-helloworld.git'
                sh 'mvn clean package' 
            }
        }
         stage('Test') {
             steps {
                sh 'mvn test'
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
                 
                 sh 'echo "artifact file" > generatedfile.txt'

            }
        }

        stage ('Deploy') {
            steps {
            sh 'ssh jenkins@3.125.242.200 rm -rf /temp_deploy/dist'
            sh 'ssh jenkins@3.125.242.200 mkdir -p /temp_deploy'
            sh 'scp -r dist jenkins@3.125.242.200"/temp_deploy/dist'
            sh 'ssh jenkins@3.125.242.200 "rm -rf /var/temp_deploy/dist && mv /var/temp_deploy/dist /var/example"'
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
    post {
        always {
             archiveArtifacts artifacts: '**/*.jar',
             fingerprint:true 
             junit 'target/surefire-reports/*.xml'
            }
         }

    
}
       
   
