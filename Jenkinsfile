node('AppServer2')
{
    def app
    stage('Clone Git')
    {
        // Clone the repository
        checkout scm
    }
    stage('SCA TEST')
    {
        snykSecurity(
          snykInstallation: 'Snyk@latest',
          snykTokenId: 'Snykid',
          severity: 'high'
        )
    }
    stage('Build and tag')
    {
        app = docker.build("noahbartell/snake_game_2140")
    }
    stage('Post to Docker Hub')
    {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
        {
            app.push("latest")
        }
    }
    stage('Pull and Deploy')
    {
        sh 'docker-compose down'
        sh 'docker-compose up -d'
    }
}
node('Sonarqube-Server-CWEB2140')
{
    stage('SonarQube Analysis'){
    def scannerHome = tool 'SonarQubeScanner';
    withSonarQubeEnv('SonarQube')
        {
        sh "${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=Snake-Game \
            -Dsonar.sources=."
        }
    }
}
