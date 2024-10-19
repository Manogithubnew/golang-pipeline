pipeline {
   agent any


   tools {
      go 'go-1.23.2'
   }
   environment {
       GO111MODULE='on'
       DOCKERHUB_CREDENTIALS = credentials('dockerhub')
       DOCKER_IMAGE = 'ephraimaudu/test-app'
       GITHUB_CREDENTIALS = 'git-secret'
       SONAR_TOKEN = credentials('SONAR_TOKEN')
   }


   stages{
       stage('Checkout'){
           steps{
               echo "checking out repo"
               git url: 'https://github.com/audu97/test-project', branch: 'master',
               credentialsId: "${GITHUB_CREDENTIALS}"
           }
       }
       stage('Run SonarQube Analysis') {
           steps {
               script {
                   echo 'starting analysis'
                   sh '/usr/local/sonar/bin/sonar-scanner -X -Dsonar.organization=eph-test-app -Dsonar.projectKey=eph-test-app-test-go-app -Dsonar.sources=. -Dsonar.host.url=https://sonarcloud.io'
               }
           }
       }
       stage('Run Docker Build'){
           steps{
               script{
                    echo "starting docker build"
                    sh "docker build build -t ${DOCKER_IMAGE}:${env.BUILD_ID} ."
                    echo "docker built successfully"
               }
           }
       }
       stage('push to docker hub'){
           steps{
               echo "pushing to docker hub"
               script{
                   docker.withRegistry('https://index.docker.io/v1/', 'dockerhub'){
                       docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push()
                   }
               }
               echo "done"
           }
       }
   }


   post {
       always{
           cleanWs()
       }
   }
}
