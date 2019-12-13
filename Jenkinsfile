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
        
        boolean testsPassed = true
        stage('Test') {
            steps {
                try {
                    sh "mvn test"
                } catch (Exception e) {
                    testsPassed = false
                }
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
                beforeInput true
                expression { testsPassed == true }
            }
            input {
                message "Release version"
                parameters {
                    string(name: 'releaseVersion', description: 'Release version')
                }
            }
            steps {
                sh "mvn release:prepare release:perform -DreleaseVersion=${releaseVersion}"
            }
        }
    }
}
