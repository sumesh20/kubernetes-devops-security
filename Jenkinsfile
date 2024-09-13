pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //update desktop second  time
            }
        }   
        stage('Unit tests') {
            steps {
              sh "mvn test"
             
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.ex'
                }
            }

        }   
         stage('Docker Build and Push') {
            steps {
              withDockerRegistry([credentialsId: "docker-hub", url: "https://github.com/sumesh20/"]) {
              sh 'printenv'
              sh 'docker build -t sumesh20/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push sumesh20/numeric-app:""$GIT_COMMIT""'
              }
            }
         }
        
    }
}
