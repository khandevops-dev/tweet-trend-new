pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    stages {
        stage('clone-code') {
            steps {

                git branch: 'main', credentialsId: 'git-credential', url: 'https://github.com/khandevops-dev/tweet-trend-new.git'
            }
        }
    }
}