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
        stage('Mutation Tests -- PIT') {
            steps {
              sh "mvn org.pitest:pitest-maven:mutationCoverage"
             
            }
            post {
                always {
                   pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
                }
            }

        } 
        stage('SonarQube -- SAST') {
            steps {
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.projectName='numeric-application' -Dsonar.host.url=http://devsecopsdemo.eastus2.cloudapp.azure.com:9000 -Dsonar.token=sqp_b0a903fb5cb55b7a1fd80dab2564246cd8a8e41e"
             
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
