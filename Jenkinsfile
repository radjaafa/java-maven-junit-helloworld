pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    node { 
      def mvnHome
      def scannerHome
      mvnHome = tool 'Maven'
      scannerHome = tool 'Sonar'
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
                echo '-----Unit Tests------'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Sonar Analysis') {
            steps {
                echo '---SonarAnalysis-----'
                bat(/"$scannerHome}\bin-sonnar-scanner" -Dsonar.projectKey=java-maven-junit-helloworld -Dsonar.sources=./)
                
          }
      }
    } 
}
