pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git-scm') {
            steps {
                git branch: 'main', url: 'https://github.com/awsRajuKing31/Ekart.git'
            }
        }
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }
        stage('owasp DP') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        stage('nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17',
                maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy -DskipTests=true"  
                }
            }
        }
        stage('docker-build') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName:'docker') {
                        sh "docker build -t awskingraju/ekart:latest -f docker/Dockerfile ."
                    }
                }
            }
        }
        stage('dockerpush') {
            steps {
                script { withDockerRegistry(credentialsId: 'docker-cred', toolName:'docker') {
                    sh "docker push awskingraju/ekart:latest"
                }
                }
            }
        }
    }
}

