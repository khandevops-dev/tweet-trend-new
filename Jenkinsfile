pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    stages {
        stage('Hello') {
            steps {

                git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/khandevops-dev/tweet-trend-new.git'
            }
        }
    }
}