
def registry = 'https://dev05.jfrog.io'
def imageName = 'dev05.jfrog.io/dev05-docker-local/ttrend'
def version   = '2.1.4'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
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
        stage("Quality Gate"){
            steps{
                script{
                    timeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                    }
                } 
            } 
          
        }
        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                        }"""
                        def buildInfo = server.upload(uploadSpec)
                        buildInfo.env.collect()
                        server.publishBuildInfo(buildInfo)
                        echo '<--------------- Jar Publish Ended --------------->'  
            
                }
            }   
        }
        stage(" Docker Build ") {
            steps {
                script {
                    echo '<--------------- Docker Build Started --------------->'
                    app = docker.build(imageName+":"+version)
                    echo '<--------------- Docker Build Ends --------------->'
                }
            }
        }

        stage (" Docker Publish "){
            steps {
                script {
                    echo '<--------------- Docker Publish Started --------------->'  
                    docker.withRegistry(registry, 'jfrog-cred'){
                    app.push()
                    }    
                    echo '<--------------- Docker Publish Ended ----------------->'
                }
            }
        }
    }
}