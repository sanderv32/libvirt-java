pipeline {    
    agent {
        label 'executor'
    }

    tools {
        maven "M3"
    }
    options {
        timestamps()
        ansiColor("xterm")
    }

    parameters {
        booleanParam(name: "RELEASE",
                description: "Build a release from current commit.",
                defaultValue: false
        )
        string(name: 'releaseVersion',
            defaultValue: '',
            description: 'Release version'
        )
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

        stage('Build & Deploy SNAPSHOT') {
            when {
                branch 'master'
            }
            steps {
                sh "mvn -B deploy"
            }
        }

        stage('Release and Publish artifact') {
            when {
                expression { params.RELEASE }
            }
            steps {
                sh "mvn release:prepare release:perform -DreleaseVersion=${params.releaseVersion}"
            }
        }
    }
}
