pipeline {    
    agent {
        label 'executor'
    }

    tools {
        maven "M3"
    }

    stages {
        stage('Build') {
            steps {
                checkout scm
                sh "mvn -DskipTests clean package"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
            
        stage('Release and Publish artifact') {
            when {
                branch "master"
            }
            steps {
                script {
                    if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
                        input {
                            message "Release version"
                            parameters {
                                string(name: 'releaseVersion', description: 'Release version')
                            }
                        }
                        sh "mvn release:prepare release:perform -DreleaseVersion=${releaseVersion}"
                    }
                }
            }
        }
    }
}
