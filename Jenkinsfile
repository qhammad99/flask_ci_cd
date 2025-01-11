pipeline {

  environment {
    dockerimagename = "agachan/lb324"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/qhammad99/flask_ci_cd.git'
      }
    }

    stage('Test Code') {
      steps{
        script {
          sh 'pytest'
        }
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("v2")
          }
        }
      }
    }
    stage('Deploying App to Kubernetes') {
      steps {
        script {
        //   kubernetesDeploy(configs: "cluster_deployment.yaml", kubeconfigId: "kubernetes")
          withKubeConfig([credentialsId: "kubernetes"]) {
            sh 'kubectl apply -f cluster_deployment.yaml'
          }
        }
      }
    }

  }

      post {
        success {
            mail to: 'testing@example.com',
                 subject: "Build Successful",
                 body: "The build was successful!"
        }
        failure {
            mail to: 'testing@example.com',
                 subject: "Build Failed",
                 body: "The build failed!"
        }
        always {
            echo 'Cleaning up resources...'
            // This block will run regardless of success or failure
        }
    }
}