pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.3/bin:$PATH"
}
    stages {
        stage("build"){
            steps{
                echo "------------------------- Build Started -----------------------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------------------------- Build Completed ----------------------"
            }
        }
        stage("Test"){
            steps{
                echo "------------------------- Test Started -----------------------"
                sh 'mvn surefire-report:report'
                echo "------------------------- Test Completed ----------------------"
            }
        }
        stage('SonarQube analysis') {
        environment {
         scannerHome = tool 'sonar-scanner'
        }
            steps{
                withSonarQubeEnv('sonarqube-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
            }
    
        }
        }
    }
}