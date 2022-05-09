pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Unit Tests - JUnit and Jacoco') {
	      steps {
	        sh "mvn test"
	      }
	      post {
	        always {
	          junit 'target/surefire-reports/*.xml'
	          jacoco execPattern: 'target/jacoco.exec'
	        }
	     }
      }   
      stage('Mutation Tests - PIT') {
		      steps {
		        sh "mvn org.pitest:pitest-maven:mutationCoverage"
		      }
		      post {
		        always {
		          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
		        }
		      }
		    }   
		  stage('SonarQube - SAST') {
	      steps {
	        sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo.eastasia.cloudapp.azure.com:9000 -Dsonar.login=9a9cb6c4eeae2e3616ac7090ff57751df41eba36"
	      }
      }  
      stage('Docker build and push') {
          steps {
            
            withDockerRegistry(credentialsId: "docker-hub", url: "") {
            		sh 'printenv'
            		sh 'docker build -t ashrujitpal/numeric-app:""$GIT_COMMIT"" .'
            		sh 'docker push ashrujitpal/numeric-app:""$GIT_COMMIT""'
              }
          }
        } 
      stage('Kubernetes Deployment - DEV') {
            steps {
              
              withKubeConfig(credentialsId: "kubeconfig") {
              		sh "sed -i 's#replace#ashrujitpal/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              		sh "kubectl apply -f k8s_deployment_service.yaml" 
                }
            }
        }
    }
}