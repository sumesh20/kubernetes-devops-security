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
        
        
        stage('SonarQube -- SAST') {
            steps {
              
              sh  "mvn clean verify sonar:sonar  -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecopsdemo.eastus2.cloudapp.azure.com:9000 -Dsonar.login=sqp_aff259da7576807c025fb2e072a902b62033c81b"
             
            }
            

        } 
         stage('Docker Build and Push') {
            steps {
              withDockerRegistry([credentialsId: "docker-hub",url: ""]) {
              sh 'printenv'
              sh 'docker build -t sumesh20/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push sumesh20/numeric-app:""$GIT_COMMIT""'
              
              }
            }
         }  
         stage('Kubernetes Deployment - DEV') {
            steps {
              withKubeConfig([credentialsId: 'kubeconfig']) {
             
              sh "sed -i 's#replace#sumesh20/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
              }
            }
         }
        
    }
}
