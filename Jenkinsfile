pipeline {
    agent none
    
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'AppServer2'
            }
            steps {
                checkout scm
            }
        }  

        stage('SCA-SAST-SNYK-TEST') {
            agent any
            steps {
                script {
                    snykSecurity(
                        snykInstallation: 'Snyk@latest',
                        snykTokenId: 'Synkid',
                        severity: 'critical'
                    )
                }
            }
        }

        stage('SonarQube Analysis') {
            agent {
                label 'Sonarqube-Server-CWEB2140'
            }
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=Snake_Game \
                            -Dsonar.sources=."
                    }
                }
            }
        }

        stage('BUILD-AND-TAG') {
            agent {
                label 'AppServer2'
            }
            steps {
                script {
                    def app = docker.build("noahbartell/snake_game_2140")
                }
            }
        }

        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'AppServer2'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        app.push("latest")
                    }
                }
            }
        }

        stage('DEPLOYMENT') {    
            agent {
                label 'AppServer2'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}
