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
      stage('Docker build and push') {
            steps {
              sh 'printenv'
              sh 'docker build -t ashrujitpal/mumeric-app:""$GIT_COMMIT""'
              sh 'docker push ashrujitpal/mumeric-app:""$GIT_COMMIT""'
            }
        } 
    }
}