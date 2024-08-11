pipeline {
    agent any
    tools {
        maven 'maven-3.9.8'
    }
    stages {
        stage('git clone') {
            steps {
                git branch: 'main', url: 'https://github.com/gspvsrgit/maven-web-app-project-kk-funda.git'
                echo "Build Number is : ${env.BUILD_NUMBER}"
                echo "Node Name is : ${env.NODE_NAME}"
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('sonar-scan') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }
        
        stage('deploy-artifact') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        
        stage('deploy-tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://3.84.157.31:8080/')], contextPath: null, war: '**/*.war'
            }
        }
    }

    post {
        always {
            script {
                notifyBuild(currentBuild.result)
            }
        }
        failure {
            script {
                currentBuild.result = "FAILED"
                throw e
            }
        }
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESSFUL'

    def color = 'RED'
    def colorCode = '#FF0000'
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        color = 'YELLOW'
        colorCode = '#FFFF00'
    } else if (buildStatus == 'SUCCESSFUL') {
        color = 'GREEN'
        colorCode = '#00FF00'
    } else {
        color = 'RED'
        colorCode = '#FF0000'
    }

    slackSend(color: colorCode, message: summary)
}
